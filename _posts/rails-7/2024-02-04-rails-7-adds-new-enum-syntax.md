---
layout: post
title: "Rails 7 Introduces a New Syntax for Enum"
categories: "rails"
description: "Rails 7 new introduces a new syntax to define enum, lets see what has changed since older versions of rails"
tags: rails enums
keywords:
  - Rails 7 Enums Syntax, Ruby on Rails Tips
  - Rail 7 new enum syntax, whats new in rails 7
---

Rails 7 introduces a new syntax for defining enum. Lets take a look what has changed.

## What is enum in Rails?
Enum ( Enumeration ) is a datatype available in Rails, which can be used to map values to integral values in the database, which means it takes less space and provides the efficiency requires. It allows the flexiblity to query it by name, makes it even better candidate to use where you want to define multiple values of same attribute. For example, post status can be `draft` or `published`.

# Old vs. New: A Quick Look

In the old days, defining enum involved a key-value pair, but not anymore! Rails 7 separates enum names and values, making your code more straightforward:

```ruby
# Before
enum status: [ :draft, :published, :archived ], _prefix: true, _scopes: false

# After
enum :status, [ :draft, :published, :archived ], prefix: true, scopes: false

```

## Defining enum with hash syntax

The changes in rails are seamlessly extends the use of hash syntax as well, lets take a look in folowing example.

```ruby
# Hash syntax
enum :status, { draft: 0, published: 1, archived: 2 }, prefix: true, scopes: false

```

## Why did they changed the enum syntax in Rails 7?

Based on the discussion on this [PR#41328](https://github.com/rails/rails/pull/41328), that is due to enum takes on hash argument only, which contains both enum definitions and reserved options. To avoid leading an underscore from reserved options as in `_prefix` for example. This new syntax was introduced.

<br/>

## Is the old enum syntax deprecated?

The old way of defining an enum is not yet deprecated. Following way of defining is still valid as of Rails 7.1.3.

```ruby
class Post < ApplicationRecord
  enum status: [:draft, :published]
end
```
<br/>

<mark>** Please note that the new `enum` validation options as mentioned in the</mark> [Article](/rails/2024/02/03/rails-7.1-enum-validation.html) <mark>will result in error if you try to use it with old syntax.</mark>

```ruby
class Post < ApplicationRecord
  enum status: [:draft, :published], validate: true
end

post = Post.new
#
# => `raise_conflict_error`: You tried to define an enum named "status" on the model "Post", but this will generate a instance method "draft?", which is already defined by another enum. (ArgumentError)
```




