---
layout: post
title: "delete_by destroy_by rails 6"
meta: "delete_by destroy_by rails-6 sparkrails"
categories: "rails"
tags: rails-6 ruby-on-rails
keywords:
  - rails-6 rubyonrails
  - rails
  - destroy_by
  - delete_by
---

## Rails 6 adds delete_by &amp; destroy_by

Rails 6 provide with some extra stuffs added to it lately which simplifies the daily tasks. In this post we will be discussing two methods delete_by and destroy_by provided in Rails 6.

<br />

### delete_by
delete_by provides an easier way to delete the Active Record instance by condition provided as an argument to delete_by method.
Here is how it works:

```ruby
# Previously
Person.where(name: 'John Doe').delete_all

# Now
Person.delete_by(name: 'John Doe')
```
<br />


### destroy_by
Similarly, destroy_by uses destroy_all in place of delete_all for its operation.
Usage:

```ruby
# Previously
Person.where(name: 'John Doe').destroy_all

# Now
Persion.destroy_by(name: 'John Doe')
```
<br />

Few Things worth noting about both of the methods:

`delete_by` - Internally uses `delete_all` to perform the operation.

`destroy_by` - Internally uses `destroy_all` to perform the operation.

<br />

See [Pull Request](https://github.com/rails/rails/pull/35316) for more info.

