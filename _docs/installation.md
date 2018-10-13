---
layout: docs
title: Installation
permalink: /docs/installation/
---
## Beta Software

Please keep in mind that Flarum is beta software. That means:

* It still has some **incomplete features** and **bugs** 🐛🐞 and
* At some point – sooner or later – it will probably **break**! 💥

Beta is all about fixing these issues and improving Flarum. We're busy working hard to make Flarum better, so we ask that you:

* **Don't use it in production.** We can't support you if things go awry. And upgrading to subsequent versions might involve getting your hands dirty.
* **Report bugs responsibly.** Poorly written bug reports take time to deal with, distracting us from adding new features and making Flarum stable.

Before you install, please read our [Contributing]({{ site.baseurl }}/docs/contributing/) guide so you will know what you're signing up for!

<a name="system-requirements"></a>

## System Requirements

> Note: As of 0.1.0-beta.3, Flarum utilizes [Composer](https://getcomposer.org) to manage its dependencies and extensions. This means that **Flarum cannot be installed on hosts without SSH (command-line) access**. Rest assured that we are planning to address this in the future and make sure that Flarum is accessible to everyone. In the meantime, if you want to run Flarum, you will need to find a host that permits SSH access.

There are a few things that you will need to have set up in order to run Flarum:

* A web server: **Apache** (with mod_rewrite), **Nginx**, or **Lighttpd**
* **PHP 5.6 up to 7.1 (not 7.2)** with the following extensions: mbstring, pdo_mysql, openssl, json, gd, dom, fileinfo, tokenizer
* **MySQL 5.5+**
* **SSH (command-line) access**

Alternatively, you can use a third party Flarum service such as [Free Flarum](https://www.freeflarum.com) (not affiliated with flarum.org).

<a name="installing-flarum"></a>

## Installing Flarum

Flarum utilizes Composer to manage its dependencies and extensions. So, before installing Flarum, you will need to install [Composer](https://getcomposer.org) on your machine. Then run this command in the location where Flarum should be installed:

```raw
composer create-project flarum/flarum . --stability=beta
```

While this command is running, you can configure [URL rewriting](#url-rewriting) on your web server. Finally, navigate to your forum in a browser and follow the instructions to complete the installation.

<a name="url-rewriting"></a>

## URL Rewriting

<a name="apache"></a>

### Apache

Flarum includes a `.htaccess` file – make sure it's been uploaded correctly. If you're using shared hosting, confirm with your hosting provider that `mod_rewrite` is enabled. You may need to add the following to your Apache configuration:

    <Directory "/path/to/your/forum">
        AllowOverride All
    </Directory>

<a name="nginx"></a>

### Nginx

Add the following lines to your server's configuration block:

```
    location / { try_files $uri $uri/ /index.php?$query_string; }
    location /api { try_files $uri $uri/ /api.php?$query_string; }
    location /admin { try_files $uri $uri/ /admin.php?$query_string; }

    location ~* ^/(composer\.(json|lock)|config\.php|flarum|storage|vendor) {
        deny all;
        return 404;
    }

    location ~* \.php$ {
        fastcgi_split_path_info ^(.+.php)(/.+)$;
        fastcgi_pass unix:/var/run/php5-fpm.sock;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param HTTP_PROXY ""; # Fix for https://httpoxy.org/ vulnerability
        fastcgi_index index.php;
    }
    
    location ~* \.html$ {
        expires -1;
    }

    location ~* \.(css|js|gif|jpe?g|png)$ {
        expires 1M;
        add_header Pragma public;
        add_header Cache-Control "public, must-revalidate, proxy-revalidate";
    }

    gzip on;
    gzip_http_version 1.1;
    gzip_vary on;
    gzip_comp_level 6;
    gzip_proxied any;
    gzip_types application/atom+xml
               application/javascript
               application/json
               application/vnd.ms-fontobject
               application/x-font-ttf
               application/x-web-app-manifest+json
               application/xhtml+xml
               application/xml
               font/opentype
               image/svg+xml
               image/x-icon
               text/css
               #text/html -- text/html is gzipped by default by nginx
               text/plain
               text/xml;
    gzip_buffers 16 8k;
    gzip_disable "MSIE [1-6]\.(?!.*SV1)";
```

<a name="lighttpd"></a>

### Lighttpd

Add the following lines to your server's configuration block:

```
    url.rewrite-if-not-file = (
        "/admin.*" => "/admin.php",
        "/api.*"   => "/api.php",
        "/.*"      => "/index.php"
    )
```

<a name="importing-data"></a>

## Importing Data

There are no official importers yet, as Flarums data model is still under development. But this will change with the [upcoming stable release](/roadmap).

However, there will be upgrade procedures for beta to stable. And the community has made several unofficial importers already:

- [phpBB](https://discuss.flarum.org/d/1117-phpbb-migrate-script-updated-for-beta-5)
- [SMF2](https://github.com/ItalianSpaceAstronauticsAssociation/smf2_to_flarum)
- [FluxBB](https://discuss.flarum.org/d/3867-fluxbb-to-flarum-migration-tool)
- [Discourse](https://github.com/TBits/discourse_to_flarum/)

This can be used to import other forums as well, by exporting to phpBB first.

Want to help out writing importers? Great! See our guidelines for [code contributions](https://github.com/flarum/flarum/blob/master/CONTRIBUTING.md).
