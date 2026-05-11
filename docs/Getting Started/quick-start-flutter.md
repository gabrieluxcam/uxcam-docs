---
title: Quick Start - Flutter
excerpt: Get UXCam running in your Flutter app in 5 minutes
deprecated: false
hidden: false
metadata:
  title: 'Flutter Quick Start - UXCam'
  description: 'Fastest way to add UXCam session recording to your Flutter app'
  robots: index
next:
  description: ''
  pages:
    - type: basic
      slug: flutter
      title: Full Flutter Guide
---

# Flutter Quick Start

Get session recording working in your Flutter app in under 5 minutes.

## Prerequisites

<Tabs>
<Tab title="Mobile">

- Flutter SDK
- iOS deployment target 12.0+ / Android minSdkVersion 21+
- A [UXCam account](https://app.uxcam.com/signup) with a mobile app key

</Tab>
<Tab title="Web">

- Flutter SDK
- A [UXCam account](https://app.uxcam.com/signup) with a web app key

</Tab>
</Tabs>

---

## Step 1: Add the Package

```bash
flutter pub add flutter_uxcam
```

This adds to your `pubspec.yaml`:

```yaml
dependencies:
  flutter_uxcam: ^x.x.x
```

---

## Step 2: Initialize UXCam

In your main widget's `initState`:

```dart
import 'package:flutter/foundation.dart';
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

    // Use the web app key on Flutter Web, mobile app key on iOS/Android.
    FlutterUxConfig config;
    if (kIsWeb) {
      config = FlutterUxConfig(
        userAppKey: "UXCAM_WEB_APP_KEY",
      );
    } else {
      FlutterUxcam.optIntoVideoRecordings();
      config = FlutterUxConfig(
        userAppKey: "UXCAM_MOBILE_APP_KEY",
        enableAutomaticScreenNameTagging: false,
      );
    }
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

---

## Step 3: Verify It Works

<Tabs>
<Tab title="Mobile">

1. Run your app on a device or emulator
2. Navigate through a few screens
3. Send the app to background
4. Check your [UXCam Dashboard](https://app.uxcam.com) — your session should appear within 30 seconds

<GitHubCallout type="tip">Check Android Studio or Xcode logs for UXCam initialization messages.</GitHubCallout>

</Tab>
<Tab title="Web">

1. Open the site in a browser
2. Check for the `[UXCam] connected successfully` message in the browser console
3. Close the tab
4. Check your [UXCam Dashboard](https://app.uxcam.com) - your session should appear within 5-10 minutes

</Tab>
</Tabs>

---

## Validation Helper (Mobile only)

Optionally verify your integration:

```dart
Future<void> validateIntegration() async {
  final isRecording = await FlutterUxcam.isRecording();
  print('UXCam Recording: $isRecording');

  if (isRecording) {
    FlutterUxcam.tagScreenName('Test Screen');
    FlutterUxcam.logEvent('integration_test');
    print('UXCam integration validated');
  }
}
```

---

## Next Steps

You're recording sessions! Now customize your integration:

<Cards columns={2}>
  <Card title="Full Flutter Guide" href="/docs/flutter" icon="fa-solid fa-feather">
    Complete setup with all configuration options
  </Card>

  <Card title="Tag Screens" href="/docs/screen-tagging-flutter" icon="fa-solid fa-mobile">
    Enable heatmaps and screen analytics
  </Card>

  <Card title="Mask Sensitive Data" href="/docs/sensitive-data-occlusion-flutter" icon="fa-solid fa-eye-slash">
    Protect passwords and PII
  </Card>

  <Card title="Track Events" href="/docs/flutter/events" icon="fa-solid fa-bolt">
    Capture custom user actions
  </Card>
</Cards>
