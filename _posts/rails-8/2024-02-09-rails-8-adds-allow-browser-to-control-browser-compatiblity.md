---
layout: post
title: "Rails 8.0: adds allow_browser to control browser compatiblity"
categories: "rails-8"
description: "Setting browser compatiblity with Rails 8 using allow_version option"
tags: rails rails-8
keywords:
  - Rails 8, allow_browser method, browser version controler rails
  - Browser version control, Web development, Modern web technologies
  - Minimum browser versions, Enhancing application security in Rails,
---

In the dynamic world of web development, ensuring consistent performance across different browsers and versions is paramount. With Rails 8.0, developers gain a powerful tool to manage browser compatibility effortlessly: the allow_browser method. Let's explore how this feature simplifies browser version control and elevates application security.

## Simplified Browser Version Control

The allow_browser method in Rails 8.0 simplifies the process of specifying minimum browser versions required for your application. Whether you're aiming for modern web capabilities or need to support specific versions, allow_browser offers flexibility and control.

### Basic Usage

- Implementing browser version control is straightforward. Here's a basic example:

```ruby
class ApplicationController < ActionController::Base
  allow_browser versions: :modern
end
```
In this setup, the `:modern` symbol ensures support for browsers capable of handling advanced web technologies seamlessly.
Customizing Browser Versions

<br/>

- Tailoring browser version requirements to your application's needs is effortless. Specify supported versions like so:

```ruby
class ApplicationController < ActionController::Base
  allow_browser versions: { safari: 20, firefox: 110, ie: 9 }
end

```

<br/>
- In this example, Firefox support is disabled while Safari and Internet Explorer versions are specified.
Fine-Tuning for Specific Actions

Refine browser version checks to apply only to specific controller actions using the only or except options:


```ruby
class ChatController < ApplicationController
  allow_browser versions: { chrome: 85, opera: 100, ie: false }, only: :show
end

```

This snippet ensures that the `allow_browser` check is limited to the `show` action of the `ChatController`, with tailored version requirements.


## Graceful Handling of Unsupported Browsers

Rails 8.0 gracefully handles unsupported browser versions by displaying a custom page (426.html) and returning the HTTP status code 426 Upgrade Required. This prompts users to upgrade their browsers for optimal performance and security.


## Conclusion

With `allow_browser`, Rails 8.0 empowers developers to manage browser version requirements effortlessly. By setting minimum versions, applications achieve enhanced compatibility, improved security, and superior user experiences across diverse browser environments.

For a deeper dive into this feature, explore the official Rails 8.0 [PR](https://github.com/rails/rails/pull/50505) here.
