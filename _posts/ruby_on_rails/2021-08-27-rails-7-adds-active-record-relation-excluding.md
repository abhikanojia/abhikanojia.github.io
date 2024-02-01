---
layout: post
title: "Rails 7 adds ActiveRecord::Relation#excluding"
categories: "rails"
tags: rails-7 ruby-on-rails
keywords:
  - Rails-7 ruby-on-rails sparkrails
---

If you have not installed Rails 7 yet, please follow our other article on [How to install rails 7](https://sparkrails.com/rails/2021/06/12/how-to-install-rails-7.html) to do it in 10 seconds.

Rails 7 adds method `excluding` in ActiveRecord::Relation, which means you can now re-write the following code in
much cleaner way.

### Without Rails 7
```ruby
# finds post by id 1
post_1 = Post.find(1) 

# Find all posts without post_1
Post.where.not(id: post_1.id)
```

### With Rails 7
```ruby
# finds post by id 1
post_1 = Post.find(1) 

# Find all posts without post_1
Post.excluding(post_1)
```
<br/>


