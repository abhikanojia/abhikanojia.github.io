---
layout: post
title: "Ruby 2.7 adds Enumerable#tally"
meta: "ruby enumerable tally sapidlabs"
categories: "ruby"
tags: 'Ruby'
description: 'Ruby 2.7 adds Enumerable#tally'
---

<br />
After introducing the Enumerable#tally method in [Ruby 2.7-preview](https://sapidlabs.com/ruby/2019/06/16/updates-in-ruby-2-7-0-preview1.html) updates.

`#tally` as the name suggests gives out the number of occurrences of elements.

### Before #tally
```ruby
  elements = [2, 3, 2, 4, 1]

  tally_hash = Hash.new(0)

  elements.each { |number| tally_hash[number] += 1 }

  tally_hash
  # => { 2 => 2, 3 => 1, 4 => 1, 1 => 1 }
```

### Ruby 2.7
  More clean and elegant solution

```ruby
  elements = [2, 3, 2, 4, 1]
  
  elements.tally 
  # => { 2 => 2, 3 => 1, 4 => 1, 1 => 1 }
```

Evolution of `#tally` is `#tally_by` that we discussed earlier in our [Ruby 2.7 Preview](https://sapidlabs.com/ruby/2019/06/16/updates-in-ruby-2-7-0-preview1.html) blog.
