---
layout: post
title: "Rails 7: New in_order_of Method in ActiveRecord"
categories: "rails"
tags: rails-7 ruby-on-rails
---

<img src="/assets/images/shared/rails-7.png" alt="default-enum-in-rails">

Rails 7 adds method `in_order_of` in QueryMethods, this allows you to specify an explicit order for the records.

### Example usage of `in_order_of`
```ruby
# Get ids of posts
Post.pluck(:id)
# => [1, 4, 5, 6, 7, 8]

# Using explicit order of posts
Post.in_order_of(:id, [4, 1, 5]).pluck(:id)
# => [4, 1, 5, 6, 7, 8]
```

Notice how it returns the order of `Posts` in specified order, followed by all other posts.

If we provide some column data that does not exists in ordering, it will be ignored silently. In the following example, `Post` with id `999` does not exists in our system.
```ruby
Post.in_order_of(:id, [4, 1, 5, 999]).pluck(:id)
# => [4, 1, 5, 6, 7, 8]
```
<br/>

