---
title: Crashes & ANRs
deprecated: false
hidden: false
metadata:
  robots: index
---
## Crash, ANR & UI‑Freeze Monitoring

UXCam ships with a **lightweight diagnostics layer** that automatically records:

| Signal        | How UXCam detects it                                                                                                                              | What is captured                       |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------- |
| **Crash**     | Installs its own `Thread.UncaughtExceptionHandler`, then calls any previous handler. Captures the stack trace and uploads it on the next session. | Java/Kotlin stack trace + device state |
| **ANR**       | A tiny runnable pings the **main thread** at fixed intervals. If the gap between pings is **> 1 s**, UXCam flags an ANR and snapshots the stack.  | Main‑thread stack trace at ANR onset   |
| **UI Freeze** | Any stretch where the **main thread is unresponsive for ≥ 2 s**, even if no touch was expected.                                                   | Start/stop timestamps + stack samples  |

> ⚠️ **Tip:** Running two crash reporters (e.g., Crashlytics *and* UXCam) can lead to conflicts. Disable one of them at app start.

***

### Enable / Disable Crash Handling

Set `enableCrashHandling` to `false` in your configuration **before** starting the SDK:

```swift
// Using UXCamConfiguration (recommended since v3.4.0)
let config = UXCamConfiguration(appKey: "YOUR_APP_KEY")
config.enableCrashHandling = false  // default: true
UXCam.start(with: config)
```

| Property              | Default | Meaning                                                                              |
| --------------------- | ------- | ------------------------------------------------------------------------------------ |
| `enableCrashHandling` | `true`  | `false` stops UXCam from recording crashes, ANRs and freezes for the current session. |

***

### Quick Verification

1. **Simulate** a crash, ANR, or long freeze in a debug build.
2. Wait for the session to upload.
3. In the dashboard, open **Issues → Crashes / ANRs / UI Freezes**.
4. Confirm the stack trace and replay are linked to the problem event.

***

### Best Practices

* Decide at launch **which** crash reporter owns crashes; do **not** toggle per session.
* Keep custom exception/event names in **PascalCase** or **snake\_case** and store them as constants.
* Avoid logging PII in exception messages—use IDs or hashed values instead.