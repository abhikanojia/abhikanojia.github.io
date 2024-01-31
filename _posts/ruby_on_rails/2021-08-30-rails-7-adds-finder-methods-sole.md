---
layout: post
title: "Rails 7 adds ActiveRecord::FinderMethods#sole"
meta: "Rails-7 ruby-on-rails sapidlabs"
categories: "rails"
tags: rails-7 ruby-on-rails
---

Rails 7 adds method `sole` and `find_sole_by` in `ActiveRecord::FinderMethods`, which enables you to get sole or single record by the attributes defined, and also asserts that there aren't multiple rows matching the condition.

### Example usage of `sole`
```ruby
Product.where(name: 'Test').sole
# => ActiveRecord::RecordNotFound ( If no record is found )

# => #<Product ..> ( If single record is found )

# => ActiveRecord::SoleRecordExceeded ( If more records are found with given name )
```

### Example usage of `find_sole_by`
```ruby
Product.find_sole_by(name: 'Test')
# => ActiveRecord::RecordNotFound ( If no record is found )

# => #<Product ..> ( If single record is found )

# => ActiveRecord::SoleRecordExceeded ( If more records are found with given name )
```
<br/>

