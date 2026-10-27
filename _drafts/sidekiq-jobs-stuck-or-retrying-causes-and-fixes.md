---
layout: post
title: "Sidekiq Jobs Stuck or Retrying? Common Causes and Fixes"
categories: "rails"
tags: rails sidekiq redis background-jobs production
description: "Why Sidekiq jobs get stuck in busy, retry over and over, or end up dead: the common causes in production Rails apps and how to fix each one."
keywords:
  - sidekiq jobs stuck
  - sidekiq jobs retrying
  - sidekiq job stuck in busy
  - sidekiq retries
  - sidekiq dead jobs
  - sidekiq activerecord recordnotfound
---

<!--
DRAFT. Before publishing:
- Replace every [ADD EXAMPLE] with a real (anonymised) case, or delete that line.
- Check every [VERIFY] against the Sidekiq wiki / changelog for your target Sidekiq version.
- Rename the file with a date prefix in _posts/ when publishing.
-->

<div class="quick-answer" markdown="1">
**Quick answer:** most "stuck" or endlessly retrying Sidekiq jobs come from one of six causes:

1. The job keeps raising the same exception. By default Sidekiq retries 25 times over roughly 3 weeks.
2. The job was enqueued **inside a database transaction**, so the record doesn't exist yet when it runs.
3. The database or Redis connection pool is smaller than Sidekiq's concurrency.
4. A slow external call has no timeout.
5. The process was killed mid-job, during a deploy or after running out of memory.
6. A busy queue is starving the others.

Check the Retries tab for the error message first. It usually tells you which one it is.
</div>

Background job problems are easy to miss because they're often silent. There's no exception in the request cycle, just work that quietly doesn't happen. In upgrades I've worked on, watching queue and retry behaviour on the first production deploy of each stage was a deliberate step for exactly that reason.

[ADD EXAMPLE: one real Sidekiq incident you debugged. What was the symptom, what did it look like in the Web UI, what was the root cause?]

## # 1. The job keeps failing (and retrying for weeks)

Sidekiq retries a failed job with exponential backoff, by default **25 times over about 21 days**, then moves it to the **Dead** set. A job that "keeps coming back" is usually a permanent error being retried as if it were temporary.

Fixes:

```ruby
class SyncInvoiceJob
  include Sidekiq::Job
  sidekiq_options retry: 5          # fewer retries for this job

  sidekiq_retries_exhausted do |job, ex|
    # alert, mark the record as failed, etc.
  end
end
```

With Active Job, make the distinction between temporary and permanent errors explicit:

```ruby
class SyncInvoiceJob < ApplicationJob
  retry_on Net::OpenTimeout, wait: :polynomially_longer, attempts: 5
  discard_on ActiveJob::DeserializationError   # record was deleted before the job ran
end
```

[VERIFY: `wait: :polynomially_longer` is the Rails 7.1+ name; older versions use `:exponentially_longer`.]

## # 2. `RecordNotFound` right after create: enqueued inside a transaction

```ruby
# Problem: the job can run before the transaction commits
Order.transaction do
  order = Order.create!(params)
  OrderConfirmationJob.perform_later(order.id)
end
```

The worker picks up the job, can't find the order yet, raises `ActiveRecord::RecordNotFound`, and succeeds on a later retry. It looks flaky. It isn't.

Fixes: enqueue from an `after_commit` callback, or move the enqueue after the transaction block. Rails 7.2 added `enqueue_after_transaction_commit` for Active Job. [VERIFY current default and config name on Rails 8.x before recommending it.]

[ADD EXAMPLE: have you seen this on a client app? How did it show up (duplicate emails, missing notifications)?]

## # 3. Connection pool too small for concurrency

Each Sidekiq thread needs a database connection. If `pool` in `database.yml` is smaller than Sidekiq's concurrency, threads wait and eventually time out:

```
ActiveRecord::ConnectionTimeoutError: could not obtain a connection from the pool within 5.000 seconds
```

```yaml
# config/database.yml
production:
  pool: <%= ENV.fetch("RAILS_MAX_THREADS", 5) %>
```

Make sure the Sidekiq process gets a pool at least as large as its concurrency (Sidekiq 7's default concurrency is 5). Also check that the database's total connection limit covers all your Puma and Sidekiq processes.

## # 4. Jobs "stuck in busy": slow calls without timeouts

A job that's been "busy" for an hour is usually waiting on something: an HTTP call, a lock, a slow query. Ruby's `Net::HTTP` has long default timeouts.

```ruby
Net::HTTP.start(host, port, open_timeout: 5, read_timeout: 10) { |http| ... }
```

Set explicit timeouts on every external call made from a job, and split long-running jobs into smaller batches.

## # 5. Jobs lost or stuck after a deploy or OOM kill

On a normal shutdown (`TERM`), Sidekiq stops fetching new jobs, waits for running jobs up to its shutdown timeout (25 seconds by default), and pushes unfinished jobs back to Redis. If the process is **killed hard** (`KILL`, or the container is OOM-killed), jobs in progress can be lost with open-source Sidekiq's basic fetch. [VERIFY wording for your Sidekiq version; Sidekiq Pro's super_fetch recovers them.] The Busy page may show stale entries until the dead process's heartbeat expires.

Fixes:

- Make sure your platform's stop timeout is longer than Sidekiq's shutdown timeout.
- Keep jobs short and **idempotent**, so running one twice is safe.
- Watch worker memory. Steadily growing Sidekiq memory is often fixed with jemalloc or `MALLOC_ARENA_MAX=2`. [ADD EXAMPLE if you've done this.]

## # 6. One queue starving the others

With **strict** queue ordering, Sidekiq always drains higher-priority queues first, so a flood of jobs in `default` can starve `low` indefinitely. Weighted queues share time:

```yaml
# config/sidekiq.yml
:queues:
  - [critical, 6]
  - [default, 3]
  - [low, 1]
```

Monitor **queue latency** (how old the oldest job is), not just queue size.

## # Check Redis too

Sidekiq stores everything in Redis. If Redis is set to evict keys under memory pressure, it can silently drop jobs. Sidekiq logs a warning at startup if `maxmemory-policy` isn't `noeviction`. Keep job Redis and cache Redis separate, so cache growth can't push out jobs.

[ADD EXAMPLE: Redis memory issue you've investigated. What was using the memory?]

## # A quick triage checklist

1. Retries tab: what's the error message, and is it one error or many?
2. Busy tab: how long have the jobs been running, and on which process?
3. Queue latency per queue.
4. Redis memory and eviction policy.
5. Worker memory and restarts around the time it started.
6. Was there a deploy, a Rails/Sidekiq upgrade, or a traffic spike?

{% include elements/post_cta.html title="Background jobs misbehaving in production?" text="I help SaaS teams debug Sidekiq and Redis problems in production Rails apps and fix the root cause." url="/sidekiq-redis-consultant/" label="See Sidekiq & Redis debugging" service="sidekiq_redis" %}

## # Related

- [Solid Queue: an overview](/rails-8/2024/02/16/solid-queue-an-overview.html)
- [Sidekiq & Redis debugging consultant](/sidekiq-redis-consultant/)
