---
layout: docs
title: Contributing
permalink: /docs/contributing/
---
## Bug Reports

If you find a bug, we'd like to know about it! Remember that the goal of a bug report is to make it easy for us to replicate the bug and develop a fix. Therefore, you should include as much relevant information as possible, and communicate it in a clear, factual way. [This article](http://www.chiark.greenend.org.uk/~sgtatham/bugs.html) is a great resource on how to report bugs effectively.

To report a bug, create an issue in the [flarum/core GitHub repository](https://github.com/flarum/core/issues). Please search beforehand to see if your issue has already been reported.

## Contributing Code

Want to help us build Flarum? Great! Contributions are welcome, and are accepted via pull requests. Please review these guidelines before submitting any pull requests.

Check out the [Roadmap](https://github.com/flarum/core/issues/74) for an overview of what needs to be done. In order to make it as easy as possible for people to start hacking Flarum, we've written detailed descriptions for many of the GitHub issues. They contain pointers on what needs to be done and where to look. [See if there's anything that you'd like to do!](https://github.com/flarum/core/issues?q=is%3Aopen+is%3Aissue+label%3A%22Good+for+New+Contributors%22)

### Development Environment

In order to start hacking, you'll need to get a development environment set up. The easiest way to do this is to install Flarum's Vagrant image:

1 - Install [Vagrant](https://www.vagrantup.com) and [VirtualBox](https://www.virtualbox.org).
2 - Clone the flarum/flarum repository and set up the Vagrant box:

```bash
git clone --recursive https://github.com/flarum/flarum.git
cd flarum
vagrant up
```

3 - Add an entry to your /etc/hosts file: `192.168.29.29 flarum.dev`
4 - Visit flarum.dev in a browser.

If you can't use Vagrant for whatever reason, you can still set up Flarum for development on your own local server. You'll need to install [Composer](), [Node.js](), and [Gulp]() on your system. Clone the flarum/flarum repository as above, and then run the [relevant commands](https://github.com/flarum/flarum/blob/master/flarum/vagrant/environment.sh) to install Flarum's dependencies.

Once you have the core installed, you'll probably also want to pull in some of the extensions that are bundled with Flarum. Clone each [extension repository](https://github.com/flarum) into your `extensions` directory, and follow the included instructions to install dependencies and compile the assets. (Generally this means running `composer install` in the root, then `npm install` inside the js/admin and js/forum directories.)

### Pull Request Guidelines

* Send a coherent commit history, making sure each individual commit in your pull request is meaningful. If you had to make multiple intermediate commits while developing, please [squash](http://git-scm.com/book/en/Git-Tools-Rewriting-History) them before submitting.
* You may also need to [rebase](http://git-scm.com/book/en/Git-Branching-Rebasing) to avoid merge conflicts.

### Tests

Flarum's testing story is still being written. If you have experience with testing, please [help us develop a solid testing strategy](https://github.com/flarum/core/issues/)!

### Style & Conventions

Consistency is important! Please make sure you follow these coding style and naming conventions.

PHP code should follow the [PSR-2](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md) coding standard and the [PSR-4](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader.md) autoloading standard.

JavaScript code should loosely follow the [Airbnb Style Guide](https://github.com/airbnb/javascript). There is an `.eslintrc` file that you can use to check your contributions.

<!-- TODO: CSS style/naming conventions, database columns, translation keys -->
