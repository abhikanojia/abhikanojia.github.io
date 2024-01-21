---
layout: post
title: "How to install Rails 7"
meta: "Rails-7 ruby-on-rails sapidlabs"
categories: "rails"
tags: rails-7 ruby-on-rails
---

<img src="/assets/images/shared/rails-7.png" alt="default-enum-in-rails">

Initializing a new project as Rails 7 project is very easy, though beginners find it very difficult to do it on the first time. 

This post assumes you have rails installed on your machine and is available to be using in the command line/terminal.

Rails 7 is released now. We can follow the following steps to install Rails 7.

```ruby
# create a new project using rails command 
rails new your_rails_7_app_name

# open the folder in your favorite editor and open the Gemfile
# replace the gem 'rails' line with the following line:
gem 'rails', '7.0.0'

# Run bundle
bundle

# check the version once the bundle command completes
rails -v
> Rails Rails 7.0.0
```
Rails 7 is now available to be used in the intialized repository. 🥳

See Official Announcement from DHH, [Fulfilling A Vision](https://rubyonrails.org/2021/12/15/Rails-7-fulfilling-a-vision)

<hr />
We are offering solutions to numerous business out there. [Improve your business with us!](https://forms.gle/u99tr1qmG1kKPWWF8)
