---
title: Recording Control
excerpt: Programmatically control UXCam session recording in Flutter apps
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---

# Recording Control

Take full control of when and how UXCam records user sessions in your Flutter application. This guide covers starting, stopping, pausing, and customizing recording behavior.

## Core Recording Methods

### Start Recording
```dart
import 'package:flutter_uxcam/flutter_uxcam.dart';

// Basic start with configuration
FlutterUxConfig config = FlutterUxConfig(
  userAppKey: "YOUR_APP_KEY",
  enableAutomaticScreenNameTagging: true,
);
await FlutterUxcam.startWithConfiguration(config);
```

### Stop Recording
```dart
// Stop current session and upload data
await FlutterUxcam.stopSessionAndUploadData();

// Check if currently recording
bool isRecording = await FlutterUxcam.isRecording();
print('Recording status: $isRecording');
```

### Pause/Resume Recording
```dart
// Pause recording (session continues, but no data captured)
await FlutterUxcam.pauseScreenRecording();

// Resume recording
await FlutterUxcam.resumeScreenRecording();
```

## Advanced Recording Patterns

### Conditional Recording
```dart
class RecordingController {
  static bool _shouldRecord = true;
  
  static Future<void> startConditionalRecording({
    required String appKey,
    required bool userConsented,
    required bool isProduction,
  }) async {
    // Only record if user consented and in production
    if (!userConsented || !isProduction) {
      _shouldRecord = false;
      return;
    }
    
    FlutterUxConfig config = FlutterUxConfig(
      userAppKey: appKey,
      enableAutomaticScreenNameTagging: true,
    );
    
    await FlutterUxcam.startWithConfiguration(config);
    _shouldRecord = true;
  }
  
  static Future<void> stopIfRecording() async {
    if (_shouldRecord && await FlutterUxcam.isRecording()) {
      await FlutterUxcam.stopSessionAndUploadData();
    }
  }
}
```

### Session Rotation
```dart
class SessionRotationManager {
  static const int MAX_SESSION_DURATION = 30 * 60 * 1000; // 30 minutes
  static Timer? _rotationTimer;
  
  static Future<void> startWithRotation(String appKey) async {
    await _startNewSession(appKey);
    _scheduleRotation(appKey);
  }
  
  static Future<void> _startNewSession(String appKey) async {
    FlutterUxConfig config = FlutterUxConfig(
      userAppKey: appKey,
      enableAutomaticScreenNameTagging: true,
    );
    
    await FlutterUxcam.startWithConfiguration(config);
    print('New UXCam session started');
  }
  
  static void _scheduleRotation(String appKey) {
    _rotationTimer?.cancel();
    _rotationTimer = Timer(
      Duration(milliseconds: MAX_SESSION_DURATION),
      () async {
        await FlutterUxcam.stopSessionAndUploadData();
        await _startNewSession(appKey);
        _scheduleRotation(appKey); // Schedule next rotation
      },
    );
  }
  
  static void stopRotation() {
    _rotationTimer?.cancel();
    FlutterUxcam.stopSessionAndUploadData();
  }
}
```

### Screen-Specific Recording
```dart
class ScreenRecordingManager {
  static final Set<String> _sensitiveScreens = {
    'LoginScreen',
    'PaymentScreen',
    'PersonalInfoScreen',
  };
  
  static Future<void> handleScreenTransition(String screenName) async {
    if (_sensitiveScreens.contains(screenName)) {
      // Pause recording on sensitive screens
      await FlutterUxcam.pauseScreenRecording();
      print('Recording paused for sensitive screen: $screenName');
    } else {
      // Resume recording on normal screens
      await FlutterUxcam.resumeScreenRecording();
      print('Recording resumed for screen: $screenName');
    }
    
    // Tag the screen
    FlutterUxcam.tagScreenName(screenName);
  }
}

// Usage in your screens
class PaymentScreen extends StatefulWidget {
  @override
  _PaymentScreenState createState() => _PaymentScreenState();
}

class _PaymentScreenState extends State<PaymentScreen> {
  @override
  void initState() {
    super.initState();
    ScreenRecordingManager.handleScreenTransition('PaymentScreen');
  }
  
  @override
  void dispose() {
    // Resume recording when leaving sensitive screen
    FlutterUxcam.resumeScreenRecording();
    super.dispose();
  }
}
```

## App Lifecycle Integration

### Comprehensive Lifecycle Management
```dart
class UXCamLifecycleManager extends WidgetsBindingObserver {
  final String appKey;
  bool _isRecording = false;
  
  UXCamLifecycleManager(this.appKey);
  
  Future<void> initialize() async {
    WidgetsBinding.instance.addObserver(this);
    await startRecording();
  }
  
  Future<void> startRecording() async {
    if (!_isRecording) {
      FlutterUxConfig config = FlutterUxConfig(
        userAppKey: appKey,
        enableAutomaticScreenNameTagging: true,
      );
      
      await FlutterUxcam.startWithConfiguration(config);
      _isRecording = true;
      print('UXCam recording started');
    }
  }
  
  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    switch (state) {
      case AppLifecycleState.paused:
        _handleAppPaused();
        break;
      case AppLifecycleState.resumed:
        _handleAppResumed();
        break;
      case AppLifecycleState.detached:
        _handleAppDetached();
        break;
    }
  }
  
  void _handleAppPaused() async {
    print('App paused - ensuring session upload');
    if (_isRecording) {
      // Optional: Stop recording when app goes to background
      // await FlutterUxcam.stopSessionAndUploadData();
      // _isRecording = false;
    }
  }
  
  void _handleAppResumed() async {
    print('App resumed');
    if (!_isRecording) {
      await startRecording();
    }
  }
  
  void _handleAppDetached() async {
    print('App detached - final session upload');
    if (_isRecording) {
      await FlutterUxcam.stopSessionAndUploadData();
      _isRecording = false;
    }
  }
  
  void dispose() {
    WidgetsBinding.instance.removeObserver(this);
    if (_isRecording) {
      FlutterUxcam.stopSessionAndUploadData();
    }
  }
}

// Usage in main app
class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  late UXCamLifecycleManager _uxcamManager;
  
  @override
  void initState() {
    super.initState();
    _uxcamManager = UXCamLifecycleManager("YOUR_APP_KEY");
    _uxcamManager.initialize();
  }
  
  @override
  void dispose() {
    _uxcamManager.dispose();
    super.dispose();
  }
}
```

## Performance Optimization

### Smart Recording Configuration
```dart
class PerformanceOptimizedRecording {
  static Future<void> startOptimizedRecording({
    required String appKey,
    bool isLowEndDevice = false,
    bool isOnMeteredConnection = false,
  }) async {
    FlutterUxConfig config = FlutterUxConfig(
      userAppKey: appKey,
      enableAutomaticScreenNameTagging: !isLowEndDevice, // Disable for performance
      enableImprovedScreenCapture: !isLowEndDevice, // Use basic capture on low-end devices
    );
    
    await FlutterUxcam.startWithConfiguration(config);
    
    // Implement session rotation for low-end devices
    if (isLowEndDevice) {
      SessionRotationManager.startWithRotation(appKey);
    }
  }
  
  static Future<bool> _isLowEndDevice() async {
    // Implement device capability detection
    // This is a simplified example
    final deviceInfo = await DeviceInfoPlugin().androidInfo;
    return deviceInfo.version.sdkInt < 26; // Android 8.0+
  }
  
  static Future<bool> _isOnMeteredConnection() async {
    final connectivityResult = await Connectivity().checkConnectivity();
    return connectivityResult == ConnectivityResult.mobile;
  }
}
```

### Memory-Conscious Recording
```dart
class MemoryAwareRecording {
  static const int MEMORY_THRESHOLD_MB = 100;
  static Timer? _memoryCheckTimer;
  
  static Future<void> startWithMemoryMonitoring(String appKey) async {
    FlutterUxConfig config = FlutterUxConfig(
      userAppKey: appKey,
      enableAutomaticScreenNameTagging: true,
    );
    
    await FlutterUxcam.startWithConfiguration(config);
    _startMemoryMonitoring();
  }
  
  static void _startMemoryMonitoring() {
    _memoryCheckTimer = Timer.periodic(
      Duration(minutes: 5),
      (timer) async {
        final memoryUsage = await _getMemoryUsage();
        
        if (memoryUsage > MEMORY_THRESHOLD_MB) {
          print('High memory usage detected: ${memoryUsage}MB');
          
          // Rotate session to free memory
          await FlutterUxcam.stopSessionAndUploadData();
          
          // Wait a bit before starting new session
          await Future.delayed(Duration(seconds: 2));
          
          FlutterUxConfig config = FlutterUxConfig(
            userAppKey: "YOUR_APP_KEY",
            enableImprovedScreenCapture: false, // Reduce quality
          );
          await FlutterUxcam.startWithConfiguration(config);
        }
      },
    );
  }
  
  static Future<double> _getMemoryUsage() async {
    // Platform-specific memory usage implementation
    // This is a simplified placeholder
    return 50.0; // MB
  }
  
  static void stopMemoryMonitoring() {
    _memoryCheckTimer?.cancel();
  }
}
```

## Error Handling

### Robust Recording Control
```dart
class RobustRecordingController {
  static bool _isInitialized = false;
  static String? _appKey;
  
  static Future<bool> startRecording(String appKey) async {
    try {
      _appKey = appKey;
      
      FlutterUxConfig config = FlutterUxConfig(
        userAppKey: appKey,
        enableAutomaticScreenNameTagging: true,
      );
      
      await FlutterUxcam.startWithConfiguration(config);
      _isInitialized = true;
      print('UXCam recording started successfully');
      return true;
      
    } catch (error) {
      print('Failed to start UXCam recording: $error');
      return false;
    }
  }
  
  static Future<bool> stopRecording() async {
    try {
      if (_isInitialized && await FlutterUxcam.isRecording()) {
        await FlutterUxcam.stopSessionAndUploadData();
        _isInitialized = false;
        print('UXCam recording stopped successfully');
        return true;
      }
      return false;
      
    } catch (error) {
      print('Failed to stop UXCam recording: $error');
      return false;
    }
  }
  
  static Future<void> ensureRecording() async {
    try {
      if (!_isInitialized && _appKey != null) {
        await startRecording(_appKey!);
      }
    } catch (error) {
      print('Failed to ensure UXCam recording: $error');
    }
  }
}
```

## Testing Recording Control

### Development Testing
```dart
class UXCamTestController {
  static bool _testMode = false;
  
  static void enableTestMode() {
    _testMode = true;
  }
  
  static Future<void> startRecording(String appKey) async {
    if (_testMode) {
      print('TEST MODE: Would start UXCam recording with key: $appKey');
      return;
    }
    
    FlutterUxConfig config = FlutterUxConfig(
      userAppKey: appKey,
      enableAutomaticScreenNameTagging: true,
    );
    
    await FlutterUxcam.startWithConfiguration(config);
  }
  
  static Future<void> stopRecording() async {
    if (_testMode) {
      print('TEST MODE: Would stop UXCam recording');
      return;
    }
    
    await FlutterUxcam.stopSessionAndUploadData();
  }
}
```

## Best Practices

### Do's
- ✅ Always check recording status before stopping
- ✅ Handle app lifecycle events properly  
- ✅ Implement error handling for all recording operations
- ✅ Use session rotation for long-running apps
- ✅ Pause recording on sensitive screens
- ✅ Monitor memory usage and optimize accordingly

### Don'ts
- ❌ Don't start multiple sessions simultaneously
- ❌ Don't ignore app lifecycle events
- ❌ Don't record sensitive user data
- ❌ Don't forget to stop recording on app termination
- ❌ Don't skip error handling in production

## Next Steps

- **[Crash Handling](crashes-and-anrs-flutter.md)** - Learn advanced error tracking
- **[User Consent](opt-in-opt-out-flutter.md)** - Implement privacy controls
- **[Integration Logging](integration-logging-guide.md)** - Debug your setup

---

*Master recording control to optimize performance and ensure data quality in your Flutter app.*