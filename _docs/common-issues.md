---
layout: docs
title: Common Issues
permalink: /docs/common-issues/
---

These issues are often reported at the support forum or discord. Cannot find your problem? Go to Troubleshooting for general help.

## "Something went wrong while trying to load the full version of this site"

![something went wrong](/img/docs/something_went_wrong.png)

A bug in Flarum causes the asset files to not regenerate properly. It happens when you enable specific extensions. This will be fixed in Flarum in a future version. For now, you solve it by running in a shell:

```raw
php flarum cache:clear
rm assets/rev-manifest.json
```

And force-reload the page in your browser (e.g. `ctrl-shift-R`). NB. this is only required when you enable extensions. Your forum visitors will not notice it. 

## Package http-interop/http-middleware is abandoned

This is a warning and you can safely ignore it. The dependency will be fixed in a future version.

## Internal Server Error / Permission denied

Flarum needs permission to write files (cache, avatars). You may get error messages if your folders are not writeable by the PHP user. 


**Solution A**

First, find out which user PHP is running under (usually `www-data`):

```raw
ps uxa | grep php-fpm
```

Then, as `root`, change the ownership to this user:

```raw
chown -R www-data:www-data assets storage/*
```

**Solution B**

If you use shared hosting, an alternative solution is to change the file permissions:

```raw
chmod 777 assets assets/* storage/*
```

## Icons are squares

This happens when your configured URL differs from the one you are using right now. Check whether the URL in `config.php` is the same. Also check for a possible `http` <> `https` mismatch.

## Slow

Flarum is one of the fastest forums around. A normal page load should happen in under 3 seconds. So what to do if it still feels slow?

First, identify whether the delay occurs in the `server`, the `network` or your `browser`. Using Chrome, open the developer tools, go to the network tab and load your forum. You will see a waterfall view like this:

![Chrome network waterfall](/img/docs/waterfall.png)

The first item is the page as rendered by the server (here: discuss.flarum.org). In this case, it took about half a second which is ok. If it takes more than a second, something is wrong with your server, see below. 

Now, look at the other items. The blue bar denotes the time that it took to download. Check the duration of the blue bar and the size of the item. Slow (>2 seconds), large (> 800KB) and Javascript? You are probably running in debug mode (check in `config.php`) which disables minification of assets. Slow but not very large items? Your network connection to the server is likely the problem.

No slow items but the total load time (see bottom right) is above 3 seconds? The delay is in your browser.

### Slow server

To find the root cause, first, identify which component is slow. Run `top` on your server. It will show you the processes that use the most capacity. Possibilities:

**Nginx or Apache**

The web server is busy. This should not happen unless you use `mod_php` but that is not recommended.

**MySQL**

Your database is busy. Use a tool like `mytop` to identify slow queries. You could also enable the slow query log (`mysql -u root -e 'SET GLOBAL slow_query_log = "ON";'`) if you are the server admin. Then, all the queries that take unusually long will get logged. This might show extensions that are not optimized for large databases (missing indexes). 

**PHP-FPM**

Flarum is busy producing a page. Enable PHP-FPM's slow query feature to identify slow functions or extensions. Add this to your pool config file:

```
slowlog = /var/log/php-fpm-slow.log
request_slowlog_timeout = 3s
```

It will save a stack trace for every request that takes more than 3 seconds. 

### Slow network

Your current ISP could be slow. Or your server could be far away. Generally, a server on the other side of the globe could add 1-3 seconds to the total load time. 

### Slow browser

Perhaps you use a very old computer/phone. Or you have an extension enabled that includes a large amount of complex Javascript. Try disabling extensions. 