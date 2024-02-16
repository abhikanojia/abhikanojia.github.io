---
layout: post
title: "What is Solid Queue?"
categories: "rails-8"
tags: rails rails-8
description: "Solid Queue is DB-based queuing backend for ActiveJob, designed with simplicity and performance in mind."
keywords:
  - rails 8, improvements in rails
  - whats new in rails, solid Queue, basecamp solid Queue
---


Solid Queue is DB-based queuing backend for ActiveJob, designed with simplicity and performance in mind. Besides regular enqueuing and processing, Solid queue supports delayed jobs, concurrency control, priortising jobs, pausing and resuming queues, priorities per queue order, bulk processing of jobs.


## #What Makes Solid Queue a Good Choice?

Why do we need Solid when we already have Resque, Sidekiq and Delayed Job in place?
In general implementation of jobs, the workers are interdependent on each other in a sense that they can only pick jobs when the other worker instance releases the lock. This prevents the concurrent operation of jobs.

With the implementation of `SELECT ... FOR UPDATE SKIP LOCKED` which allows a session to query the queue table, skip rows currently locked by other sessions, select the next unlocked row, and lock it for processing.

This was previously not available in MySQL, with the [introduction of this clause in MySQL 8](https://dev.mysql.com/blog-archive/mysql-8-0-1-using-skip-locked-and-nowait-to-handle-hot-rows/), Solid Queue becomes the first to implement this approach in its segment. This gives the concurrency that you would expect while working with similar system that utilizes Redis to do the same.

## #Three things that make up the Solid Queue

- <strong>Workers</strong><br/>
  Workers are in charge of running the jobs and picking them up from `solid_queue_ready_executions` table created by solid queue gem when you install it and run `bin/rails solid_queue:install:migrations`

- <strong>Dispatchers</strong><br/>
  Dispatchers are in charge of selecting jobs which are scheduled to run in future and making them available for workers, i.e moving jobs from `solid_queue_scheduled_executions` table to `solid_queue_ready_executions`. Concurrency control is another responsiblity of Dispatchers.

- <strong>Supervisors</strong><br/>
Supervisors forks workers and dispatchers according to the configuration provided in `config/solid-queue.yml`. Example config:

```YAML
# config/solid-queue.yml
production:
  dispatchers:
    - polling_interval: 1
      batch_size: 500
      concurrency_maintenance_interval: 300
  workers:
    - queues: "*"
      threads: 3
      polling_interval: 2
    - queues: [ real_time, background ]
      threads: 5
      polling_interval: 0.1
      processes: 3
```

## # What makes it a great choice for Rails 8
The idea based on the discussion [here](https://github.com/rails/rails/issues/50442) is to make it hassle free and so that user does not have to configure it whatsoever. Following the Rails convention over configuration pattern.

In summary, Rails 8 brings performance enhancements, improved developer experience, modern technology integrations, and enhanced security measures, making it a significant milestone in the evolution of the Rails framework. As we await its official release, Rails developers can look forward to building even more robust and scalable web applications with ease.
<br/>

>
> ### 💡 Do You Know?
> [Rails 8.0 adds security tool Brakeman to new apps as default](/rails-8/2024/02/14/rails-8-adds-brakeman-as-default-to-new-apps.html){:target="_blank"}
>
