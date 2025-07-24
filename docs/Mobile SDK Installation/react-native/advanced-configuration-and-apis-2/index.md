---
title: Advanced Configuration and APIs
deprecated: false
hidden: false
metadata:
  robots: index
---
This section covers advanced configuration options and APIs available in the React Native UXCam SDK. These features allow you to fine-tune the SDK behavior, control recording sessions, handle crashes and exceptions, and manage user consent.

## Available Configuration Options

The React Native SDK supports several configuration options that can be passed during initialization:

```javascript
import RNUxcam from 'react-native-ux-cam';

const configuration = {
  userAppKey: 'YOUR_API_KEY',
  enableAutomaticScreenNameTagging: false, // Required for React Native
  enableImprovedScreenCapture: true,
  enableCrashHandling: true,
  enableMultiSessionRecord: false,
  enableNetworkLogging: false,
};

RNUxcam.startWithConfiguration(configuration);
```

## Key Features

* **Session Control**: Start, stop, and manage recording sessions
* **Crash Handling**: Automatic crash detection and reporting
* **Exception Reporting**: Report handled exceptions with context
* **User Consent**: Opt-in/opt-out functionality
* **Performance Monitoring**: ANR detection and UI freeze monitoring
* **Integration Logging**: Debug and troubleshoot SDK issues

## Next Steps

Explore the specific APIs and configuration options available for React Native development.