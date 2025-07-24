---
title: Flutter Troubleshooting Guide
excerpt: Common issues and solutions for UXCam Flutter SDK integration
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---

# Flutter Troubleshooting Guide

This guide covers common issues encountered when integrating UXCam with Flutter applications and their solutions.

## Quick Diagnostics Checklist

Before diving into specific issues, verify these basics:

- [ ] Flutter SDK version 3.10+ is installed
- [ ] UXCam Flutter package is latest version (`flutter pub outdated`)
- [ ] Valid UXCam app key is configured
- [ ] Internet connectivity is available
- [ ] App permissions are granted (if required)

---

## Installation & Setup Issues

### "Package not found" or Dependency Resolution Errors

**Symptoms:**
```bash
Because myapp depends on flutter_uxcam which doesn't exist, version solving failed.
```

**Solutions:**
1. **Check package name**: Ensure you're using `flutter_uxcam` (not `uxcam_flutter`)
```yaml
dependencies:
  flutter_uxcam: ^2.6.1  # Correct package name
```

2. **Update pub cache**:
```bash
flutter pub cache repair
flutter pub get
```

3. **Check Flutter version compatibility**:
```bash
flutter --version  # Must be 3.10+
```

### Build Failures After Adding UXCam

**Android Build Issues:**

**Symptoms:**
```
FAILURE: Build failed with an exception.
* What went wrong: Execution failed for task ':app:checkDebugDuplicateClasses'.
```

**Solutions:**
1. **Update Android Gradle Plugin** in `android/build.gradle`:
```gradle
dependencies {
    classpath 'com.android.tools.build:gradle:8.0.0'  // Update to 8.0+
}
```

2. **Set minimum SDK version** in `android/app/build.gradle`:
```gradle
android {
    defaultConfig {
        minSdkVersion 21  // UXCam requires API 21+
    }
}
```

**iOS Build Issues:**

**Symptoms:**
```
ld: library not found for -lflutter_uxcam
```

**Solutions:**
1. **Clean and rebuild**:
```bash
cd ios
rm -rf Pods/ Podfile.lock
cd ..
flutter clean
flutter pub get
cd ios
pod install
```

2. **Update iOS deployment target** in `ios/Podfile`:
```ruby
platform :ios, '12.0'  # UXCam requires iOS 12.0+
```

---

## Runtime Issues

### Sessions Not Appearing in Dashboard

**Symptoms:**
- App seems to work normally
- No sessions visible in UXCam dashboard after 5+ minutes
- "Waiting for data" message persists

**Debugging Steps:**

1. **Enable debug logging**:
```dart
import 'package:flutter_uxcam/flutter_uxcam.dart';

FlutterUxConfig config = FlutterUxConfig(
  userAppKey: "YOUR_APP_KEY",
  enableAutomaticScreenNameTagging: true,
  enableImprovedScreenCapture: true,
);

// Enable debug logs
FlutterUxcam.startWithConfiguration(config);
```

2. **Check logs in console**:
   - **Android**: Use `adb logcat | grep UXCam`
   - **iOS**: Use Xcode console or `flutter logs`

3. **Verify app key**:
```dart
// Double-check your app key matches the dashboard
print("UXCam App Key: ${config.userAppKey}");
```

**Common Solutions:**

**Wrong App Key:**
- Verify the key matches your UXCam project
- Ensure no extra spaces or characters
- Use test app key for development, production key for release

**Network Issues:**
```dart
// Test network connectivity
import 'dart:io';

try {
  final result = await InternetAddress.lookup('uxcam.com');
  print('Network connection: OK');
} catch (e) {
  print('Network connection: Failed - $e');
}
```

**App Lifecycle Issues:**
```dart
class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> with WidgetsBindingObserver {
  @override
  void initState() {
    super.initState();
    WidgetsBinding.instance.addObserver(this);
    // Initialize UXCam here
  }

  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    // Ensure session is uploaded when app goes to background
    if (state == AppLifecycleState.paused) {
      print('App backgrounded - session should upload');
    }
  }
}
```

### Hot Reload Issues

**Symptoms:**
- UXCam stops working after hot reload
- Configuration changes not taking effect
- Duplicate sessions or crashes

**Solutions:**

1. **Restart app after config changes**:
```dart
// Changes to UXCam configuration require full restart
FlutterUxConfig config = FlutterUxConfig(
  userAppKey: "YOUR_APP_KEY",
  enableAutomaticScreenNameTagging: false,  // Changed this
);
```

2. **Guard against reinitializing**:
```dart
class _MyAppState extends State<MyApp> {
  static bool _uxcamInitialized = false;

  @override
  void initState() {
    super.initState();
    if (!_uxcamInitialized) {
      _initializeUXCam();
      _uxcamInitialized = true;
    }
  }
}
```

---

## Screen Tagging Issues

### Screens Not Being Tagged

**Symptoms:**
- Sessions recorded but no screen names visible
- All screens show as "Unknown" or default names

**Solutions:**

1. **Enable automatic screen tagging**:
```dart
FlutterUxConfig config = FlutterUxConfig(
  userAppKey: "YOUR_APP_KEY",
  enableAutomaticScreenNameTagging: true,  // Enable this
);
```

2. **Manual screen tagging for named routes**:
```dart
// In your MaterialApp
MaterialApp(
  onGenerateRoute: (settings) {
    // Tag screen based on route name
    FlutterUxcam.tagScreenName(settings.name ?? 'Unknown');
    
    switch (settings.name) {
      case '/home':
        return MaterialPageRoute(builder: (_) => HomeScreen());
      case '/profile':
        return MaterialPageRoute(builder: (_) => ProfileScreen());
    }
  },
)
```

3. **Widget-level screen tagging**:
```dart
class HomeScreen extends StatefulWidget {
  @override
  _HomeScreenState createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  @override
  void initState() {
    super.initState();
    // Tag screen manually
    FlutterUxcam.tagScreenName('Home Screen');
  }
}
```

---

## Performance Issues

### App Startup Slowdown

**Symptoms:**
- Noticeable delay in app startup after adding UXCam
- ANR (Android) or watchdog timeout (iOS)

**Solutions:**

1. **Async initialization**:
```dart
class _MyAppState extends State<MyApp> {
  bool _uxcamReady = false;

  @override
  void initState() {
    super.initState();
    _initializeUXCamAsync();
  }

  Future<void> _initializeUXCamAsync() async {
    // Initialize UXCam after first frame
    WidgetsBinding.instance.addPostFrameCallback((_) async {
      FlutterUxConfig config = FlutterUxConfig(
        userAppKey: "YOUR_APP_KEY",
      );
      await FlutterUxcam.startWithConfiguration(config);
      setState(() {
        _uxcamReady = true;
      });
    });
  }
}
```

2. **Reduce capture quality for performance**:
```dart
FlutterUxConfig config = FlutterUxConfig(
  userAppKey: "YOUR_APP_KEY",
  enableImprovedScreenCapture: false,  // Disable for performance
);
```

### Memory Usage Issues

**Symptoms:**
- App crashes with out-of-memory errors
- Increased memory usage visible in profiler

**Solutions:**

1. **Limit session length**:
```dart
// Stop recording after certain duration
Timer(Duration(minutes: 30), () {
  FlutterUxcam.stopSessionAndUploadData();
});
```

2. **Implement session rotation**:
```dart
class SessionManager {
  static int _sessionCount = 0;
  
  static void rotateSession() {
    _sessionCount++;
    if (_sessionCount % 5 == 0) {
      // Start new session every 5th session
      FlutterUxcam.stopSessionAndUploadData();
      FlutterUxcam.startWithConfiguration(config);
    }
  }
}
```

---

## Platform-Specific Issues

### Android Specific

**ProGuard/R8 Issues:**
Add to `android/app/proguard-rules.pro`:
```proguard
-keep class com.uxcam.** { *; }
-dontwarn com.uxcam.**
```

**Network Security Config:**
Add to `android/app/src/main/res/xml/network_security_config.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <domain-config cleartextTrafficPermitted="false">
        <domain includeSubdomains="true">uxcam.com</domain>
    </domain-config>
</network-security-config>
```

### iOS Specific

**App Transport Security:**
Add to `ios/Runner/Info.plist`:
```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSExceptionDomains</key>
    <dict>
        <key>uxcam.com</key>
        <dict>
            <key>NSExceptionRequiresForwardSecrecy</key>
            <false/>
        </dict>
    </dict>
</dict>
```

**Archive/Distribution Issues:**
1. Clean build folder: Product → Clean Build Folder
2. Update signing certificates
3. Ensure UXCam framework is properly embedded

---

## Getting Additional Help

### Debug Information to Collect

When contacting support, include:

1. **Environment Info**:
```bash
flutter doctor -v
flutter --version
```

2. **Package Info**:
```bash
flutter pub deps
```

3. **UXCam Logs**:
   - Enable debug logging
   - Copy relevant log entries
   - Include crash logs if available

4. **Minimal Reproduction**:
   - Create simple test app demonstrating the issue
   - Include relevant code snippets

### Contact Support

- **Email**: [team@uxcam.com](mailto:team@uxcam.com)
- **Documentation**: [Flutter Integration Guide](index.md)
- **Changelog**: [Version History](flutter-sdk-changelog.md)

### Community Resources

- **GitHub Issues**: Report bugs and feature requests
- **Stack Overflow**: Tag questions with `flutter-uxcam`
- **Discord/Slack**: Join UXCam developer community

---

*Last updated: December 2024*