---
layout: post
title: "How to use Enum in Rails"
categories: "rails"
tags: rails-6 ruby-on-rails enum
keywords:
  - enum rails-6 ruby on rails
---


# Enum in Rails

"Enum" for short for the word "Enumerator" is widely used in different programming languages to define different values that can be a constant. When it comes to Rails, enum plays an important role. How?

<br />
Example: Say you have a `Post` model in your rails application, and you have serveral states of the Post example `:draft`, `:published` and `:trashed`. Now, you want to query all the posts that are **published** or **in draft** or **in trash** state.

```ruby
class Post < ApplicationRecord
  enum status: [ :draft, :published, :trashed ]
  # will be stored as 0,  1,  2 in the database.
end
```

You can define a state field as integer and map that to an `enum` state.

<br />

By using in our Post model, we get scopes based on the values we provided in enum.

# Scopes provided by enum in Rails
For example: Here we get the following scopes:

```ruby
  Post.draft => all the posts which are in `draft` state
  and
  Post.published => all the posts which are in `published` state
  and
  Post.trashed => all the posts which are in `trashed` state
```

<br />

# Predicate methods provided by enum in Rails

Here, are the predicate methods that each `Post` instance will have:

```ruby
  post = Post.first => returns the first post
  
  post.draft? => returns true the `Post` is in `draft` state
  and
  post.trashed? => returns true the `Post` is in `trashed` state
  and
  post.published? => returns true the `Post` is in `published` state
```
<br />

# Good practice to declare enum in Rails

A good practice to follow while declaring enum in Rails is by creating it as a Hash. You will thank yourself later when it comes to changing those enums i.e adding and removing values. Something like the following:

```ruby
class Post < ApplicationRecord
  
  enum state: {
    draft: 0,
    published: 1,
    trashed: 2
  }

end
```

# Multiple enums with same value in rails

Say in our `Post` model we have another enum defined as `comment_state` which has all the three values we used for `state` i.e `:draft`, `:published` and `:trashed`.

Here comes `_prefix` and `_suffix` to the rescue.
You can either use `true` option or the custom suffix you want to provide. By providing the `_suffix: true` the method that will be created will be as follows:

### Using _suffix in rails enum

```ruby
class Post < ApplicationRecord
  enum state: {
    draft: 0,
    published: 1,
    trashed: 2
  }, _suffix: true
end

# scopes available will be 
Post.draft_state
Post.published_state
Post.trashed_state

# methods 
post = Post.new
post.draft_state?
```

### Using _prefix in rails enum

```ruby
class Post < ApplicationRecord
  enum comment_state: {
    draft: 0,
    published: 1,
    trashed: 2
  }, _prefix: :comments
end

# scopes available will be 
Post.comments_draft
Post.comments_published
Post.comments_trashed

# methods 
post = Post.new
post.comments_draft?
```

# Bonus: Negative Scopes in Rails 6
Read about [Negative scopes in Rails](https://sparkrails.com/rails/2019/06/25/add-negative-scope-for-all-enum-values.html). 

<br />

[Official Ruby Enum Guide](https://api.rubyonrails.org/v5.2.3/classes/ActiveRecord/Enum.html)
