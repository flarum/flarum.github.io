---
layout: docs
title: Packaging
permalink: /docs/extend/packaging/
---
## Setting Up

Before you can start building an extension, you'll need to set up a few tools on your system:

* Download and install [Composer](https://getcomposer.org) *globally*, which is required to generate an autoloader for your extension.
* Set up [Node.js](https://nodejs.org) and install [Gulp](http://gulpjs.com) *globally*, which is required to compile your extension's client JavaScript.

Alternatively, you can install Flarum's [Vagrant development image]({{ site.baseurl }}/docs/contributing), which gives you an environment where all of these tools are automatically set up for you.

## Generating a Skeleton

Flarum comes with a handy terminal utility which creates a skeleton extension for you, so you can get right to coding. Open up the terminal and run the following command from your forum's root directory:

```bash
php flarum/flarum generate:extension
```

The utility will ask you for a few details. The extension name is a unique name for your extension and may only contain alphanumeric and dash (`-`) characters. The namespace is used to isolate your code; you'll probably want to use your vendor name followed by the extension name (e.g. `Tobscure\Attachments`).

Once you've filled out all the details, the utility will create a new directory in the `extensions` directory at the root of your forum. It might take a few minutes to get everything ready; in the meantime, let's check out what was generated for us:

* **bootstrap.php** Flarum loads this file on every request when your extension is enabled. Where it all begins! You shouldn't ever need to touch this file.
* **flarum.json** Contains meta information about your extension (title, description, author, dependencies). More information a bit further down.
* **js/** Where your extension's JavaScript lives. You'll learn more about this in [Extending the Client]({{ site.baseurl }}/docs/extend/client).
* **less/** Where your extension's CSS styles live. You'll learn more about this in [Theming]({{ site.baseurl }}/docs/extend/theming).
* **locale/** Where your extension's translations live. You'll learn more about this in [Internationalization]({{ site.baseurl }}/docs/extend/internationalization).
* **migrations/** Database migrations that are run when your extension is installed/upgraded/uninstalled. You'll learn more about these in [Extending the Domain]({{ site.baseurl }}/docs/extend/domain).
* **src/** Where your extension's back-end source code lives, autoloaded by Composer using the PSR-4 standard.

## flarum.json

todo

## Event Subscribers 

As you will know from the introduction, the only real way to get anything done with an extension is to **listen for and respond to events**. Let's have a look at how we go about doing that.

We'll start from the beginning. If you take a look at `bootstrap.php`, you'll see that it returns the name of an Extension class. At the start of every request, if the extension is enabled, Flarum includes this file and registers this class as a [Service Provider](http://laravel.com/docs/5.1/providers). This class is your key to getting stuff done.

So let's check it out! Flarum generated it for you in the `src` directory. If you take a look inside, you'll see this lonely method:

```php
    public function listen(Dispatcher $events)
    {
        $events->subscribe('Your\Namespace\Listeners\AddClientAssets');
    }
```

Flarum calls the `listen` method of each extension service provider as soon as it has set up the eventing system. It passes an instance of [Laravel's event dispatcher](http://laravel.com/docs/5.1/events), so you can listen to events as you please.

We're doing something a little different here, though. Instead of adding event listeners directly, we call the `subscribe` method to register an event subscriber. **Subscribers** are merely a way of **grouping related event listeners**, which helps to keep your code organised. You can organise your event listeners however you want, but Flarum's bundled extensions use the convention of subscribers named after the task that they are accomplishing (e.g. `AddClientAssets` or `AddApiAttributes`).

Alright, so let's check out the AddClientAssets listener that was generated for us:

```php
use Flarum\Events\RegisterLocales;
use Flarum\Events\BuildClientView;
use Illuminate\Contracts\Events\Dispatcher;

class AddClientAssets
{
    public function subscribe(Dispatcher $events)
    {
        $events->listen(RegisterLocales::class, [$this, 'addLocale']);
        $events->listen(BuildClientView::class, [$this, 'addAssets']);
    }
```

*This* is where we register event listeners. We import the event classes, and then in the subscriber's `subscribe` method we call `listen()` on the dispatcher, passing along the name of the event we wish to subscribe to, and a method to handle the event.

These particular event listeners serve to register all of the default translation, JavaScript, and CSS files that were generated for us. For more information about them, you can read the [Extending the Client]({{ site.baseurl }}/docs/extend/client) section.
