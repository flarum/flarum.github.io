---
layout: docs
title: Contributing
permalink: /docs/contributing/
---
## Reporting Bugs

Thank you for helping us test Flarum. We're happy to have you on the team! We need people who can *troubleshoot issues patiently* and *communicate them clearly*. As you probably know, good bug reporting takes some time and effort. If you're fine with that, then let's get started!

Found a bug already? Wonderful! We'd love to hear about it &mdash; but first you should check around to make sure you're not wasting your time on a known issue:

- Search our [Support forum](http://discuss.flarum.org/t/support) to see if it's already been reported.
- We could be working on a fix, so search our [issue tracker](https://github.com/flarum/core/issues) too.

If you've searched *thoroughly* and come up empty-handed, we'll welcome your report. If it's just a simple issue (a misspelled word or graphics glitch, for example) skip to the next paragraph. But if you're seeing errors, or something is clearly broken, we'll need you to gather some information first. Please head over to our [Troubleshooting](http://flarum.org/docs/troubleshooting/) guide and follow the instructions there. Collect as much info as you can!

Okay, time to get writing. Head back to the [Support forum](http://discuss.flarum.org/t/support) and start a new discussion. Be sure to give it a title that's clear and concise, and then explain the problem thoroughly. Don't forget to mention:

- What you were doing when the problem occurred
- All the information you gathered while troubleshooting
- Anything you can tell us about your sever environment

Remember: the goal of a bug report is to make it easy for us to replicate the bug and fix it. You might want to read [this article](http://www.chiark.greenend.org.uk/~sgtatham/bugs.html) for some useful tips on how to write an effective bug report. And please take a moment or two to format your post so it's easy to tell what's what.

Once you've posted your report, we'd ask that you please *follow the discussion* that you created and wait patiently. We may need to ask for further details or clarification; but we've always got plenty to do, and it could be a while before we can give your report the time it deserves.

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

If you can't use Vagrant for whatever reason, you can still set up Flarum for development on your own local server. You'll need to install [Composer](https://getcomposer.org/download/), [Node.js](https://nodejs.org/en/download/), and [Gulp](https://github.com/gulpjs/gulp/blob/master/docs/getting-started.md) on your system. Clone the flarum/flarum repository as above, and then run the [relevant commands](https://github.com/flarum/flarum/blob/master/scripts/environment.sh) to install Flarum's dependencies.

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
