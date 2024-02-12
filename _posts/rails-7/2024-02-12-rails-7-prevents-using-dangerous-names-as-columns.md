---
layout: post
title: "Rails 7 prevents use of reserved names as model attributes"
categories: "rails-7"
tags: rails rails-7
description: "Rails 7 prevents use of reserved names by raising an error while running migration. This is helpful to prevent mistakes like using names which are already available in rails"
keywords:
  - rails 7, ruby on rails, model attributes
  - rails migrations, rails reserved method names
  - rails 7 updates, dangerous attribute error rails 
---

Rails 7 prevents use of reserved names by raising an error while running migration. This is helpful to prevent mistakes like using names which are already available in rails as a part of instance methods from `ActiveRecord`. For Example: `attributes`, `attribute_names`, `errors` e.t.c

### Before Rails 7.1

It was possible to use reserved names and rails won't complaint until it is used in the code. Here is the example:

```ruby
class CreateUsers < ActiveRecord::Migration[6.0]
  def change
    create_table :users do |t|
      t.string :name
      t.string :email
      t.jsonb :attributes, default: {}
      t.timestamps
    end
  end
end
```

Running this migration would just like any other migration without any issues. Its only when you try to use it in the code it raises the error.

```ruby
User.create!(
  name: "SparkRails",
  email: "hello@sparkrails.com",
  attributes: { checked: true }
)

=> attributes is defined by Active Record. 
Check to make sure that you don't have an attribute 
or 
method with the same name. (ActiveRecord::DangerousAttributeError)
```

## In Rails 7

Rails 7 raises an error that says "Could not generate field `attributes` as it is already defined by Active Record". This is valid for any public and private methods that is already part of ActiveRecord. This will save your time by raising an error right before you add it to the table.

```ruby
rails g migration create_users name:string email:string attributes:jsonb

=> Could not generate field 'attributes', as it is already defined by Active Record.
```
