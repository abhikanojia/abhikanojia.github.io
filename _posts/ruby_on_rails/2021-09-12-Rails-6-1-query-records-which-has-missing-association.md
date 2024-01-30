---
layout: post
title: "How to query records which has missing associations in Rails 6"
categories: "rails"
tags: rails-6 ruby-on-rails
---


Rails 6 provides a simpler way to query the records which does not have any associated records or orphan records so to say.

### How to query records with No associations in Rails
This reads, <b>Fetch all the users which have no posts.</b>
```ruby
  User.left_outer_joins(:posts).where(posts: { user_id: nil })
```

### Fetch Records with missing associations Rails 6
It is as easy as using `missing` with Rails 6
```ruby
  User.where.missing(:posts)
  # => Gonna give out same results as previous query
```
