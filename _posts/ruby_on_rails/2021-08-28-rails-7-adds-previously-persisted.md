---
layout: post
title: "Rails 7 adds ActiveRecord::Base#previously_persisted?"
categories: "rails"
tags: rails-7 ruby-on-rails
---

If you have not installed Rails 7 yet, please follow our other article on [How to install rails 7](https://sapidlabs.com/rails/2021/06/12/how-to-install-rails-7.html) to do it in 10 seconds.

Rails 7 adds method `previously_persisted?` in `ActiveRecord::Base`, which enables you to check if the object was existed previously and now is destroyed or deleted.

### Example
```ruby
# finds post by id 1
post = Post.find(1) 

# delete/destroy post
post.destroy
# or
post.delete

# check if object persisted previously?
post.previously_persisted?
=> true
```

### Implementation
The implementation of this method is as simple as checking if the record is not new and is destroyed
```ruby
  def previously_persisted?
    !new_record? && destroyed?
  end
```
<br/>

