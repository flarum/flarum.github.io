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
* **PHP 5.5+** with the following extensions: mbstring, pdo_mysql, openssl, json, gd, dom, fileinfo
* **MySQL 5.5+**
* **SSH (command-line) access**

<a name="installing-flarum"></a>

## Installing Flarum

Flarum utilizes Composer to manage its dependencies and extensions. So, before installing Flarum, you will need to install [Composer](https://getcomposer.org) on your machine. Then run this command in the location where Flarum should be installed:

```bash
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
server {
        # listen defines the port used. 80 is used for http
        # 443 and ssl are used for listening to https connections
        # and require certificate configuration to work properly
        # If using ssl uncomment the following line

        # listen 443 ssl;
    listen 80;
        
        # Change root location to where flarum is installed.
    root /flarum/location;
    index index.php;
        
        # This should look something like (for example)
        # server_name discuss.flarum.org
    server_name domain.tld;

    location / { try_files $uri $uri/ /index.php?$query_string; }
    location /api { try_files $uri $uri/ /api.php?$query_string; }
    location /admin { try_files $uri $uri/ /admin.php?$query_string; }

    location /flarum {
        deny all;
        return 404;
    }

    location ~* \.php$ {
        fastcgi_split_path_info ^(.+.php)(/.+)$;
        fastcgi_pass unix:/var/run/php5-fpm.sock;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
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
               text/plain
               text/xml;
    gzip_buffers 16 8k;
    gzip_disable "MSIE [1-6]\.(?!.*SV1)";
    
        #############################
        # SSL configuration
        # For more information: http://nginx.org/en/docs/http/configuring_https_servers.html
        #############################
    
        # Nginx cannot hangle intermediate certificate as a separate file. See above url for more information.
        
      # ssl_certificate     /directory/to/fullchain.pem;  
      # ssl_certificate_key /directory/to/private_key/privkey.pem;
    
        # modern configuration. tweak to your needs.  
    # ssl_protocols TLSv1 TLSv1.1 TLSv1.2;  
    # ssl_session_timeout 1d;  
    
    # ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';
    # ssl_prefer_server_ciphers on;
    # ssl_session_cache shared:SSL:50m;
    # ssl_dhparam /etc/nginx/ssl/dhparam.pem;
    
        # HSTS (ngx_http_headers_module is required) (15768000 seconds = 6 months)  
        # Will force the usage of HTTPS. Test your SSL configuration well before using this!
    # add_header Strict-Transport-Security max-age=15768000;
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

<a name="configuring-smtp"></a>

## Configuring SMTP

There's currently no GUI to configure SMTP (see [#258](https://github.com/flarum/core/issues/258)). For now you can enter your details in manually in the `settings` database table using a tool like phpMyAdmin:

```
mail_driver: smtp
mail_host: ...
mail_from: ...
mail_port: ...
mail_username: ...
mail_password: ...
mail_encryption: ...
```

<a name="importing-data"></a>

## Importing Data

Eventually we hope to build data importers so you can migrate to Flarum from other forum software. However, it is far too early at this stage – we need to achieve stability first!
