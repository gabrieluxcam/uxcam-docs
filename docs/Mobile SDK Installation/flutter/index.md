---
title: Flutter SDK Integration Guide
excerpt: Complete Flutter UXCam integration with session analytics and user insights
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
# Flutter UXCam Integration Guide

Transform your Flutter app into a data-driven product with comprehensive user session analytics, heatmaps, and behavioral insights. This guide provides a complete integration roadmap from basic setup to advanced customization.

## What Does a Successful Integration Look Like?

With a properly integrated UXCam SDK, you'll have complete visibility into user behavior across your Flutter app. From detailed session replays and screen analytics to conversion funnels and user journey mapping, you'll understand exactly how users interact with your product.

**Key Benefits:**

* **Session Recordings**: Visual replays of user interactions with Flutter widgets
* **Screen Analytics**: Heat maps and engagement metrics per screen/route
* **User Journey Analysis**: Complete flow tracking across your app's navigation
* **Privacy Compliance**: GDPR/CCPA compliant data collection with granular consent
* **Cross-Platform Insights**: Unified analytics for iOS and Android from single codebase

## Integration Complexity Assessment

**Effort Level**: Low-Medium (2-4 hours total)\
**Technical Complexity**: Low (Flutter package integration)
**Team Coordination**: Minimal (primarily mobile team)
**Risk Level**: Very Low (non-breaking additions)

### Prerequisites Checklist

* [ ] Flutter 3.10+ with Dart 3.0+
* [ ] UXCam account with app key
* [ ] iOS deployment target 12.0+ / Android minSdkVersion 21+
* [ ] Development environment configured (for debug validation)
* [ ] Network access to uxcam.com domain

### Project Type Decision Matrix

| Project Type           | Setup Approach             | Key Considerations                         | Integration Time |
| ---------------------- | -------------------------- | ------------------------------------------ | ---------------- |
| **Standard Flutter**   | Direct pub.dev integration | Full feature access, straightforward setup | 0-1 hours        |
| **Flutter + Native**   | Standard integration       | Works with platform channels, no conflicts | 2-3 hours        |
| **Complex Navigation** | Manual screen tagging      | Custom route handling may be needed        | 3-4 hours        |

### Quick Start: Only a Couple of Lines of Code

[![npm version](https://img.shields.io/pub/v/flutter_uxcam)](https://pub.dev/packages/flutter_uxcam)

Let's get you started with the basics. With just a few lines of code, you'll be on your way to capturing user sessions in your test app.

#### Flutter Integration

1. Add the UXCam package to your Flutter app:
   <Terminal>
     {`
                 $ flutter pub add flutter_uxcam
               `}
   </Terminal>

2. This will add a line similar to this to your package's pubspec.yaml file
   ```yaml
   dependencies:
     flutter_uxcam: ^x.x.x
   ```

3. Import UXCam in your app:
   ```dart
   import 'package:flutter_uxcam/flutter_uxcam.dart';
   ```

4. Initialise UXCam:\
   To ensure UXCam is properly started, it's recommended to initialize it within the initState method of a StatefulWidget. This ensures that the SDK starts as soon as the widget is created.

```dart
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
> This will complete the integration process.\
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

## Integration Verification

### Validate Your Setup

After initialization, verify UXCam is working correctly:

```dart
class UXCamValidator {
  static Future<void> validateIntegration() async {
    // Check if UXCam is recording
    final isRecording = await FlutterUxcam.isRecording();
    print('UXCam Recording Status: $isRecording');
    
    if (isRecording) {
      // Test screen tagging
      FlutterUxcam.tagScreenName('Integration Test Screen');
      
      // Test event logging
      FlutterUxcam.logEvent('integration_validated', {
        'timestamp': DateTime.now().toIso8601String(),
        'flutter_version': '3.16.0', // Your Flutter version
      });
      
      print('✅ UXCam integration validated successfully');
    } else {
      print('❌ UXCam is not recording - check your configuration');
    }
  }
}
```

### Expected Results

Within 5 minutes of running your app:

1. **Dashboard Activity**: New session appears in UXCam dashboard
2. **Screen Analytics**: Screen names show in session replay
3. **Event Tracking**: Custom events appear in session timeline
4. **Debug Logs**: Console shows UXCam initialization messages

## What's Next?

🚀 **Ready to unlock the full potential?** Continue with our comprehensive feature guides:

1. **[Screen Tagging](screen-tagging-flutter)** - Implement detailed screen analytics and navigation tracking
2. **[Privacy Protection](sensitive-data-occlusion-flutter)** - Ensure GDPR/CCPA compliance with data masking
3. **[User Analytics](users-and-properties-flutter)** - Enable user-level insights and segmentation
4. **[Event Tracking](sending-events-flutter)** - Capture business-critical user actions and conversions
5. **[Advanced Configuration](advanced-configuration-and-apis/)** - Optimize and customize for complex use cases

### Quick Links

📖 **[Changelog](flutter-sdk-changelog)** - Version history and breaking changes\
🔧 **[Troubleshooting Guide](troubleshooting-flutter)** - Common issues and solutions
⚙️ **[Advanced APIs](advanced-configuration-and-apis/)** - Recording control, crash handling, and more

### Support Resources

* **Integration Issues**: [troubleshooting-flutter](troubleshooting-flutter)
* **API Questions**: [team@uxcam.com](mailto:team@uxcam.com)
* **Feature Requests**: GitHub Issues
* **Community**: Join our developer Discord

***

*Happy analyzing with UXCam! 🎉*