---
layout: docs
title: Installation
permalink: /docs/installation/
---
## System Requirements

* A web server: either Apache (with mod_rewrite) or Nginx
* PHP 5.5+
* MySQL 5.5+

## Web Installation

1. Download and unzip the beta onto your webserver.
2. Go to your forum's root in a web browser.
3. Enter your details and click "Install Flarum".

## Console Installation

1. Download and unzip the beta onto your webserver.
2. Go to your forum's root in a terminal and run `php flarum/flarum install`.
3. Enter your details.

## URL Rewriting

You'll need to set up URL rewriting in order to install and run Flarum. Flarum comes with a `.htaccess` file which contains the appropriate rules for Apache. If you are using another web server, you'll need to add the following rules to your configuration file:

### Nginx

```
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location /api {
        try_files $uri $uri/ /api.php?$query_string;
    }
    
    location /admin {
        try_files $uri $uri/ /admin.php?$query_string;
    }

    location /flarum {
        deny all;
        return 404;
    }
```

## Importing Data

Eventually we hope to build data importers so you can migrate to Flarum from other forum software. However, it is far too early at this stage – we need to achieve stability first!
