---
layout: docs
title: Themes
permalink: /docs/extend/themes/
---
Flarum compiles LESS files into a CSS file which is then served as part of the client. For per-forum customizations can easily add your own LESS/CSS to the mix in the Administrator CP Appearance section by clicking on "Edit Custom CSS".

## Compiling Assets

The extension skeleton includes an empty `less/extension.less` file where you can put any custom LESS/CSS styles. This file is registered using the `BuildClientView` event:

```php
use Flarum\Events\BuildClientView;

$events->listen(BuildClientView::class, function (BuildClientView $event) {
    $event->forumAssets([
        __DIR__.'/../../less/extension.less'
    ]);
});
```

## Variables

There are a number of LESS variables that define the theme colors and other values used throughout the LESS. They are listed in [variables.less](https://github.com/flarum/core/blob/master/less/lib/variables.less). You should use these variables to implement the forum's color scheme into your extension.

## Conventions

Flarum uses a BEM-style naming convention for CSS classes:

    .ComponentName-child--modifier
    
The ComponentName part should be consistent with the name of the JavaScript component if one exists.

## Templates

You can use a custom template for the forum client layout if needed. (The default one is located [here](https://github.com/flarum/core/blob/master/views/forum.blade.php).) Your custom template must include elements with all of the same IDs.

```php
use Flarum\Events\BuildClientView;
use Flarum\Forum\Actions\ClientAction as ForumClientAction;

$events->listen(BuildClientView::class, function (BuildClientView $event) {
    if ($event->action instanceof ForumClientAction) {
        $event->view->setLayout(__DIR__.'/../forum.blade.php');
    }
});
```
