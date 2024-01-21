---
layout: post
title: "Default Enum in Rails 6"
meta: "default enum rails 6"
categories: "rails"
tags: rails-6 ruby-on-rails
description: "Default enum in Rails 6"
keywords:
  - enum in rails 6
  - default enum in rails 6
---

<img src="{{ site.base_url }}{{ site.default_rails_image }}" alt="{{ page.title }}">

Rails 6 provides a way to define default enum value. Let's see how it works with an example.
For this example we are using Rails alpha.
<br />

<h1 class="light">Default enum in action</h1>
Using the `Post` model, with status as `draft` & `published`, and will be using `draft` as default will be something like:

```ruby
  class Post < ApplicationRecord
    enum status: %i[draft published], _default: :draft
  end
```
<br />
On checking a new post's status turns out to be a `draft`. In previous versions of Rails this turns out to be `nil`.

<hr />
<img src="/assets/images/default-enum-in-rails/default-enum-in-rails.png" alt="default-enum-in-rails" class="img-50 max-width">

---
<span class="pull-right">
[Pull Request](https://github.com/rails/rails/pull/39820)
</span>
