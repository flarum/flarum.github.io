---
layout: docs
title: Extending the Domain
permalink: /docs/extend/domain/
---
As mentioned in the Introduction, the domain layer is responsible for **managing and modelling the forum's data**. This includes storing data in a database, providing programmatic commands to modify the data, and handling related logic (e.g. when a user makes a post, we add one to the user's post count).

In this section we will explore in-depth how to extend Flarum's domain.

## Migrations

If your extension introduces a new type of entity (e.g. tags) or adds new attributes to an existing entity (e.g. an `is_sticky` attribute to discussions), then you'll need to update Flarum's database schema so that this new data can be stored.

This is achieved through **migrations**, which are based on [Laravel's implementation](http://laravel.com/docs/5.1/migrations). Migrations must be placed in your extension's `migrations` directory, and should be named with a timestamp and a description of the modification they make:

	2015_02_24_000000_create_tags_table.php

The file should contain a class, with the same description, that extends the `Flarum\Migrations\Migration` class. It must be under the namespace `Flarum\Migrations\{ExtensionName}`.

A migration class contains two methods: `up` and `down`. The `up` method is called whenever your extension is enabled (if the migration has not been run previously), so you can add new tables, columns, or indexes to the database, or perform any other setup tasks. The `down` method is called if the extension is uninstalled, and should reverse the operations performed by the `up` method.

Migrations have access to a `$schema` variable, which is an instance of [Laravel's schema builder](http://laravel.com/docs/5.1/migrations#writing-migrations). This should be used to make changes to the database schema:

```php
<?php

namespace Flarum\Migrations\Tags;

use Illuminate\Database\Schema\Blueprint;
use Flarum\Migrations\Migration;

class CreateTagsTable extends Migration
{
    public function up()
    {
        $this->schema->create('tags', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name', 100);
            $table->string('slug', 100);
            $table->text('description')->nullable();
        });
    }

    public function down()
    {
        $this->schema->drop('tags');
    }
}
```

To run new migrations without having to re-enable your extension, simply run the upgrade command:

	php flarum upgrade

## Extending Models

In order to work with any new database schema that you set up, you'll want to create a new Model or extend an existing one. In Flarum, a Model extends `Flarum\Core\Model`, which in turn extends Laravel's `Eloquent\Model` class. [Get familiar with Eloquent!](http://laravel.com/docs/5.1/eloquent)

### Attributes

On Eloquent models, you don't need to worry about defining attributes. You can simply access them as a property of the instance, like `$discussion->is_sticky`. That makes things easy for us!

However, there is one exception: If you add a date attribute, you'll need to tell the model that this attribute should be treated as a date. This can be achieved by listening for the `ModelDates` event:

```php
use Flarum\Core\Discussions\Discussion;
use Flarum\Events\ModelDates;

$events->listen(ModelDates::class, function (ModelDates $event) {
	if ($event->model instanceof Discussion) {
		$event->dates[] = 'stickied_at'; // Treat the stickied_at attribute as a date
	}
});
```

### Validation

Every time a model is saved, its data is validated against a set of rules using Laravel's [Validation component](http://laravel.com/docs/5.1/validation). These rules can be modified by extensions so you can perform additional validation on a model's attributes.

```php
use Flarum\Core\Users\User;
use Flarum\Events\ModelValidator;

$events->listen(ModelValidator::class, function (ModelValidator $event) {
	if ($event->model instanceof User) {
		// Make usernames require at least 5 characters
		$event->validator->mergeRules('username', 'min:5');
	}
});
```

### Relationships

If you need to define a new relationship for an existing model, you may do so with the `ModelRelationship` event. This event is called when an unknown method/attribute is accessed on a model. If the name of the method matches your relationship's name, you should return an Eloquent Relation object using the model's `hasOne`, `belongsTo`, `belongsToMany`, or [any other relationship method](http://laravel.com/docs/5.1/eloquent-relationships).

```php
use Flarum\Core\Discussions\Discussion;
use Flarum\Events\ModelRelationship;

$events->listen(ModelRelationship::class, function (ModelRelationship $event) {
	if ($event->model instanceof Discussion && $event->relationship === 'category') {
		return $event->model->belongsTo('category');
	}
});
```

## Persisting Data

As described in the Introduction, Flarum makes use of the command bus pattern to provide a way for data to be modified. In order to allow any new attributes/relationships on a core entity to be persisted to the database, you will need to be aware of what's going on.

As an example, let's walk through what happens when we make a PATCH request to the `/discussions/123` API endpoint:

1. The request is passed to the appropriate API Action class (`Flarum\Api\Actions\Discussions\UpdateAction`).
2. The action creates a new instance of the `Flarum\Core\Discussions\Commands\EditDiscussion` command, filling it with the request input.
3. The action dispatches this command to the command bus, which will pass it to the appropriate command handler: `Flarum\Core\Discussions\Commands\EditDiscussionHandler`
4. The `EditDiscussionHandler` applies changes to the Discussion model based on the command input, and saves the discussion.

In step number 4, the `DiscussionWillBeSaved` event is fired. This is an opportunity for extensions to inspect the command input, and apply any additional changes to the model before it is saved.

```php
use Flarum\Events\DiscussionWillBeSaved;

$events->listen(DiscussionWillBeSaved::class, function (DiscussionWillBeSaved $event) {
	if (isset($event->data['attributes']['isSticky'])) {
		$event->discussion->is_sticky = $event->data['attributes']['isSticky'];
	}
});
```

## Permissions

Flarum includes a powerful permission system. It allows extensions to define logic that allows or disallows certain users/groups from performing certain actions on certain entities.

### Model Locking

Whenever you need to determine if a user can perform an action on a model, you may call the `can()` method on that model (defined in the `Flarum\Core\Support\Locked` trait). This method returns `true` if the user is allowed, or `false` if they are not.

	$allowed = $discussion->can($user, 'sticky');

Extensions can hook into this method using the `ModelAllow` event, and return either `true` or `false` to grant or deny permission:

```php
use Flarum\Core\Discussions\Discussion;
use Flarum\Events\ModelAllow;

$events->listen(ModelAllow::class, function (ModelAllow $event) {
	if ($event->model instanceof Discussion && $event->action === 'sticky') {
		// Allow the user to sticky the discussion if they authored it
		if ($event->actor->id === $event->model->user_id) {
			return true;
		}
	}
});
```

### Priorities

It's worth noting here that when registering an event listener, you can pass an integer as the third argument to give that event listener a priority. This is particularly useful for the `ModelAllow` event, where the first non-`null` value returned ceases the execution of subsequent event listeners.

### Group Permissions

Flarum also has a map of groups and the general permissions that they have been granted (according to the Permissions page in the Admin CP). This information is stored in the `permissions` database table, and can be accessed using the `hasPermission()` method on the user model:

	$granted = $user->hasPermission('discussion.sticky');

For users in the Administrator group,  this method always returns `true` (i.e. admins are allowed to do everything). Otherwise, it will return `true` or `false` depending on the user's groups and the permission map.

This system is used in conjunction with the model locking system to allow users to perform certain actions if they are in a group that has been granted a general permission. By default, checking if a user can perform a certain action on a discussion model will check for a group permission named `discussion.{action}`. The same goes for posts, users, and groups.

To learn how to add permission settings in the Administrator CP, see the [Administration]({{ site.baseurl }}/docs/extend/admin) section.

### Visibility Scopes

Model locking is only useful when checking permissions on a model that has already been fetched from the database. In order to filter which models should be fetched from the database in the first place (i.e. determine which discussions/posts are visible to the user), Flarum uses a mechanism called **visibility scopes**.

A visibility scope is simply a set of query conditions that can be applied when querying the database for a model. The scope can be applied using the `whereVisibleTo()` method (defined in the `Flarum\Core\Support\VisibleScope` trait):

	// Only get discussions which are visible to $user
	$discussions = Discussion::whereVisibleTo($user)->get();
	
Extensions can hook into this method using the `ScopeModelVisibility` event, and apply conditions to the query builder:

```php
use Flarum\Core\Discussions\Discussion;
use Flarum\Events\ScopeModelVisibility;

$events->listen(ScopeModelVisibility::class, function (ScopeModelVisibility $event) {
	if ($event->model instanceof Discussion) {
		// Only let the user see discussions which they started
		$event->query->where('start_user_id', $event->actor->id);
	}
});
```

#### Post Visibility Scope

A similar pattern is used to filter a discussion's posts to only those that are visible to the user:

	$posts = $discussion->postsVisibleTo($user)->get();
	
Similarly, extensions can hook into this method using the `ScopePostVisibility` event, and apply conditions to the query builder. The discussion whose posts are being queried is passed in the event.
