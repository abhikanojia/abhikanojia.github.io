---
layout: post
title: "Extract! method in Rails - 6"
categories: "rails"
tags: rails-6 ruby-on-rails
---

Rails - 6 adds `extract!` method for array.

`extract!` method removes and return the elements for which the block returns a true value.
If no block is given an Enumerator is returned instead.

Now, you may be wondering we can use `reject!` or `select!` similar to what we thought of earlier. This might seem tricky at the first look but lets have a look at some examples which clears your doubt.

<h1 class="light">Array#select! method in Ruby</h1>

---
```ruby

numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Extracting all the odd numbers
# It instead modifies the existing data.
numbers.select! { |number| number.odd? }
  => [2, 4, 6, 8, 10]

numbers
  => [2, 4, 6, 8, 10]
```

<h1 class="light">Array#reject! method in Ruby</h1>

---
```ruby

numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Rejecting all the even numbers
# It instead modifies the existing array.
numbers.reject! { |number| number.even? }
  => [1, 3, 5, 7, 9]

numbers
=> [1, 3, 5, 7, 9]
```
Now let's have a look at `extract!` provided in Rails 6.

```ruby

numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Extracting all the even numbers
numbers.extract! { |number| number.even? }
  => [2, 4, 6, 8, 10]

numbers
  => [1, 3, 5, 7, 9]

```
---
<span class="pull-right">
[Pull Request](https://github.com/rails/rails/pull/33137)
</span>
