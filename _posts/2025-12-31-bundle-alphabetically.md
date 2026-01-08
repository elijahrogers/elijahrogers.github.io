---
layout: post
title: bundle_alphabetically
date: 2025-12-31 17:31 -0800
excerpt_separator: <!--more-->
---

You know what's nice? When things go where they're supposed to. Mise en place. I know keeping everything neat and tidy is an uphill battle but a little hygiene goes a long way.

This is especially true with the oft neglected Gemfile. Dependencies are important and they shouldn't be shoved in a junk drawer. Besides keeping around commented out gems, there is one sin that seems to be ubiquitous: keeping gems out of order.

<!--more-->

"I wasn't aware that there was a correct order" you might be thinking but, of course, that's wrong. Clearly gems should be in alphabetical order.

Why? Well, just look at this mess:

```ruby
source "https://rubygems.org"
git_source(:github) { |repo| "https://github.com/#{repo}.git" }

ruby "3.2.2"

gem "rails", "~> 7.1.5"
gem "sprockets-rails"
gem 'pg'
# Build JSON APIs with ease [https://github.com/rails/jbuilder]
gem "jbuilder"
gem 'devise'
gem "puma", "~> 6.0"
gem 'jsbundling-rails'
gem "turbo-rails"
gem "stimulus-rails"
gem "jbuilder"
gem "redis", "~> 4.0"

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem "tzinfo-data", platforms: %i[ mingw mswin x64_mingw jruby ]

# Reduces boot times through caching; required in config/boot.rb
gem "bootsnap", require: false
gem "image_processing", "~> 1.2"
gem "good_job", "~> 3.23"
gem "cssbundling-rails", "~> 1.4"
gem "font-awesome-sass", "~> 6.5"
gem "fuzzy_match", "~> 2.1"
gem 'google-cloud-storage'
gem "kamal", "~> 2.2"
```

Now look at this pristine Gemfile:


```ruby
source "https://rubygems.org"
git_source(:github) { |repo| "https://github.com/#{repo}.git" }

ruby "3.2.2"

# Reduces boot times through caching; required in config/boot.rb
gem "bootsnap", require: false
gem "cssbundling-rails", "~> 1.4"
gem 'devise'
gem "font-awesome-sass", "~> 6.5"
gem "fuzzy_match", "~> 2.1"
gem "good_job", "~> 3.23"
gem 'google-cloud-storage'
gem "image_processing", "~> 1.2"

# Build JSON APIs with ease [https://github.com/rails/jbuilder]
gem "jbuilder"
gem 'jsbundling-rails'
gem "kamal", "~> 2.2"
gem 'pg'
gem "puma", "~> 6.0"
gem "rails", "~> 7.1.5"
gem "redis", "~> 4.0"
gem "sprockets-rails"
gem "stimulus-rails"
gem "turbo-rails"

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem "tzinfo-data", platforms: %i[ mingw mswin x64_mingw jruby ]

```

Which one sparks joy? Which one makes you excited to work on the project? I could argue that this makes the gems "easily scannable" but the reality is that it's just aesthetically pleasing. It feels nice.

The trouble is that maintaining this order is annoying. I personally love to just `bundle add gem` and install but this quickly becomes messy. Why should I have to eschew the niceties of bundler to keep things looking good?

Of course, this class of problem has been solved many times over. We have all kinds of linters and formatters that enforce consistent styles automatically (shout out RuboCop) but this seems like the responsibility of bundler.

So I built a bundler plugin. It's not polished but it should work for most setups. Once installed, it hooks into bundler's install command to keep everything where it belongs.

Give it a try and keep your Gemfile pretty: `bundle plugin install bundle_alphabetically`

Or check out the source code if you're into that kind of thing: [bundle_alphabetically](https://github.com/elijahrogers/bundle_alphabetically)
