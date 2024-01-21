---
layout: post
title: "Negative Scope in Rails 6"
meta: "negative scope rails-6 sapidlabs"
categories: "rails"
tags: rails-6 ruby-on-rails
---

![Rails 6 Tutorials - SapidLabs][rails]

# Rails 6 Adds negative scope for all enum values

Rails 6 provide with some extra stuffs added to it lately which simplifies the daily tasks. If you have used rails previously like we did very often, then you must be knowing that we quite often, stumbled upon a situation where we need Negative scope for a defined enum values.

<br />
Example: Say you have a `Post` model in your rails application, and you have serveral states of the Post example `:draft`, `:published` and `:trashed`. Now, you want to query all the posts that are **not published** or **not in draft** or **not in trash** state.

<br />

Easy ! Isn't it ? Kids stuff !

```ruby
  Post.where.not(state: :draft)
  and
  Post.where.not(state: :published)
  and
  Post.where.not(state: :trashed)
```

Imagine a situation where there are 10 different states for your any defined entity. You will definitely end up defining tens of different methods based your requirements. Isn't it?

<br />

Here, Rails 6 comes to the rescue. Rails 6 ships with an predefined negative scopes, pre-fixed with `not_{enum_name}`. So, above snippet can be re-written as:

```ruby
  Post.where.not(state: :draft) becomes Post.not_draft
  and
  Post.where.not(state: :published) becomes Post.not_published
  and
  Post.where.not(state: :trashed) becomes Post.not_trashed
```
<br />

Super easy and saves some time as well.

[Pull Request](https://github.com/rails/rails/pull/35381/commits/c3d2a5be48aeef0f3beb49902b1fe1c3a8c42c19)

[rails]: /assets/images/shared/rails-6.jpg
