---
layout: post
title: "Ruby 3.1 adds subclasses method"
categories: "ruby"
tags: Ruby
---

Ruby 3.1 adds `Class#subclasses` method, which returns an array of subclasses directly inheriting from the
receiver, not including singleton classes.

<img src="/assets/images/ruby-3-1-adds-subclasses-method/ruby-3-1-subclasses.png" alt="ruby-subclasses" class="img-center img-50-imp">
<br />

```ruby
  class Parent; end
  
  class ChildA < Parent; end
  class ChildB < Parent; end
  
  class GrandChildA < ChildA; end

  Parent.subclasses
  => [ChildA, ChildB]

  ChildA.subclasses    
  => [GrandChildA]
  
  GrandChildA.subclasses    
  => []
```

Reference [#Pull Request](https://github.com/ruby/ruby/pull/5045)
<br/>
