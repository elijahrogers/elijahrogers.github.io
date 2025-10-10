---
layout: post
title: I spent 200 hours building a Postgres chat client
date: 2025-10-02 16:34 -0700
excerpt_separator: <!--more-->
---

I'm a big proponent of Cal Newport's _Deep Work_ philosophy because I've found it to be incredibly effective. My productivity seems to be directly proportional to how much uninterrupted time I can spend on a task.

Of course, this isn't easy in the real world. Even when I can stave off the endless torrent of information the internet provides there are people who break your flow. Usually coworkers. This is manageable but there's one particular type of interruption that's a killer: data questions.

<!--more-->

<!-- <img src="{{ site.url }}/assets/images/deep_work.png" class="image"  style="max-height: 700px;"> -->

<img src="{{ site.url }}/assets/images/stupid_questions.jpg" class="image-left" style="max-width: 50%;">
  <em>A abstract representation of my Slack messages</em>
</img>

"Can you tell me how many..."

"Can you build me a report that shows..."

"Can you run another report for August..."

These questions are usually well intentioned but they're incredibly distracting. Many times they're not even sure what they want. Most of the time, the answer to their question won't actually change anything. Does it matter whether we have 1000 or 1001 customers? Does it change our priorities? Our strategy?

I can't really blame them though. Everyone has hunches or curiosities they want to indulge without any real outcome in mind. I do the same thing myself. The problem is that their curiosity is breaking my flow.


It would be great if I could just remove myself from the loop. Let them indulge their hunches and I can get back to work. Of course, I'm not advocating for giving non technical users access to the production database. Just some way for them to access the data without interrupting me.

They can't write SQL but I know someone who's pretty good at it: Claude. Sure he doesn't have all of the domain knowledge but LLMs are superb at working with strucured data.

So I set off to build <a href="https://mahoganychat.com">Mahogany Chat</a>. A way for non technical users to get insights from data without bugging developers, DBA's, engineers, etc.

## Something something 10,000 hours

After creating a working MVP I dove head first into building. Everything was moving quickly and I figured I'd be able to knock this out in a month or two. I'm sure you can guess what happened next.

I fell down the rabbit hole. A chat interface is good start but there should be a way to view the results as a table. A way to export those results. A way to generate a chart from the results automatically. A way to connect via an SSH tunnel. User preferences. User onboarding. And of course, a demo database so that users can try it out as soon as they sign up. Oh and I can't forget to manage the database connections with a connection pool so that we don't hit their database too hard. etc. etc.

I couldn't help myself. I had to build it all. It was fun but it took way longer than I expected.

## The Result

Now that I've built it I just need to tell people about it. Easy right? Well that turned out to be a whole new rabbit hole called marketing and sales. I needed a landing page with just the right copy to explain it, a demo video, pricing, and an email for support. This rabbit hole wasn't as deep but it was just as difficult because I was learning as I went.

It was in the midst of this learning journey that I decided to do what the professionals call "market research". You may not be as surprised as I was to learn that this is usually done before you build an entire application. I was equally surprised to learn that this idea I had was so good that someone else already had it. Like 20 some elses. With teams of people and venture backing.

I'll be honest. At this point I considered bailing. But due to my lack of prior market research I was already too far in. I decided to see it through.

## The Launch

I'm happy to say that I've finally launched <a href="https://mahoganychat.com">Mahogany Chat</a>. It's not perfect but it's probably the coolest thing I've built on my own. I'm proud of finally shipping it.

I've put a lot of time and effort into this project and I'm excited to see where it goes.
