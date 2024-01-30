---
layout: post
title: "Including and Excluding in Rails-6"
categories: "rails"
tags: rails-6 ruby-on-rails
description: "#including and #excluding in rails 6, This method has actually been renamed from `without` to `excluding` keeping `without` as an alias to excluding."
keywords:
  - including in rails 6
  - excluding in rails 6
  - rails blogs
  - new in rails 6
---

<img src="{{ site.base_url }}{{ site.default_rails_image }}" alt="{{ page.title }}">

Rails 6 has been very exciting since it has so many features, which we found quite helpful from development perspective.

Let's have a few method that has been added worth looking at `including` and `excluding`.

<br />

<h1 class="light">Using #excluding in Rails 6</h1>

Previously this was provided as `without` for `Enumerable#without` or `Array#without` returns the array without the provided value as a parameter.

Example:
```ruby
# In Rails 5
[1, 2, 3].without(2)
=> [1, 3]

[1, 2, 3].without([1, 3])
=> [2]
```

This method has actually been renamed from `without` to `excluding` keeping `without` as an alias to excluding. So, in rails-6 we will be able to use both of them.

Example:

```ruby
# In Rails 6
[1, 2, 3].excluding(2)
  => [1, 3]

[1, 2, 3].excluding([1, 3])
  => [2]
```
<br />

<h1 class="light">Including</h1>
Rails-6 adds `including` method as a counter part to `excluding` or `without` method. `including` does what excluding does, in opposite manner. It includes the passed value as parameter along with the Array or Enumerable on which it was invoked.

Example:

```ruby
[1, 2, 3].including(33)
=> [1, 2, 3, 33]

[1, 2, 3].including([22, 23])
=> [1, 2, 3, 22, 23]


# With duplicate values

[1, 2, 3].including([2, 3])
=> [1, 2, 3, 2, 3]
```

