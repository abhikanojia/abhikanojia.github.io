---
layout: post
title: "Rails 7 deprecate defining enum with keyword arguments"
categories: "rails-7"
tags: rails rails-7
description: "Rails 7 deprecate enum definition by keyword arguments, From Rails 7.3 this old syntax will not be available."
keywords:
  - rails 7, ruby on rails, deprecated enum syntax
  - improvements in rails 7
---

Rails 7 deprecate enum definition by keyword arguments, From Rails 7.3 this old syntax will not be available.This is important for those who are willing to upgrade their rails version and already using the older syntax for defining Enum. In case you are wondering what is the new Rails syntax for defining rails, checkout this [article](/rails/2024/02/04/rails-7-adds-new-enum-syntax.html).

## # Before Rails 7.3

```ruby
class User < ApplicationRecord
  # This syntax is valid
  enum status: [:active, :inactive]
end
```

## # After Rails 7.3

```ruby
class User < ApplicationRecord
  enum :status, [:active, :inactive]
end
```

Example of new enum syntax with Hash as enum options.

```ruby
class User < ApplicationRecord
  enum :status, { active: 0, inactive: 1 }
end
```

Refer to this [Commit](https://github.com/rails/rails/commit/8c5425197c7969ff50f675e9792fce1998fb9bc2){:target="_blank"} for more details.

>
> ### 💡 Did You Know?
> [Rails 7 Introduces validations for Enum](/rails/2024/02/03/rails-7.1-enum-validation.html){:target="_blank"}
>
