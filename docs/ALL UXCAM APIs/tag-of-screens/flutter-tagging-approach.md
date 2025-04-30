---
title: Flutter tagging approach
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: noindex
next:
  description: ''
---
### Flutter Tagging Approach:

> 🚧 Important:
>
> **Flutter SDK version 2.2.2** enables the option to add tagging from the Flutter side of your app. This is an optional feature that will be still improved upon, so please do provide any feedback you may have when testing. 
>
> This feature **does not currently support Bottom Navigation or Tab Navigation** for the tagging if using Navigator 1.0 or 2.0, however there is ongoing work on Navigator 2.0 that will tackle similar issues in the near future.

From the FlutterUXCam SDK version 2.2.2 you'll have now the option to add a FlutterUxcamNavigatorObserver to your app, which will enable capturing your routes (screens) for screen Tagging without having to manually tag them.

#### Follow the below steps to add this to your app:

1. Inside your dart file import <strong>`flutter_uxcam`</strong> like this:

```go Flutter
import 'package:flutter_uxcam/flutter_uxcam.dart';
```

2. Make sure the config parameter **`enableAutomaticScreenNameTagging`** is set as **`false`**
3. Add the observer:

* If using Navigator 1.0:

```go Flutter
MaterialApp(
   ...
   navigatorObservers: [
     FlutterUxcamNavigatorObserver(), 
   ],
   ...
 )
```

* If using Navigator 2.0: 

Add it to the observers for your Router.

*<span style={{ fontSize: "0.9em" }}>(The below example is using GoRouter)</span>*

```go Flutter
final GoRouter router = GoRouter(
    ...
 observers: [
    FlutterUxcamNavigatorObserver(),
 ],
    ...
)
```

4. It is suggested you add names to your routes, otherwise some routes may show as default, i.e `'/ '`.

```swift Flutter
GoRoute(
  path: `{routeName}`,
  name: `{Preferred Route Name}`,
  builder: (BuildContext context, GoRouterState state) {
    return ScreenName(key: state.pageKey);
  },
),
```