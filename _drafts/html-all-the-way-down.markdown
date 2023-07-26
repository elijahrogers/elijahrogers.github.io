---
layout: post
title:  "HTML All the Way Down"
date:   2022-06-13 15:08:15 -0800
categories: jekyll update
excerpt_separator: <!--more-->
---

While doing some recent debugging using Chrome's dev tools, I did something I've never thought to do before: use the dev tools shortcut on the dev tools pane. The result was certainly suprising.

[screenshot]()

This was not intentional, but piqued my curiosity. Why would such a core piece of technology use HTML/CSS/JS? Was this feature useful for anyone outside of Google? How many layers deep does this recursion go?

As it turns out, this feature does get some use outside of Google because it allows developers to debug their chrome extensions. This might also explain the technology selection: there are a lot more developers familiar with web technologies than whatever proprietary framework they could have built for this purpose. 

Of course, this still doesn't explain the depth of the rabbit hole. There doesn't appear to be a hard limit to the number of dev tools for dev tools you can spawn and it's not clear why you might need to debug the dev tools for the dev tools of an exension that works in devtools.
