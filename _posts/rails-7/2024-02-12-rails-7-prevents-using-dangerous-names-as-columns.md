---
layout: post
title: "Rails DangerousAttributeError: Reserved Column Name Fix"
categories: "rails-7"
tags: rails rails-7 activerecord
description: "Why Rails raises ActiveRecord::DangerousAttributeError for columns like attributes, how Rails 7.1 generators block them, and how to fix existing tables."
last_modified_at: 2026-10-27
keywords:
  - activerecord dangerousattributeerror
  - is defined by active record
  - rails reserved column names
  - could not generate field as it is already defined by active record
  - rails 7.1 dangerous attribute
---

<div class="quick-answer" markdown="1">
**Quick answer:** `ActiveRecord::DangerousAttributeError: attributes is defined by Active Record` means a column has the same name as a method Active Record already defines (`attributes`, `attribute_names`, `save` and so on). Since **Rails 7.1**, `rails generate` refuses to create such columns. For a table that already has one, rename the column, or add it to `ignored_columns` if you can't rename it yet.
</div>

Rails prevents reserved names by failing early, when you generate the migration. This stops you from using names that Active Record already uses for its own instance methods, for example `attributes` or `attribute_names`.

### Before Rails 7.1

You could use reserved names, and Rails wouldn't complain until the code used them. Here is an example:

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

This migration runs like any other. The error appears only when you use the model:

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

## In Rails 7.1 and later

The model and migration generators now raise "Could not generate field `attributes`, as it is already defined by Active Record". This covers any public or private method already defined by Active Record, so you find out before the column reaches the table.

```ruby
rails g migration create_users name:string email:string attributes:jsonb

=> Could not generate field 'attributes', as it is already defined by Active Record.
```

Note that this check lives in the **generators**. A migration you write by hand will still create the column, and you'll hit `DangerousAttributeError` at runtime as before.

## Fixing an existing table

If a legacy table already has a dangerous column name:

1. **Rename the column.** This is the clean fix:

   ```ruby
   class RenameUsersAttributes < ActiveRecord::Migration[8.0]
     def change
       rename_column :users, :attributes, :settings
     end
   end
   ```

   On a busy production table, check how your database handles renames and deploy the code change and migration together. Code that still references the old name will break.

2. **Ignore the column** if other systems still write to it and you can't rename it yet. Active Record then won't generate methods for it:

   ```ruby
   class User < ApplicationRecord
     self.ignored_columns += ["attributes"]
   end
   ```

   You can still read the raw value with SQL (for example, `User.where(id: id).pick(Arel.sql("attributes"))`) while you migrate the data to a new column.

{% include elements/post_cta.html text="Legacy schemas with surprises like this are common in older Rails apps. I help SaaS teams upgrade and clean up production Rails apps without downtime." %}

## Related

- [Rails ActiveRecord cookbook](/rails-active-record-cookbook/)
- [How to rename a column in Rails](/rails/2020/05/10/rename-column-in-rails.html)
- [8 steps before you upgrade Rails 6 to Rails 7](/rails/2026/06/19/rails-6-to-7-upgrade-checklist.html)
