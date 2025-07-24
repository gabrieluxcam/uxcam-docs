---
title: Crash and ANR Handling
excerpt: Comprehensive error tracking and crash reporting for Flutter apps
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---

# Crash and ANR Handling

Enhance your Flutter app's reliability with comprehensive crash detection, ANR monitoring, and custom exception tracking integrated with UXCam session recordings.

## Overview

UXCam's Flutter SDK provides advanced crash handling capabilities that capture crashes and ANRs (Application Not Responding) alongside session recordings, giving you complete context for debugging issues.

**Key Features:**
- Automatic crash detection and reporting
- ANR detection and tracking
- Custom exception reporting
- Integration with session replays
- Stack trace capture
- Device and environment context

## Basic Crash Handling Setup

### Enable Automatic Crash Handling
```dart
import 'package:flutter_uxcam/flutter_uxcam.dart';

FlutterUxConfig config = FlutterUxConfig(
  userAppKey: "YOUR_APP_KEY",
  enableCrashHandling: true,  // Enable automatic crash detection
  enableAutomaticScreenNameTagging: true,
);

await FlutterUxcam.startWithConfiguration(config);
```

### Flutter Error Handling Integration
```dart
void main() {
  // Capture Flutter framework errors
  FlutterError.onError = (FlutterErrorDetails details) {
    // Report to UXCam
    FlutterUxcam.recordException(
      details.exception.toString(),
      details.stack?.toString() ?? 'No stack trace available',
    );
    
    // Also print to console in debug mode
    if (kDebugMode) {
      FlutterError.presentError(details);
    }
  };
  
  // Capture errors not caught by Flutter
  PlatformDispatcher.instance.onError = (error, stack) {
    FlutterUxcam.recordException(error.toString(), stack.toString());
    return true;
  };
  
  runApp(MyApp());
}
```

## Advanced Crash Handling

### Custom Exception Reporting
```dart
class CustomExceptionHandler {
  static void reportException(
    dynamic error,
    StackTrace? stackTrace, {
    String? context,
    Map<String, dynamic>? additionalData,
  }) {
    // Create detailed error message
    String errorMessage = error.toString();
    if (context != null) {
      errorMessage = '$context: $errorMessage';
    }
    
    // Add additional context
    if (additionalData != null) {
      errorMessage += '\nAdditional Data: ${additionalData.toString()}';
    }
    
    // Report to UXCam
    FlutterUxcam.recordException(
      errorMessage,
      stackTrace?.toString() ?? 'No stack trace available',
    );
    
    // Log locally for debugging
    debugPrint('Exception reported to UXCam: $errorMessage');
  }
  
  static Future<T> wrapWithExceptionHandling<T>(
    Future<T> future, {
    String? context,
    Map<String, dynamic>? additionalData,
  }) async {
    try {
      return await future;
    } catch (error, stackTrace) {
      reportException(
        error,
        stackTrace,
        context: context,
        additionalData: additionalData,
      );
      rethrow;
    }
  }
}

// Usage examples
class DataService {
  static Future<List<User>> fetchUsers() async {
    return CustomExceptionHandler.wrapWithExceptionHandling(
      _performNetworkRequest(),
      context: 'DataService.fetchUsers',
      additionalData: {'endpoint': '/api/users'},
    );
  }
  
  static Future<List<User>> _performNetworkRequest() async {
    // Your network request implementation
    throw Exception('Network error');
  }
}
```

### Business Logic Error Tracking
```dart
class BusinessLogicErrorHandler {
  static void reportBusinessError({
    required String operation,
    required String errorType,
    required String errorMessage,
    Map<String, dynamic>? context,
  }) {
    // Create structured error message
    final errorData = {
      'operation': operation,
      'errorType': errorType,
      'message': errorMessage,
      'timestamp': DateTime.now().toIso8601String(),
      'context': context ?? {},
    };
    
    // Report as custom exception
    FlutterUxcam.recordException(
      'Business Logic Error: $operation',
      'Error Type: $errorType\n'
      'Message: $errorMessage\n'
      'Context: ${errorData.toString()}',
    );
    
    // Also log as custom event for analytics
    FlutterUxcam.logEvent('business_error', errorData);
  }
}

// Usage in business logic
class PaymentService {
  static Future<PaymentResult> processPayment(PaymentRequest request) async {
    try {
      // Payment processing logic
      if (request.amount <= 0) {
        BusinessLogicErrorHandler.reportBusinessError(
          operation: 'processPayment',
          errorType: 'ValidationError',
          errorMessage: 'Invalid payment amount',
          context: {
            'amount': request.amount,
            'currency': request.currency,
            'userId': request.userId,
          },
        );
        throw PaymentException('Invalid payment amount');
      }
      
      // Continue with payment processing
      return await _processPaymentInternal(request);
      
    } catch (error, stackTrace) {
      CustomExceptionHandler.reportException(
        error,
        stackTrace,
        context: 'PaymentService.processPayment',
        additionalData: {'requestId': request.id},
      );
      rethrow;
    }
  }
}
```

## ANR Detection

### Custom ANR Monitoring
```dart
class ANRDetector {
  static Timer? _watchdogTimer;
  static DateTime _lastUpdate = DateTime.now();
  static const Duration ANR_THRESHOLD = Duration(seconds: 5);
  
  static void startMonitoring() {
    _watchdogTimer = Timer.periodic(Duration(seconds: 1), (timer) {
      final now = DateTime.now();
      final timeSinceLastUpdate = now.difference(_lastUpdate);
      
      if (timeSinceLastUpdate > ANR_THRESHOLD) {
        _reportANR(timeSinceLastUpdate);
      }
      
      _lastUpdate = now;
    });
  }
  
  static void _reportANR(Duration anrDuration) {
    final anrMessage = 'ANR detected: UI thread blocked for ${anrDuration.inMilliseconds}ms';
    
    FlutterUxcam.recordException(
      anrMessage,
      _getCurrentStackTrace(),
    );
    
    // Log as custom event for analytics
    FlutterUxcam.logEvent('anr_detected', {
      'duration_ms': anrDuration.inMilliseconds,
      'screen': 'current_screen', // Add current screen context
      'timestamp': DateTime.now().toIso8601String(),
    });
  }
  
  static String _getCurrentStackTrace() {
    return StackTrace.current.toString();
  }
  
  static void heartbeat() {
    _lastUpdate = DateTime.now();
  }
  
  static void stopMonitoring() {
    _watchdogTimer?.cancel();
    _watchdogTimer = null;
  }
}

// Usage in main app
class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> with WidgetsBindingObserver {
  @override
  void initState() {
    super.initState();
    WidgetsBinding.instance.addObserver(this);
    ANRDetector.startMonitoring();
  }
  
  @override
  void dispose() {
    ANRDetector.stopMonitoring();
    WidgetsBinding.instance.removeObserver(this);
    super.dispose();
  }
  
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      navigatorObservers: [ANRNavigatorObserver()],
      // Your app content
    );
  }
}

class ANRNavigatorObserver extends NavigatorObserver {
  @override
  void didPush(Route<dynamic> route, Route<dynamic>? previousRoute) {
    ANRDetector.heartbeat();
    super.didPush(route, previousRoute);
  }
  
  @override
  void didPop(Route<dynamic> route, Route<dynamic>? previousRoute) {
    ANRDetector.heartbeat();
    super.didPop(route, previousRoute);
  }
}
```

## Network Error Handling

### HTTP Error Tracking
```dart
class NetworkErrorHandler {
  static Future<T> handleNetworkRequest<T>(
    Future<T> request, {
    required String endpoint,
    required String method,
    Map<String, dynamic>? requestData,
  }) async {
    try {
      return await request;
    } on SocketException catch (error, stackTrace) {
      _reportNetworkError(
        'Network Connection Error',
        endpoint,
        method,
        error.toString(),
        stackTrace,
        requestData,
      );
      rethrow;
    } on TimeoutException catch (error, stackTrace) {
      _reportNetworkError(
        'Network Timeout Error',
        endpoint,
        method,
        error.toString(),
        stackTrace,
        requestData,
      );
      rethrow;
    } on HttpException catch (error, stackTrace) {
      _reportNetworkError(
        'HTTP Error',
        endpoint,
        method,
        error.toString(),
        stackTrace,
        requestData,
      );
      rethrow;
    } catch (error, stackTrace) {
      _reportNetworkError(
        'Unknown Network Error',
        endpoint,
        method,
        error.toString(),
        stackTrace,
        requestData,
      );
      rethrow;
    }
  }
  
  static void _reportNetworkError(
    String errorType,
    String endpoint,
    String method,
    String errorMessage,
    StackTrace stackTrace,
    Map<String, dynamic>? requestData,
  ) {
    final contextMessage = '''
Network Error Details:
Type: $errorType
Endpoint: $endpoint
Method: $method
Error: $errorMessage
Request Data: ${requestData?.toString() ?? 'None'}
''';
    
    FlutterUxcam.recordException(contextMessage, stackTrace.toString());
    
    // Also track as event for analytics
    FlutterUxcam.logEvent('network_error', {
      'error_type': errorType,
      'endpoint': endpoint,
      'method': method,
      'error_message': errorMessage,
    });
  }
}

// Usage with HTTP client
class ApiService {
  static final HttpClient _httpClient = HttpClient();
  
  static Future<Map<String, dynamic>> get(String endpoint) async {
    return NetworkErrorHandler.handleNetworkRequest(
      _performGetRequest(endpoint),
      endpoint: endpoint,
      method: 'GET',
    );
  }
  
  static Future<Map<String, dynamic>> post(
    String endpoint,
    Map<String, dynamic> data,
  ) async {
    return NetworkErrorHandler.handleNetworkRequest(
      _performPostRequest(endpoint, data),
      endpoint: endpoint,
      method: 'POST',
      requestData: data,
    );
  }
}
```

## State Management Error Integration

### Bloc Error Handling
```dart
class UXCamBlocObserver extends BlocObserver {
  @override
  void onError(BlocBase bloc, Object error, StackTrace stackTrace) {
    super.onError(bloc, error, stackTrace);
    
    // Report bloc errors to UXCam
    FlutterUxcam.recordException(
      'Bloc Error in ${bloc.runtimeType}: ${error.toString()}',
      stackTrace.toString(),
    );
    
    // Log as custom event
    FlutterUxcam.logEvent('bloc_error', {
      'bloc_type': bloc.runtimeType.toString(),
      'error': error.toString(),
      'timestamp': DateTime.now().toIso8601String(),
    });
  }
  
  @override
  void onTransition(BlocBase bloc, Transition transition) {
    super.onTransition(bloc, transition);
    
    // Track critical state transitions
    if (_isCriticalTransition(transition)) {
      FlutterUxcam.logEvent('critical_state_transition', {
        'bloc_type': bloc.runtimeType.toString(),
        'from_state': transition.currentState.runtimeType.toString(),
        'to_state': transition.nextState.runtimeType.toString(),
        'event': transition.event.runtimeType.toString(),
      });
    }
  }
  
  bool _isCriticalTransition(Transition transition) {
    // Define your critical transitions
    return transition.nextState.toString().contains('Error') ||
           transition.nextState.toString().contains('Failed');
  }
}

// Initialize in main
void main() {
  Bloc.observer = UXCamBlocObserver();
  runApp(MyApp());
}
```

### Provider Error Handling
```dart
class UXCamChangeNotifier extends ChangeNotifier {
  void safeNotifyListeners() {
    try {
      notifyListeners();
    } catch (error, stackTrace) {
      FlutterUxcam.recordException(
        'ChangeNotifier Error: ${error.toString()}',
        stackTrace.toString(),
      );
      
      // Still notify listeners if possible
      super.notifyListeners();
    }
  }
  
  @override
  void notifyListeners() {
    safeNotifyListeners();
  }
}
```

## Testing Crash Handling

### Development Testing
```dart
class CrashTestWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Crash Testing')),
      body: Column(
        children: [
          ElevatedButton(
            onPressed: () {
              // Test custom exception
              CustomExceptionHandler.reportException(
                Exception('Test exception'),
                StackTrace.current,
                context: 'Manual test',
              );
            },
            child: Text('Test Custom Exception'),
          ),
          ElevatedButton(
            onPressed: () {
              // Test network error
              throw SocketException('Test network error');
            },
            child: Text('Test Network Error'),
          ),
          ElevatedButton(
            onPressed: () {
              // Test business logic error
              BusinessLogicErrorHandler.reportBusinessError(
                operation: 'testOperation',
                errorType: 'ValidationError',
                errorMessage: 'Test validation failed',
                context: {'testData': 'sample'},
              );
            },
            child: Text('Test Business Error'),
          ),
        ],
      ),
    );
  }
}
```

## Production Considerations

### Error Rate Monitoring
```dart
class ErrorRateMonitor {
  static int _errorCount = 0;
  static DateTime _windowStart = DateTime.now();
  static const Duration MONITORING_WINDOW = Duration(minutes: 5);
  static const int ERROR_THRESHOLD = 10;
  
  static void reportError(dynamic error, StackTrace stackTrace) {
    _errorCount++;
    
    final now = DateTime.now();
    if (now.difference(_windowStart) > MONITORING_WINDOW) {
      _checkErrorRate();
      _resetWindow();
    }
    
    FlutterUxcam.recordException(error.toString(), stackTrace.toString());
  }
  
  static void _checkErrorRate() {
    if (_errorCount > ERROR_THRESHOLD) {
      FlutterUxcam.logEvent('high_error_rate_detected', {
        'error_count': _errorCount,
        'window_minutes': MONITORING_WINDOW.inMinutes,
        'timestamp': DateTime.now().toIso8601String(),
      });
      
      // Could trigger additional actions like:
      // - Reducing app functionality
      // - Showing user a message
      // - Switching to safe mode
    }
  }
  
  static void _resetWindow() {
    _errorCount = 0;
    _windowStart = DateTime.now();
  }
}
```

## Best Practices

### Do's
- ✅ Enable automatic crash handling in production
- ✅ Wrap critical operations with exception handling
- ✅ Include relevant context in error reports
- ✅ Monitor error rates and patterns
- ✅ Test crash handling in development
- ✅ Integrate with your existing error tracking

### Don'ts
- ❌ Don't catch and ignore exceptions without reporting
- ❌ Don't include sensitive data in error reports
- ❌ Don't overwhelm UXCam with too many exceptions
- ❌ Don't forget to test error scenarios
- ❌ Don't rely solely on automatic crash detection

## Next Steps

- **[User Consent](opt-in-opt-out-flutter.md)** - Implement privacy controls
- **[Integration Logging](integration-logging-guide.md)** - Debug your setup
- **[Troubleshooting](troubleshooting-faqs.md)** - Solve integration issues

---

*Comprehensive error tracking helps you build more reliable Flutter apps with better user experiences.*