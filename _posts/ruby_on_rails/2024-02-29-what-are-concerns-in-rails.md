---
layout: post
title: "What are Concerns in Rails?"
categories: "rails-8"
tags: rails rails-8
description: "Learn about rails concerns their uses and benefits under 2 minutes. Lets get started then."
keywords:
  - rails ruby concerns modules-in-rails
  - what-are-concerns-in-rails
---

## # Isn't that Concerning?
Where does this came from? The word `Concern`. It came from the Active Support Concern or the Module `ActiveSupport::Concern` available `active_support/concern`. It allows you to seamlessly mix in the methods to the class that you are using it in.

But thats what a module does, No? Yes, But there is more when we talk about Concerns in rails.

## # Module vs Concern

**Module** comes from the Ruby itself. Module is a collection of methods and constants. The methods in the module may be a module methods or instance methods. Instance methods defined in the module will appear as instance methods of the class the module is included in. For example:

```ruby
module Sparkrails
  def give_me_best_software
    "Best Software"
  end
end

class YourDreamProject
  include Sparkrails
end

> your_enterprise = YourDreamProject.new
> your_enterprise.give_me_best_software

=> "Best Software"
```

As seen in above example the Module `Sparkrails`, got included in the `YourDreamProject`, the method available in `Sparkrails` module are available as instance methods for `YourDreamProject`.

<br/>

**Concern** on the other hand is a simplified version of Module, In other words, Concern is just a ruby module which extends `ActiveSupport::Concern`. In a concern you will always this as a first line. Lets take the same example again:

```ruby
module Sparkrails
  extend ActiveSupport::Concern

  included do
    # instance methods here
  end

  class_method do
    # class methods here
  end

  prepended do
    # methods availabel when module is prepended
  end
end

class YourEnterprise
  include Sparkrails
end
```

Concern gives you hook methods like `included`, `class_methods` and `prepended`. You can use these to define the methods that goes in the the class.

1. **included** Methods that goes in this block will be available as instance methods for the host class instances.

2. **class_methods** Methods that goes in this block will be available as class methods for the host class.

3. **prepended** These methods are only available when a module prepended in the Host class or module. `prepend` changes the order in the method chain basically.


## # Benefits of Using Concerns

Ease of organisation of code is the primary goal of Concern in rails. It is usually very helpful when you want to extract out common code which works in Isolation with other classes as well. Think of Concerns like a magic wand that enables your class to do something special given that it has all the things that you can work with. For example:

```ruby
module Archivable
  extend ActiveSupport::Concern

  included do
    scope :archived, -> { where(status: 'archived') }

    def mark_archive
      # code to archive entity
    end
  end

end

class Photo
  include Archivable
end

class Comment
  include Archivable
end

# You can do
=> comment = Comment.last
> comment.mark_archive

> all_archived_photos = Photo.archived
# => #<ActiveRecord::Collection [#<Photo id:....,]

```

