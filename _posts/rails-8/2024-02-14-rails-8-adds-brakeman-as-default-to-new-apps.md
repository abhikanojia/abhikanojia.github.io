---
layout: post
title: "Rails 8 adds Brakeman by default to New Apps"
categories: "rails-8"
tags: rails rails-8
description: "Rails 8 includes Brakeman by default, enhancing security."
keywords:
  - rails 8, ruby on rails, brakeman
  - improvements in rails 8, security enhancement in rails
---

Rails 8 Boosts Security with Brakeman Integration!

Brakeman, a powerful static analysis security tool, by default. Let's delve into what this integration means for Rails developers and how it enhances the security landscape of Ruby on Rails applications.

## # Understanding Brakeman:

Brakeman is a static analysis security tool specifically designed for Ruby on Rails applications. It scans the codebase for potential security vulnerabilities, including but not limited to SQL injection, cross-site scripting (XSS), and mass assignment vulnerabilities. By analyzing the application's source code, Brakeman identifies security risks early in the development process, enabling developers to address them proactively before deploying the application.

## # Key Features of Brakeman:

- **Vulnerability Detection:**
  Brakeman meticulously scans the Rails application codebase, pinpointing potential security vulnerabilities such as unprotected user inputs or insecure database queries.

- **Continuous Monitoring:**
  Integrating Brakeman into the development workflow allows for continuous monitoring of the codebase, ensuring that any new vulnerabilities introduced during development are promptly identified and remediated.

- **Customizable Reports:**
  Brakeman generates comprehensive reports detailing identified vulnerabilities, their severity levels, and recommendations for mitigation. These reports aid developers in understanding and addressing security issues effectively.

- **Easy Integration:**
  With Rails 8 incorporating Brakeman by default, developers can seamlessly leverage its capabilities without the need for additional configuration or setup, streamlining the development process.

## # Milestone Rails 8.0.0
Adding Brakeman by default was one of the [milestone of Rails 8.0.0](https://github.com/rails/rails/issues/50501)
Here is the link to #PR [Rails 8 includes Brakeman by default](https://github.com/rails/rails/pull/50507)

## # Conclusion:
Rails 8's integration of Brakeman by default marks a significant step forward in enhancing the security posture of Ruby on Rails applications. By leveraging Brakeman's advanced static analysis capabilities, developers can identify and mitigate security vulnerabilities early in the development lifecycle, reducing the risk of exploitation and ensuring the integrity of their applications. Embracing security as a fundamental aspect of development, Rails 8 sets a precedent for prioritizing robust security measures in modern web application development.

<br/>
>
> ### 💡 Did You Know?
> [Rails 8.0 adds `allow_browser` to control browser compatiblity](/rails-8/2024/02/09/rails-8-adds-allow-browser-to-control-browser-compatiblity.html){:target="_blank"}
>
