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

> ⚠️ **Important:** Currently, UXCam only captures **native crashes** (iOS/Android). JavaScript crashes are not automatically captured and require manual reporting.

> ⚠️ **Tip:** Running two crash reporters (e.g., Crashlytics *and* UXCam) can lead to conflicts. Disable one of them at app start.

***

### Enable / Disable Crash Handling

Call **before** `RNUxcam.startWithConfiguration()`:

```javascript
// Disable UXCam crash, ANR and freeze capture
RNUxcam.disableCrashHandling(true);
```

| Parameter  | Default | Meaning                                                                              |
| ---------- | ------- | ------------------------------------------------------------------------------------ |
| `disabled` | `false` | `true` stops UXCam from recording crashes, ANRs and freezes for the current session. |

***

### Quick Verification

1. **For native crashes**: Simulate a native crash in a debug build.
2. **For JavaScript errors**: Use manual reporting with `reportExceptionEvent()`.
3. Wait for the session to upload.
4. In the dashboard, open **Issues → Crashes / ANRs / UI Freezes**.
5. Confirm the stack trace and replay are linked to the problem event.

> **Note**: JavaScript crashes will only appear if manually reported using `reportExceptionEvent()`.

***

### React Native Specific Considerations

#### JavaScript Error Handling (Manual Reporting Required)

Since UXCam doesn't automatically capture JavaScript crashes, you need to manually report them:

```javascript
import RNUxcam from 'react-native-ux-cam';

// Set up global error handler for manual reporting
const originalErrorHandler = ErrorUtils.setGlobalHandler;

ErrorUtils.setGlobalHandler = (error, isFatal) => {
  // Manually report JavaScript errors to UXCam
  RNUxcam.reportExceptionEvent(error, {
    errorType: 'JavaScriptError',
    isFatal: isFatal,
  });

  // Call original handler
  if (originalErrorHandler) {
    originalErrorHandler(error, isFatal);
  }
};
```

#### Component Error Boundaries

```javascript
import React from 'react';
import RNUxcam from 'react-native-ux-cam';

class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // Report to UXCam
    RNUxcam.reportExceptionEvent(error, {
      componentStack: errorInfo.componentStack,
      errorType: 'ReactErrorBoundary',
    });
  }

  render() {
    if (this.state.hasError) {
      return <Text>Something went wrong.</Text>;
    }

    return this.props.children;
  }
}
```

#### Async Error Handling

```javascript
import RNUxcam from 'react-native-ux-cam';

// Handle async errors with manual reporting
const handleAsyncError = async (asyncFunction) => {
  try {
    return await asyncFunction();
  } catch (error) {
    // Manually report async errors since they're not automatically captured
    RNUxcam.reportExceptionEvent(error, {
      errorType: 'AsyncError',
      functionName: asyncFunction.name,
    });
    throw error;
  }
};
```

***

### Best Practices

* **Native crashes are automatic**: UXCam automatically captures native iOS/Android crashes
* **JavaScript crashes require manual reporting**: Use `reportExceptionEvent()` for JavaScript errors
* Decide at launch **which** crash reporter owns crashes; do **not** toggle per session.
* Keep custom exception/event names in **PascalCase** or **snake\_case** and store them as constants.
* Avoid logging PII in exception messages—use IDs or hashed values instead.
* Use React Error Boundaries for component-level error handling.
* Handle async errors properly to prevent unhandled promise rejections.

***

### TypeScript Support

```typescript
import RNUxcam from 'react-native-ux-cam';

interface ExceptionProperties {
  errorType?: string;
  componentStack?: string;
  functionName?: string;
  [key: string]: string | number | boolean | undefined;
}

// Typed exception reporting
const reportException = (error: Error, properties?: ExceptionProperties) => {
  RNUxcam.reportExceptionEvent(error, properties);
};
```

<br />

```typescript
import RNUxcam from 'react-native-ux-cam';

interface ExceptionProperties {
  errorType?: string;
  componentStack?: string;
  functionName?: string;
  [key: string]: string | number | boolean | undefined;
}

// Typed exception reporting
const reportException = (error: Error, properties?: ExceptionProperties) => {
  RNUxcam.reportExceptionEvent(error, properties);
};
```