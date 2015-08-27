---
layout: docs
title: FAQ
permalink: /docs/faq/
---
## I have an idea/request for a feature. What should I do?

Before you do anything, we'd be grateful if you could check a few places to see if your idea has already been talked about:

* See the [Features]({{ site.baseurl }}/features) page to see an overview of what's done and what's coming soon.
* See the [Roadmap]({{ site.baseurl }}/roadmap) for an overview of what we are planning to implement.
* Search the [GitHub issues]({{ site.baseurl }}/issues) to see if your idea is planned.
* Search the [forum](http://discuss.flarum.org) to see if your idea has already been discussed.

If you don't find anything, then your next step is to work out whether your feature is actually a good fit for Flarum's core. [Read about the difference]({{ site.baseurl }}/docs/extend/) between core, bundled extensions, and third-party extensions. Is this something that the majority of forums will want to use – or is it something that would be better suited to a third-party extension?

If you're positive that it belongs in core, as a bundled extension, or if you're not sure, then [go crazy](https://discuss.flarum.org/t/features)!

## What server specs do I need to run Flarum?

As long as your toaster has at least two slots for bread and is plugged into a wall power point, Flarum should install and run just fine.

But seriously, Flarum requires **PHP 5.5+** and **MySQL 5.x**. The GD or Imagick extensions are required to process avatars. We've tested Flarum on servers with Apache and Nginx. It should run well on any low-end shared host or VPS – let us know your experience on the [forums](http://discuss.flarum.org)!

## How much does Flarum cost?

Flarum is available to you at the very low cost of absolutely nothing. Flarum is a 100% free and open-source product and will continue to be this way forever. No, seriously. Forever.

However, we do accept donations. They are in no way required to use the software, but if you would like to show you appreciation for the product in the form of a financial donation, you can do so [here]({{ site.baseurl }}/donate). Donations will be used to cover the costs of hosting and pay for dedicated development time.

## Will I be able to migrate my forum to Flarum?

Yes, but not for a while. Our focus right now is on getting Flarum stable and feature-complete. Once we're confident that Flarum is ready to be used in production, we'll start building tools to import data from other forum software like esoTalk, FluxBB, phpBB, and others.

## When will Flarum be stable?

It's impossible for us to say at the moment – there are too many variables! You can follow our progress on the [Roadmap]({{ site.baseurl }}/roadmap).

## Why can't I format my posts?

Flarum currently does not have a visual editor – it's just something we didn't get to in time for beta. Rest assured, it is planned and will be available in the core soon.

Despite not having a visual editor at the moment, Flarum does support Markdown and BBCode. Markdown is a plain text format for writing structured documents, based on conventions used for indicating formatting in email and usenet posts. If you are completely new to Markdown, check out [this cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) to help you with formatting your posts on Flarum.

## Will Flarum be localized for other languages?

Yes, depending on community contribution! We already have a number of awesome members offering their expertise to help with future localization. For anyone interested in getting involved in localization, check out the [Localization docs]({{ site.baseurl }}/docs/extend/localization), as well as [this discussion](http://discuss.flarum.org/d/336-potential-l10n-stumbling-blocks).
