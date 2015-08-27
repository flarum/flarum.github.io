---
layout: docs
title: Distribution
permalink: /docs/extend/distribution/
---
The goal with distribution is to allow people to simply drop an extension into their extensions directory. They shouldn't need to install any of the tools or compile any code – that's your job as the developer!

So, before you package up your extension, you'll need to install all composer dependencies and compile your JavaScript application. An example build script:

```bash
#!/usr/bin/env bash

base=${PWD}

# Unzip an archive of the latest committed code
rm -rf /tmp/extension-release
mkdir /tmp/extension-release
git archive --format zip --worktree-attributes HEAD > /tmp/extension-release/release.zip
cd /tmp/extension-release
unzip release.zip -d ./
rm release.zip

# Install all Composer dependencies
cd /tmp/extension-release/flarum
composer install --prefer-dist --optimize-autoloader --ignore-platform-reqs --no-dev

# Compile JavaScript
# Assumes: npm install -g gulp flarum-gulp babel-core
cd /tmp/extension-release/js
for app in forum admin; do
  cd "/tmp/extension-release/js/${app}"
  npm link gulp flarum-gulp babel-core
  gulp --production
  rm -rf "/tmp/extension-release/js/${app}/node_modules"
done
```
