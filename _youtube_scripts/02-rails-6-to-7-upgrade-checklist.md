# Episode 1 — "The Rails 6 → Rails 7 Upgrade Checklist I Wish I Had"

**Target length:** 10–11 minutes (~1500 words of VO at ~150 wpm)
**Funnel destination:** `/rails-upgrade-consultant/` + (later) the Upgrade Pre-Flight Checklist PDF
**Voice:** Voice-neutral script. No identity claims in VO — works with any ElevenLabs voice. Recommended: clone your own voice (Instant Voice Cloning, 60-sec sample) or use an Indian English narrator. See README.
**Model / settings:** Eleven Multilingual v2, Stability 60%, Similarity 80%, Style 10%, Speaker Boost ON
**Music:** Track A for intro (0:00–0:30), Track B for body (0:30–10:00), Track C for outro (10:00–10:45)

---

## Title options

Pick one — A/B by gut, or test on different videos.

1. The Rails 6 → 7 Upgrade Checklist I Wish I Had
2. **8 Steps Before You Upgrade Rails 6 to Rails 7 (Production-Safe)** ← recommended
3. How I'd Plan a Rails 6 → 7 Upgrade for a Legacy SaaS App

## Thumbnail idea

Dark background, big yellow text **"6 → 7"**, subtitle **"Upgrade Checklist"**, small wine SparkRails logo in corner. No faces, no arrows. Calm and senior.

---

## [INTRO — 0:00–0:45]

**ON SCREEN:**

- 0:00–0:05 → Title card: **"The Rails 6 → 7 Upgrade Checklist I Wish I Had"** on dark background.
- 0:05–0:25 → Quick montage: a Gemfile open in editor, a long-running upgrade branch in git log, a CI run with red failures.
- 0:25–0:45 → Static text card with an 8-item table of contents on screen.

**VO:**

> A Rails 6 to Rails 7 upgrade isn't really one change. It's a stack of small, related changes that interact in ways that aren't obvious until they hit production.
>
> In this video, I'll walk through the eight things I check on every Rails 6 to 7 upgrade I take on as a consultant. In the order I actually do them. So you can use this as a checklist for your own app.
>
> This is for teams on Rails 6.0 or 6.1 who are looking at the jump to 7.0 or 7.1. If you're on 5.2, do the 6.1 upgrade first. If you're already on 7.0, you can still use this to plan your move to 7.1 or 7.2.
>
> Nothing here is fancy. It's just the order that keeps the production risk small.

---

## [STEP 1 — 0:45–1:30 — Know Your Starting Point]

**ON SCREEN:**

- Terminal with `cat .ruby-version`, `cat Gemfile | head -20`, `bundle info rails` output.
- Highlight Ruby version line and Rails version line in yellow boxes.

**VO:**

> Step one. Know exactly where you're starting from.
>
> I want three numbers up on a wall before I touch anything. The current Ruby version. The current Rails version. And the target Rails version you want to land on.
>
> If you're on Ruby two-point-seven, you'll need to move to at least Ruby three-point-zero before Rails 7. If you're on Ruby three-point-zero already, you can stay there for Rails 7.0 but Rails 7.1 wants three-point-one.
>
> Write these down. The whole upgrade plan branches off these three numbers.

---

## [STEP 2 — 1:30–2:30 — Upgrade Rails Before Anything Else]

**ON SCREEN:**

- Split-screen showing two git log timelines: one labeled "Risky: everything at once" with chaotic commits, one labeled "Safer: one variable at a time" with cleaner sequential commits.
- Then a terminal showing the Gemfile diff: `gem "rails", "~> 6.1.0"` → `gem "rails", "~> 7.0.0"`.

**VO:**

> Step two. Resist the temptation to upgrade everything at once.
>
> Rails first. Ruby separately. Gems separately. One variable at a time.
>
> If you change the Ruby version, the Rails version, and ten gems in the same pull request, you will spend more time bisecting failures than you will actually upgrading.
>
> So pin Ruby. Pin every gem you can. Upgrade Rails. Get green. Then move on to the next variable.
>
> If you're on Rails 6.0, go to 6.1 first, not straight to 7.0. The 6.0 to 6.1 hop is small. The 6.1 to 7.0 hop has the real surprises.

---

## [STEP 3 — 2:30–4:00 — Gem Audit Before Touching `Gemfile`]

**ON SCREEN:**

- Terminal running `bundle outdated` with output visible.
- Then switch to a notes file (markdown or Notion-style) showing three columns:
  - **Keep**
  - **Replace**
  - **Remove**
- Drag a few gem names into each column to demonstrate.

**VO:**

> Step three. Audit the Gemfile before you change a single version.
>
> Run `bundle outdated`. Look at every gem you depend on. Then put each gem into one of three buckets.
>
> Keep. Replace. Remove.
>
> Keep means the gem is maintained and works on Rails 7. Replace means there's a better-maintained alternative now. Remove means you're not really using it anymore.
>
> The gems I see causing the most upgrade pain are the ones nobody on the team remembers adding. An old paperclip. An old delayed job sitting next to Sidekiq. A devise extension that hasn't released since 2019.
>
> Clean these up before the upgrade, not during. Every gem you remove is one less thing that can fail later.

---

## [STEP 4 — 4:00–6:00 — `new_framework_defaults`: The Meaty Part]

**ON SCREEN:**

- Open `config/application.rb` showing `config.load_defaults 6.1`.
- Switch to `config/initializers/new_framework_defaults_7_0.rb` (the generated file).
- Scroll through it slowly. Pause on a few specific lines:
  - `Rails.application.config.action_view.button_to_generates_button_tag = true`
  - `Rails.application.config.action_dispatch.cookies_serializer = :json`
  - `Rails.application.config.active_support.cache_format_version = 7.0`
- Show each commented-out default with a wine-colored callout: **"What this changes"**.

**VO:**

> Step four. This is the part that breaks most upgrades.
>
> When you bump the Rails version, Rails doesn't automatically switch you to the new framework defaults. It generates a file at config slash initializers slash new framework defaults underscore seven underscore zero dot rb with every new default commented out. You're expected to enable them one at a time.
>
> Most teams either ignore this file completely and live with the old defaults forever, or flip them all on at once and break production.
>
> Neither is the answer. The answer is: enable them one at a time, in their own pull request, with a clear way to test or roll back each one.
>
> Some of these defaults are cosmetic. Some change cookie serialization. Some change cache format. The cache one in particular: if you have a Rails cache shared across deploys, switching cache format version mid-deploy means cache misses across your fleet, which can cascade into database load.
>
> So before you flip cache format version to seven point zero, plan how you'll warm the cache or accept the brief miss spike.
>
> I'll do a full video just on this initializer file, because every line in it has a story. For now: don't delete this file, don't flip everything at once, and don't ignore it.

---

## [STEP 5 — 6:00–6:45 — ActiveStorage and Image Processing]

**ON SCREEN:**

- Gemfile snippet showing `gem "image_processing"`.
- Terminal showing `which libvips` and `which convert`.
- Brief diff showing migration from MiniMagick to Vips.

**VO:**

> Step five. ActiveStorage and image processing.
>
> Rails 7 prefers Vips over MiniMagick for image variants. Vips is faster and uses less memory. If your production environment has ImageMagick installed but not libvips, your image variants will silently break.
>
> Check your production Dockerfile or provisioning script. If libvips isn't there, add it before the upgrade ships. Not after.
>
> If you're heavy on image processing, also benchmark Vips on your actual workload before you decide whether the switch is worth it. For small variants the difference isn't huge. For lots of large images, it's significant.

---

## [STEP 6 — 6:45–7:45 — Sidekiq and Redis Compatibility]

**ON SCREEN:**

- `Gemfile` showing `gem "sidekiq", "~> 6.5"`.
- Sidekiq web UI showing healthy queue.
- Terminal running `redis-cli INFO server` showing Redis version.

**VO:**

> Step six. Sidekiq and Redis.
>
> Rails 7 doesn't break Sidekiq. But the version of Sidekiq you're running might be old enough that it doesn't play nicely with the Redis client Rails 7 ships with.
>
> Check three things. Your Sidekiq major version. Your Redis server version. And whether you have any monkey-patches on Sidekiq middleware. The monkey-patches are where most surprises live.
>
> If you're on Sidekiq six point five or later, you're fine. If you're on five point something, plan a separate Sidekiq upgrade either before or after the Rails upgrade. Not at the same time.
>
> Same principle as step two. One variable at a time.

---

## [STEP 7 — 7:45–9:00 — Deployment and Rollback Plan]

**ON SCREEN:**

- Whiteboard-style text on screen, three columns:
  - **Before deploy**
  - **During deploy**
  - **Rollback plan**
- Fill in items under each.
- Then a fake error tracker (Sentry / Honeybadger style) showing a clean dashboard.

**VO:**

> Step seven. Before you ship, plan the rollback.
>
> Write down three things. What's the deploy command. What's the rollback command. What's the first signal you'd see if something went wrong.
>
> The first signal is usually error rate in your error tracker, or latency in your APM. Have the dashboards open during the deploy. Not in a tab somewhere. Open.
>
> If you have a long-running migration in the upgrade, make sure it's reversible. If it's not reversible, ship the migration in a separate deploy from the upgrade itself. So you can roll back the code without rolling back the schema.
>
> This is the boring part of upgrades. It's also the part that decides whether the upgrade is a Friday night incident or a Tuesday morning non-event.

---

## [STEP 8 — 9:00–10:00 — Test Coverage on the Critical Paths]

**ON SCREEN:**

- Code coverage report (SimpleCov style) showing 40% coverage.
- Then zoom in on three highlighted files: `app/controllers/checkout_controller.rb`, `app/models/subscription.rb`, `app/jobs/billing_job.rb`.

**VO:**

> Step eight. If your test coverage is thin, don't try to fix it everywhere. Fix it where it matters.
>
> Before any Rails upgrade, the test files I want to be solid are the ones around money, auth, and background jobs. Checkout, billing, subscriptions, login, password reset, and the jobs that handle paid work.
>
> If a test is missing on the user profile page, that's fine. If a test is missing on the checkout flow, write it before you change Rails versions. Not after.

---

## [OUTRO + CTA — 10:00–10:45]

**ON SCREEN:**

- Recap card listing the eight steps in a clean numbered list.
- Then SparkRails logo with **sparkrails.com/rails-upgrade-consultant** below.
- Final card: **"Subscribe for the next video — `new_framework_defaults_7_0` line by line."**

**VO:**

> Those are the eight steps. Starting point. Rails before everything else. Gem audit. Framework defaults one at a time. ActiveStorage and Vips. Sidekiq and Redis. Deployment and rollback. Tests where they matter.
>
> If you want this as a one-page checklist you can put in front of your team, the link is in the description.
>
> If your team is stuck mid-upgrade, or you want a senior pair of eyes on the upgrade plan before you ship, I do that as a service. The booking link is in the description too.
>
> Next video walks through every commented-out line in the new framework defaults seven zero file and what it actually changes. Subscribe so you catch it.

---

## End-screen cards (last 5 sec, no VO)

- Subscribe button + a card linking to `/rails-upgrade-consultant/`.

---

## YouTube description template

```
The Rails 6 to Rails 7 upgrade checklist I use on every paid Rails upgrade engagement at SparkRails. Eight steps, in the order they actually run.

📄 Get the one-page checklist (free): [link to checklist landing page once built]

🛠 Book a Rails upgrade consultation: https://sparkrails.com/rails-upgrade-consultant/?utm_source=youtube&utm_medium=video&utm_campaign=upgrade_checklist

💼 All Rails consulting services: https://sparkrails.com/services/?utm_source=youtube&utm_medium=video&utm_campaign=upgrade_checklist

Chapters:
0:00 Intro
0:45 Step 1 — Know your starting point
1:30 Step 2 — Upgrade Rails before anything else
2:30 Step 3 — Gem audit
4:00 Step 4 — new_framework_defaults
6:00 Step 5 — ActiveStorage and Vips
6:45 Step 6 — Sidekiq and Redis
7:45 Step 7 — Deployment and rollback
9:00 Step 8 — Test coverage on critical paths
10:00 Recap

Run by Abhishek Kanojia, senior Rails consultant at SparkRails.
Available for US, UK, EU, Canada, Singapore, and remote-first teams.
```

---

## Pinned comment (paste after upload)

> Mid-upgrade and stuck? I do this as a service.
> Book a Rails upgrade consultation → https://sparkrails.com/rails-upgrade-consultant/

---

## ElevenLabs pronunciation reminders (script-specific)

- Sidekiq → "side kick"
- Vips → spell lowercase, will read as "vips"
- MiniMagick → "mini magick"
- `new_framework_defaults` → script reads "new framework defaults" (no underscores)
- `cache_format_version` → script reads "cache format version"
- `bundle outdated` → reads naturally
- Rails 7.0, 7.1 → "Rails seven point zero", "Rails seven point one"
- libvips → "lib vips"
- ImageMagick → "image magick"

---

## Companion blog post

After the video ships, publish the same content as a blog post on sparkrails.com (recommended slug: `/blog/rails-6-to-7-upgrade-checklist/`). Embed the YouTube video at the top. This is the SEO compound — the post ranks on Google for upgrade-related searches, the video shows up in YouTube search, and both link to the consulting page.
