---
layout: docs
title: Extending the Client
permalink: /docs/extend/client/
---
As mentioned in the Introduction, the client layer exposes a **user interface** so that Flarum can be used by humans. There are two parts to the client: a basic read-only HTML version of the content, and an interactive single-page JavaScript application. Both consume the JSON API.

## Routes

All of the forum's default routes are registered in `Flarum\Forum\ForumServiceProvider`. Each route has a corresponding **Action** class, which is essentially a controller. An Action receives a [PSR-7](https://github.com/php-fig/http-message) Request object and must return a Response.

`Flarum\Forum\Actions\ClientAction` is the base action class which sets up and responds with a ClientView instance. ClientView is a view which displays a master HTML template, including all assets required for the client JavaScript application. It is highly configurable, and you can:

* Set the title of the document with `setTitle()`
* Set the SEO content of the page (displayed in `<noscript>` tags) with `setContent()`
* Set an API response document to be preloaded into the page with `setDocument()`
* Add HTML to the head or foot of the page with `addHeadString()` and `addFootString()`

New forum routes can be registered using the `RegisterForumRoutes` event:

```php
use Flarum\Events\RegisterForumRoutes;

$events->listen(RegisterForumRoutes::class, function (RegisterForumRoutes $event) {
    $event->get(
        '/tags', // The route URI
        'forum.tags', // A unique name for the route
        'Flarum\Tags\Forum\TagsAction' // The Action class to handle the request (optional)
    );
});
```

Specifying an Action class (the third parameter) is optional. If not specified, Flarum will use the default ClientAction which responds with an empty page that boots up the JavaScript client application.

## JavaScript Application

The better half of Flarum's front-end is the fast, interactive JavaScript client application. This is powered by [Mithril](http://mithril.js.org), a lightweight rendering framework that is similar to [React](http://facebook.github.io/react/). This section assumes that you are familiar with Mithril and some of the ideas from React.

### Compiling Assets

Flarum's JavaScript application is written in [ES6](https://babeljs.io/docs/learn-es2015/) and transpiled to ES5 using [Gulp](http://gulpjs.com) and [Babel](https://babeljs.io). [ES6 modules](https://babeljs.io/docs/learn-es2015/#modules) are used extensively and transpiled to the [System.js format](https://github.com/systemjs/systemjs). Extension JavaScript should be written in the same way.

The extension skeleton that Flarum generated for you contains all of the necessary tooling to get started. Simply navigate to the `js/forum` directory and run the following command to compile your extension's JavaScript:

    gulp watch
    
The compiled JavaScript will be outputted to `js/forum/dist/extension.js`. This file must be registered with the client's asset manager so that it's included in the page. A bootstrapper – the name of a module to load during the JS application's boot process – must also be registered. You can do this with the `BuildClientView` event (this is already a part of the extension skeleton):

```php
use Flarum\Events\BuildClientView;

$events->listen(BuildClientView::class, function (BuildClientView $event) {
    $event->forumAssets([
        __DIR__.'/../../js/forum/dist/extension.js'
    ]);
    
    $event->forumBootstrapper('extension-name/main');
});
```

### Monkey Patching

Having registered your extension's `main` module as a bootstrapper, the JavaScript application will execute code in the `js/forum/src/main.js` file before it boots. This is your extension's opportunity to make some changes!

As we touched on in the introduction, we can make changes to the JavaScript application by monkey-patching (replacing) methods. Flarum provides a couple of helpers to do this in a safe and easy way. These helpers, `extend` and `override`, can be imported from the `flarum/extend` module. Pass in the object you wish to modify (usually the prototype of some class), the name of the property to replace, and a callback.

The `extend` helper allows you to specify a callback to be run *after* an original method is called. It passes through the return value of the original method, so you can modify it as necessary.

The `override` helper allows you to specify a callback to be run *instead of* an original method. It passes through a reference to the original method, so you can call it if desired.

To demonstrate how these helpers work, let's take a look at an example:

```javascript
import {extend, override} from 'flarum/extend';

class Foo {
  aMethod() {
    return {
      color: 'red'
    };
  }
  
  bMethod() {
    return 'Gday';
  }
}

const foo = new Foo();
foo.aMethod(); // {color: 'red'}
foo.bMethod(); // 'Gday'

extend(Foo.prototype, 'aMethod', value => {
  value.color = 'yellow';
});

override(Foo.prototype, 'bMethod', original => {
  return original() + ' mate';
});

foo.aMethod(); // {color: 'yellow'}
foo.bMethod(); // 'Gday mate'
```

When monkey patching methods, you should ensure that your extension/replacement complies with the original method's signature (i.e. the number and types of values that it accepts and returns).

### Components

Components are reusable pieces of UI that make up Flarum's interface. You can find a full list of components [here](https://github.com/flarum/core/tree/master/js/forum/src/components).

Components are implemented on top of [Mithril's component system](http://mithril.js.org/mithril.component.html) in a React-like way. A component must extend the base `flarum/Component` class and implement a `view()` method. (The default extension tooling setup transpiles JSX to Mithril's `m()` function.)

```javascript
class WelcomeMessage extends Component {
  view() {
    return (
      <div className="WelcomeMessage">
        <h1>Hello, {this.props.name}!</h1>
      </div>
    );
  }
}
```

A component can interact with its root DOM element by implementing a `config()` method. This method is the same as Mithril's `config` API, except it skips the first argument (`element`). Instead, you can access the element as a jQuery object using the `$()` method.

```javascript
config(isInitialized, context) {
  if (isInitiailized) return;
  
  this.$().on('click', () => alert('clicked'));
}
```

### Item Lists

The `ItemList` class is a utility which collects and arranges an array of items by name:

```
import ItemList from 'flarum/utils/ItemList';

const items = new ItemList();

items.add('foo', 'Foo');
items.add('bar', 'Bar');
items.add('qux', 'Qux', 10); // Higher priorities are arranged first

delete items.foo; // Delete an item

items.bar.content = 'Baz'; // Change an item's content

items.toArray(); // ['Qux', 'Baz']
```

Many components and other utilities contain methods that return an instance of the `ItemList` class. (Their names are usually suffixed with 'Items'.) For example, the `CommentPost` component's `headerItems()` method returns an ItemList instance containing items to render in the header of each post. You can alter the items before they are rendered via monkey patching:

```javascript
import CommentPost from 'flarum/components/CommentPost';

extend(CommentPost.prototype, 'headerItems', function(items) {
  items.add('location', 'This user lives in ' + this.props.post.user().location());
});
```

### App Global

The `app` global is an instance of the `flarum/ForumApp` class, and gives you access to a whole range of application-level component instances and utilities.

### Store

The Store is an object which interacts with Flarum's API. It can be accessed via `app.store`.

The Store maps resources contained in API responses to Models. Models make accessing attributes and relationships easy. For example, part of the Discussion model looks like:

```javascript
class Discussion extends mixin(Model, {
  title: Model.attribute('title'),
  startTime: Model.attribute('startTime', Model.transformDate),
  startUser: Model.hasOne('startUser'),
  posts: Model.hasMany('posts'),
```

To get a discussion model from the API, use the store's `find` method, which returns a promise:

```javascript
app.store.find('discussions', 1).then(discussion => {
  discussion.id(); // "1"
  discussion.title(); // "Discussion title"
  discussion.startUser(); // flarum/models/User object
});
```

You may also do more advanced queries using the `find` method:

```javascript
app.store.find('discussions', {
  sort: '+time',
  page: {limit: 5},
  filter: {q: 'test'}
});
```

The `find` method will always hit the API. To retrieve models from the store's local cache without hitting the API, use the `getById` and `all` methods:

```javascript
const discussion = app.store.getById('discussions', 1);
const discussions = app.store.all('discussions');
```

Model attributes and relationships can be persisted using the `save` method. Models can be deleted using the `delete` method. Both return a promise.

```javascript
discussion.save({
  title: 'New title',
  relationships: {
    tags: [] // Array of Tag models
  }
});
discussion.delete();
```

New records can be created using the store's `createRecord` method:

```javascript
const discussion = app.store.createRecord('discussions', {
  title: 'New discussion'
});
discussion.save();
```

### Routes

You can add/modify client routes in the `app.routes` object. Routes are keyed by a unique name, and defined by an object with `path` and `component` properties. (Note that this can only be done on initialization, before the app is booted.)

```javascript
app.routes.tag = {path: '/tag/{slug}', component: TagPage.component()};
```

You can generate a URL to a route using the `app.route()` method:

```javascript
<a href={app.route('tag', {slug: 'blog'})} config={m.route}>Blog</a>
```
