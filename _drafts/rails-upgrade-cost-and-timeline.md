---
layout: post
title: "How Much Does a Rails Upgrade Cost, and How Long Does It Take?"
categories: "rails"
tags: rails rails-upgrade legacy-rails production
description: "What actually drives the cost and timeline of a Rails upgrade: version gap, test coverage, gems, Ruby and deployment, plus how to scope one before you commit."
keywords:
  - rails upgrade cost
  - how long does a rails upgrade take
  - rails upgrade timeline
  - rails upgrade estimate
  - rails upgrade consultant cost
---

<!--
DRAFT. Before publishing:
- Every [ADD RANGE] needs YOUR real numbers (or delete the sentence). Don't publish placeholder ranges.
- Replace every [ADD EXAMPLE] with a real (anonymised) case, or delete that line.
- Rename the file with a date prefix in _posts/ when publishing.
-->

<div class="quick-answer" markdown="1">
**Quick answer:** the cost of a Rails upgrade depends far more on **test coverage, gem health and how many versions you're behind** than on the size of the codebase. A small, well-tested app one minor version behind can be [ADD RANGE]. A legacy app several major versions behind with limited tests is more like [ADD RANGE]. The reliable way to get a real number is a short upgrade audit first.
</div>

"How long will the upgrade take?" is the first question on almost every Rails upgrade call. The honest answer is "it depends", so this post is about *what* it depends on, and how to turn "it depends" into a number you can plan around.

## # What actually drives the cost

**1. How many versions you're crossing.** Each minor version (6.1 → 7.0 → 7.1 → 7.2 → 8.0) has its own deprecations, framework defaults and gem requirements. In the [staged 6.1 → 7.1 upgrade case study](/work/legacy-rails-upgrade/), the work was split into four independently shippable steps, and 6.1 → 7.0 was by far the largest.

**2. Test coverage.** This is the biggest multiplier. With a trusted test suite, most upgrade breakage shows up in CI. Without one, every step needs more manual checking, smaller deploys and closer production monitoring, which means more elapsed time.

**3. Gem health.** Abandoned gems, forks, and gems nobody remembers adding all need to be updated, replaced or removed. A Gemfile audit early on tells you how much of this there is.

**4. Ruby version.** If the target Rails version needs a newer Ruby, that's a separate upgrade with its own risks, and should be its own step.

**5. Framework defaults.** Reviewing and enabling `new_framework_defaults` one by one takes time, especially changes to cookies, cache format and serialization that affect running production traffic.

**6. Background jobs, caching and infrastructure.** Sidekiq/Redis versions, cache format changes, ActiveStorage/libvips and deploy scripts are where upgrades fail in production even when CI is green.

**7. Team availability.** An upgrade that has to wait for review slots and deploy windows takes longer in calendar time than in hours.

[ADD EXAMPLE: one upgrade where a single factor (e.g. one abandoned gem, or no tests on billing) dominated the timeline.]

## # Typical phases

| Phase | What happens | Typical effort |
|---|---|---|
| Audit | Versions, Gemfile, tests, defaults, jobs, deploy, written plan | [ADD RANGE] |
| Preparation | Latest patch release, deprecation cleanup, gem removals, test gaps on critical paths | [ADD RANGE] |
| Each version step | Bump, fix, review defaults, deploy, monitor | [ADD RANGE per step] |
| Defaults rollout | Enable remaining new defaults one at a time | [ADD RANGE] |

## # Why "just jump to the latest version" usually costs more

Jumping several versions in one branch looks faster on paper. In practice:

- When something breaks, you can't tell which version (or gem) caused it.
- The diff is too big for anyone to review properly.
- Rollback is all or nothing.
- The branch lives for weeks, and keeps diverging from main.

Smaller steps mean more deploys, but each one is a checkpoint you can stand on.

## # How to keep the cost down

- Stay on the **latest patch** release of your current version.
- Fix deprecation warnings as they appear (see Rails 7.1, 7.2 and 8 deprecation warnings [ADD LINK once the deprecations post is published]).
- Remove unused gems before the upgrade, not during it.
- Add tests around the riskiest paths (billing, auth, background jobs) before you start.
- Don't let a Rails app fall more than one major version behind. Catching up gets more expensive the longer you wait.

## # How engagements are usually priced

[ADD: describe how YOU price it. The services page currently says: one-time audit, focused project with fixed price or capped budget, or monthly fractional support. Add real ranges only if you're comfortable publishing them.]

[ADD EXAMPLE: anonymised example of an audit → project → outcome, with timeline.]

{% include elements/post_cta.html title="Planning a Rails upgrade?" text="Start with a Rails Upgrade Audit: a written plan with the real risks, a recommended sequence and an effort estimate for your app." label="Request a Rails Upgrade Audit" %}

## # Related

- [8 steps before you upgrade Rails 6 to Rails 7](/rails/2026/06/19/rails-6-to-7-upgrade-checklist.html)
- [Case study: safely upgrading a legacy Rails application](/work/legacy-rails-upgrade/)
- [Rails upgrade consultant](/rails-upgrade-consultant/)
