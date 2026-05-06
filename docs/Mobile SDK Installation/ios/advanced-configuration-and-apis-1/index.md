---
title: Advanced Configuration and APIs
deprecated: false
hidden: false
metadata:
  description: >-
    Advanced UXCam iOS SDK configuration options, recording control, crash
    handling, and privacy APIs.
  robots: index
---

# Advanced Configuration & APIs

This section covers advanced UXCam iOS SDK features beyond the basic integration.

<Cards columns={2}>
  <Card title="Control Recording" href="control-recording-copy-1-ios" icon="fa-video">
    Start, stop, and manage session recording programmatically.
  </Card>

  <Card title="Crashes & ANRs" href="crashes-anrs-copy-ios" icon="fa-bug">
    How UXCam captures crash reports and ANR events.
  </Card>

  <Card title="Opt In / Opt Out" href="opt-in-opt-out-copy-ios" icon="fa-user-shield">
    Give users control over session recording for privacy compliance.
  </Card>

  <Card title="Pause & Resume Recording" href="pause-and-resume-video-recording-copy-ios" icon="fa-pause-circle">
    Temporarily pause recording for sensitive flows without stopping the session.
  </Card>

  <Card title="Crash Symbolication" href="crash-symbolication-ios" icon="fa-code">
    Set up dSYM upload for readable crash stack traces on the dashboard.
  </Card>

  <Card title="Integration Logging" href="integration-logging-guide-for-developers-copy-1-ios" icon="fa-terminal">
    Enable verbose SDK logs for debugging your integration.
  </Card>

  <Card title="Report Handled Exceptions" href="report-handled-exceptions-copy-ios" icon="fa-exclamation-triangle">
    Send caught exceptions to UXCam alongside session replays.
  </Card>
</Cards>

---

## Configuration Reference

All options are set on the `UXCamConfiguration` object before calling `UXCam.start(with:)`:

```swift
let config = UXCamConfiguration(appKey: "YOUR_APP_KEY")

// Screen tagging
config.enableAutomaticScreenNameTagging = true       // default: true

// Recording behavior
config.enableMultiSessionRecord = true               // default: true
config.enableCrashHandling = true                    // default: true
config.enableAdvancedGestureRecognition = true        // default: true

// Debugging
config.enableIntegrationLogging = false              // default: false

// Network monitoring (v3.7.9+)
config.enableNetworkLogging = false                  // default: false

// Environment filtering
config.environment = .release                        // .alpha | .beta | .release

// Privacy — set occlusion at init time
config.occlusion = UXCamOcclusion(/* ... */)         // optional

UXCam.start(with: config)
```

> 📘 **Deprecation Note (v3.8.0+)**
>
> `optIntoSchematicRecordings()` / `optOutOfSchematicRecordings()` are deprecated. Use `optIntoVideoRecordings()` / `optOutOfVideoRecordings()` instead. Schematic vs. video recording is now controlled exclusively from the dashboard.
