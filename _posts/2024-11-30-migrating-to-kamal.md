---
layout: post
title: Migrating to Kamal
date: 2024-11-30 19:22 -0800
excerpt_separator: <!--more-->
---

When I created SnippetStash I did some research on the easiest way to deploy a new Rails app. The default answer used to be Heroku but times have changed.
Based on my research, Heroku had become expensive and there were a few viable alternatives. Many suggested Fly.io or Render with various tradeoffs.

I decided that Render would be the easiest to set up, and automatic deploys were ideal for a hobby project. This was mostly true but I ran into some issues.

<!--more-->

> "We do these things not because they are easy but because we thought they would be easy."

Generating a `render.yaml` file and setting up a basic Postgres database was straightforward. Pleasant almost. The issue was setting up background workers. This required a separate service with a custom Dockerfile.

Despite my limited experience with Docker, I think this would have been simple enough to figure out. There are a lot of base images available for common setups and this is more or less a solved problem.

Unfortunately, I also needed a working Python environment. This meant I needed to either

1. Start from a base Python image and install Ruby/Rails or
2. Start from a base Ruby image and install Python.

Neither of these options seemed great but I figured I'd have better luck grafting a Rails environment onto a Python image than vice versa. I still think this was right approach but the end result was not what I hoped for.

Instead of a simple deploy setup, I had a two YAML files (one in a proprietary format) and three different services. It was also expensive for the minimal amount of compute I was using. It's not the end of the world but ~$250/year for a hobby project running a combined ~1 vCPU and less than 2GB of memory seems excessive.

Of course, there was also the backup solution: Kamal. I had neglected this path initially because I thought it was going to be more difficult to setup.

While it also had a learning curve, Kamal wasn't any harder to figure out than Render and the flexibility is amazing. I was reminded of something DHH himself said on a [recent podcast](https://www.youtube.com/watch?v=tWduT9ygUQ4)

> "It is so easy in your quest to try to completely erase an area of complexity that the solution you come up with eventually becomes more complicated. [...]
> because if you're not willing to understand that [inherent complexity], you're going to be forced into understanding essentially the same thing, wrapped in usually a commercial package."

In the end, Kamal will save me almost $200/year and I'll be able to move providers whenever I choose. Win-win.
