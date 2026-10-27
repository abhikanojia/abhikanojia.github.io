---
layout: post
title: "Rails 7.1, 7.2 and 8 Deprecation Warnings: What They Mean and How to Fix Them"
categories: "rails"
tags: rails rails-7 rails-8 rails-upgrade deprecations
description: "The Rails 7.1, 7.2 and 8.0 deprecation warnings you'll actually hit during an upgrade, what each one means, and the fix, in the order to tackle them."
keywords:
  - rails 7.1 deprecation warnings
  - rails 7.2 deprecation warnings
  - rails 8 deprecation warnings
  - rails deprecation warning fix
  - rails upgrade deprecations
---

<!--
DRAFT. Before publishing:
- Replace every [ADD EXAMPLE] with a real (anonymised) case from your own upgrades, or delete that line.
- Check every [VERIFY] against the Rails CHANGELOGs / release notes for the exact warning text.
- Rename the file with a date prefix in _posts/ when publishing, and add last_modified_at.
-->

<div class="quick-answer" markdown="1">
**Quick answer:** fix deprecations **one Rails minor version at a time**, before you bump to the next one. A warning in Rails 7.2 is usually a removal in Rails 8.0. Turn on `config.active_support.deprecation = :raise` in test, fix what fails, then upgrade. The ones that cause the most upgrade pain are enum keyword arguments, `serialize` with a positional coder, `Rails.application.secrets`, and old cache format versions.
</div>

On the staged upgrades I've done (for example [Rails 6.1 → 7.0 → 7.1 on a production app with limited tests](/work/legacy-rails-upgrade/)), one rule has saved the most time: **resolve each version's deprecations before moving to the next version**. Carrying deprecation noise forward makes it much harder to see when something genuinely new is broken.

One such thing happened in one of the project where the deprecation warning was ignored and later on during upgrade it blew on production.

## # First: make deprecations visible

By default, deprecations go to the log, where nobody reads them. During an upgrade, make them loud:

```ruby
# config/environments/test.rb
config.active_support.deprecation = :raise
```

```ruby
# config/environments/development.rb
config.active_support.deprecation = :log   # or :stderr while you're actively upgrading
```

In production, log them and count them, so you know what's still being hit by real traffic and not only by tests:

```ruby
# config/environments/production.rb
config.active_support.report_deprecations = true   # [VERIFY] default and behaviour on your Rails version
```

[ADD EXAMPLE: how you collect deprecations in production on client apps, e.g. grep logs, error tracker, ActiveSupport::Notifications subscriber.]

## # Rails 7.1 deprecations

### `serialize` with a positional coder

```ruby
# Deprecated in 7.1
serialize :preferences, JSON

# Fix
serialize :preferences, coder: JSON
# or, for a class:
serialize :settings, type: Hash
```

### `Rails.application.secrets`

`Rails.application.secrets` and `config/secrets.yml` are deprecated in 7.1 and removed in 7.2. Move values to `Rails.application.credentials` or environment variables before you go to 7.2.


### The `ActiveSupport::Deprecation` singleton

Calling `ActiveSupport::Deprecation.warn` directly is deprecated in 7.1. Libraries and app code should use their own deprecator:

```ruby
# Fix
MyApp.deprecator = ActiveSupport::Deprecation.new("2.0", "MyApp")
Rails.application.deprecators[:my_app] = MyApp.deprecator
```

This mostly shows up from **gems**. If the warning comes from a gem, upgrade the gem rather than patching around it.

### Old cache format version

```
[VERIFY exact text] Support for `config.active_support.cache_format_version = 6.1` has been deprecated...
```

Move to `cache_format_version = 7.0` (or `7.1`), but plan the switch. On a shared `Rails.cache`, changing the format means a cache miss spike across every server, which can turn into database load. See step 4 of the [Rails 6 to 7 upgrade checklist](/rails/2026/06/19/rails-6-to-7-upgrade-checklist.html).


## # Rails 7.2 deprecations

### Enums with keyword arguments

The most-hit one by far:

```
DEPRECATION WARNING: Defining enums with keyword arguments is deprecated and will be removed
in Rails 8.0. Positional arguments should be used instead:

enum :status, [:active, :inactive]
```

Fix: `enum status: [...]` → `enum :status, [...]`, and drop leading underscores from options. Full walkthrough: [fixing the enum keyword arguments deprecation](/rails-7/2024/02/13/rails-7-deprecated-enum-with-keywords-args.html).

### `ActiveRecord::Base.connection`

In 7.2, `ActiveRecord::Base.connection` is soft-deprecated in favour of `with_connection` (block form) or `lease_connection`. It only warns if you opt in with `ActiveRecord.permanent_connection_checkout = :deprecated`. [VERIFY] Worth doing in apps with lots of threads or background jobs, because holding a connection longer than needed is a common cause of pool exhaustion.

```ruby
# Prefer
ActiveRecord::Base.with_connection do |conn|
  conn.execute("SELECT 1")
end
```

## # Rails 8.0: deprecations become removals

Anything still deprecated on 7.2 is likely to be **an error on 8.0**, not a warning. Before bumping:

- No enum keyword arguments left (they raise on 8.0).
- No `Rails.application.secrets` usage.
- Test suite green with `deprecation = :raise` on 7.2.

New deprecations in 8.0 that are worth fixing early:

- `Benchmark.ms` (the Active Support extension) is deprecated. [VERIFY] Use `Process.clock_gettime(Process::CLOCK_MONOTONIC)` or your own helper.
- `to_time` timezone behaviour: `[VERIFY exact text] to_time will always preserve the receiver timezone rather than system local time in Rails 8.1...`. Set `config.active_support.to_time_preserves_timezone = :zone` once you've checked code that relies on the old behaviour.


## # A practical order of work

1. Get to the **latest patch** of your current Rails version.
2. Turn on `deprecation = :raise` in test and fix everything that fails.
3. Check production logs for deprecations tests don't cover.
4. Upgrade gems that emit deprecations.
5. Only then bump to the next minor version, and repeat.

{% include elements/post_cta.html text="Deprecation cleanup is where most upgrades stall. I help SaaS teams plan and ship Rails 7.2 and Rails 8 upgrades on production apps." %}

## # Related

- [8 steps before you upgrade Rails 6 to Rails 7](/rails/2026/06/19/rails-6-to-7-upgrade-checklist.html)
- [Rails enum syntax cheat sheet](/rails/2024/02/04/rails-7-enum-syntax.html)
- [Case study: safely upgrading a legacy Rails application](/work/legacy-rails-upgrade/)
