---
layout: post
title: "Flutter ListGroup package"
meta: "list group flutter sapidlabs"
categories: "flutter"
tags: flutter dart
description: "Our new post is about how to create list group in flutter. list_group is a flutter package which facilitates creation of grouped list items in flutter."
keywords:
  - create grouped list flutter
  - how to group list items in flutter
  - bootstrap style list group in flutter
  - how to use list group item package in flutter
---

![Flutter - SapidLabs][flutter]

<h1 class="light">ListGroup package flutter</h1>

We are happy to announce, we have started contributing to the flutter community. Recently we released [ListGroup](https://pub.dev/packages/list_group).

<h1 class="light">ListGroup</h1>

A flutter package that enables flutter developer to use Grouped list in flutter, which looks something like:

<br />

<img src="{{ site.base_url }}/assets/images/list-group-flutter-package/list_group_preview.jpg" class="img-center img-50 max-width">

<h1 class="light">Basics of ListGroup Package flutter</h1>

Group list can be easily generated with use of two primary Widgets that list_group package provides i.e `ListGroup` and `ListGroupItem`.

<br />

#### Add dependency to pubspec.yaml

This is the very first step for using package. `pubspec.yaml` is the place where we specify dependencies required for the application. Simply, add `list_group` to the flutter dependencies.

```yaml
dependencies:
  flutter:
    sdk: flutter
  list_group:
```

Run the following command to fetch the package.
```dart
  flutter pub get
```

<br />

<h1 class="light">Example Implementation of ListGroup in flutter</h1>

```dart
import 'package:list_group/list_group.dart';
import 'package:list_group/list_group_item.dart';


ListGroup(
  items: [
    ListGroupItem(
      leading: Icon(
        FontAwesomeIcons.solidHeart,
        color: Colors.red,
      ),
      title: Text('Likes'),
      subtitle: Text('5 new', style: TextStyle(fontSize: 15),),
      trailing: Icon(Icons.chevron_right),
    ),
    ListGroupItem(
      leading: Icon(
        FontAwesomeIcons.eye,
        color: Colors.green,
      ),
      title: Text('Visitors'),
      subtitle: Text('10 recent', style: TextStyle(fontSize: 15),),
      trailing: Icon(Icons.chevron_right),
    ),
    ListGroupItem(
      leading: Icon(
        FontAwesomeIcons.userFriends,
        color: Colors.blue,
      ),
      title: Text('Followers'),
      subtitle: Text('Trusted', style: TextStyle(fontSize: 15),),
      trailing: Icon(Icons.chevron_right),
      lastItem: true,
    )
  ],
);
```

<br />

<h1 class="light">ListGroup</h1>

The list group widget expects `items` to be List of `ListGroupItem`. This is similar to `DropDownButton` and `DropDownMenuItem` structure.


<br />

<h1 class="light">ListGroupItem</h1>

This makes up the list items within this list group.

#### Options
1. `leading` : expects a widget to be placed within the list item.
2. `title` : expects a widget, usually a Text widget which displays the text in the list.
3. `subtitle` : expects a widget, usually a Text widget which is displayed below the title.
4. `trailing` : expects a widget to be placed within the list item.
5. `dense` : expects either true or false defaults to true, compacts height of list tile.
6. `enabled` : whether list item is enabled, defaults to true.
7. `lastItem` : **Important** this decides whether to add divider after the list item. defaults to false.
8. `onTap` : expects a callback method to be executed on tap of list item.
9. `onLongPress` : expects a callback method to be executed on long press of list item.

<br />

### Show Some Love <3
Do give it a star to support the [project](https://pub.dev/packages/list_group). We are working on announcing some new packages and widgets. Stay Tuned for More on Flutter !!


[flutter]: /assets/images/shared/flutter-sapidlabs.jpg
<!-- [list_group_preview]: /assets/images/list-group-flutter-package/list_group.jpg -->
