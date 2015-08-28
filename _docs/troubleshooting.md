---
layout: docs
title: Troubleshooting
permalink: /docs/troubleshooting/
---
## Installation

If you're having a problem installing Flarum, check out the [Installation tag](http://discuss.flarum.org/t/installation) on the support forum. Someone might've had the same problem as you! If not, start a discussion and we'll do our best to help.

### Known Issues

* Currently the installer does not validate input correctly; if you enter an invalid admin username, the installer will silently fail. Usernames must only contain letters, numbers, dashes, and underscores.
* The installer will crash if the `flarum/storage/framework/views` directory is not writable. Make sure PHP can write to this directory.

## Debugging

If you encounter an error while using your forum, the first thing to do is enable debug mode. Simply open up config.php with a text editor, and change the `debug` value to `true`. This will cause Flarum to show detailed error messages, giving you an insight into what's going wrong.

If you're still getting a blank page, try setting the `display_errors` option to `On` in your php.ini configuration. Alternatively, you can add the following line of code to the top of `flarum/bootstrap.php`:

```php
ini_set('display_errors', 'On');
```

*Be sure to revert these changes once the error is fixed!*
