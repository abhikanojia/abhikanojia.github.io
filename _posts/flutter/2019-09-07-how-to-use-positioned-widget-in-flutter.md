---
layout: post
title: "How to use positioned widget in flutter?"
meta: "sapidlabs positioned widget flutter"
categories: "flutter"
tags: flutter dart flutter-weekly
description: "Our new post which is part of flutter weekly, presents how to use Stack and Positioned Widget in flutter with ease."
keywords:
  - flutter weekly
  - flutter stack widget example
  - flutter positioned widget example
  - stack and positioned widget example
---

![Flutter - Stack and Postioned Widget][flutter]

This Recommends checking out our previous post on, the usage of [Stack widget](https://sapidlabs.com/flutter/2019/09/07/how-to-use-stack-widget-flutter.html)( 2 min read ). I am waiting....

Ok Done? So let's get started.

<br />

<h1 class="light">How to use Postioned Widget in flutter?</h1>

[Positioned Widget](https://api.flutter.dev/flutter/widgets/Positioned-class.html), as the name suggests It is something related to position of some widget. Right !! Positioned widget allow us to control where a child of Stack is positioned, within [Stack](https://sapidlabs.com/flutter/2019/09/07/how-to-use-stack-widget-flutter.html).


So referring to our previous example, of a Stack with three colored container in it. Let's wrap them up into a `Positioned` widget.

The following code will draw three containers on the mobile screen, with green, red and indigo colored containers respectively.

```dart
...
Stack(
  children: <Widget>[
    Positioned(
      top: 20,
      left: 20,
      height: 200,
      width: 200,
      child: Container(
        width: 100,
        height: 100,
        color: Colors.green[300],
        child: Text(
          'Green',
          style: TextStyle(
            color: Colors.white,
            fontSize: 20
          ),
        ),
      ),
    ),
    Positioned(
      top: 60,
      left: 50,
      width: 200,
      height: 200,
      child: Container(
        width: 90,
        height: 90,
        color: Colors.red[400],
        child: Text(
          'Red',
          style: TextStyle(color: Colors.white, fontSize: 20),
        ),
      ),
    ),
    Positioned(
      top: 100,
      left: 75,
      width: 200,
      height: 200,
      child: Container(
        width: 80,
        height: 80,
        color: Colors.indigo[300],
        child: Text(
          'Blue',
          style: TextStyle(color: Colors.white, fontSize: 20),
        ),
      ),
    ),
  ],
),
...
```
<br />

<h1 class="light">Positioned Widget Options</h1>

1. `top` : The distance that the child's top edge.
2. `left` : The distance that the child's left edge.
3. `width` : Width of the child. ***This overrides the Child Container height if any**
4. `height` : Height of the child. ***This overrides the Child Container height if any**
5. `right` : The distance that the child's right edge.
6. `bottom` : The distance that the child's bottom edge.

<br />

### App Preview

<img src="{{ site.base_url }}/assets/images/positioned-widget-flutter/stack_positioned_app.png" class="img img-center img-50-imp" alt="stack-app-flutter">



[flutter]: /assets/images/shared/flutter-sapidlabs.jpg
