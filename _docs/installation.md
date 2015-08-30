---
layout: docs
title: Installation
permalink: /docs/installation/
---
<a name="system-requirements"></a>

## System Requirements

* A web server: **Apache** (with mod_rewrite), **Nginx**, or **Lighttpd**
* **PHP 5.5+** with the following extensions: mbstring, pdo_mysql, openssl, json, gd, dom
* **MySQL 5.5+**

<a name="web-installation"></a>

## Web Installation

1. [Download]({{ site.baseurl }}/download) and unzip the beta onto your webserver.
2. Configure URL rewriting (see below).
3. Go to your forum's root in a web browser.
4. Enter your details and click "Install Flarum".

<a name="url-rewriting"></a>

## URL Rewriting

<a name="apache"></a>

### Apache

Flarum includes a `.htaccess` file – make sure it's been uploaded correctly. If you're using shared hosting, confirm with your hosting provider that `mod_rewrite` is enabled. Otherwise, you may need to add the following to your Apache configuration:

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

    location /flarum {
        deny all;
        return 404;
    }

    location ~ .php$ {
        fastcgi_split_path_info ^(.+.php)(/.+)$;
        fastcgi_pass unix:/var/run/php5-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
    }
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

<a name="troubleshooting"></a>

## Troubleshooting

If you're having a problem installing Flarum, check out the [Installation tag](http://discuss.flarum.org/t/installation) on the support forum. Someone might've had the same problem as you! If not, start a discussion and we'll do our best to help.

<a name="known-issues"></a>

### Known Issues

* Currently the installer does not validate input correctly; if you enter an invalid admin username, the installer will silently fail. Usernames must only contain letters, numbers, dashes, and underscores.
* The installer will crash if the `flarum/storage/framework/views` directory is not writable. Make sure PHP can write to this directory.

<a name="configuring-smtp"></a>

## Configuring SMTP

There's currently no GUI to configure SMTP (see [#258](https://github.com/flarum/core/issues/258)). For now you can enter your details in manually in the `config` database table using a tool like phpMyAdmin:

```
mail_driver: smtp
mail_host: ...
mail_port: ...
mail_username: ...
mail_password: ...
mail_encryption: ...
```

<a name="importing-data"></a>

## Importing Data

Eventually we hope to build data importers so you can migrate to Flarum from other forum software. However, it is far too early at this stage – we need to achieve stability first!
