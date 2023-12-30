---
layout: post
title:  "Irb's Easter Egg"
date:   2023-11-27 13:57:05 -0800
tags: ruby
excerpt_separator: <!--more-->
---

If you've used Ruby for more than 5 minutes, you've probably come across the [IRB](https://github.com/ruby/irb) gem or it's popular counterpart [Pry](https://github.com/pry/pry). Both of these provide a REPL to explore and execute Ruby code.

IRB is old - nearly as old as the language itself in fact. For a mature library like this, I would expect a slow development cycle mostly comprised of incremental improvements, bug fixes, and performance enhancements. Imagine my surprise then when I stumbled on the following [Easter egg](https:///github.com/ruby/irb/pull/69/files) that was added in 2020:

![easter egg]({{ site.url }}/assets/images/easter_egg.png)

Instead of generating autocomplete suggestions for any module or class in the `RubyVM` namespace, IRB shows an ASCII easter egg.

This makes sense when you realize that the `RubyVM` class is a special one. It's only defined in MRI ruby and provides access to the internals of the interpreter itself. This is dangerous territory and the [offical docs](https://ruby-doc.org/3.2.1/RubyVM.html) warn: "Normal users must not use it." In order to disable this behavior, you can set the aptly named environment variable `RUBY_YES_I_AM_NOT_A_NORMAL_USER`.

The `RubyVM` class itself only defines one method - `stat` - which keeps track of some counters like the number of global constants defined. The only other contents of this namespace are a few constants and the `AbstractSyntaxTree` and `InstructionSequence` classes used to turn Ruby code into instructions for the VM.

It turns out that the ASCII ruby is just a fun way to ward off unsuspecting users from Ruby internals. There is still one mystery that remains unsolved however: there's an animated version which (as far as I can tell) isn't used anywhere:

![easter egg]({{ site.url }}/assets/images/easter_egg.gif)