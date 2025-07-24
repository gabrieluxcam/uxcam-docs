---
title: Advanced Configuration and APIs
excerpt: Advanced Flutter SDK features for power users and complex integrations
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---

# Advanced Configuration and APIs

Unlock the full potential of UXCam's Flutter SDK with advanced configuration options, custom integrations, and powerful APIs for complex use cases.

## Overview

This section covers advanced features that go beyond basic integration:

- **Recording Control** - Start, stop, and pause session recording programmatically
- **Crash & ANR Handling** - Advanced error tracking and custom exception reporting  
- **User Consent Management** - Implement GDPR/CCPA compliant opt-in/opt-out flows
- **Debug & Logging** - Comprehensive logging and integration validation
- **Troubleshooting** - Advanced debugging techniques and performance optimization

## When to Use Advanced Features

### Recording Control
Use when you need to:
- Control recording based on user actions or app state
- Implement custom session management logic
- Optimize performance for resource-constrained devices
- Create demo modes or restricted recording areas

### Crash Handling
Essential for:
- Production apps requiring comprehensive error tracking
- Apps with complex navigation or state management
- Integration with existing crash reporting tools
- Custom error classification and reporting

### User Consent
Required for:
- GDPR compliance in European markets
- CCPA compliance in California
- Apps with strict privacy requirements
- Configurable privacy settings per user

## Quick Start Examples

### Basic Recording Control
```dart
import 'package:flutter_uxcam/flutter_uxcam.dart';

// Stop recording during sensitive operations
await FlutterUxcam.stopSessionAndUploadData();

// Resume recording
FlutterUxConfig config = FlutterUxConfig(
  userAppKey: "YOUR_APP_KEY",
);
await FlutterUxcam.startWithConfiguration(config);
```

### Simple Crash Tracking
```dart
// Enable automatic crash handling
FlutterUxConfig config = FlutterUxConfig(
  userAppKey: "YOUR_APP_KEY",
  enableCrashHandling: true,
);

// Report custom exceptions
try {
  // Your code here
} catch (error, stackTrace) {
  FlutterUxcam.recordException(error.toString(), stackTrace.toString());
}
```

### Basic User Consent
```dart
// Check if user has opted in
bool hasConsent = await FlutterUxcam.optStatus();

if (!hasConsent) {
  // Show consent dialog
  bool userConsent = await showConsentDialog();
  
  if (userConsent) {
    FlutterUxcam.optIntoSchematicRecordings();
  } else {
    FlutterUxcam.optOutOfSchematicRecordings();
  }
}
```

## Integration Patterns

### Singleton Configuration Manager
```dart
class UXCamManager {
  static UXCamManager? _instance;
  static UXCamManager get instance => _instance ??= UXCamManager._();
  
  UXCamManager._();
  
  bool _isInitialized = false;
  
  Future<void> initialize({
    required String appKey,
    bool enableCrashHandling = true,
    bool enableAutomaticScreenTagging = true,
  }) async {
    if (_isInitialized) return;
    
    FlutterUxConfig config = FlutterUxConfig(
      userAppKey: appKey,
      enableCrashHandling: enableCrashHandling,
      enableAutomaticScreenNameTagging: enableAutomaticScreenTagging,
    );
    
    await FlutterUxcam.startWithConfiguration(config);
    _isInitialized = true;
  }
  
  Future<void> pauseRecording() async {
    await FlutterUxcam.stopSessionAndUploadData();
  }
  
  Future<void> resumeRecording() async {
    // Reinitialize with stored config
    await initialize(appKey: "YOUR_APP_KEY");
  }
}
```

### Error Boundary Integration
```dart
class UXCamErrorBoundary extends StatelessWidget {
  final Widget child;
  
  const UXCamErrorBoundary({Key? key, required this.child}) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    return child;
  }
}

// In your main.dart
void main() {
  FlutterError.onError = (FlutterErrorDetails details) {
    // Report to UXCam
    FlutterUxcam.recordException(
      details.exception.toString(),
      details.stack?.toString() ?? '',
    );
    
    // Also report to other crash services
    FlutterError.presentError(details);
  };
  
  runApp(MyApp());
}
```

## Performance Considerations

### Memory Management
- **Session Rotation**: Implement automatic session rotation for long-running apps
- **Buffer Management**: Monitor memory usage and adjust recording quality accordingly
- **Background Handling**: Properly handle app lifecycle events

### Network Optimization
- **Batch Uploads**: Configure upload timing based on network conditions
- **Compression**: Use appropriate compression levels for your use case
- **Retry Logic**: Implement robust upload retry mechanisms

### Battery Impact
- **Recording Quality**: Balance quality with battery consumption
- **CPU Usage**: Monitor and optimize for sustained performance
- **Background Activity**: Minimize processing when app is backgrounded

## Security & Privacy

### Data Protection
- **Local Storage**: Secure local session data storage
- **Network Security**: Implement certificate pinning if required
- **Data Minimization**: Only collect necessary user data

### Compliance Features
- **Data Retention**: Configure appropriate data retention policies
- **User Rights**: Implement data deletion and export capabilities
- **Audit Trails**: Maintain logs of consent and configuration changes

## Next Steps

Explore the detailed guides for each advanced feature:

1. **[Recording Control](control-recording-flutter.md)** - Master session lifecycle management
2. **[Crash Handling](crashes-and-anrs-flutter.md)** - Implement comprehensive error tracking
3. **[User Consent](opt-in-opt-out-flutter.md)** - Build privacy-compliant experiences
4. **[Integration Logging](integration-logging-guide.md)** - Debug and validate your setup
5. **[Troubleshooting](troubleshooting-faqs.md)** - Solve complex integration challenges

## Support Resources

- **Documentation**: [Flutter Integration Guide](../index.md)
- **API Reference**: Complete method documentation
- **Examples**: Working code samples for each feature
- **Support**: [team@uxcam.com](mailto:team@uxcam.com)

---

*Ready to implement advanced features? Choose a specific topic from the navigation menu to dive deeper.*