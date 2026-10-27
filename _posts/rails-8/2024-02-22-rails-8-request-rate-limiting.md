---
layout: post
title: "Rails rate_limit: Built-in Rate Limiting in Rails 7.2 & 8"
categories: "rails-8"
tags: rails rails-8 rails-7 security
description: "Rails 7.2 added rate_limit to controllers and Rails 8 adds named limits. Usage, options, the cache store gotcha, and when you still need rack-attack."
last_modified_at: 2026-10-27
keywords:
  - rails rate_limit
  - rails 8 rate limiting
  - rails 7.2 rate limit
  - rate_limit to within
  - rails rate limiting without rack-attack
---

<div class="quick-answer" markdown="1">
**Quick answer:** add `rate_limit to: 10, within: 3.minutes, only: :create` to a controller. Requests over the limit get **429 Too Many Requests**. It was added in **Rails 7.2**, and counts are stored in your cache store (`Rails.cache` by default), so it only works if that cache is shared across processes, such as Redis, Memcached or Solid Cache.
</div>

> **Version note:** this post originally said rate limiting arrived in Rails 8. `rate_limit` actually shipped in Rails 7.2. Rails 8.0 added the `name:` option for multiple limits, and the Rails 8 authentication generator uses it on the sessions controller.

## # What is Rate Limiting?
Rate limiting controls how many requests a client can make to a server within a set time. It protects login and signup forms from brute-force attempts, keeps expensive endpoints from being hammered, and limits the impact of a single misbehaving client.

## # How to Use `rate_limit`

```ruby
class SessionsController < ApplicationController
  rate_limit to: 4, within: 1.minute, only: :create

  def create
    # ...
  end
end
```

`rate_limit` accepts the following options:

`to:`
The maximum number of requests allowed in the time window.

`within:`
The length of the time window.

`only:` and `except:`
Like any controller callback, choose which actions the limit applies to.

`by:`
What to count requests by. Defaults to the client IP (`request.remote_ip`).

`with:`
What to do when the limit is hit. Defaults to `head :too_many_requests`.

`store:`
Where to keep the counters. Defaults to `config.action_controller.cache_store`, which falls back to `Rails.cache`.

`name:` (Rails 8.0+)
Lets you define more than one limit in the same controller.

A custom key and response:

```ruby
class SessionsController < ApplicationController
  rate_limit to: 4, within: 1.minute,
    by: -> { request.domain },
    with: -> { redirect_to new_session_url, alert: "Too many login attempts. Try again later." },
    only: :create

  def create
    # ...
  end
end
```

Two limits on one action (Rails 8.0+):

```ruby
class SignupsController < ApplicationController
  rate_limit to: 3, within: 2.seconds, name: "short-term", only: :create
  rate_limit to: 10, within: 5.minutes, name: "long-term", only: :create
end
```

## # The cache store gotcha

The counters live in the cache, so the store you use decides whether the limit works:

- **`:null_store`** (the development default unless you run `bin/rails dev:cache`) stores nothing, so the limit never triggers. This is why it "doesn't work" locally.
- **`:memory_store`** keeps a separate count per process. With several Puma workers or servers, each one has its own counter, and the real limit becomes `to:` × the number of processes.
- **Redis, Memcached or Solid Cache** are shared, so counts are accurate across processes.

## # Do you still need rack-attack?

`rate_limit` covers the common case: throttling specific controller actions. [rack-attack](https://github.com/rack/rack-attack){:target="_blank"} still makes sense when you need to block or throttle **before** a request reaches the Rails router (for example, bad IPs across the whole app), use allow and block lists, or apply rules to requests that don't hit a controller.

{% include elements/post_cta.html text="Adopting new Rails defaults is easier on a current Rails version. I help SaaS teams upgrade production Rails apps to Rails 7.2 and Rails 8." %}

## # Related

- [Rails `allow_browser` and the 406 unsupported browser page](/rails-8/2024/02/09/rails-8-adds-allow-browser-to-control-browser-compatiblity.html)
- [Brakeman is included by default in new Rails apps](/rails-8/2024/02/14/rails-8-adds-brakeman-as-default-to-new-apps.html)
- [Solid Queue: an overview](/rails-8/2024/02/16/solid-queue-an-overview.html)
