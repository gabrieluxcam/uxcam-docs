---
title: Advanced Configuration and APIs
deprecated: false
hidden: false
metadata:
  description: >-
    Advanced UXCam React Native SDK configuration options, recording control,
    crash handling, and privacy APIs.
  robots: index
---

# Advanced Configuration & APIs

This section covers advanced UXCam React Native SDK features beyond the basic integration.

<Cards columns={2}>
  <Card title="Control Recording" href="control-recording-2-react-native" icon="fa-video">
    Start, stop, and manage session recording programmatically.
  </Card>

  <Card title="Pause & Resume Recording" href="pause-and-resume-react-native" icon="fa-pause-circle">
    Temporarily pause recording for sensitive flows without stopping the session.
  </Card>

  <Card title="Crashes & ANRs" href="crashes-and-anrs-react-native" icon="fa-bug">
    How UXCam captures crash reports and ANR events.
  </Card>

  <Card title="Report Handled Exceptions" href="report-handled-exceptions-react-native" icon="fa-exclamation-triangle">
    Send caught exceptions to UXCam alongside session replays.
  </Card>

  <Card title="Opt In / Opt Out" href="opt-in-opt-out-your-users-react-native" icon="fa-user-shield">
    Give users control over session recording for privacy compliance.
  </Card>

  <Card title="Integration Logging" href="integration-logging-react-native" icon="fa-terminal">
    Enable verbose SDK logs for debugging your integration.
  </Card>
</Cards>

---

## Configuration Reference

All options are set on the configuration object before calling `startWithConfiguration()`:

```javascript
import RNUxcam from 'react-native-ux-cam';

const configuration = {
  userAppKey: 'YOUR_API_KEY',

  // Screen tagging
  enableAutomaticScreenNameTagging: false,  // default: true (set false for RN)

  // Recording behavior
  enableImprovedScreenCapture: true,        // default: false
  enableMultiSessionRecord: true,           // default: true
  enableCrashHandling: true,                // default: true

  // Debugging
  enableIntegrationLogging: false,          // default: false

  // Network monitoring
  enableNetworkLogging: false,              // default: false
};

RNUxcam.optIntoVideoRecordings();
RNUxcam.startWithConfiguration(configuration);
```

> 📘 **Deprecation Note**
>
> `optIntoSchematicRecordings()` / `optOutOfSchematicRecordings()` are deprecated. Use `optIntoVideoRecordings()` / `optOutOfVideoRecordings()` instead. Schematic vs. video recording is now controlled exclusively from the dashboard.
