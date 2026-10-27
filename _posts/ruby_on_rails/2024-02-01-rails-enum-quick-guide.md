---
layout: post
title: "Rails 8 Enums: A Quick Guide to Syntax, Methods and Scopes"
categories: "rails"
tags: rails rails-8 enums
description: "How enums work in Rails 8: the enum :status syntax, the predicate, bang and scope methods Rails generates, validate: true, and when not to use an enum."
last_modified_at: 2026-10-27
keywords:
  - rails 8 enum
  - rails enum
  - rails enum methods
  - rails enum scopes
  - how to use enum in rails 8
---

<div class="quick-answer" markdown="1">
**Quick answer:** in Rails 8, declare an enum with the name as a positional argument, `enum :status, { pending: 0, in_progress: 1, completed: 2 }`, on an integer (or string) column. Rails generates `task.pending?`, `task.pending!`, the scopes `Task.pending` / `Task.not_pending`, and `Task.statuses`. The older `enum status: [...]` form no longer works in Rails 8.0 ([how to fix it](/rails-7/2024/02/13/rails-7-deprecated-enum-with-keywords-args.html)).
</div>

### What an enum is

An enum maps a small, fixed set of named values to values stored in the database (usually integers). Your code reads `task.completed?` instead of `task.status == 2`.

<br/>

### Declaring an enum

Start with a column for it:

```ruby
class AddStatusToTasks < ActiveRecord::Migration[8.0]
  def change
    add_column :tasks, :status, :integer, default: 0, null: false
  end
end
```

Then declare the enum in the model. Prefer the Hash form, so the stored integers are explicit and don't change if someone reorders the list:

```ruby
class Task < ApplicationRecord
  enum :status, { pending: 0, in_progress: 1, completed: 2 }
end
```

<br/>

### Methods Rails generates

```ruby
task = Task.new

task.status          # => "pending"  (always the name, as a String)
task.pending?        # => true
task.in_progress!    # updates the column and saves the record
task.completed?      # => false

Task.pending         # scope: WHERE status = 0
Task.not_pending     # scope: WHERE status != 0
Task.statuses        # => {"pending"=>0, "in_progress"=>1, "completed"=>2}

Task.where(status: :completed)   # querying by name works too
```

<br/>

### Invalid values and validation

By default, assigning a value that isn't in the enum raises an `ArgumentError` (`'archived' is not a valid status`). That's often a 500 error when the value comes from a form or an API. Since Rails 7.1 you can turn it into a normal validation error instead:

```ruby
class Task < ApplicationRecord
  enum :status, { pending: 0, in_progress: 1, completed: 2 }, validate: true
end

task = Task.new(status: "archived")
task.valid?            # => false
task.errors[:status]   # => ["is not included in the list"]
```

More on this in [Rails enum validation](/rails/2024/02/03/rails-7.1-enum-validation.html).

<br/>

### Useful options

```ruby
enum :status, { pending: 0, completed: 1 }, prefix: true        # task.status_pending?
enum :status, { pending: 0, completed: 1 }, scopes: false       # no Task.pending scope
enum :status, { pending: 0, completed: 1 }, default: :pending   # default for new records
enum :status, { pending: 0, completed: 1 }, instance_methods: false  # no pending? / pending!
```

The [Rails enum syntax cheat sheet](/rails/2024/02/04/rails-7-enum-syntax.html) covers every option with old-to-new conversions.

<br/>

### Best practices

- **Use enums for small, fixed sets** like statuses, roles and visibility. If the list is edited by users or changes often, use a separate table.
- **Never reorder or remove values** in an array-style enum. The stored integers are positions, so reordering silently changes the meaning of existing rows. Use the Hash form.
- **Watch for name clashes.** Two enums on the same model can't share a value name (both would generate `active?`). Use `prefix:` or `suffix:`.
- **Use a string column** with a string-valued hash (`{ pending: "pending" }`) when raw database values need to be readable by other tools or reports.

<hr/>

### See It In Action

<div class="text-center">
  <iframe width="100%" height="600" src="https://www.youtube.com/embed/xLk_-dnQ6yU" title="Mastering Rails Enums in 60 Seconds: Clean Code Magic! ✨🚀 #RubyOnRails #CodingTips" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
</div>
<br/>

{% include elements/post_cta.html text="Upgrading to Rails 8 means fixing every old-style enum, and plenty more. I help SaaS teams plan and ship Rails upgrades on production apps." %}

<hr/>
