---
title: Troubleshooting FAQs
excerpt: Solutions to common UXCam Flutter SDK integration issues
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---

# Troubleshooting FAQs

Quick solutions to the most common UXCam Flutter SDK integration issues, organized by category for easy reference.

## Installation Issues

### Q: "Package flutter_uxcam does not exist"

**Symptoms:**
```bash
Because myapp depends on flutter_uxcam which doesn't exist, version solving failed.
```

**Solutions:**
1. **Verify package name** - Ensure you're using the correct package name:
   ```yaml
   dependencies:
     flutter_uxcam: ^2.6.1  # Correct package name
   ```

2. **Check pub.dev availability**:
   ```bash
   flutter pub deps --no-precompile
   flutter pub cache repair
   ```

3. **Update Flutter and try again**:
   ```bash
   flutter upgrade
   flutter pub get
   ```

### Q: Build fails after adding UXCam dependency

**Android Build Errors:**

**Error**: `Duplicate class found`
```
Execution failed for task ':app:checkDebugDuplicateClasses'.
```

**Solution**: Update your `android/build.gradle`:
```gradle
android {
    compileSdkVersion 34
    
    defaultConfig {
        minSdkVersion 21  // UXCam requires API 21+
        targetSdkVersion 34
    }
}

dependencies {
    classpath 'com.android.tools.build:gradle:8.0.0'  // Use latest
}
```

**iOS Build Errors:**

**Error**: `library not found for -lflutter_uxcam`

**Solution**: Clean and rebuild iOS:
```bash
cd ios
rm -rf Pods/ Podfile.lock
cd ..
flutter clean
flutter pub get
cd ios
pod install --repo-update
```

---

## Recording Issues

### Q: Sessions not appearing in UXCam dashboard

**Symptoms:**
- App runs normally
- No sessions visible after 5+ minutes
- "Waiting for data" message persists

**Diagnostic Steps:**

1. **Verify app key**:
   ```dart
   // Double-check your app key
   FlutterUxConfig config = FlutterUxConfig(
     userAppKey: "YOUR_ACTUAL_APP_KEY", // Verify this is correct
   );
   ```

2. **Check recording status**:
   ```dart
   final isRecording = await FlutterUxcam.isRecording();
   print('UXCam recording status: $isRecording');
   ```

3. **Enable debug logging** and check console output:
   ```dart
   // Look for UXCam logs in your debug console
   FlutterUxcam.startWithConfiguration(config);
   ```

4. **Test network connectivity**:
   ```dart
   import 'dart:io';
   
   try {
     final result = await InternetAddress.lookup('uxcam.com');
     print('Network: OK');
   } catch (e) {
     print('Network: Failed - $e');
   }
   ```

**Common Solutions:**

- **Wrong app key**: Verify the key matches your UXCam project
- **Network blocking**: Check firewall/proxy settings
- **App lifecycle**: Ensure app goes to background (don't force-close)
- **Development vs Production**: Use correct app key for environment

### Q: Recording stops working after hot reload

**Symptoms:**
- UXCam works initially
- Stops recording after hot reload
- Configuration changes don't take effect

**Solutions:**

1. **Restart app after configuration changes**:
   ```dart
   // Any UXCam config changes require full app restart
   FlutterUxConfig config = FlutterUxConfig(
     userAppKey: "YOUR_APP_KEY",
     enableAutomaticScreenNameTagging: false, // Changed
   );
   ```

2. **Prevent duplicate initialization**:
   ```dart
   class UXCamManager {
     static bool _initialized = false;
     
     static Future<void> initialize() async {
       if (_initialized) return;
       
       FlutterUxConfig config = FlutterUxConfig(
         userAppKey: "YOUR_APP_KEY",
       );
       
       await FlutterUxcam.startWithConfiguration(config);
       _initialized = true;
     }
   }
   ```

---

## Screen Tagging Issues

### Q: Screen names not appearing in dashboard

**Symptoms:**
- Sessions recorded successfully
- All screens show as "Unknown" or generic names
- No screen analytics available

**Solutions:**

1. **Enable automatic screen tagging**:
   ```dart
   FlutterUxConfig config = FlutterUxConfig(
     userAppKey: "YOUR_APP_KEY",
     enableAutomaticScreenNameTagging: true, // Ensure this is true
   );
   ```

2. **Manual screen tagging for complex navigation**:
   ```dart
   class MyScreen extends StatefulWidget {
     @override
     _MyScreenState createState() => _MyScreenState();
   }
   
   class _MyScreenState extends State<MyScreen> {
     @override
     void initState() {
       super.initState();
       // Tag screen manually
       FlutterUxcam.tagScreenName('My Screen');
     }
   }
   ```

3. **Route-based tagging**:
   ```dart
   MaterialApp(
     onGenerateRoute: (settings) {
       // Tag based on route name
       final screenName = settings.name ?? 'Unknown';
       FlutterUxcam.tagScreenName(screenName);
       
       // Return your page widget
       return MaterialPageRoute(builder: (_) => MyPage());
     },
   )
   ```

### Q: Screen names are generic (like "MaterialPageRoute")

**Solution**: Use explicit screen tagging:
```dart
class ScreenTaggingNavigatorObserver extends NavigatorObserver {
  @override
  void didPush(Route<dynamic> route, Route<dynamic>? previousRoute) {
    super.didPush(route, previousRoute);
    
    String screenName = 'Unknown';
    
    if (route.settings.name != null) {
      screenName = route.settings.name!;
    } else if (route is PageRoute) {
      // Extract screen name from route type or arguments
      screenName = route.runtimeType.toString();
    }
    
    FlutterUxcam.tagScreenName(screenName);
  }
}

// Add to MaterialApp
MaterialApp(
  navigatorObservers: [ScreenTaggingNavigatorObserver()],
  // ... rest of your app
)
```

---

## Performance Issues

### Q: App startup is slow after adding UXCam

**Symptoms:**
- Noticeable delay in app startup
- ANR (Android) or watchdog timeout (iOS)
- Users complain about app slowness

**Solutions:**

1. **Async initialization**:
   ```dart
   class _MyAppState extends State<MyApp> {
     @override
     void initState() {
       super.initState();
       // Initialize UXCam after first frame
       WidgetsBinding.instance.addPostFrameCallback((_) {
         _initializeUXCam();
       });
     }
     
     Future<void> _initializeUXCam() async {
       FlutterUxConfig config = FlutterUxConfig(
         userAppKey: "YOUR_APP_KEY",
       );
       await FlutterUxcam.startWithConfiguration(config);
     }
   }
   ```

2. **Reduce capture quality for low-end devices**:
   ```dart
   FlutterUxConfig config = FlutterUxConfig(
     userAppKey: "YOUR_APP_KEY",
     enableImprovedScreenCapture: false, // Disable for performance
   );
   ```

3. **Conditional initialization based on device capability**:
   ```dart
   Future<void> initializeBasedOnDevice() async {
     final deviceInfo = await DeviceInfoPlugin().androidInfo;
     final isLowEndDevice = deviceInfo.version.sdkInt < 26;
     
     FlutterUxConfig config = FlutterUxConfig(
       userAppKey: "YOUR_APP_KEY",
       enableAutomaticScreenNameTagging: !isLowEndDevice,
       enableImprovedScreenCapture: !isLowEndDevice,
     );
     
     await FlutterUxcam.startWithConfiguration(config);
   }
   ```

### Q: High memory usage or crashes

**Symptoms:**
- App crashes with out-of-memory errors
- Memory usage grows continuously
- Performance degradation over time

**Solutions:**

1. **Implement session rotation**:
   ```dart
   class SessionRotationManager {
     static Timer? _rotationTimer;
     static const Duration ROTATION_INTERVAL = Duration(minutes: 30);
     
     static void startRotation(String appKey) {
       _rotationTimer = Timer.periodic(ROTATION_INTERVAL, (timer) async {
         await FlutterUxcam.stopSessionAndUploadData();
         
         // Wait before starting new session
         await Future.delayed(Duration(seconds: 2));
         
         FlutterUxConfig config = FlutterUxConfig(userAppKey: appKey);
         await FlutterUxcam.startWithConfiguration(config);
       });
     }
     
     static void stopRotation() {
       _rotationTimer?.cancel();
     }
   }
   ```

2. **Monitor memory usage**:
   ```dart
   class MemoryMonitor {
     static Timer? _monitorTimer;
     
     static void startMonitoring() {
       _monitorTimer = Timer.periodic(Duration(minutes: 5), (timer) {
         // Check memory usage and rotate session if needed
         _checkMemoryAndRotate();
       });
     }
     
     static void _checkMemoryAndRotate() async {
       // Platform-specific memory check implementation
       // Rotate session if memory usage is high
     }
   }
   ```

---

## Platform-Specific Issues

### Android Issues

**Q: ProGuard/R8 obfuscation breaks UXCam**

**Solution**: Add ProGuard rules in `android/app/proguard-rules.pro`:
```proguard
-keep class com.uxcam.** { *; }
-dontwarn com.uxcam.**
-keepattributes Signature
-keepattributes *Annotation*
```

**Q: Network security config blocks UXCam**

**Solution**: Update `android/app/src/main/res/xml/network_security_config.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <domain-config cleartextTrafficPermitted="false">
        <domain includeSubdomains="true">uxcam.com</domain>
        <trust-anchors>
            <certificates src="system"/>
        </trust-anchors>
    </domain-config>
</network-security-config>
```

### iOS Issues

**Q: App Store submission fails**

**Symptoms:**
- Build archives successfully
- Upload to App Store Connect fails
- Bitcode or framework issues

**Solutions:**

1. **Update iOS deployment target** in `ios/Podfile`:
   ```ruby
   platform :ios, '12.0'  # UXCam requires iOS 12.0+
   ```

2. **Clean and rebuild**:
   ```bash
   cd ios
   rm -rf build/ Pods/ Podfile.lock
   cd ..
   flutter clean
   flutter pub get
   cd ios
   pod install
   ```

3. **Check framework embedding** in Xcode:
   - Open `ios/Runner.xcworkspace`
   - Check that UXCam framework is properly embedded
   - Verify signing configuration

**Q: App crashes on iOS 17+**

**Solution**: Ensure you're using the latest UXCam SDK version:
```yaml
dependencies:
  flutter_uxcam: ^2.6.1  # Use latest version
```

---

## Configuration Issues

### Q: Custom events not appearing

**Symptoms:**
- Events logged in code
- No events visible in UXCam dashboard
- Event tracking seems broken

**Diagnostic Code:**
```dart
// Test event logging
void testEventLogging() {
  final testEvent = 'test_event_${DateTime.now().millisecondsSinceEpoch}';
  final properties = {
    'test_property': 'test_value',
    'timestamp': DateTime.now().toIso8601String(),
  };
  
  FlutterUxcam.logEvent(testEvent, properties);
  print('Test event logged: $testEvent');
}
```

**Solutions:**

1. **Check event name format**:
   ```dart
   // Valid event names
   FlutterUxcam.logEvent('user_signup', properties); ✅
   FlutterUxcam.logEvent('User Signup', properties); ✅
   
   // Avoid special characters
   FlutterUxcam.logEvent('user@signup!', properties); ❌
   ```

2. **Verify properties format**:
   ```dart
   // Valid properties
   final properties = {
     'string_prop': 'value',
     'number_prop': 42,
     'boolean_prop': true,
   };
   
   // Avoid complex objects
   final invalidProperties = {
     'object_prop': MyComplexObject(), // ❌
   };
   ```

3. **Ensure recording is active**:
   ```dart
   final isRecording = await FlutterUxcam.isRecording();
   if (isRecording) {
     FlutterUxcam.logEvent('my_event', properties);
   } else {
     print('Warning: UXCam not recording, event will be lost');
   }
   ```

### Q: User properties not updating

**Solution**: Verify user identity is set first:
```dart
// Set user identity before properties
FlutterUxcam.setUserIdentity('user123');

// Then set properties
FlutterUxcam.setUserProperties({
  'subscription_tier': 'premium',
  'app_version': '1.2.3',
});
```

---

## Debug and Testing

### Q: How to verify UXCam is working correctly?

**Complete Integration Test:**
```dart
class UXCamIntegrationTest {
  static Future<void> runTest() async {
    print('=== UXCam Integration Test ===');
    
    // 1. Check initialization
    final isRecording = await FlutterUxcam.isRecording();
    print('Recording Status: $isRecording');
    
    // 2. Test screen tagging
    FlutterUxcam.tagScreenName('Test Screen');
    print('Screen tagged: Test Screen');
    
    // 3. Test event logging
    FlutterUxcam.logEvent('test_event', {
      'test_property': 'test_value',
      'timestamp': DateTime.now().toIso8601String(),
    });
    print('Test event logged');
    
    // 4. Test user properties
    FlutterUxcam.setUserIdentity('test_user');
    FlutterUxcam.setUserProperties({
      'test_property': 'test_value',
    });
    print('User properties set');
    
    // 5. Test recording control
    await FlutterUxcam.pauseScreenRecording();
    await Future.delayed(Duration(seconds: 1));
    await FlutterUxcam.resumeScreenRecording();
    print('Recording pause/resume tested');
    
    print('=== Test Complete ===');
  }
}

// Run in your debug build
void main() {
  runApp(MyApp());
  
  if (kDebugMode) {
    // Run test after app initialization
    WidgetsBinding.instance.addPostFrameCallback((_) {
      UXCamIntegrationTest.runTest();
    });
  }
}
```

### Q: How to export debug information for support?

**Debug Information Exporter:**
```dart
class DebugExporter {
  static Future<String> generateDebugReport() async {
    final buffer = StringBuffer();
    
    // App information
    final packageInfo = await PackageInfo.fromPlatform();
    buffer.writeln('App Version: ${packageInfo.version}');
    buffer.writeln('Build Number: ${packageInfo.buildNumber}');
    
    // Device information
    final deviceInfo = DeviceInfoPlugin();
    if (Platform.isAndroid) {
      final androidInfo = await deviceInfo.androidInfo;
      buffer.writeln('Device: ${androidInfo.brand} ${androidInfo.model}');
      buffer.writeln('Android: ${androidInfo.version.release}');
    }
    
    // UXCam status
    final isRecording = await FlutterUxcam.isRecording();
    buffer.writeln('UXCam Recording: $isRecording');
    
    // Network connectivity
    try {
      await InternetAddress.lookup('uxcam.com');
      buffer.writeln('Network: Connected');
    } catch (e) {
      buffer.writeln('Network: Error - $e');
    }
    
    return buffer.toString();
  }
  
  static Future<void> exportAndShare() async {
    final report = await generateDebugReport();
    
    // Save to file or share via email
    final subject = 'UXCam Flutter Debug Report';
    final uri = Uri(
      scheme: 'mailto',
      path: 'team@uxcam.com',
      queryParameters: {
        'subject': subject,
        'body': report,
      },
    );
    
    if (await canLaunchUrl(uri)) {
      await launchUrl(uri);
    }
  }
}
```

---

## Getting Additional Help

### Before Contacting Support

Run this diagnostic checklist:

```dart
class DiagnosticChecklist {
  static Future<void> runDiagnostics() async {
    print('=== UXCam Diagnostic Checklist ===');
    
    // 1. SDK Version
    print('✓ Using latest flutter_uxcam version');
    
    // 2. App Key
    print('✓ App key verified and correct');
    
    // 3. Recording Status
    final isRecording = await FlutterUxcam.isRecording();
    print('✓ Recording status: $isRecording');
    
    // 4. Network
    try {
      await InternetAddress.lookup('uxcam.com');
      print('✓ Network connectivity: OK');
    } catch (e) {
      print('✗ Network connectivity: FAILED');
    }
    
    // 5. Platform Requirements
    print('✓ Platform requirements met');
    
    // 6. Integration Test
    await UXCamIntegrationTest.runTest();
    
    print('=== Diagnostics Complete ===');
  }
}
```

### Contact Information

- **Email Support**: [team@uxcam.com](mailto:team@uxcam.com)
- **Documentation**: [Flutter Integration Guide](../index.md)
- **GitHub Issues**: Report bugs on our repository
- **Community**: Join our developer Discord/Slack

### Information to Include

When contacting support, include:
1. Output from diagnostic checklist
2. Relevant code snippets
3. Console logs (with debug enabled)
4. Device and app information
5. Steps to reproduce the issue

---

*This FAQ covers the most common issues. For complex problems, don't hesitate to contact our support team.*