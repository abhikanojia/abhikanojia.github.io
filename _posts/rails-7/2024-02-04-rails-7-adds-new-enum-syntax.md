---
layout: post
title: "Why Rails 7 Changed the Enum Syntax (and What It Means in Rails 8)"
categories: "rails"
description: "What a Rails enum is, why Rails 7 moved to enum :name, values, and the timeline: new syntax in 7.0, old syntax deprecated in 7.2 and removed in Rails 8.0."
tags: rails rails-7 rails-8 enums
last_modified_at: 2026-10-27
keywords:
  - rails 7 new enum syntax
  - why did rails change enum syntax
  - whats new in rails 7 enum
  - rails enum history
---

<div class="quick-answer" markdown="1">
**Quick answer:** Rails 7.0 added `enum :status, [...]` because the old `enum status: [...]` packed enum definitions and options into the same Hash. Options needed awkward leading underscores (`_prefix:`) so Rails could tell them apart. The old form was deprecated in **Rails 7.2** and **removed in Rails 8.0**.
</div>

## What is enum in Rails?
An enum (enumeration) maps a set of names to values stored in the database, usually integers. Integers take less space than strings, and you still query and read values by name. It's a good fit for an attribute with a small, fixed set of values. For example, a post's status can be `draft` or `published`.

For a practical walkthrough of the methods Rails generates, see the [Rails 8 enums quick guide](/rails/2024/02/01/rails-enum-quick-guide.html).

## What changed

Rails 7 separates the enum name and its values:

```ruby
# Before
enum status: [ :draft, :published, :archived ], _prefix: true, _scopes: false

# After
enum :status, [ :draft, :published, :archived ], prefix: true, scopes: false
```

The same applies to the Hash syntax:

```ruby
enum :status, { draft: 0, published: 1, archived: 2 }, prefix: true, scopes: false
```

## Why did they change the enum syntax in Rails 7?

The discussion on [PR #41328](https://github.com/rails/rails/pull/41328){:target="_blank"} explains it:

>
> That is due to enum takes one hash argument only, which contains both
> enum definitions and reserved options.
> I propose new syntax for enum to avoid leading _ from reserved
> options, by allowing enum(attr_name, ..., **options) more Attribute
> API like syntax.

In other words, `enum` took a single Hash. Any key could be either an enum name (`status:`) or an option (`_prefix:`), so options needed an underscore to avoid clashing with column names. The positional form, `enum(attr_name, values, **options)`, works like the Attributes API (`attribute :name, :type, **options`) and removes the ambiguity.

## Is the old enum syntax deprecated?

Yes, as of Rails 7.2. This post originally said the old syntax was still valid, which was true up to Rails 7.1. The timeline is now:

- **Rails 7.0** — new positional syntax added; both forms work.
- **Rails 7.1** — new options `validate:` and `instance_methods:`, which only work with the new syntax.
- **Rails 7.2** — the keyword form prints `DEPRECATION WARNING: Defining enums with keyword arguments is deprecated and will be removed in Rails 8.0`.
- **Rails 8.0** — the keyword form is removed.

If you still have old-style enums, here's [how to fix the deprecation warning](/rails-7/2024/02/13/rails-7-deprecated-enum-with-keywords-args.html). For every option in the new syntax, see the [Rails enum syntax cheat sheet](/rails/2024/02/04/rails-7-enum-syntax.html).

{% include elements/post_cta.html text="Changes like this pile up when an app sits on an old Rails version. I help SaaS teams plan and ship Rails upgrades without stalling feature work." %}
