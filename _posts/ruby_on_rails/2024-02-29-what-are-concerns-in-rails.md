---
layout: post
title: "What Are Concerns in Rails? ActiveSupport::Concern Explained"
categories: "rails ruby"
tags: rails ruby
description: "A Rails concern is a module that extends ActiveSupport::Concern to share scopes, validations and methods across models. Examples, plus when not to use one."
last_modified_at: 2026-10-27
keywords:
  - what are concerns in rails
  - rails concerns
  - activesupport concern
  - rails concern vs module
  - included do class_methods rails
---

<div class="quick-answer" markdown="1">
**Quick answer:** a concern is a plain Ruby module that `extend`s `ActiveSupport::Concern`. On top of a normal module it gives you an `included do ... end` block that runs **inside the class** (for scopes, validations, associations and callbacks), a `class_methods do ... end` block for class methods, and automatic handling of dependencies between modules. Rails apps keep them in `app/models/concerns` and `app/controllers/concerns`.
</div>

## # Isn't that Concerning?
Where does the word `Concern` come from? From `ActiveSupport::Concern`, defined in `active_support/concern`. It lets you mix shared behaviour into any class that includes it.

But that's what a module does, no? Yes, but a concern does a bit more.

## # Module vs Concern

A **module** comes from Ruby itself. It is a collection of methods and constants. Instance methods defined in a module become instance methods of the class that includes it. For example:

```ruby
module Sparkrails
  def give_me_best_software
    "Best Software"
  end
end

class YourDreamProject
  include Sparkrails
end

> your_enterprise = YourDreamProject.new
> your_enterprise.give_me_best_software

=> "Best Software"
```

The `Sparkrails` module is included in `YourDreamProject`, so its methods are available as instance methods on `YourDreamProject`.

The limitation shows up when you want to call class-level macros like `scope`, `validates` or `has_many`. Those must run in the context of the class, and a plain module body runs in the context of the module. Before concerns, you had to write a `self.included(base)` hook and call `base.class_eval` yourself.

A **concern** is just a Ruby module that extends `ActiveSupport::Concern`, which hides that boilerplate:

```ruby
module Sparkrails
  extend ActiveSupport::Concern

  included do
    # Runs inside the including class: scopes, validations, associations, callbacks
  end

  class_methods do
    # Class methods for the including class
  end

  # Plain method definitions become instance methods, like in any module
  def give_me_best_software
    "Best Software"
  end
end

class YourEnterprise
  include Sparkrails
end
```

1. **`included`** — this block is evaluated in the context of the host class when the concern is included, so `scope`, `validates`, `belongs_to`, `before_save` and similar macros work here.
2. **`class_methods`** — methods defined in this block become class methods on the host class.
3. **`prepended`** — like `included`, but runs when the concern is `prepend`ed instead of `include`d (available since Rails 6.1). `prepend` puts the module ahead of the class in the method lookup chain.

Concerns also resolve dependencies: if concern `B` depends on concern `A`, including `B` in a class includes `A` correctly, so the host class doesn't need to know about `A`.

## # A practical example

The main reason to use a concern is to organise behaviour that several models share. For example:

```ruby
module Archivable
  extend ActiveSupport::Concern

  included do
    scope :archived, -> { where(status: "archived") }
  end

  class_methods do
    def archive_all
      update_all(status: "archived")
    end
  end

  def mark_archived
    update!(status: "archived")
  end
end

class Photo < ApplicationRecord
  include Archivable
end

class Comment < ApplicationRecord
  include Archivable
end

Comment.last.mark_archived
Photo.archived        # => #<ActiveRecord::Relation [#<Photo id: ...>]>
Comment.archive_all
```

## # When not to use a concern

Concerns are easy to overuse. A common pattern in older codebases is a model including many concerns, where each one quietly depends on columns and callbacks defined in the others. A few rules of thumb:

- Use a concern when the behaviour is genuinely **shared** by more than one class and makes sense on its own (`Archivable`, `Searchable`, `Sluggable`).
- If a concern is only included in one model, it's mostly just moving code into another file. A plain method, or a separate service or query object, is usually easier to follow.
- Keep callbacks in concerns to a minimum. Hidden `before_save` hooks spread across several files are a common source of hard-to-trace production bugs.

{% include elements/post_cta.html title="Working in a tangled legacy Rails codebase?" text="I help SaaS teams untangle and stabilise older Rails apps — concerns, callbacks and all — without a rewrite." url="/legacy-rails-maintenance/" label="See legacy Rails maintenance" service="legacy_maintenance" %}

## # Related

- [Rails ActiveRecord cookbook](/rails-active-record-cookbook/)
- [The `after_save_commit` callback in Rails](/rails/2019/06/30/after-save-commit-in-rails-6.html)
- [Ruby: `descendants` vs `subclasses`](/rails/2022/01/21/descendants-vs-subsclasses-ruby.html)
