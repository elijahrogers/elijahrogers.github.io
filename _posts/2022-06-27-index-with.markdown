---
layout: post
title:  "Index with"
date:   2022-06-27 08:38:15 -0800
categories: rails enumerable
excerpt_separator: <!--more-->
---

Rail's `Enumerable` module provides a number of time saving methods for dealing with array-like structures. The one that's provided the most utility (for me at least) is `index_with`. I often find myself with an array of items I'd like to use as keys for a new nested hash. With `index_with` this is as simple as:

```ruby
months = ['Jan', 'Feb', 'Mar']
months.index_with({})
=> { 'Jan' => {}, 'Feb' => {}, 'Mar' => {} }
```

This pattern has generally served me quite well, but there is a bug hidden in plain sight.

```ruby
months['Jan'][:days] = 31
=> ?
```

If you, like me, assumed that the above snippet will set the value of `:days` in the 'Jan' hash to 31 you would only be partially correct. In fact, it sets the `:days` for ~every~ month to 31.

```ruby
months['Jan'][:days] = 31
puts months
=> { 'Jan' => { days: 31 }, 'Feb' => { days: 31 }, 'Mar' => { days: 31 } }
```

So how do you get the intended result? You must use a new hash each time.
Beneath the terse syntax, `index_with` is a simple `each` loop that builds a new hash with our provided default value:

```ruby
def index_with(default = (no_default = true))
...
  result = {}
  each { |elem| result[elem] = default }
  result
...
end
```

When the provided default is a literal like `{}`, `index_with` reuses the same exact object each time. The result is that each of the `month` values are the exact same object:

```ruby
month.values.map(&:object_id)
=> [29000, 29000, 29000]
```

Avoiding the literal here gives you the intended behavior:

```ruby
months = ['Jan', 'Feb', 'Mar']
months.index_with(Array.new)
month.values.map(&:object_id)
=> [29001, 29002, 29003]
```