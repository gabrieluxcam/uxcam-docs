---
title: Flutter
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
So you've got your account set up, now let’s make sure your Flutter app is equipped with the insights that UXCam can provide. This guide will take you through the first steps of integrating UXCam, sending your first session, and setting up key features. Our goal? A successful integration that sets you up for product-led growth, better usability insights, and happier users.

## What Does a Successful Integration Look Like?

With a solid integration, you’ll have a complete picture of how users interact with your app. From screen journeys and user behaviours to session replays and user properties, you’ll be able to understand and enhance every aspect of your product’s user experience. Follow along with this quick guide, and you’ll be up and running in no time.

### Quick Start: Only a Couple of Lines of Code

[![npm version](https://img.shields.io/pub/v/flutter_uxcam)](https://pub.dev/packages/flutter_uxcam)

Let's get you started with the basics. With just a few lines of code, you'll be on your way to capturing user sessions in your test app.

#### Flutter Integration

1. Add the UXCam package to your Flutter app:
   <pre><code class="language-objectivec">flutter pub add flutter_uxcam</code></pre>

2. This will add a line similar to this to your package's pubspec.yaml file
   <pre><code class="language-objectivec">dependencies:
     flutter_uxcam: ^x.x.x
   </code></pre>

3. Import UXCam in your app:
   <pre><code class="language-objectivec">import 'package:flutter_uxcam/flutter_uxcam.dart';</code></pre>

4. Initialise UXCam:  
   To ensure UXCam is properly started, it's recommended to initialize it within the initState method of a StatefulWidget. This ensures that the SDK starts as soon as the widget is created.

```coffeescript Flutter
import 'package:flutter/material.dart';
import 'package:flutter_uxcam/flutter_uxcam.dart';

void main() => runApp(MyApp());

class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  @override
  void initState() {
    super.initState();
    FlutterUxcam.optIntoSchematicRecordings();
    FlutterUxConfig config = FlutterUxConfig(
      userAppKey: "UXCAM_APP_KEY",
      enableAutomaticScreenNameTagging: false,
    );
    FlutterUxcam.startWithConfiguration(config);
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: MyHomePage(title: 'Flutter Demo Home Page'),
    );
  }
}
```

> 👍 As Simple As That!
>
> This will complete the integration process.  
> Your session will be shown on the dashboard within a few seconds after the app goes in the background.
>
> We recommend that after you've set this up and have reviewed some sessions from your tests, get to the customisation features UXCam offers, let's go to the next steps!

> ❗️ Troubleshooting Data Delays
>
> If you have successfully integrated the Flutter SDK but are still seeing "Waiting for data to arrive" in your dashboard, here are some common causes and solutions:
>
> * App State: Ensure the app is sent to the background and not fully closed. Data transmission may not occur if the app is force-closed before the session is uploaded.
> * SDK Version: Verify you are using the latest version of the UXCam Flutter SDK to avoid compatibility issues. You can check the changelog in our developer docs to verify the latest version.
> * Check Logs: Use Android Studio or Xcode to check for any errors related to UXCam in your app logs.

## Next Steps ➡️

You’ve successfully integrated UXCam and sent some sessions, great job! 🎉 But there's so much more you can do. Now, let’s go further into setting things up.