---
layout: post
title: "7 Warning Signs Your Rails SaaS Billing System Needs an Audit"
categories: "rails ruby"
tags: rails ruby saas billing stripe
description: "Practical warning signs that a Ruby on Rails SaaS billing system is drifting into inconsistency, written from the perspective of a Rails consultant who has audited mature production applications."
keywords:
  - Ruby on Rails SaaS, Rails billing system, Stripe subscriptions
  - Rails Stripe integration, Rails subscription management, Stripe webhooks
  - SaaS billing, Rails consultant, Rails architecture audit
---

Most Ruby on Rails SaaS applications begin with a very small billing surface. A single plan. One `subscriptions` table. A webhook endpoint that updates a status column. For the first year or two, that is usually enough.

Then the product grows. A yearly plan is added. Trials become configurable. A grandfathered plan appears for early customers. Coupons arrive. Then seat-based pricing. Then a self-serve upgrade flow, and later a sales-assisted one. Somewhere along the way the team ships a second product and reuses parts of the billing model. Migrations accumulate. People leave. New engineers inherit the code and quietly work around the parts they don't understand.

Billing is unusual because it sits at the intersection of revenue, customer access, support workload and trust. Small inconsistencies here do not stay small. A customer who is charged but locked out will email support. A customer who is not charged but keeps access will not. Both cases are expensive, but only one of them is visible, which is part of why these systems are so easy to under-invest in.

The following are seven patterns I frequently see when I start reviewing a Ruby on Rails SaaS billing system. Any one of them can exist in isolation without much harm. When several appear together, it is usually a sign that the underlying model has drifted far enough from the business rules that it needs a deliberate review rather than another patch. The pattern I keep coming back to is that these systems rarely break because of a single bad decision; they break because dozens of individually reasonable decisions, made over several years and by different people, no longer add up to a coherent architecture.

<br/>

## # Warning Sign #1: Stripe and the application database no longer agree

The most common symptom I see in mature Rails applications is a growing gap between what Stripe believes about a customer and what the Rails application believes about that same customer. A subscription is `active` in Stripe but `canceled` locally. A customer's plan changed in Stripe two weeks ago but the local `plan_id` still points at the old one. A payment failed, but the user's account is still marked as in good standing.

This usually starts innocently. In the early version of the Rails Stripe integration, a single webhook handler updates a few columns and everything stays consistent. Over time, new flows are added that update the same columns from different code paths: a self-serve upgrade controller, an admin action, a background job that reconciles trials, a Rake task written during an incident, sometimes a migration that back-fills a column and quietly overrides a live value. Each path is reasonable in isolation. Together they overwrite each other in ways that are hard to reason about because no single component owns the transition.

The business risk is quiet revenue leakage and quiet access errors. Neither shows up in a dashboard until a customer complains, and by the time teams notice this problem the drift has usually been present for months and affects far more accounts than the one that triggered the ticket.

<br/>

## # Warning Sign #2: Subscription logic is scattered across the codebase

If you search a mature Rails application for the word `subscription`, you often find it in a surprising number of places: models, controllers, service objects, view helpers, background jobs, mailers, feature flags, admin panels, and sometimes in JavaScript. Each of these has its own small opinion about what a subscription is and when it counts.

In production systems this typically appears after several years of feature development. A helper method might treat a trialing user as subscribed. A controller might treat them as not subscribed. A background job might treat them as subscribed only if their trial has more than three days left. A feature flag added during a launch might quietly override all of the above for a specific plan. None of these are wrong on their own. They are just different definitions of the same concept, applied inconsistently by different developers who each solved a real problem at the time.

This scatters the Rails subscription management rules across the application, which means changing a rule requires editing many files, and forgetting one of them is how regressions happen. When the product team asks a seemingly small question — "can we let paused subscribers access the export feature?" — the honest answer becomes "we don't fully know where that decision is made."

<br/>

## # Warning Sign #3: There are multiple sources of truth for "is this customer paying?"

Closely related to the previous point, but worth calling out separately. In a healthy system there is one canonical answer to "does this account currently have access to paid features?" In a system that needs an audit, there are usually three or four, and they occasionally disagree.

One pattern I frequently see: a boolean `active` on `users`, a `status` on `subscriptions`, a `plan_id` on `accounts`, a Redis flag written by the webhook handler, and — in older codebases — a legacy `paid_until` column that predates the Rails Stripe integration entirely. Each of these is read from somewhere. None of them are fully trusted. Engineers develop an intuition about which one to check in which context, and that intuition does not survive team turnover.

The risk is not just bugs. It is that no single person can confidently answer questions from finance, support or the CEO without reading code.

Architecturally, a healthy SaaS billing pipeline in a Rails application tends to look something like this — one path in, one canonical state, one place authorization is decided from:

![Healthy Rails SaaS billing architecture: a linear flow from Stripe through webhook processing into a single subscription state, then authorization, then the Rails application]({{ site.baseurl }}/assets/img/articles/rails-billing-audit-healthy.svg){: .article-diagram }

What I more often find in production is closer to this — multiple writers, multiple readers, and no single component that is unambiguously the source of truth:

![Rails SaaS billing architecture after years of drift: Stripe, admin panel and rake tasks all write into multiple overlapping state stores, which combined with feature flag overrides feed scattered authorization checks throughout the Rails application]({{ site.baseurl }}/assets/img/articles/rails-billing-audit-unhealthy.svg){: .article-diagram }

The second diagram is not a caricature. It is roughly what most billing systems look like after four or five years of incremental feature work, and it is why a question as simple as "is this customer paying?" can take an engineer half an hour to answer honestly.

<br/>

## # Warning Sign #4: Manual admin fixes have become routine

Every mature SaaS has an admin console with some ability to correct billing state. That is healthy. What is not healthy is when using it becomes part of the weekly rhythm — support toggling an `active` flag to restore access, engineers running console commands to reset a subscription after a failed webhook, an operations person editing a `plan_id` because the upgrade flow "didn't take."

By the time teams notice this problem, two things are true at once. First, the system is not converging on correct state on its own, so someone has to nudge it. Second, every manual nudge is an undocumented, unaudited state change, which makes future debugging harder. The corrections are themselves a source of drift, and they mask the underlying architectural issue by absorbing its symptoms into human effort.

This pattern is worth taking seriously precisely because it does not feel like an emergency. It feels like the team is coping well. But the coping is expensive, it depends on specific people staying on the team, and it hides the underlying problem from the people who could authorize fixing it.

<br/>

## # Warning Sign #5: Webhook events are treated as reliable and ordered

Stripe webhooks are neither guaranteed to arrive exactly once nor guaranteed to arrive in order. Most Rails Stripe integrations know this in principle. Fewer are actually built to tolerate it in practice, and this is one of the areas where architectural drift shows up most sharply — the original handler was often written when there were three event types, and now handles twenty.

The symptoms are subtle. A `customer.subscription.updated` event arrives after a `customer.subscription.deleted` event and reactivates a canceled subscription. A duplicate `invoice.payment_succeeded` extends an access period twice. A retried webhook re-runs a side effect that was meant to happen once, such as sending a receipt or provisioning a resource. These are not always caught by tests because the tests usually simulate one Stripe webhook at a time in the intended order, with no duplicates and no interleaving.

The business risk here is the hardest to explain to non-engineers, because from the outside the system looks fine most days. Then, during a Stripe incident, a deploy that briefly delays a queue, or a burst of traffic, several customers end up in impossible states at once and the team spends a week reconstructing what happened.

<br/>

## # Warning Sign #6: Nobody can confidently explain a specific customer's billing state

A useful test I often propose to teams: pick a real customer whose billing has been non-trivial — a trial that converted, then upgraded, then downgraded, then had a failed payment, then recovered. Ask an engineer to explain, without touching Stripe, exactly what plan the customer is on right now, what they will be charged next, when, and why.

In a healthy Rails subscription management setup, that answer takes a few minutes and can be read off the application's own data. In a system that needs an audit, it takes an hour, requires cross-referencing Stripe, and ends with a sentence that begins "I *think* what happened is…"

The risk is not only operational. It is a signal that the domain model no longer reflects how the business actually charges customers. Once the model diverges from the business, every new billing feature becomes disproportionately expensive to build, because engineers have to reason from first principles each time, and every estimate quietly grows a "we don't know what this will touch" buffer.

<br/>

## # Warning Sign #7: Billing history is not reliably traceable

This is the sign I find last, because it only becomes visible when someone actually needs the history. A customer disputes a charge from four months ago. Finance asks why MRR dropped in a specific week. A support engineer wants to know who canceled a subscription and when.

In systems that have grown organically, the answers are often incomplete. The `subscriptions` table stores current state, not the sequence of transitions. Webhook payloads were logged for a while, then log retention changed, then the format changed after a Stripe API upgrade. Admin actions were not recorded. Background jobs that changed subscription state left no breadcrumbs. Stripe itself has the truth of what was charged, but the *reasons* — which coupon was applied by which flow, which plan change was initiated by whom — live in the application, and the application did not think to keep them.

The business risk is a slow one: reduced ability to answer trust-critical questions, and reduced ability to detect problems retrospectively.

<br/>

## # Common Root Causes

It is worth naming what usually causes these patterns, because the causes are almost never a single bad decision. When I review a Ruby on Rails SaaS billing system that is showing several of the symptoms above, the underlying story is almost always some combination of the following.

**Architectural drift.** The billing model that was correct for one plan and one flow gradually becomes incorrect as the number of plans, flows and edge cases grows. No individual change is unreasonable. The cumulative shape is. This is the single most common root cause I see, and it is invisible in any one pull request.

**Business rules that accumulated over many years.** Pricing, trials, grandfathering, seat models, referral credits and refund policies evolve as the company grows. Each change is typically expressed as a small patch on top of the existing model rather than a re-modeling. After enough patches, the model no longer describes the business — it describes the history of the business.

**Feature flags that never went away.** Flags introduced for a launch, a specific customer, or an experiment quietly become permanent parts of billing logic. Over time they layer on top of subscription state and become a second, undocumented axis of authorization.

**Multiple developers implementing billing independently.** Billing rarely has a single owner. Different engineers, at different times, added the pieces they needed. Each piece is coherent within itself. Together they form a system that no one designed on purpose.

**Insufficient observability.** Billing systems often have less instrumentation than the rest of the application, because they "just work" most of the time. When something goes wrong, the team discovers there is no good way to see what happened, and reconstruction depends on log retention that was tuned for other purposes.

**Unclear ownership of billing logic.** Billing touches product, engineering, support and finance. In practice this often means no single person has both the authority and the context to insist on a coherent design, so decisions are made locally and the whole is never revisited.

**Technical debt that compounds asymmetrically.** In most parts of a Rails application, a small inconsistency causes a small bug. In billing, a small inconsistency can cause a customer to be charged incorrectly, or to keep access they should not have, which turns a technical issue into a trust issue. The same category of debt that is tolerable in, say, a reporting module is genuinely dangerous here.

These causes are the same forces I usually see behind the need for [legacy Rails maintenance](/legacy-rails-maintenance/) work more broadly. Billing is simply the place where they become most visible, because it is where mistakes cost money directly.

<br/>

## # What an Audit Actually Looks For

An audit is not a rewrite. It is a structured investigation. The goal is to understand the current system honestly before recommending any changes. The questions I work through are mostly the ones a careful engineer would ask if they had the time, but usually do not.

- **Where is the real source of truth for each billing fact?** For "is this account paying," "what plan are they on," "when does access end," which column, service or external system is authoritative — and does the rest of the application agree?
- **Which events are allowed to change billing state, and from where?** Webhooks, controllers, admin actions, background jobs, console commands and migrations are all candidates. The set is usually larger than the team expects.
- **How is access actually determined at request time?** Which method, on which object, is the gate? Is it consistent across the web app, the API, background jobs and admin tools?
- **How does the system recover from failure?** What happens when a webhook is missed, delivered twice, delivered out of order, or fails halfway through processing? What happens when Stripe is briefly unreachable?
- **How are webhook events recorded?** Are payloads stored, deduplicated by event ID, and replayable? Can the team reconstruct the sequence of events that led to a customer's current state?
- **How is billing history traced?** Can you reliably answer "what changed for this customer, when, and why," using only the application's own data?
- **How does the model map to the business rules as they exist today?** Not as they were when the code was written. As they are now.

The output of an audit is a written picture of the system as it actually behaves, a prioritized list of the specific inconsistencies that pose the most risk, and a recommendation for the safest sequence of changes. It is deliberately not a pull request. The point is to give the team enough clarity to make good decisions, including the decision to leave some things alone.

<br/>

## # Closing

Subscription systems in Ruby on Rails SaaS applications rarely become unreliable because Stripe is unreliable. Stripe is one of the more predictable dependencies in a modern application, and Stripe webhooks are, in practice, a solved problem at the vendor's end. What is not solved — and what no vendor can solve for you — is what happens on the Rails side as business rules accumulate.

They become unreliable because those business rules accumulate over time, one launch and one edge case at a time, until the billing architecture no longer has a single clear source of truth. The webhook handler was written for one plan and now serves eight. The `active?` method was written for one product and now decides access for three. The admin console was written to fix rare edge cases and is now used weekly. Nothing broke. The architecture simply stopped matching the business, and no one had a mandate to re-align it.

That is the thought I would like to leave you with. Billing failures in mature Rails applications are almost never about Stripe. They are about the slow loss of a single, coherent model of what a subscription is inside your own application.

<br/>

## # Is Your Rails Billing System Showing These Symptoms?

If your team recognizes several of the warning signs above, the next step is usually not another patch. Another admin fix, another webhook handler branch, or another flag on the `users` table will typically deepen the drift rather than resolve it. What is more useful at that stage is a clear picture of how the billing architecture has actually evolved, and where the specific risks now live.

That is the purpose of a **Rails Billing System Audit**. It is a structured, read-first engagement — no code changes during the audit itself — designed to give founders, CTOs and engineering managers a trustworthy view of their SaaS billing system before any further changes are made.

A typical audit includes:

- **Identifying the real source of truth** for subscription state, plan, and access — and where the application disagrees with itself or with Stripe.
- **Reviewing the subscription lifecycle logic**: trials, upgrades, downgrades, cancellations, pauses, dunning and reactivation, and how each transition is expressed in the code.
- **Reviewing Stripe webhook processing**: idempotency, ordering, retries, event storage, and how webhook failures are recovered.
- **Identifying inconsistent billing state** across Stripe and the Rails application, and characterizing how widespread each inconsistency is likely to be.
- **Reviewing access control and authorization** as it relates to billing — where the "is this account paying?" decision is actually made across the web app, API, background jobs and admin tools.
- **Highlighting architectural risks** in the current Rails subscription management model, including scattered logic, feature-flag entanglement, and unclear ownership.
- **Prioritized recommendations** describing which issues are most urgent, which are safe to defer, and what a sensible sequence of changes would look like.

The output is a written report and a working session with your team. It is deliberately not a rewrite, a promised fix, or a guarantee. The point is to give you and your engineers a shared, honest understanding of the system before any structural changes are decided.

If this sounds like the right next step for your Ruby on Rails SaaS, you can see how I approach similar engagements on the [Selected Work](/work/) page, or read more about my [Rails Upgrade Consulting](/rails-upgrade-consultant/) and [Legacy Rails Maintenance](/legacy-rails-maintenance/) practice — billing audits sit alongside these as part of the same investigative approach to production Rails applications.

To discuss a Rails Billing System Audit for your application, you can reach me through [SparkRails](https://sparkrails.com). A short initial conversation is usually enough to tell whether an audit is worth doing, and to scope it honestly if it is.
