---
title: Crashes and ANRs
deprecated: false
hidden: false
metadata:
  robots: index
---
## Crash, ANR & UI‑Freeze Monitoring

UXCam ships with a **lightweight diagnostics layer** that automatically records:

| Signal           | How UXCam detects it                                                                                                                              | What is captured                      |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------- |
| **Native Crash** | Installs its own `Thread.UncaughtExceptionHandler`, then calls any previous handler. Captures the stack trace and uploads it on the next session. | Native stack trace + device state     |
| **ANR**          | A tiny runnable pings the **main thread** at fixed intervals. If the gap between pings is **> 1 s**, UXCam flags an ANR and snapshots the stack.  | Main‑thread stack trace at ANR onset  |
| **UI Freeze**    | Any stretch where the **main thread is unresponsive for ≥ 2 s**, even if no touch was expected.                                                   | Start/stop timestamps + stack samples |

> ⚠️ **Important:** UXCam captures **native crashes** (iOS/Android) only. JavaScript exceptions are not captured automatically, and the React Native SDK does not currently expose a handled‑exception reporting API — `reportExceptionEvent()` is available for the native iOS, Android and Flutter SDKs only. See [JavaScript errors](#javascript-errors) below for the current workaround.

> ⚠️ **Tip:** Running two crash reporters (e.g., Crashlytics *and* UXCam) can lead to conflicts. Disable one of them at app start.

***

### Enable / Disable Crash Handling

Crash handling is controlled through the configuration object passed to `RNUxcam.startWithConfiguration()`:

```javascript
import RNUxcam from 'react-native-ux-cam';

RNUxcam.startWithConfiguration({
  userAppKey: 'YOUR_APP_KEY',
  enableCrashHandling: false, // default: true
});
```

| Parameter             | Default | Meaning                                                                                                                      |
| --------------------- | ------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `enableCrashHandling` | `true`  | `false` stops UXCam from recording native crashes for the session. On iOS this also disables ANR and UI‑freeze capture. |

***

### Quick Verification

1. Simulate a **native** crash in a debug build.
2. Wait for the session to upload.
3. In the dashboard, open **Issues → Crashes / ANRs / UI Freezes**.
4. Confirm the stack trace and replay are linked to the problem event.

***

### JavaScript errors

Uncaught JavaScript exceptions are handled by the React Native runtime, not by the native layer UXCam hooks into, so they do not appear in **Issues → Crashes**. The React Native SDK does not yet provide `reportExceptionEvent()`.

Until it does, you can record JavaScript errors as **custom events** from a global error handler so they still show up on the session timeline:

```javascript
import RNUxcam from 'react-native-ux-cam';

const previousHandler = ErrorUtils.getGlobalHandler();

ErrorUtils.setGlobalHandler((error, isFatal) => {
  RNUxcam.logEvent('js_error', {
    name: error?.name ?? 'Error',
    message: String(error?.message ?? error).slice(0, 1000),
    fatal: isFatal ? 1 : 0,
  });

  if (previousHandler) {
    previousHandler(error, isFatal);
  }
});
```

The same call works inside a React Error Boundary's `componentDidCatch` or a `catch` block around async code. Only number and string property values are supported (max 100 properties, 1 KiB each).

***

### Best Practices

* **Native crashes are automatic**: UXCam captures native iOS/Android crashes without extra code.
* **JavaScript errors are not**: use the `logEvent` workaround above until a React Native `reportExceptionEvent()` ships.
* Decide at launch **which** crash reporter owns crashes; do **not** toggle per session.
* Keep custom event names in **PascalCase** or **snake\_case** and store them as constants.
* Avoid logging PII in error messages — use IDs or hashed values instead.
