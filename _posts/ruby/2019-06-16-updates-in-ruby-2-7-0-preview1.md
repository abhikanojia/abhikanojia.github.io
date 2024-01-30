---
layout: post
title: "Updates in Ruby 2.7.0-preview1"
categories: "ruby"
tags: Ruby
description: "There are couple of new features introduced to Ruby 2.7-preview, so we thought we should give it a try! You can do as well as we do. Lets checkout updates in ruby 2.7"
keywords:
 - updates in ruby 2.7
 - latest ruby version updates
 - new in ruby 2.7
 - new in ruby
---

There are couple of new features introduced to Ruby 2.7-preview, so we thought we should give it a try! You can do as well as we do.

Lets get started

As per the post on ruby lang news page, [Official announcement](https://www.ruby-lang.org/en/news/2019/05/30/ruby-2-7-0-preview1-released/). The release announced, talks about various new features and performance improvements. The Final release is planned for December, 2019.

Most notably of amoung them are:

### REPL Improvements
REPL stands for Read-Evaluate-Print-Loop, the regular IRB console that you used to try out ruby for the first time. It has finally got some required features. Let me tell you it will be a huge success for all those who are trying to learn ruby or just trying it for the first time.

After these changes are available, you will able to scroll up through your classes/methods that you are trying to test on IRB, which you were unable to do on previous versions. Also, the classes/objects are now colorised.

<div style="display: table;margin: 0 auto;">
  <iframe width="420" height="315" src="https://cache.ruby-lang.org/pub/media/irb_improved_with_key_take2.mp4" frameborder="0" allowfullscreen></iframe>
</div>

### Pattern Matching
Coming to pattern matching part, this one is introduced as experimental feature to ruby-2.7. From a ruby perspective, `case/when` is where you can match data. This is also allowed for other data types such as `Array` or `Hash`. we tried out a simpler snippets to test pattern matching.

#### Number

```ruby
a = 10

case a
  in 1
    :small
  in 10
    :large
end
(irb):1: warning: Pattern matching is experimental, and the behavior may change in future versions of Ruby!
=> :large
```

#### Array

```ruby
a = [1,2]

case a
  in [1,b]
   p b
end

(irb):1: warning: Pattern matching is experimental, and the behavior may change in future versions of Ruby!
2
=> 2
# Notice how 2 is assigned to b using pattern matching
```
#### Hash
```ruby
a = { a: 10, b: { c: 20, d: 30 } }

case a
  in { a: 10, value }
   p value
end
(irb):1: warning: Pattern matching is experimental, and the behavior may change in future versions of Ruby!
{ c: 20, d: 30 }
=> { c: 20, d: 30 }
# Notice how { c: 20, d: 20} is assigned to `value` variable.
```

#### Type Comparison
```ruby
a = "Hello"

case a
  in String => value # value will contain "hello"
   puts value
  in Integer => integer # if a is integer
   puts integer
 else
  puts "Nothing matches"
end
(irb):2: warning: Pattern matching is experimental, and the behavior may change in future versions of Ruby!
hello
 => nil
```

Note that If else block is not present and case doesn't satisfy any of the condition this will lead to `NoPatternMatchingError`.

### Numbered Parameter
According to this new change, a default block parameter will be made available in terms of number e.g `@1`. So, when iterating through list of elements on an array something like:

```ruby
[1,2,3].each { puts @1 }
1
2
3
=> [1,2,3]
```
This might add confusion with existing `instance variables` we use in ruby, and it might take away the beauty of writing loops as
```ruby
[1,2,3].each { |x| puts x }
```
### Beginless Range
Using a typical range operator in ruby, i.e `a..b` we can define a range from `a` to `b` inclusive. If you want to save some time addressing a range from `0` all the way to a number say `3` this is where beginless range comes handy. For example:
```ruby
 # define an array
 a = (1..10).to_a
 => [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

 # Address array from 1 to 3
 # In ruby 2.6 or earlier
 a[0..2]
 => [1, 2, 3]

 # In Ruby 2.7.0
 a[..2]
 => [1, 2, 3]

 # Similarly, exclusive range can be addressed as
 a[...2]
 => [1, 2]
```

### Enumerable#tally
```ruby
['a', 'A'].tally
=> { 'a' => 1, 'A' => 1 } # easy ^_^

```
According to the feature discussion, there is another version proposed besides `tally` and that is `tally_by` which can take a method as a parameter and does the job accordingly. we found it very useful and handy to be honest. Although, `tally_by` **is not yet accepted**. Since, this method is defined in `Enumerable` module this is available to `Hash` as well.

As per the suggestion, `tally_by` was expected to do the following task:

```ruby
['a', 'A'].tally_by(&:downcase)
=> { 'a' => 2 }
```
Let us wait for this make to the upcoming ruby versions by a different name.

### Compaction GC

This release introduces Compaction GC which can defragment a fragmented memory space.
What leads to fragmented memory?

Multi-threaded programs in ruby can cause these fragmentation in memory, which leads to high memory usage and degraded speed. So to overcome this `GC.compact` method is introduced in order to defrag the memory space.

An Illustration describing issue and solution:

![Updates in ruby 2.7][ruby_compaction]

[ruby_compaction]: /assets/images/updates-in-ruby-2-7-0-preview1/ruby-2-7-0-gc-compaction.png
