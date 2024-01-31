---
layout: post
title: "How to rename column name in Rails"
meta: "rename_column rails-6 sapidlabs"
categories: "rails"
tags: rails-6 ruby-on-rails
---


# Rails `rename_column` 
Rails provides a simple method `rename_column` which can be used in a migration.

<br />
Example: Say you have a `User` model in your rails application, with a db field as `name`. Now, you want to change the name of the column. we should generate a migration by using `rails g migration change_users_name_to_title`. Our migration should look like:

```ruby
class ChangeUsersNameToTitle < ActiveRecord::Migration[6.0]
  def change
    rename_column :users, :name, :title
  end
end
```
<hr /> 
Now run `rails db:migrate` in order to make the changes to the database.

```ruby
## Rename column method
rename_column(:table_name, :column_name, :new_column_name)
```

<br />
Verify from the `schema.rb` to see if the field actually changed.

<br />

