---
layout: post
title: "Fix \"Defining enums with keyword arguments is deprecated\" in Rails"
categories: "rails-7"
tags: rails rails-7 rails-8 enums rails-upgrade
description: "Rails 7.2 warns that defining enums with keyword arguments is deprecated, and Rails 8 removes it. Switch to enum :status, [...] and drop the _prefix options."
last_modified_at: 2026-10-27
keywords:
  - defining enums with keyword arguments is deprecated
  - rails enum deprecation warning
  - rails 7.2 enum deprecated
  - rails 8 enum syntax
  - positional arguments should be used instead enum
---

<div class="quick-answer" markdown="1">
**Quick answer:** change `enum status: [...]` to `enum :status, [...]`, pass the enum name as the first positional argument, and drop the leading underscore from options (`_prefix:` → `prefix:`). The keyword form is deprecated in **Rails 7.2** and **removed in Rails 8.0**, so fix it before you upgrade.
</div>

If you upgraded to Rails 7.2 and your logs or test output started showing this, you are in the right place:

```
DEPRECATION WARNING: Defining enums with keyword arguments is deprecated and will be removed
in Rails 8.0. Positional arguments should be used instead:

enum :status, [:active, :inactive]
```

The warning even prints the replacement for you. The new positional syntax has been available since Rails 7.0, so you can make this change on Rails 7.0, 7.1 or 7.2 and it will keep working on Rails 8. If you want the background on why the syntax changed, see [why Rails changed the enum syntax](/rails/2024/02/04/rails-7-adds-new-enum-syntax.html).

## # Rails version timeline

| Rails version | `enum status: [...]` (keyword) | `enum :status, [...]` (positional) |
|---|---|---|
| 6.1 and earlier | Works | Not available |
| 7.0 / 7.1 | Works | Works |
| 7.2 | Works, prints the deprecation warning | Works |
| 8.0 and later | Removed — raises an `ArgumentError` | Works |

## # Before: keyword arguments (deprecated)

```ruby
class User < ApplicationRecord
  # Deprecated in Rails 7.2, removed in Rails 8.0
  enum status: [:active, :inactive]
end
```

## # After: positional arguments

```ruby
class User < ApplicationRecord
  enum :status, [:active, :inactive]
end
```

The same works with a Hash, which is the safer choice because the integer mapping is explicit and won't shift if someone reorders the array:

```ruby
class User < ApplicationRecord
  enum :status, { active: 0, inactive: 1 }
end
```

## # Converting enum options

With the old syntax, options needed a leading underscore so Rails could tell them apart from enum names. With the positional syntax the underscore goes away:

```ruby
# Before (deprecated)
enum status: [:active, :inactive], _prefix: true, _scopes: false, _default: :active

# After
enum :status, [:active, :inactive], prefix: true, scopes: false, default: :active
```

| Old option | New option |
|---|---|
| `_prefix:` | `prefix:` |
| `_suffix:` | `suffix:` |
| `_scopes:` | `scopes:` |
| `_default:` | `default:` |
| `_instance_methods:` | `instance_methods:` |

## # Multiple enums on one line

The keyword syntax allowed several enums in a single call. Split them into one `enum` call each:

```ruby
# Before (deprecated)
enum status: [:active, :inactive], role: [:member, :admin]

# After
enum :status, [:active, :inactive]
enum :role, [:member, :admin]
```

## # Finding every old-style enum in a codebase

On a large app it's easy to miss one. A quick search catches most of them:

```bash
grep -rnE "^\s*enum\s+[a-z_]+:" app/ lib/
```

Then run your test suite with deprecations raised as errors, so anything the search missed fails loudly instead of only logging a warning:

```ruby
# config/environments/test.rb
config.active_support.deprecation = :raise
```

Refer to this [commit](https://github.com/rails/rails/commit/8c5425197c7969ff50f675e9792fce1998fb9bc2){:target="_blank"} for the original deprecation change.

{% include elements/post_cta.html text="Enum deprecations are usually the easy part. I help SaaS teams plan and ship Rails 7.2 and Rails 8 upgrades on production apps." %}

## # Related

- [Rails enum syntax cheat sheet: every option, old vs new](/rails/2024/02/04/rails-7-enum-syntax.html)
- [Rails enum validation with `validate: true`](/rails/2024/02/03/rails-7.1-enum-validation.html)
- [8 steps before you upgrade Rails 6 to Rails 7](/rails/2026/06/19/rails-6-to-7-upgrade-checklist.html)
