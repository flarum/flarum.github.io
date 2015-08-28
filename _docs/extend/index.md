---
layout: docs
title: Introduction
permalink: /docs/extend/
---
Flarum is designed to be lightweight and highly extensible. In fact, most of the features that ship with Flarum are actually Extensions. This approach makes Flarum extremely customizable. A user can disable any features they don't use on their forum, and install other Extensions that give them the desired functionality.

In order to achieve this extensibility, Flarum has been built with rich APIs and extension points. This section of the documentation aims to teach you how Flarum works, and how to use the APIs so that you can build your own Extensions.

> **The Extension API is unstable and will change over the next few months as we refine it.** Additionally, this documentation is a work in progress. [We would love your feedback!](https://github.com/flarum/core/issues/)

## Core vs. Extensions

Where do we draw the line between Flarum's core and its extensions? Why are some features included in the core, and others aren't? It is important to understand this distinction so that we can maintain consistency and quality within Flarum's ecosystem.

**Flarum's core** is not intended to be packed full of features. Rather, it is a scaffold, or a framework, which provides a reliable foundation on which extensions can build. It contains only basic, unopinionated functionality that is essential to a forum: discussions, posts, users, groups, and notifications.

**Bundled extensions** are features that are packaged with Flarum and enabled by default. They are extensions just like any other, and may be disabled and uninstalled. While their scope is not intended to address all use-cases, the idea is to make them generic and configurable enough that they can satisfy the majority.

**Third-party extensions** are features which are made by others and are not officially supported by the Flarum team. They should be built and used to address more specific use-cases.

If you are aiming to address a bug or shortcoming of the core, or of an existing bundled extension, it may be appropriate to *contribute to the respective project* rather than disperse effort on a new third-party extension. It is a good idea to start a discussion on the [Flarum Community](http://discuss.flarum.org) to get the perspective of the Flarum developers.

## How Flarum Works

Before you can build extensions, you must first understand how Flarum works. This section aims to give a quick overview of the various parts that make up Flarum.

At the highest level, Flarum has a three-layered architecture:

* The **domain** layer, which is responsible for all of Flarum's data structures and domain logic.
* The **API** layer, which exposes these data structures in a standardized format (i.e. a JSON API).
* The **client** layer, which provides the default web user interface that consumes the API.

In order to build an extension, you will need to know a little bit about how to extend each of these layers. For example, the *Sticky* extension:

* Extends the domain layer, adding a new `is_sticky` property for discussions.
* Extends the API layer, allowing the property's value to be changed via the JSON API.
* Extends the client layer, adding a discussion control item which calls the JSON API when clicked.

Let's look a little more in-depth into each of these layers, and how they are built.

### Domain

The domain layer is responsible for **managing and modelling the forum's data**. This includes storing data in a database, providing programmatic commands to modify the data, and handling related logic (e.g. when a user makes a post, we add one to the user's post count).

The domain layer is built with **object-oriented PHP**, under the `Flarum\Core` namespace. Each entity in the domain (discussions, posts, etc.) has its own child namespace.

The domain layer relies on Laravel's [Database component](http://laravel.com/docs/5.1/database) and its [Eloquent ORM](http://laravel.com/docs/5.1/eloquent). Each entity is represented by an Eloquent active-record model. These models have built-in mechanisms for validation, permissions, and extension.

To provide a way for data to be modified, Flarum makes use of the command bus pattern with Laravel's [Bus component](http://laravel.com/docs/5.0/bus). There are various commands related to each entity (e.g. `Flarum\Core\Discussions\Commands\StartDiscussion`, `Flarum\Core\Posts\Commands\PostReply`) which can be dispatched to act on Flarum's domain.

### API

The API layer sits on top of the domain, exposing a **public JSON API which can read and write the forum's data**. It conforms to the [JSON-API 1.0 specification](http://jsonapi.org).

The API layer is built with **object-oriented PHP**, under the `Flarum\Api`. Each API endpoint has a corresponding **Action** (essentially a controller) which receives a Request object and returns a Response.

Most Actions make use of the [tobscure/json-api library](https://github.com/tobscure/json-api) to output a JSON-API document. Data from Flarum's domain is transformed into a publicly-consumable format by Serializers. Actions which write data use the command bus to dispatch commands to the domain layer.

### Client

The client layer sits on top of the API, exposing a **user interface** so that Flarum can be used by humans. There are two parts to the client: a basic read-only HTML version of the content, and an interactive single-page JavaScript application. Both consume the JSON API.

The client HTML is served up using **object-oriented PHP**, under the `Flarum\Forum`. Similar to the API, each of the forum's routes has a corresponding **Action** (essentially a controller) which receives a [PSR-7](https://github.com/php-fig/http-message) Request and returns a Response.

The typical client Response contains a basic HTML representation of the page's content, as well as code to boot the JavaScript application. It is constructed using Laravel's [View component](http://laravel.com/docs/5.1/views) and [Blade templating system](http://laravel.com/docs/5.1/blade).

The JavaScript application is powered by [Mithril](http://mithril.js.org), a lightweight rendering framework that is similar to [React](http://facebook.github.io/react). It is transpiled from an ES6 codebase using [Gulp](http://gulpjs.com) and [Babel](https://babeljs.io).

The client also includes a [LESS CSS](http://lesscss.org) compiler to compile a stylesheet for the client application.

## How Extensions Work

Now that you've gained a bit of an understanding of how Flarum works, let's look at the basic concepts of how we can extend Flarum.

### Events

Throughout Flarum's back-end PHP code (i.e. the domain, API, and part of the client layers), there are many **Events** that extensions may listen and respond to. For example, after a discussion has been started, the `DiscussionWasStarted` event is fired. An extension could listen to this event in order to send some kind of notification whenever a discussion is started.

All events that can be listened for are found in the `Flarum\Events` namespace.

Listening for events is as simple as calling the `listen()` method on the event dispatcher, passing the class name of the event and a closure to handle the event. You will learn more about how to register event listeners in the [Packaging]({{ site.baseurl }}/docs/extend/packaging) section.

### Monkey Patching

Extending Flarum's front-end JavaScript application uses a concept called [monkey patching](https://en.wikipedia.org/wiki/Monkey_patch). Monkey patching is the replacement of functions, methods, and attributes at runtime.

Flarum's front-end interface is made up of many components, each of which is defined by a class. Extensions may monkey-patch methods of these classes, mutating the return values as appropriate. For example, an extension could monkey-patch the `moderationControls` method of the `DiscussionControls` class in order to add a new button which stickies a discussion.

You will learn more about how to apply monkey-patches to the Flarum's JavaScript application in the [Extending the Client]({{ site.baseurl }}/docs/extend/client) section.
