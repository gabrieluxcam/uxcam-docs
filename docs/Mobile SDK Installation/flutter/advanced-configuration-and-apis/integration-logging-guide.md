---
title: Integration Logging & Debug Guide
excerpt: Debug and validate your UXCam Flutter SDK integration
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---

# Integration Logging & Debug Guide

Master debugging and validation techniques for your UXCam Flutter SDK integration with comprehensive logging, testing, and troubleshooting strategies.

## Overview

Effective logging is crucial for:
- Validating proper SDK integration
- Debugging configuration issues
- Monitoring data collection quality
- Troubleshooting production problems
- Ensuring compliance with privacy settings

## Basic Debug Logging

### Enable UXCam Debug Logs
```dart
import 'package:flutter_uxcam/flutter_uxcam.dart';

// Enable debug logging before starting UXCam
FlutterUxConfig config = FlutterUxConfig(
  userAppKey: "YOUR_APP_KEY",
  enableAutomaticScreenNameTagging: true,
  enableCrashHandling: true,
);

// Debug logs will show UXCam internal operations
await FlutterUxcam.startWithConfiguration(config);
```

### Flutter Debug Console Integration
```dart
class UXCamDebugger {
  static bool _debugEnabled = kDebugMode;
  
  static void enableDebug(bool enabled) {
    _debugEnabled = enabled;
  }
  
  static void log(String message, {String? tag}) {
    if (_debugEnabled) {
      final timestamp = DateTime.now().toIso8601String();
      final tagPrefix = tag != null ? '[$tag] ' : '';
      print('[$timestamp] UXCam Debug: $tagPrefix$message');
    }
  }
  
  static void logError(String message, {dynamic error, StackTrace? stackTrace}) {
    if (_debugEnabled) {
      final timestamp = DateTime.now().toIso8601String();
      print('[$timestamp] UXCam Error: $message');
      if (error != null) {
        print('Error details: $error');
      }
      if (stackTrace != null) {
        print('Stack trace: $stackTrace');
      }
    }
  }
  
  static void logConfig(FlutterUxConfig config) {
    if (_debugEnabled) {
      log('Configuration:');
      log('  App Key: ${config.userAppKey}');
      log('  Auto Screen Tagging: ${config.enableAutomaticScreenNameTagging}');
      log('  Crash Handling: ${config.enableCrashHandling}');
      log('  Improved Screen Capture: ${config.enableImprovedScreenCapture}');
    }
  }
}

// Usage in your app initialization
class UXCamManager {
  static Future<void> initialize(String appKey) async {
    UXCamDebugger.log('Initializing UXCam SDK...', tag: 'INIT');
    
    try {
      FlutterUxConfig config = FlutterUxConfig(
        userAppKey: appKey,
        enableAutomaticScreenNameTagging: true,
        enableCrashHandling: true,
      );
      
      UXCamDebugger.logConfig(config);
      
      await FlutterUxcam.startWithConfiguration(config);
      UXCamDebugger.log('UXCam initialized successfully', tag: 'INIT');
      
    } catch (error, stackTrace) {
      UXCamDebugger.logError(
        'Failed to initialize UXCam',
        error: error,
        stackTrace: stackTrace,
      );
    }
  }
}
```

## Advanced Logging Strategies

### Session Lifecycle Logging
```dart
class SessionLifecycleLogger {
  static DateTime? _sessionStartTime;
  static int _sessionCount = 0;
  
  static Future<void> startSession(String appKey) async {
    _sessionStartTime = DateTime.now();
    _sessionCount++;
    
    UXCamDebugger.log(
      'Starting session #$_sessionCount at ${_sessionStartTime}',
      tag: 'SESSION',
    );
    
    try {
      FlutterUxConfig config = FlutterUxConfig(
        userAppKey: appKey,
        enableAutomaticScreenNameTagging: true,
      );
      
      await FlutterUxcam.startWithConfiguration(config);
      
      final isRecording = await FlutterUxcam.isRecording();
      UXCamDebugger.log(
        'Session started. Recording: $isRecording',
        tag: 'SESSION',
      );
      
    } catch (error) {
      UXCamDebugger.logError('Failed to start session', error: error);
    }
  }
  
  static Future<void> endSession() async {
    if (_sessionStartTime != null) {
      final duration = DateTime.now().difference(_sessionStartTime!);
      UXCamDebugger.log(
        'Ending session #$_sessionCount. Duration: ${duration.inMinutes}m ${duration.inSeconds % 60}s',
        tag: 'SESSION',
      );
    }
    
    try {
      await FlutterUxcam.stopSessionAndUploadData();
      UXCamDebugger.log('Session ended and data uploaded', tag: 'SESSION');
    } catch (error) {
      UXCamDebugger.logError('Failed to end session', error: error);
    }
    
    _sessionStartTime = null;
  }
  
  static void logScreenTransition(String screenName) {
    final timestamp = DateTime.now();
    UXCamDebugger.log(
      'Screen transition: $screenName at $timestamp',
      tag: 'SCREEN',
    );
    
    // Tag the screen in UXCam
    FlutterUxcam.tagScreenName(screenName);
  }
  
  static void logEvent(String eventName, Map<String, dynamic>? properties) {
    UXCamDebugger.log(
      'Event: $eventName with properties: ${properties?.toString() ?? 'none'}',
      tag: 'EVENT',
    );
    
    // Log the event in UXCam
    FlutterUxcam.logEvent(eventName, properties);
  }
}
```

### Network Request Logging
```dart
class NetworkRequestLogger {
  static void logRequest({
    required String method,
    required String url,
    Map<String, String>? headers,
    dynamic body,
  }) {
    UXCamDebugger.log(
      'HTTP $method: $url',
      tag: 'NETWORK',
    );
    
    if (headers != null && headers.isNotEmpty) {
      UXCamDebugger.log('Headers: $headers', tag: 'NETWORK');
    }
    
    if (body != null) {
      UXCamDebugger.log('Body: ${body.toString()}', tag: 'NETWORK');
    }
  }
  
  static void logResponse({
    required String method,
    required String url,
    required int statusCode,
    required Duration duration,
    dynamic response,
  }) {
    UXCamDebugger.log(
      'HTTP $method $url -> $statusCode (${duration.inMilliseconds}ms)',
      tag: 'NETWORK',
    );
    
    if (statusCode >= 400) {
      UXCamDebugger.logError(
        'HTTP Error $statusCode for $method $url',
        error: response,
      );
      
      // Log network errors as UXCam events
      FlutterUxcam.logEvent('network_error', {
        'method': method,
        'url': url,
        'status_code': statusCode,
        'duration_ms': duration.inMilliseconds,
      });
    }
  }
}

// HTTP client wrapper with logging
class LoggingHttpClient {
  final http.Client _client = http.Client();
  
  Future<http.Response> get(String url, {Map<String, String>? headers}) async {
    final startTime = DateTime.now();
    
    NetworkRequestLogger.logRequest(
      method: 'GET',
      url: url,
      headers: headers,
    );
    
    try {
      final response = await _client.get(Uri.parse(url), headers: headers);
      final duration = DateTime.now().difference(startTime);
      
      NetworkRequestLogger.logResponse(
        method: 'GET',
        url: url,
        statusCode: response.statusCode,
        duration: duration,
        response: response.body,
      );
      
      return response;
    } catch (error) {
      final duration = DateTime.now().difference(startTime);
      UXCamDebugger.logError('GET $url failed after ${duration.inMilliseconds}ms', error: error);
      rethrow;
    }
  }
  
  Future<http.Response> post(
    String url, {
    Map<String, String>? headers,
    dynamic body,
  }) async {
    final startTime = DateTime.now();
    
    NetworkRequestLogger.logRequest(
      method: 'POST',
      url: url,
      headers: headers,
      body: body,
    );
    
    try {
      final response = await _client.post(
        Uri.parse(url),
        headers: headers,
        body: body,
      );
      final duration = DateTime.now().difference(startTime);
      
      NetworkRequestLogger.logResponse(
        method: 'POST',
        url: url,
        statusCode: response.statusCode,
        duration: duration,
        response: response.body,
      );
      
      return response;
    } catch (error) {
      final duration = DateTime.now().difference(startTime);
      UXCamDebugger.logError('POST $url failed after ${duration.inMilliseconds}ms', error: error);
      rethrow;
    }
  }
}
```

## Integration Validation

### Automated Validation Suite
```dart
class UXCamValidationSuite {
  static Future<ValidationResult> runFullValidation() async {
    final results = <String, bool>{};
    final errors = <String>[];
    
    UXCamDebugger.log('Starting UXCam validation suite...', tag: 'VALIDATION');
    
    // Test 1: SDK Initialization
    try {
      final initResult = await _validateInitialization();
      results['initialization'] = initResult;
      if (!initResult) errors.add('SDK initialization failed');
    } catch (e) {
      results['initialization'] = false;
      errors.add('SDK initialization error: $e');
    }
    
    // Test 2: Recording Status
    try {
      final recordingResult = await _validateRecording();
      results['recording'] = recordingResult;
      if (!recordingResult) errors.add('Recording validation failed');
    } catch (e) {
      results['recording'] = false;
      errors.add('Recording validation error: $e');
    }
    
    // Test 3: Screen Tagging
    try {
      final screenResult = await _validateScreenTagging();
      results['screen_tagging'] = screenResult;
      if (!screenResult) errors.add('Screen tagging validation failed');
    } catch (e) {
      results['screen_tagging'] = false;
      errors.add('Screen tagging validation error: $e');
    }
    
    // Test 4: Event Logging
    try {
      final eventResult = await _validateEventLogging();
      results['event_logging'] = eventResult;
      if (!eventResult) errors.add('Event logging validation failed');
    } catch (e) {
      results['event_logging'] = false;
      errors.add('Event logging validation error: $e');
    }
    
    // Test 5: User Properties
    try {
      final userResult = await _validateUserProperties();
      results['user_properties'] = userResult;
      if (!userResult) errors.add('User properties validation failed');
    } catch (e) {
      results['user_properties'] = false;
      errors.add('User properties validation error: $e');
    }
    
    final overallSuccess = results.values.every((result) => result);
    
    UXCamDebugger.log(
      'Validation complete. Success: $overallSuccess',
      tag: 'VALIDATION',
    );
    
    return ValidationResult(
      success: overallSuccess,
      results: results,
      errors: errors,
    );
  }
  
  static Future<bool> _validateInitialization() async {
    UXCamDebugger.log('Validating initialization...', tag: 'VALIDATION');
    
    // Check if UXCam is recording
    final isRecording = await FlutterUxcam.isRecording();
    UXCamDebugger.log('Recording status: $isRecording', tag: 'VALIDATION');
    
    return isRecording;
  }
  
  static Future<bool> _validateRecording() async {
    UXCamDebugger.log('Validating recording capabilities...', tag: 'VALIDATION');
    
    // Test pause/resume functionality
    await FlutterUxcam.pauseScreenRecording();
    await Future.delayed(Duration(milliseconds: 100));
    await FlutterUxcam.resumeScreenRecording();
    
    UXCamDebugger.log('Pause/resume test completed', tag: 'VALIDATION');
    return true;
  }
  
  static Future<bool> _validateScreenTagging() async {
    UXCamDebugger.log('Validating screen tagging...', tag: 'VALIDATION');
    
    // Test screen tagging
    final testScreenName = 'ValidationTestScreen_${DateTime.now().millisecondsSinceEpoch}';
    FlutterUxcam.tagScreenName(testScreenName);
    
    UXCamDebugger.log('Screen tagged: $testScreenName', tag: 'VALIDATION');
    return true;
  }
  
  static Future<bool> _validateEventLogging() async {
    UXCamDebugger.log('Validating event logging...', tag: 'VALIDATION');
    
    // Test event logging
    final testEventName = 'validation_test_event';
    final testProperties = {
      'test_id': DateTime.now().millisecondsSinceEpoch.toString(),
      'validation_run': true,
    };
    
    FlutterUxcam.logEvent(testEventName, testProperties);
    UXCamDebugger.log('Test event logged: $testEventName', tag: 'VALIDATION');
    
    return true;
  }
  
  static Future<bool> _validateUserProperties() async {
    UXCamDebugger.log('Validating user properties...', tag: 'VALIDATION');
    
    // Test user properties
    final testUserId = 'validation_user_${DateTime.now().millisecondsSinceEpoch}';
    final testProperties = {
      'validation_run': 'true',
      'test_timestamp': DateTime.now().toIso8601String(),
    };
    
    FlutterUxcam.setUserIdentity(testUserId);
    FlutterUxcam.setUserProperties(testProperties);
    
    UXCamDebugger.log('User properties set for: $testUserId', tag: 'VALIDATION');
    return true;
  }
}

class ValidationResult {
  final bool success;
  final Map<String, bool> results;
  final List<String> errors;
  
  ValidationResult({
    required this.success,
    required this.results,
    required this.errors,
  });
  
  @override
  String toString() {
    final buffer = StringBuffer();
    buffer.writeln('UXCam Validation Result:');
    buffer.writeln('Overall Success: $success');
    buffer.writeln('Individual Results:');
    
    results.forEach((test, result) {
      buffer.writeln('  $test: ${result ? "PASS" : "FAIL"}');
    });
    
    if (errors.isNotEmpty) {
      buffer.writeln('Errors:');
      errors.forEach((error) {
        buffer.writeln('  - $error');
      });
    }
    
    return buffer.toString();
  }
}
```

## Production Monitoring

### Performance Monitoring
```dart
class UXCamPerformanceMonitor {
  static final Map<String, Stopwatch> _timers = {};
  static final List<PerformanceMetric> _metrics = [];
  
  static void startTimer(String operation) {
    _timers[operation] = Stopwatch()..start();
    UXCamDebugger.log('Started timer: $operation', tag: 'PERF');
  }
  
  static void endTimer(String operation) {
    final timer = _timers[operation];
    if (timer != null) {
      timer.stop();
      final duration = timer.elapsedMilliseconds;
      
      _metrics.add(PerformanceMetric(
        operation: operation,
        duration: duration,
        timestamp: DateTime.now(),
      ));
      
      UXCamDebugger.log(
        'Completed timer: $operation (${duration}ms)',
        tag: 'PERF',
      );
      
      // Log performance metrics to UXCam
      FlutterUxcam.logEvent('performance_metric', {
        'operation': operation,
        'duration_ms': duration,
        'timestamp': DateTime.now().toIso8601String(),
      });
      
      _timers.remove(operation);
      
      // Alert on slow operations
      if (duration > 5000) { // 5 seconds
        UXCamDebugger.logError('Slow operation detected: $operation (${duration}ms)');
        FlutterUxcam.logEvent('slow_operation_detected', {
          'operation': operation,
          'duration_ms': duration,
        });
      }
    }
  }
  
  static List<PerformanceMetric> getMetrics() {
    return List.unmodifiable(_metrics);
  }
  
  static void clearMetrics() {
    _metrics.clear();
    UXCamDebugger.log('Performance metrics cleared', tag: 'PERF');
  }
  
  static Map<String, dynamic> getPerformanceSummary() {
    if (_metrics.isEmpty) return {};
    
    final operationGroups = <String, List<int>>{};
    
    for (final metric in _metrics) {
      operationGroups.putIfAbsent(metric.operation, () => [])
          .add(metric.duration);
    }
    
    final summary = <String, dynamic>{};
    
    operationGroups.forEach((operation, durations) {
      durations.sort();
      final count = durations.length;
      final min = durations.first;
      final max = durations.last;
      final avg = durations.reduce((a, b) => a + b) / count;
      final median = count % 2 == 0
          ? (durations[count ~/ 2 - 1] + durations[count ~/ 2]) / 2
          : durations[count ~/ 2];
      
      summary[operation] = {
        'count': count,
        'min_ms': min,
        'max_ms': max,
        'avg_ms': avg.round(),
        'median_ms': median,
      };
    });
    
    return summary;
  }
}

class PerformanceMetric {
  final String operation;
  final int duration;
  final DateTime timestamp;
  
  PerformanceMetric({
    required this.operation,
    required this.duration,
    required this.timestamp,
  });
}

// Usage wrapper for performance monitoring
T monitorPerformance<T>(String operation, T Function() function) {
  UXCamPerformanceMonitor.startTimer(operation);
  try {
    final result = function();
    UXCamPerformanceMonitor.endTimer(operation);
    return result;
  } catch (e) {
    UXCamPerformanceMonitor.endTimer(operation);
    rethrow;
  }
}

Future<T> monitorAsyncPerformance<T>(
  String operation,
  Future<T> Function() function,
) async {
  UXCamPerformanceMonitor.startTimer(operation);
  try {
    final result = await function();
    UXCamPerformanceMonitor.endTimer(operation);
    return result;
  } catch (e) {
    UXCamPerformanceMonitor.endTimer(operation);
    rethrow;
  }
}
```

## Log Export and Analysis

### Log Export Functionality
```dart
class LogExporter {
  static Future<String> exportLogs({
    DateTime? startDate,
    DateTime? endDate,
    List<String>? tags,
  }) async {
    final buffer = StringBuffer();
    
    // Add header
    buffer.writeln('UXCam Flutter SDK Debug Log Export');
    buffer.writeln('Generated: ${DateTime.now().toIso8601String()}');
    buffer.writeln('App Version: ${await _getAppVersion()}');
    buffer.writeln('Flutter Version: ${await _getFlutterVersion()}');
    buffer.writeln('Device Info: ${await _getDeviceInfo()}');
    buffer.writeln('');
    buffer.writeln('--- LOGS ---');
    
    // Add performance summary
    final perfSummary = UXCamPerformanceMonitor.getPerformanceSummary();
    if (perfSummary.isNotEmpty) {
      buffer.writeln('');
      buffer.writeln('Performance Summary:');
      perfSummary.forEach((operation, metrics) {
        buffer.writeln('  $operation: $metrics');
      });
    }
    
    // Add validation results
    final validationResult = await UXCamValidationSuite.runFullValidation();
    buffer.writeln('');
    buffer.writeln('Validation Results:');
    buffer.writeln(validationResult.toString());
    
    return buffer.toString();
  }
  
  static Future<void> saveLogsToFile(String logs) async {
    try {
      final directory = await getApplicationDocumentsDirectory();
      final file = File('${directory.path}/uxcam_debug_logs_${DateTime.now().millisecondsSinceEpoch}.txt');
      await file.writeAsString(logs);
      
      UXCamDebugger.log('Logs saved to: ${file.path}', tag: 'EXPORT');
    } catch (e) {
      UXCamDebugger.logError('Failed to save logs to file', error: e);
    }
  }
  
  static Future<void> shareLogsViaEmail() async {
    final logs = await exportLogs();
    
    final subject = 'UXCam Flutter SDK Debug Logs';
    final body = 'Please find the debug logs attached.\n\n$logs';
    
    final uri = Uri(
      scheme: 'mailto',
      queryParameters: {
        'subject': subject,
        'body': body,
      },
    );
    
    if (await canLaunchUrl(uri)) {
      await launchUrl(uri);
    } else {
      UXCamDebugger.logError('Could not launch email client');
    }
  }
  
  static Future<String> _getAppVersion() async {
    try {
      final packageInfo = await PackageInfo.fromPlatform();
      return '${packageInfo.version}+${packageInfo.buildNumber}';
    } catch (e) {
      return 'Unknown';
    }
  }
  
  static Future<String> _getFlutterVersion() async {
    // This would require platform channel implementation
    return 'Unknown';
  }
  
  static Future<String> _getDeviceInfo() async {
    try {
      final deviceInfo = DeviceInfoPlugin();
      if (Platform.isAndroid) {
        final androidInfo = await deviceInfo.androidInfo;
        return '${androidInfo.brand} ${androidInfo.model} (Android ${androidInfo.version.release})';
      } else if (Platform.isIOS) {
        final iosInfo = await deviceInfo.iosInfo;
        return '${iosInfo.name} ${iosInfo.model} (iOS ${iosInfo.systemVersion})';
      }
    } catch (e) {
      // Ignore
    }
    return 'Unknown';
  }
}
```

## Best Practices

### Do's
- ✅ Enable debug logging during development
- ✅ Monitor performance metrics in production
- ✅ Validate integration after major changes
- ✅ Export and analyze logs for troubleshooting
- ✅ Use structured logging with tags
- ✅ Implement automated validation tests

### Don'ts
- ❌ Don't leave debug logging enabled in production
- ❌ Don't ignore performance warnings
- ❌ Don't skip integration validation
- ❌ Don't log sensitive user data
- ❌ Don't overwhelm logs with unnecessary information
- ❌ Don't forget to handle logging errors

## Next Steps

- **[Troubleshooting FAQs](troubleshooting-faqs.md)** - Common issues and solutions
- **[Recording Control](control-recording-flutter.md)** - Advanced recording management
- **[Crash Handling](crashes-and-anrs-flutter.md)** - Error tracking integration

---

*Effective logging and validation ensure your UXCam integration works reliably in production.*