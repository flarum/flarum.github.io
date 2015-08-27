---
layout: docs
title: Extending the API
permalink: /docs/extend/api/
---
As mentioned in the Introduction, the Flarum exposes a **public JSON API which can read and write the forum's data**. It conforms to the [JSON-API 1.0 specification](http://jsonapi.org).

In this section we will explore in-depth how to extend Flarum's API.

## JSON-API & Serializers

Flarum makes use of the [tobscure/json-api](https://github.com/tobscure/json-api) library to output data in a format that complies with the JSON-API specification. This involves the use of Serializers, which are classes that transform Flarum's domain data into a publicly-consumable format. Serializers are found under the `Flarum\Api\Serializers` namespace.

### Attributes

Serialized attributes can be added or modified using the `ApiAttributes` event:

```php
use Flarum\Api\Serializers\DiscussionSerializer;
use Flarum\Events\ApiAttributes;

$events->listen(ApiAttributes::class, function (ApiAttributes $event) {
    if ($event->serializer instanceof DiscussionSerializer) {
        $event->attributes['isSticky'] = (bool) $event->model->is_sticky;
        $event->attributes['canSticky'] = $event->model->can($event->actor, 'sticky');
    }
});
```

### Relationships

New relationships can be added to a serializer using the `ApiRelationship` event. Flarum's base Serializer class provides two handy methods (`hasOne` and `hasMany`) which will construct a Tobscure\JsonApi\Relationship object using relation data from the model being serialized.

```php
use Flarum\Api\Serializers\DiscussionSerializer;
use Flarum\Events\ApiRelationship;

$events->listen(ApiRelationship::class, function (ApiRelationship $event) {
    if ($event->serializer instanceof DiscussionSerializer && $event->relationship === 'tags') {
        return $event->serializer->hasMany('Flarum\Tags\Api\TagSerializer');
    }
});
```

In order for relationships to be linked or included in the response document, you must add the relationship to the appropriate Actions – see below.

## Actions

Flarum's API endpoints are defined in `Flarum\Api\ApiServiceProvider`. Each endpoint routes to a corresponding **Action** class, which is essentially a controller. The Action class will receive a `Flarum\Api\Request` object, and should return PSR-7 Response.

There are a number of abstract Action classes in the `Flarum\Api\Actions` namespace that can be extended to easily implement CRUD actions. These include the `SerializeResourceAction` and `SerializeCollectionAction` classes, which do all the work of setting up and passing data through a Serializer for you. Children are only required to implement the `data` method and return an Eloquent Model or Collection.

### Altering Action Properties

The `data` method receives a request object with a few extra parameters: sort, include, link, limit, and offset. These parameters are sanitized using properties on the action class; These properties may be modified by extensions using the `BuildApiAction` event in order to permit new relationships to be included, new fields to be sorted by, and more.

```php
use Flarum\Api\Actions\Discussions;
use Flarum\Events\BuildApiAction;

$events->listen(BuildApiAction::class, function (BuildApiAction $event) {
    if ($event->action instanceof Discussions\IndexAction ||
        $event->action instanceof Discussions\ShowAction ||
        $event->action instanceof Discussions\CreateAction ||
        $event->action instanceof Discussions\UpdateAction) {
        // Include the tags relationship on discussion resources by default
        $event->addInclude('tags');
    }
});
```

### Adding an Endpoint

If you need to add a new API endpoint, you can extend any of the base Action classes, and then register a new route using the `RegisterApiRoutes` event:

```php
use Flarum\Events\RegisterApiRoutes;

$events->listen(RegisterApiRoutes::class, function (RegisterApiRoutes $event) {
    $event->get(
        '/tags', // The endpoint URI
        'api.tags.index', // A unique name for the endpoint
        'Flarum\Tags\Api\IndexAction' // The Action class to handle the request
    );
});
```
