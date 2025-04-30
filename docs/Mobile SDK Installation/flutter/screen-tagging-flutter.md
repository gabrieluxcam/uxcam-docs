---
title: Screen Tagging
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    The document provides guidance on manually tagging screens in a Flutter app
    for accurate analytics, emphasizing the importance of disabling automatic
    tagging and using lifecycle methods like `initState()` for consistent screen
    identification. It also introduces the `FlutterUxcamNavigatorObserver` for
    automatic route capture, with considerations for navigation events and
    consistent naming to enhance data analysis.
  robots: index
next:
  description: ''
---
So you've integrated the SDK and have some sessions going already, good! Now it's time to dig into screens—what they are, how to tag them, and why it matters for understanding user behaviour in your app.

# Manual Screen Tagging - For In Depth Customisation

While native frameworks may offer automatic tagging, in Flutter, the architecture requires manual tagging to ensure all screens are clearly and consistently identified in your analytics. This approach gives you full control over how each screen is represented, leading to more accurate and meaningful insights.

> **IMPORTANT**: Please make sure to set `enableAutomaticScreenNameTagging`: **`false`** for better results

**Tagging a Screen Manually: One Simple Line of Code:**

In Flutter, tagging a screen manually is straightforward. You can use the following line of code to tag a screen:

```coffeescript Flutter
import 'package:flutter_uxcam/flutter_uxcam.dart';

UXCam.tagScreenName("Home Screen");
```

**Example**: **The Recommended Place to Tag**

To ensure your screens are tagged consistently, we recommend placing the tagging code inside the initState() method of your widget or using a navigation listener to call the screen tagging method every time the screen appears.

```coffeescript Flutter
import 'package:flutter/material.dart';
import 'package:flutter_uxcam/flutter_uxcam.dart';

class HomeScreen extends StatefulWidget {
  @override
  _HomeScreenState createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  @override
  void initState() {
    super.initState();
    UXCam.tagScreenName("Home Screen");
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Home Screen"),
      ),
      body: Center(
        child: Text("Welcome to the Home Screen!"),
      ),
    );
  }
}
```

This approach ensures that every time a user navigates back to this screen, it's correctly tagged in your analytics.

> 👍 Summary:
>
> * **Automatic Tagging Should be Off**: Due to how Flutter manages navigation, automatic tagging may not always be reliable.
> * **Manual Tagging**: Use lifecycle methods like initState() or navigation listeners to tag screens accurately every time they are shown.

By taking this approach, you can make data-driven decisions and gain actionable insights into how users navigate through your app.

# Flutter Automatic Screen Tagging:

> 🚧 Important:
>
> Flutter SDK version 2.2.2 introduces the option to add tagging from the Flutter side of your app. This feature is optional and still under active development, so any feedback during testing is highly appreciated.
>
> Note: This feature currently does not support Bottom Navigation or Tab Navigation for tagging when using Navigator 1.0 or 2.0. However, ongoing work on Navigator 2.0 aims to address these issues in the future.

## Adding `FlutterUxcamNavigatorObserver` to Your App:

From FlutterUXCam SDK version 2.2.2, you can add a `FlutterUxcamNavigatorObserver` to your app, enabling automatic capture of your routes (screens) for screen tagging without requiring manual intervention.

### Steps to Integrate FlutterUxcamNavigatorObserver:

1. **Import the SDK:**\
   In your Dart file, import the `flutter_uxcam` package:\
   `import 'package:flutter_uxcam/flutter_uxcam.dart';`
2. **Disable Automatic Screen Name Tagging**\
   Ensure the configuration parameter `enableAutomaticScreenNameTagging` is set to `false`.
3. **Add the Navigation Observer**
   * **Using Navigator 1.0:**\ <br />\
     Add the `FlutterUxcamNavigatorObserver` to the `navigatorObservers` of your \`MaterialApp
     ```coffeescript Flutter
     MaterialApp(
       ...
       navigatorObservers: [
         FlutterUxcamNavigatorObserver(),
       ],
       ...
     )
     ```
   * **Using Navigator 2.0:**\ <br />\
     Add it to the observers for your router. Below is an example using GoRouter:
     ```coffeescript Flutter
     final GoRouter router = GoRouter(
       ...
       observers: [
         FlutterUxcamNavigatorObserver(),
       ],
       ...
     );
     ```
4. **Add Names to Your Routes**\ <br />\
   It is recommended to add names to your routes to ensure clear tagging. If no names are provided, routes may show up as default values such as '/'.

### Additional Considerations for Flutter

**Navigation Events**: If you're using a navigation package like flutter\_bloc or provider, ensure that screen tagging is part of the navigation logic. This guarantees that every screen transition is tracked properly.

**Consistent Naming**: Be consistent with your screen names across the app. This will make analyzing data in the UXCam dashboard more intuitive and prevent confusion.

<br />

## How to Tag WebViews

WebViews can be a unique challenge when it comes to screen tagging, as they often contain dynamic content. To properly tag your WebView screens, you can follow our detailed guide that walks you through the best practices and methods for effective WebView tagging.

For more information, click the button below:

[Tagging WebViews Documentation](/docs/web-view-tagging)

## Next Steps ➡️

You've already set up one of the core UXCam features for tracking user sessions and tagging screens. Now let's dive into protecting sensitive information to ensure your users' privacy is safeguarded while you gain insights.
