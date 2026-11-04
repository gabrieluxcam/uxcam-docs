---
title: Report Handled Exceptions
deprecated: false
hidden: false
metadata:
  description: >-
    Record caught exceptions and JavaScript errors in UXCam as custom events so
    they appear on the session timeline next to the replay.
  robots: index
---
# Report Handled Exceptions

Record the errors your app catches, so they appear on the session timeline next to the replay. You can see exactly what the user was doing when the error happened.

> 🚧 No exception API in React Native yet
>
> The React Native SDK doesn't have a `reportExceptionEvent()` method yet (checked up to `react-native-ux-cam` 6.0.26). Calling it throws `undefined is not a function`. Until a dedicated API ships, record errors as **custom events** with `RNUxcam.logEvent()`, as shown below. Native iOS and Android crashes are still captured automatically. See [Crashes and ANRs](https://developer.uxcam.com/docs/crashes-and-anrs-react-native).

## Logging an Error as an Event

```javascript
import RNUxcam from 'react-native-ux-cam';

RNUxcam.logEvent('js_error', {
  name: error?.name ?? 'Error',
  message: String(error?.message ?? error).slice(0, 1000),
});
```

Event properties accept string and number values, with up to 20 properties per event and 1,024 characters per value.

## Error Boundary Integration

Capture React component errors with an Error Boundary:

```javascript components/UXCamErrorBoundary.js
import React, { Component } from 'react';
import RNUxcam from 'react-native-ux-cam';

class UXCamErrorBoundary extends Component {
  state = { hasError: false };

  static getDerivedStateFromError() {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // Record the error on the session timeline
    RNUxcam.logEvent('js_error', {
      source: 'error_boundary',
      name: error?.name ?? 'Error',
      message: String(error?.message ?? error).slice(0, 1000),
      componentStack: errorInfo?.componentStack?.substring(0, 500) ?? '',
    });
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || null;
    }
    return this.props.children;
  }
}

export default UXCamErrorBoundary;
```

Usage:

```javascript App.js
<UXCamErrorBoundary fallback={<ErrorScreen />}>
  <YourAppContent />
</UXCamErrorBoundary>
```

## Global Error Handler

Capture unhandled JavaScript errors using React Native's `ErrorUtils`:

```javascript utils/errorHandler.js
import RNUxcam from 'react-native-ux-cam';

const previousHandler = ErrorUtils.getGlobalHandler();

ErrorUtils.setGlobalHandler((error, isFatal) => {
  RNUxcam.logEvent('js_error', {
    source: 'global_handler',
    name: error?.name ?? 'Error',
    message: String(error?.message ?? error).slice(0, 1000),
    fatal: isFatal ? 1 : 0,
  });

  // Keep React Native's default behaviour
  if (previousHandler) {
    previousHandler(error, isFatal);
  }
});
```

## Async Error Pattern

Record errors from API calls and async operations:

```javascript
const fetchUserData = async (userId) => {
  try {
    const response = await api.getUser(userId);
    return response.data;
  } catch (error) {
    RNUxcam.logEvent('api_error', {
      endpoint: 'getUser',
      status: String(error?.response?.status ?? ''),
      message: String(error?.message ?? error).slice(0, 1000),
    });
    throw error;
  }
};
```

## Best Practices

- **Use one event name for errors**, for example `js_error`, so you can filter and count them in the dashboard.
- **Record meaningful errors.** Don't log expected conditions such as "user not logged in".
- **Keep messages short.** Trim long messages and stacks to stay within the 1,024-character limit per value.
- **Don't swallow errors.** Re-throw or handle the error after logging it.
- **Avoid PII** in error messages. Use IDs or hashed values instead.
