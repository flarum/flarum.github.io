---
layout: docs
title: Troubleshooting
permalink: /docs/troubleshooting/
---
## Debugging Tools

If you encounter an error while using your forum, the first thing to do is enable debug mode. Simply open up config.php with a text editor, and change the `debug` value to `true`. This will cause Flarum to show detailed error messages, giving you an insight into what's going wrong.

If you're still getting a blank page, try setting the `display_errors` option to `On` in your php.ini configuration. Alternatively, you can add the following line of code to the top of `flarum/bootstrap.php`:

```php
ini_set('display_errors', 'On');
```

*Be sure to revert these changes once the error is fixed!*

## Oops! Something went wrong

If you get a red "Oops! Something went wrong" message while using your forum, enable debug mode as outlined above, then follow these steps to find out more information about what's going wrong:

### Google Chrome

1. Open the Inspector: View > Developer > JavaScript Console
2. Choose the *Network* tab
3. Repeat the action that caused the error message to appear
4. Click on the red request that has failed
5. Select the *Response* tab and include the contents in your support request

### Mozilla Firefox

1. Open the Inspector: Tools > Web Developer > Network
2. Repeat the action that caused the error message to appear
3. Click on the red request that has failed
5. Select the *Response* tab and include the contents in your support request
