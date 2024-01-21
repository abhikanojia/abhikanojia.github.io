---
layout: post
title: "Descendants vs Subclasses in Ruby"
categories: "rails"
tags: Ruby Rails
---

Descendants has been a part of Rails [ActiveSupport::DescendantsTracker](https://api.rubyonrails.org/classes/ActiveSupport/DescendantsTracker.html) for quite a while. However, `Class#subsclasses` is recently introduced in [Ruby 3.1](https://sapidlabs.com/ruby/2022/01/21/ruby-3-adds-subclasses-method.html)

<img src="/assets/images/ruby-3-1-adds-subclasses-method/ruby-3-1-subclasses.png" alt="ruby-subclasses" class="img-center img-50-imp">

`descendants` is available in Rails.

`subsclasses` is native ruby method and is available in ruby 3.1

```ruby
# available in rails
  Parent.descendants
  => [ChildA, GrandChildA, ChildB]

# available in ruby
  Parent.subclasses
  => [ChildA, ChildB]
```

Notice: `descendants` return all the child classes and `subclasses` only returns child classes directly associated to parent class.

