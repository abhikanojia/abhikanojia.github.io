---
layout: post
title: "How to Add Rating and Feedback using Flutter"
meta: "Adding rating and feedback in flutter"
categories: "flutter"
tags: flutter dart flutter widgets tutorial
description: "how to add Rating and feedback to Android and IOS app using Flutter"
keywords:
  - what is flutter and how to use it
  - why use flutter
  - start using flutter
  - flutter for android and ios
---

![Flutter - How to add rating and feedback to android and ios app using flutter][flutter]

<h1 class="light">For Beginners out there</h1>

[Flutter](https://flutter.dev/) is Free and Open Source project, and is developed and maintained by Google and is one of the reason why we loved Flutter. Flutter provides beautiful pre-build components which are called Widgets in flutter. Everything in flutter is a [Widget](https://flutter.dev/docs/development/ui/widgets-intro)!

<br />

<h1 class="light">How to add rating and feedback to Android and iOS APP using Flutter?</h1>
Let's get started. Adding Rating and feedback can be time take as you have to develop a custom widget in order to do so.

<br />
We got you covered!!

<br />
With [QuickFeedback](https://pub.dev/packages/quick_feedback) adding rating and feedback is quite easy.

<br />
<h1 class='light'>Follow Along</h1>

Start by adding `quick_feedback:` to `pubspec.yaml` file and run the following command.
```dart
  flutter pub get
  // This will fetch the Quick feedback package from pub.dev.
```

**Important: Restart the emulator !!**
<br />

Next step is to use it as per requirement. i.e you can either use it on the dialog or on a new screen.
For the purpose of this tutorial we will be using a single button to trigger the feedback and rating on a dialog.

Update your `build` method in `main.dart` file as follows:

```dart
@override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Example App',
      debugShowCheckedModeBanner: false,
      home: Scaffold(
        appBar: AppBar(
          title: Text('Quick Feedback'),
        ),
        body: SingleChildScrollView(
          child: Container(
            child: Center(
              child: FlatButton(
                onPressed: () => _showFeedback(context), // this will trigger the feedback modal
                child: Text('Feedback'),
              ),
            ),
          ),
        ),
      ),
    );
  }
```
<br />

Let's add the `_showFeedback` dialog.

```dart
void _showFeedback(context) {
  showDialog(
    context: context,
    builder: (context) {
      return QuickFeedback(
        title: 'Leave a feedback', // Title of dialog
        showTextBox: true, // default false
        textBoxHint:
            'Share your feedback', // Feedback text field hint text default: Tell us more
        submitText: 'SUBMIT', // submit button text default: SUBMIT
        onSubmitCallback: (feedback) {
          print('$feedback'); // map { rating: 2, feedback: 'some feedback' }
          Navigator.of(context).pop();
        },
        askLaterText: 'ASK LATER',
        onAskLaterCallback: () {
          print('Do something on ask later click');
        },
      );
    },
  );
}
```

Our final output should look something like the following:

![Flutter - How to add rating and feedback to android and ios app using flutter][feedback_output]

<br />

Find complete example [here](https://github.com/sapidlabs/quick-feedback/blob/master/example/main.dart)
Give it a try!!


[flutter]: /assets/images/shared/flutter-sapidlabs.jpg
[feedback_output]: https://raw.githubusercontent.com/sapidlabs/quick-feedback/master/screenshots/QuickFeedback.gif
