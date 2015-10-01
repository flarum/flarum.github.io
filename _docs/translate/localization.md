---
layout: docs
title: Localization
permalink: /docs/translate/localization/
---
## Translations

The extension skeleton includes an empty `locale/en.yml` where you can put your extension's English translations. Keys should use `snake_case`. They should be namespaced under the extension's name. Translations may contain `{placeholders}`.

```yaml
tags:
  tag_new_discussion_title: Choose Tags for Your Discussion
  edit_discussion_tags_title: "Edit Tags for {title}"
```

You can register these translations with Flarum's locale manager using the `RegisterLocales` event (the extension skeleton does this by default):

```php
use Flarum\Events\RegisterLocales;

$events->listen(RegisterLocales::class, function (RegisterLocales $event) {
    $event->addTranslations('en', __DIR__.'/../../locale/en.yml');
});
```

In order for the translations to be compiled into the client locale JavaScript file and used by the JavaScript client, they must be explicitly registered using the `BuildClientView` event. If you specify a key to be included, all of its children will be included as well.

```php
use Flarum\Events\BuildClientView;

$events->listen(BuildClientView::class, function (BuildClientView $event) {
    $event->forumTranslations([
        'tags.tag_new_discussion_title',
        'tags.edit_discussion_tags_title'
    ]);
});
```

You will need to delete the `forum-en-xxxx.js` file from your forum's `assets` directory in order to force a recompilation of the locale JavaScript.

Translations may then be used in the client using the `app.trans()` method:

```javascript
app.trans('tags.edit_discussion_tags_title', {title: discussion.title()});
```

## Pluralization

If a `count` parameter is passed into a translation, the translator will look for children under that translation according to a set of pluralization rules. The default pluralization rule is for English: if the count is 1, then it looks for the `one` key, otherwise it looks for the `other` key:

```yaml
tags:
  choose_primary_tags:
    one: Choose a primary tag
    other: "Choose {count} primary tags"
```

```javascript
app.trans('tags.choose_primary_tags', {count: 1}); // 'Choose a primary tag'
app.trans('tags.choose_primary_tags', {count: 7}); // 'Choose 7 primary tags'
```

## Configuration

You can configure pluralization rules and other parts of the translation system by registering a configuration JavaScript file with the locale manager:

```javascript
// locale/en.js
app.translator.plural = function(count) {
    return count === 1 ? 'one' : 'other';
};

// You can do other things here too, e.g. configure moment.js locale
```

```php
use Flarum\Events\RegisterLocales;

$events->listen(RegisterLocales::class, function (RegisterLocales $event) {
    $event->addJsFile('en', __DIR__.'/../../locale/en.js');
});
```
