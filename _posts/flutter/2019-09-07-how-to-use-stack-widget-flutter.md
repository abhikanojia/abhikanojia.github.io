---
layout: post
title: "How to use Stack Widget flutter?"
meta: "how to use stack widget flutter"
categories: "flutter"
tags: flutter dart flutter-weekly
description: "Our new post which is part of flutter weekly, presents how to use Stack widget in flutter with ease."
keywords:
 - flutter weekly
 - how to use stack widget flutter
 - using stack widget in flutter
 - how to use positioned widget in flutter
---

![Flutter - How to use Stack Widget in flutter][flutter]

If you are wondering, checkout our 2 minute read post [What is flutter](https://sapidlabs.com/flutter/2019/08/12/what-is-flutter.html). Since Everything in flutter is a Widget. It is good to have a basic understanding of what a particular widget does. We will be picking up one widget per weekly to explain about it in brief about it. Keep yourself updated.

<br />

### What is Stack in flutter?

[Stack](https://api.flutter.dev/flutter/widgets/Stack-class.html) is another widget provided by flutter that positions its child widgets relative to edge of the example. Doesn't ring any bell?? We will cover with example.

Stack can have two types of child widgets within them. These are [Positioned](https://api.flutter.dev/flutter/widgets/Positioned-class.html) and Non-positioned widgets.

Postioned - Those which are wrapped within [Positioned](https://sapidlabs.com/flutter/2019/09/07/how-to-use-positioned-widget-in-flutter.html) widget, something like the following:

```dart
Positioned(
  child: Container(
    ...
  )
)
```

Stack takes children as argument, which is list of elements that will be part of the stack. The elements are placed on the screen starting from the first child being at the bottom to the last child at the top and is left to right in alignment.

The following code will draw three containers on the mobile screen, with green, red and indigo colored containers respectively.

```dart
...
Stack(
  children: <Widget>[
    Container(
      width: 400,
      height: 400,
      color: Colors.green[300],
      child: Center(
        child: Text(
          'Green',
          style: TextStyle(color: Colors.white, fontSize: 20),
        ),
      ),
    ),
    Container(
      width: 250,
      height: 150,
      color: Colors.red[400],
      child: Center(
        child: Text(
          'Red',
          style: TextStyle(color: Colors.white, fontSize: 20),
        ),
      ),
    ),
    Container(
      width: 100,
      height: 100,
      color: Colors.indigo[300],
      child: Center(
        child: Text(
          'Indigo',
          style: TextStyle(color: Colors.white, fontSize: 20),
        ),
      ),
    ),
  ],
...
```
<br />

### App Preview

<img src="{{ site.base_url }}/assets/images/stack-widget-flutter/stack_app.png" class="img img-center img-50-imp" alt="stack-app-flutter">

As you can see in the example, that the Green was provided as the first child so it is bottom most amoung the three containers and Indigo colored container being at the top, and the Red one is in middle.

### Using Postioned Widget with Stack widget.
<br />


<br />

The `Positioned` widget when used with stack widget allows to position child widget within the space. Let's see how : [How to use Positioned Widget with Stack?](https://sapidlabs.com/flutter/2019/09/07/how-to-use-positioned-widget-in-flutter.html) (2 min read.)

[flutter]: /assets/images/shared/flutter-sapidlabs.jpg
