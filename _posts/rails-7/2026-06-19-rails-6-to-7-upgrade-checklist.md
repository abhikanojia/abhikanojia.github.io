---
layout: post
title: "8 Steps Before You Upgrade Rails 6 to Rails 7 (Production-Safe)"
categories: "rails"
description: "A production-safe Rails 6 to Rails 7 upgrade checklist. Eight steps covering Ruby version, gem audit, new_framework_defaults, ActiveStorage and Vips, Sidekiq and Redis, deployment, rollback, and test coverage — in the order that keeps risk low."
tags: rails rails-7 rails-upgrade legacy-rails production
keywords:
  - Rails 6 to Rails 7 upgrade
  - Rails 7 upgrade checklist
  - new_framework_defaults Rails 7
  - Ruby on Rails upgrade plan
  - legacy Rails upgrade
  - Rails upgrade consultant
  - Sidekiq Rails 7 compatibility
  - ActiveStorage Vips Rails 7
  - Rails production upgrade
  - Rails 6.1 to 7.0
---

A Rails 6 to Rails 7 upgrade isn't really one change. It's a stack of small, related changes that interact in ways that aren't obvious until they hit production. Old gems, custom patches, framework default flips, background-job assumptions, cache format shifts, and deployment scripts all push back at the same time.

What follows is the eight-step checklist I run on every Rails 6 → 7 upgrade I take on as a consultant — in the order I actually do them. Use it as a checklist for your own app.

This is written for teams on **Rails 6.0 or 6.1** planning the jump to **7.0 or 7.1**. If you're on 5.2, do the 6.1 upgrade first. If you're already on 7.0, the same steps apply to 7.0 → 7.1 and 7.1 → 7.2.

Nothing here is fancy. It's just the order that keeps production risk small.

## Step 1 — Know your starting point

Before touching anything, get three numbers up on a wall:

1. **Current Ruby version** — `cat .ruby-version`
2. **Current Rails version** — `bundle info rails | head -1`
3. **Target Rails version** — the one you want to land on

These three numbers decide the whole upgrade plan.

- If you're on **Ruby 2.7**, you'll need to move to at least Ruby 3.0 before Rails 7.
- If you're on **Ruby 3.0**, you can stay there for Rails 7.0, but Rails 7.1 wants Ruby 3.1+.
- If you're on **Ruby 3.1+**, you're fine through Rails 7.2.

Write these down. The rest of the upgrade branches off them.

## Step 2 — Upgrade Rails before anything else

Resist the temptation to upgrade everything in one branch.

**Rails first. Ruby separately. Gems separately. One variable at a time.**

If you change the Ruby version, the Rails version, and ten gems in the same pull request, you'll spend more time bisecting failures than actually upgrading. Pin Ruby. Pin every gem you can. Upgrade Rails. Get green. Then move on to the next variable.

```ruby
# Gemfile — change one thing at a time
gem "rails", "~> 7.0.0"  # not "~> 7.0", not "~> 7"
```

And **don't skip minor versions**. If you're on Rails 6.0, go to 6.1 first, not straight to 7.0. The 6.0 → 6.1 hop is small. The 6.1 → 7.0 hop has the real surprises. Skipping 6.1 means absorbing both at once.

## Step 3 — Audit your Gemfile before you change a version

Before you bump a single version, audit every gem you depend on.

```bash
bundle outdated
```

Then put each gem into one of three buckets:

- **Keep** — maintained and works on Rails 7.
- **Replace** — there's a better-maintained alternative now.
- **Remove** — you're not really using it anymore.

The gems I see causing the most upgrade pain are the ones nobody on the team remembers adding. An old `paperclip` sitting next to `active_storage`. An old `delayed_job` sitting next to `sidekiq`. A `devise` extension that hasn't released since 2019. A gem that was added for one feature that got removed two years ago.

**Clean these up before the upgrade, not during.** Every gem you remove is one less thing that can fail later. This step alone often saves a week of debugging mid-upgrade.

## Step 4 — `new_framework_defaults`: the meaty part

This is the part that breaks most upgrades.

When you bump the Rails version, Rails doesn't automatically switch you to the new framework defaults. It generates a file:

```ruby
# config/initializers/new_framework_defaults_7_0.rb
# Rails.application.config.action_view.button_to_generates_button_tag = true
# Rails.application.config.action_dispatch.cookies_serializer = :json
# Rails.application.config.active_support.cache_format_version = 7.0
# ... and more
```

Every new default is commented out. You're expected to enable them one at a time.

Most teams do one of two things, both wrong:

1. Ignore the file and live with the old defaults forever (you carry old behavior into a new Rails version, then forget why).
2. Flip them all on at once and break production (because at least one of these defaults touches something you didn't realise depended on the old behavior).

**The answer is: enable them one at a time, each in its own pull request, with a clear way to test or roll back each one.**

Some defaults are cosmetic. Some change cookie serialization. Some change cache format. The cache one is the most expensive to get wrong:

```ruby
Rails.application.config.active_support.cache_format_version = 7.0
```

If you have a shared `Rails.cache` across your fleet, switching `cache_format_version` mid-deploy means cache misses across every server, which can cascade into database load. Before you flip this one, plan how you'll warm the cache or accept the brief miss spike.

**Don't delete this file. Don't flip everything at once. Don't ignore it.**

## Step 5 — ActiveStorage and image processing

Rails 7 prefers **Vips** over **MiniMagick** for image variants. Vips is faster and uses less memory. The trap: if your production environment has ImageMagick installed but not libvips, your image variants will silently break.

Check your production environment for libvips:

```bash
which libvips
# /usr/bin/libvips — good
# (no output) — your variants will break after the upgrade
```

If libvips isn't there, add it to your production Dockerfile or provisioning script **before** the upgrade ships. Not after.

```dockerfile
# Dockerfile
RUN apt-get update && apt-get install -y libvips
```

If you're heavy on image processing, also benchmark Vips on your actual workload before you decide whether the switch is worth it. For small variants the difference isn't huge. For lots of large images, it's significant.

## Step 6 — Sidekiq and Redis compatibility

Rails 7 doesn't break Sidekiq. But the version of Sidekiq you're running might be old enough that it doesn't play nicely with the Redis client Rails 7 ships with.

Check three things:

1. **Your Sidekiq major version** — `bundle info sidekiq`
2. **Your Redis server version** — `redis-cli INFO server | grep redis_version`
3. **Any monkey-patches on Sidekiq middleware** — `grep -r "Sidekiq::Middleware" config/ lib/`

If you're on **Sidekiq 6.5 or later**, you're fine. If you're on 5.x, plan a separate Sidekiq upgrade — either before or after the Rails upgrade, not at the same time. Same principle as Step 2: one variable at a time.

The monkey-patches are where most surprises live. If somebody on the team patched Sidekiq middleware two years ago to add logging or job filtering, that patch is the first thing to break on an upgrade.

## Step 7 — Deployment and rollback plan

Before you ship, plan the rollback.

Write down three things:

| Question | Answer |
|----------|--------|
| What's the deploy command? | `e.g. cap production deploy` |
| What's the rollback command? | `e.g. cap production deploy:rollback` |
| What's the first signal you'd see if something went wrong? | usually error rate in your tracker, or p95 latency in your APM |

The first signal is usually error rate in your error tracker or latency in your APM. **Have those dashboards open during the deploy. Not in a tab somewhere — open.**

If you have a long-running migration in the upgrade, make sure it's reversible. If it's not reversible, ship the migration in a **separate deploy** from the upgrade itself. So you can roll back the code without rolling back the schema.

This is the boring part of upgrades. It's also the part that decides whether the upgrade is a Friday-night incident or a Tuesday-morning non-event.

## Step 8 — Test coverage on the critical paths

If your test coverage is thin, don't try to fix it everywhere. Fix it where it matters.

Before any Rails upgrade, the test files I want solid are the ones around **money, auth, and background jobs**:

- Checkout flow
- Billing and subscriptions
- Login, signup, password reset
- The jobs that handle paid work (invoicing, billing webhooks, payouts)

If a test is missing on the user profile page, that's fine. If a test is missing on the checkout flow, write it **before** you change Rails versions, not after.

The point isn't to chase a coverage number. It's to make sure that when the upgrade is on staging, you can press one button and trust the result on the paths that earn the business money.

## The eight steps, in order

1. **Know your starting point** — Ruby, current Rails, target Rails.
2. **Upgrade Rails before anything else** — one variable at a time.
3. **Audit the Gemfile** — keep, replace, remove.
4. **`new_framework_defaults`** — enable one at a time, each in its own PR.
5. **ActiveStorage and Vips** — install libvips in production before shipping.
6. **Sidekiq and Redis** — Sidekiq 6.5+, check Redis version, audit monkey-patches.
7. **Deployment and rollback plan** — deploy command, rollback command, first signal.
8. **Tests where they matter** — money, auth, background jobs.

Most stalled Rails upgrades I see aren't stalled because Rails 7 is hard. They're stalled because the team tried to do all eight steps in the same branch. Done in order, in separate pull requests, the upgrade becomes a sequence of small, boring changes — which is exactly what a production upgrade should be.

---

## Need a senior pair of eyes on your Rails upgrade?

If your team is mid-upgrade and stuck, or you want a second pair of senior eyes on the upgrade plan before you ship it, that's what I do at SparkRails. I help SaaS teams plan and execute Rails 6.x and 7.x upgrades with less production risk.

[**Book a Rails upgrade consultation →**](/rails-upgrade-consultant/)

Available for US, UK, EU, Canada, Singapore, and remote-first teams.
