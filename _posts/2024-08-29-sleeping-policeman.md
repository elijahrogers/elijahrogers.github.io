---
layout: post
title: Sleeping Policeman
date: 2024-08-29 08:17 -0700
---

# The Problem

There are a plethora of articles and discussion on the pernicious effects of news feeds. They are so pervasive now that it's easy to forget they're even there. But time and time again they quietly suck you into a tightly controlled feedback loop design to "maximize user engagement."

Limiting their effects can be difficult. Designed and perfected through millions of man hours over the last decade or so, these infinitely scrolling, attention draining nightmares are so effective at pulling you in that you often waste time - sometimes hours - before waking up from your daze of consumption.

The only 100% effective way to avoid their pull is to avoid them altogether but who wants to do that? For all their downsides, there are plenty of times where the time on them is well spent. Limiting screen time might work for some people but I've found it to be broadly ineffective. Moderation is difficult to achieve for the same reason that people develop gambling addictions - the incentives are not aligned and the system is designed in an adverserial way.

<!-- <figure style="float: left; margin-right: 10px; text-align: center; max-width: 60%">
  <img src="{{ site.url }}/assets/images/load_time.jpg" alt="Description of the image" style="max-width: 50%;">
  <figcaption style="font-style: italic; font-size: 0.75rem; margin-top: 0.25rem">.</figcaption>
</figure> -->

# The Solution

<img src="{{ site.url }}/assets/images/load_time.jpg" alt="Description of the image" style="margin: 2rem auto;">

<!-- ![load time vs bounce rate]({{ site.url }}/assets/images/load_time.jpg)
*From https://www.thinkwithgoogle.com/marketing-strategies/app-and-mobile/page-load-time-statistics/* -->


Of course, the real way to beat the feed is to use its own tactics against it. There are several attack vectors here but the simplest is speed. Anyone who's optimized for user engagement would tell you that speed is key - when things slow down the feedback loop breaks down and users quickly lose interest.

You may have even noticed this yourself if you've even used slow public Wifi. Below a certain speed, using the internet becomes boring. You can find a number of different statistics on this from various website monitoring, SEO, and ecommerce companies. Gooogle (ostensibly an authoratative source) estimates that an increase in the loading time from [1 to 3 seconds increases the probability of a bounce by 32%](https://www.thinkwithgoogle.com/marketing-strategies/app-and-mobile/page-load-time-statistics/).

<img src="{{ site.url }}/assets/images/sleeping_policeman.png" alt="Description of the image" style="float: left; margin: 2rem;max-width: 40%;">

Whatever source you use though the relationship is the same - load times and bounce rates are strongly positively correlated. This means that artificially increasing the load time of a news feed over time should slowly ramp up the probability that you stop scrolling.

To test my theory I built Sleeping Policeman. It's a gentle speed bump for the feed. By counting the number of requests and gradually adding a delay to each subsequent request it slow nudges you in the right direction. It's just a tool to help you get back the time that the feed has taken from you.

<!-- ![Sleeping Policeman]({{ site.url }}/assets/images/sleeping_policeman.png) -->

There's no lockout, no paternalism, no admonition for wasting time. Force is rarely effective for influencing the behaviour of adults with autonomy and often ilicits pushback. You can turn it off at any time if you like with a simple toggle. It's your decision.

You can try out the extension right now for free if you're using [Firefox](https://addons.mozilla.org/en-US/firefox/addon/sleeping-policeman/) or see the [source code](https://github.com/elijahrogers/sleeping_policeman) for yourself.

