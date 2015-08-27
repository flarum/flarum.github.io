---
layout: default
permalink: /story/
---
<header class="Hero">
  <div class="container container-narrow" style="text-align: left">
    <h2>Why I'm Building Flarum</h2>
  </div>
</header>

<article class="container container-narrow story">
<div style="max-width: 800px" markdown="1">
My name is Toby Zerner. I'm a 22 year old Aussie. Last year I completed my third year of undergrad medical school.

This year, I'm taking a break from study to work on a hobby: *Building neat forum software.*

This is a story about what led me to do this, and in turn, where I want to lead Flarum in the future.

## Forum Software Sucks

When I was growing up, my older brother Simon taught me programming (in the loosest sense of the word).

Simon was exceptionally bright. Perhaps not technically, as [PHP 4 spaghetti](http://en.wikipedia.org/wiki/Spaghetti_code) was all we knew at the time. But in a broader sense, Simon got one thing that many others don't: *simplicity*.

Forum software at the time was not simple. To use the word "designed" would be generous. It was really a product of careless iteration: A feature would be added to meet some need, but it would bring extra complexity with it. Repeat that a hundred times, and you had bloat. You couldn't simply be a forum user – you had to be a *power user*, spending precious minutes battling with cluttered, unintuitive interfaces.

Simon wanted to take a step back and simplify. To go back to the basics of conversation. After all, why should talking to people on the web be anything *but* simple? So we got to work building [esoTalk](http://esotalk.org).

We swapped out the daunting forum/sub-forum hierarchy for a simple list of the most recent discussions. We got rid of signatures. We implemented a powerful "gambit" search system (a primitive version of [GitHub's search tokens](https://help.github.com/articles/searching-issues/)). We used short-polling so people could have near-real-time conversations on forums. esoTalk was something different, and people were intrigued.

<iframe src="https://player.vimeo.com/video/2867330" width="100%" height="450" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

Tragically, Simon passed away in 2009. The world lost a great man.

It was a sad time, but I endeavoured to continue working on esoTalk in his memory. However, I was a young developer writing sloppy code and [unknowingly reinventing wheels](http://esotalk.org/blog/future-of-esotalk.html). As I started focusing on other areas of my life, esoTalk gradually became stale.

But I didn't stop thinking.

## Building Something Better

Despite its stagnation, people still liked and used esoTalk. Some were even willing to [sponsor further development](http://esotalk.org/forum/478-esotalk-update-xojo-forum). Through all the mistakes, lessons, iteration, and feedback... I started to really believe that people wanted forum software that wasn't a pain in the ass, and that maybe I was capable of designing it.

As an exercise of the imagination, I begun mocking up "esoTalk 2.0". Some of the aesthetic ideas ended up making their way back into a subsequent esoTalk release. But more so these designs were part of a longer brainstorm – one that ultimately led to the conception of Flarum.

In 2013, I entered the idea of Flarum into an entrepreneurial competition at my university, with the help of my buddy [Stephen](http://www.nephets.com). We started building a prototype and dreamt up a business model. Second place, some cash, and a lot of encouragement begged the question: *Could we actually do this?*

Maybe not. It turns out that pitching new forum software in the real world is hard. Our Kickstarter campaign [generated interest, but not traction](https://www.kickstarter.com/projects/1221714515/flarum-forums-reimagined/posts/1023315). Investment or incubation might have been an option, but I didn't want to give up studying medicine, nor the idea of a community-driven project.

It seemed the best way forward was to bootstrap it. I would take a year of leave from study and build this thing out in the open. Then I could market it and build a small service-based business around it. Open-source project first, monetization second.

It turns out that you need to reach a certain stage before open-source collaboration becomes viable. That took a little longer than I'd hoped. But eight months later and here we are: Flarum is in beta. It is a product that I believe fulfils a strong vision, one that has endured ever since the first version of esoTalk that Simon and I released together. Flarum is:

* **Fast and simple.** No clutter, no bloat, no complex dependencies. Flarum is built with PHP so it's quick and easy to deploy. The interface is powered by Mithril, a performant JavaScript framework with a tiny footprint.

* **Beautiful and responsive.** This is forum software for humans. Flarum is carefully designed to be consistent and intuitive across platforms, out of the box.

* **Powerful and extensible.** Every community is different, and you should be able to customize, extend, and integrate your software to suit. Flarum's architecture is super flexible, prioritizing comprehensive APIs and great [documentation]({{ site.baseurl }}/docs).

* **Free and open.** Flarum is released under the [MIT license](https://github.com/flarum/flarum/blob/master/LICENSE).

## Stabilizing Flarum

Put those party poppers away, because there is still a lot of work to do!

One thing I learned from esoTalk is this: A piece of software that depends on extensions will surely fail without the establishment of an ecosystem. I do not intend to make the same mistake with Flarum. The plan is to have a Marketplace where anyone can buy and sell extensions – complete with easy installation and updates. However, something like this will only be possible if Flarum is stable.

Meanwhile, I actually have no idea what I'm doing, and Flarum is certainly an embodiment of that fact. I have done the bulk of development alone, with some help from Franz Liedke. In the spirit of "getting it done", I have thus far neglected to [write tests](https://github.com/flarum/core/issues/245) and [audit performance](https://github.com/flarum/core/issues/127). Without the feedback of a team, I have produced some low-quality code and APIs that will surely need revision.

My point is this: *Flarum needs your help!*

The goal for beta was to get the codebase out of flux, enough to onboard developers. Now, to kickstart potential contributors, we have written detailed descriptions for many of the GitHub issues. They contain pointers on what needs to be done and where to look. [See if there's anything that you'd like to do!]({{ site.baseurl }}/issues)

In particular, we would like to get a critical discussion about the Extension API going. You can dive in and read the [preliminary documentation]({{ site.baseurl }}/docs/extend) to learn how it currently works, then there is an [issue for feedback on GitHub](https://github.com/flarum/core/issues/246).

If you're not a developer, then please consider [donating]({{ site.baseurl }}/donate). All donations will be used to cover server costs and pay for dedicated development time. (If you're interested in doing some paid work on an open-source project: Show us what you can do by contributing!)

Over the next few months, let's slowly but surely work our way towards a [stable version of Flarum]({{ site.baseurl }}/roadmap).

## Thank You

I have talked a lot about myself in this recount. But everything we've achieved thus far would not have been possible without the help of others.

**Franz Liedke** is the developer of [FluxBB](http://fluxbb.org) and joined forces with me earlier in the year. He shares my passion, exceeds my talent, and has committed many hours of work so far. Thank you!

Thanks to the people who have enthusiastically followed along with development, particularly **Berlo**, **Dominion**, and **Stephen**. You guys have kept me motivated and have significantly improved Flarum through your testing and feedback.

**Matthew McKinlay** was my mentor in the entrepreneurial competition, and has since become a great friend too. He's kept me on track with valuable advice and validation (*Just Do It™*). Thanks Matt.

**My parents and siblings** have housed and supported me in pursuit of my dream. For this I am extremely grateful. I have a pretty darn cool family.

Finally, my brother, **Simon**, who planted the seed and watered it right up until his last day. Flarum is dedicated to you; I hope I have made you proud.
</div>
</article>
