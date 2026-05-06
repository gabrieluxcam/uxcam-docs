---
title: Advanced Configuration and APIs
deprecated: false
hidden: false
metadata:
  description: >-
    Advanced UXCam Android SDK configuration options, recording control, crash
    handling, and privacy APIs.
  robots: index
---

# Advanced Configuration & APIs

This section covers advanced UXCam Android SDK features beyond the basic integration.

<Cards columns={2}>
  <Card title="Control Recording" href="control-recording-android" icon="fa-video">
    Start, stop, and manage session recording programmatically.
  </Card>

  <Card title="Crashes & ANRs" href="crashes-anrs-android" icon="fa-bug">
    How UXCam captures crash reports and ANR events.
  </Card>

  <Card title="Opt In / Opt Out" href="opt-in-opt-out-android" icon="fa-user-shield">
    Give users control over session recording for privacy compliance.
  </Card>

  <Card title="Pause & Resume Recording" href="pause-and-resume-video-recording-android" icon="fa-pause-circle">
    Temporarily pause recording for sensitive flows without stopping the session.
  </Card>

  <Card title="Integration Logging" href="integration-logging-guide-for-developers-copy-android" icon="fa-terminal">
    Enable verbose SDK logs for debugging your integration.
  </Card>

  <Card title="Report Handled Exceptions" href="report-handled-exceptions-1-android" icon="fa-exclamation-triangle">
    Send caught exceptions to UXCam alongside session replays.
  </Card>

  <Card title="Troubleshooting FAQs" href="troubleshooting-faqs-android" icon="fa-question-circle">
    Common issues and solutions during Android integration.
  </Card>
</Cards>

---

## UXConfig Builder Reference

All configuration is set via `UXConfig.Builder` before calling `UXCam.startWithConfiguration()`:

```kotlin Kotlin
val config = UXConfig.Builder("YOUR_APP_KEY")
    // Screen tagging
    .enableAutomaticScreenNameTagging(true)            // default: true
    .enableReadableFragmentNames(true)                 // default: true (v3.10.0+)

    // Recording behavior
    .enableMultiSessionRecord(true)                    // default: true
    .enableCrashHandling(true)                         // default: true

    // Debugging
    .enableIntegrationLogging(false)                   // default: false
    .enableVerboseLogging(false)                       // default: false

    // Security
    .honorFlagSecure(false)                            // default: false

    // Environment filtering
    .environment(Environment.RELEASE)                  // ALPHA | BETA | RELEASE

    // Privacy — set occlusion at init time
    .occlusions(listOf(/* UXCamOcclusion objects */))  // optional

    // Experimental
    // .enableExperimentalForceQuitVideoRecovery()     // opt-in: recovers video on force quit

    .build()

UXCam.startWithConfiguration(config)
```

```java Java
UXConfig config = new UXConfig.Builder("YOUR_APP_KEY")
    .enableAutomaticScreenNameTagging(true)
    .enableReadableFragmentNames(true)                 // v3.10.0+
    .enableMultiSessionRecord(true)
    .enableCrashHandling(true)
    .enableIntegrationLogging(false)
    .honorFlagSecure(false)
    .environment(Environment.RELEASE)
    .build();

UXCam.startWithConfiguration(config);
```

### Configuration Options

| Option | Default | Description |
|--------|---------|-------------|
| `enableAutomaticScreenNameTagging` | `true` | Auto-tag screens based on Activity/Fragment names |
| `enableReadableFragmentNames` | `true` | Show `Activity/Fragment` instead of hex hashes in timeline (v3.10.0+) |
| `enableMultiSessionRecord` | `true` | Record multiple sessions per app launch |
| `enableCrashHandling` | `true` | Capture crash reports and link them to session replays |
| `enableIntegrationLogging` | `false` | Print verbose SDK logs to Logcat for debugging |
| `enableVerboseLogging` | `false` | Extra-detailed logging (use only when requested by support) |
| `honorFlagSecure` | `false` | Respect Android's `FLAG_SECURE` window flag |
| `environment` | — | Filter sessions by build type: `ALPHA`, `BETA`, or `RELEASE` |
| `occlusions` | `[]` | Pre-configured occlusion rules applied at SDK start |
| `enableExperimentalForceQuitVideoRecovery` | `false` | **Experimental**: attempt to recover video data after force quit |

> ⚠️ **Deprecated Methods**
>
> The following initialization methods are deprecated since v3.4.0. Use `UXConfig.Builder` + `startWithConfiguration()` instead:
> - `startWithKey(String)` and all its overloads
> - `setAutomaticScreenNameTagging(boolean)`
> - `setImprovedScreenCaptureEnabled(boolean)` — improved capture is now always on
> - `enableMultiSessionRecording(boolean)` on old `UXConfig` — use builder
