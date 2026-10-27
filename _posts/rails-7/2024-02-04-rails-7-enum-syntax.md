---
layout: post
title: "Rails Enum Syntax Cheat Sheet: Old vs New (Rails 7, 7.2, 8)"
categories: "rails"
description: "Every Rails enum option in the positional syntax (prefix, suffix, scopes, default, validate, instance_methods) with old-to-new conversions for Rails 8."
tags: rails rails-7 rails-8 enums
last_modified_at: 2026-10-27
keywords:
  - rails 7 enum syntax
  - rails enum syntax
  - rails enum options
  - rails enum prefix suffix
  - rails enum new syntax
---

<div class="quick-answer" markdown="1">
**Quick answer:** the current enum syntax is `enum :name, values, **options`, for example `enum :status, { draft: 0, published: 1 }, prefix: true`. It was added in Rails 7.0. The old `enum status: [...], _prefix: true` form was deprecated in Rails 7.2 and removed in Rails 8.0. Options lose their leading underscore.
</div>

This page is a reference. If you're here because of the deprecation warning, go to [fixing "Defining enums with keyword arguments is deprecated"](/rails-7/2024/02/13/rails-7-deprecated-enum-with-keywords-args.html). If you want the background, read [why Rails changed the enum syntax](/rails/2024/02/04/rails-7-adds-new-enum-syntax.html).

## Old vs. New at a glance

```ruby
# Old (deprecated in 7.2, removed in 8.0)
enum status: [ :draft, :published, :archived ], _prefix: true, _scopes: false

# New (Rails 7.0+)
enum :status, [ :draft, :published, :archived ], prefix: true, scopes: false

# New, with explicit values (recommended)
enum :status, { draft: 0, published: 1, archived: 2 }, prefix: true, scopes: false
```

## Every option

| Option | Since | Example | Effect |
|---|---|---|---|
| `prefix:` | 7.0 (as `_prefix` earlier) | `prefix: true` | `post.status_draft?` instead of `post.draft?` |
| `prefix:` with a name | 7.0 | `prefix: :post` | `post.post_draft?` |
| `suffix:` | 7.0 (as `_suffix` earlier) | `suffix: true` | `post.draft_status?` |
| `scopes:` | 7.0 (as `_scopes` earlier) | `scopes: false` | No `Post.draft` / `Post.not_draft` scopes |
| `default:` | 7.0 (as `_default` earlier) | `default: :draft` | Default value for new records |
| `validate:` | 7.1 | `validate: true` | Invalid values add a validation error instead of raising `ArgumentError` |
| `validate:` with options | 7.1 | `validate: { allow_nil: true }` | Validation that also allows `nil` |
| `instance_methods:` | 7.1 | `instance_methods: false` | No `draft?` / `draft!` methods |

See [Rails enum validation](/rails/2024/02/03/rails-7.1-enum-validation.html) and [controlling enum instance methods](/rails/2024/02/05/rails-7-1-control-activerecord-enums-instance-methods.html) for the Rails 7.1 options in detail.

## Combining options

```ruby
class Post < ApplicationRecord
  enum :status, { draft: 0, published: 1, archived: 2 },
       prefix: true,
       default: :draft,
       validate: true
end

post = Post.new
post.status_draft?   # => true
Post.status_published  # scope
```

## String-backed enums

Enums don't have to store integers. With a string column, map names to strings:

```ruby
enum :status, { draft: "draft", published: "published" }
```

## Watch out: new options don't work with the old syntax

<mark>The Rails 7.1 options like `validate:` only work with the new syntax.</mark> With the old keyword syntax they are treated as another enum definition, and you get a confusing error:

```ruby
class Post < ApplicationRecord
  enum status: [:draft, :published], validate: true
end

post = Post.new
#
# => `raise_conflict_error`: You tried to define an enum named "status" on the model "Post", but this will generate a instance method "draft?", which is already defined by another enum. (ArgumentError)
```

Switch to `enum :status, [:draft, :published], validate: true` and it works.

{% include elements/post_cta.html text="Enum syntax is one of dozens of small changes between Rails 6, 7 and 8. I help SaaS teams upgrade production Rails apps safely." %}

## Related

- [Rails 8 enums: a quick guide to methods and scopes](/rails/2024/02/01/rails-enum-quick-guide.html)
- [8 steps before you upgrade Rails 6 to Rails 7](/rails/2026/06/19/rails-6-to-7-upgrade-checklist.html)
