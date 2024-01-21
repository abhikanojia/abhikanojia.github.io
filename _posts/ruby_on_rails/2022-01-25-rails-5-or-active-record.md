---
layout: post
title: "Rails 5 adds OR Activerecord method"
categories: "rails"
tags: rails-5 ruby-on-rails
---

This `or` method was introduced in Rails 5, It returns a new `ActiveRecord::Relation` which is logical union or two relations.

Example:

```ruby
  Post.where("id = 1").or(Post.where("author_id = ?", 66))
  => SELECT `posts`.* FROM `posts` WHERE ((id = 1) OR (author_id = 66))
```

**Note:** The two relations must be structurally compatible, i.e they must scope same model.

## Combining OR with Scope

<br />
ActiveRecord `or` can be used along with the defined scope in the model.

Example:

```ruby
  class Post < ApplicationRecord
    scope :active, -> { where(active: true) }
  end

  Post.active.or(Post.where("id = ?", 66))
  => returns all Posts where Post are active or Post id is 66
```

Reference [#Pull Request](https://github.com/rails/rails/commit/b0b37942d729b6bdcd2e3178eda7fa1de203b3d0)
