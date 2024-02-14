---
layout: post
title: "After save commit callback in Rails-6"
meta: "Rails-6 ruby-on-rails sparkrails after-save-commit"
categories: "rails"
tags: rails-6 ruby-on-rails
---


Rails 6 adds another helpful, shortcut which provides an easier way to write the following active model callback for model.

```ruby
  after_commit :do_something, on: [:create, :update]
```

Now we can simply write it as:

```ruby
  after_save_commit :do_something
```

Following is the very basic example of its usage:

Please note that `after_save_commit` is different from `after_save`

**`after_save`** - calls the defined hook method just after saving the model instance but before the commit sequence of saving transaction. That means you get a drawback here if some error occured during saving sequence of the model object, the callback is going to call hook method anyways.

To ensure, an action that we need to run after actual saving of data to the database, we should use `after_commit` callbacks or similar as per requirements.
<br />

<br />

**`after_save_commit`** - calls the defined hook method after the saving the model and after commit sequence. Also worth noticing that this will fire on every saving and updating of model object.
