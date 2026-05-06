---
title: Report Handled Exceptions
deprecated: false
hidden: false
metadata:
  description: >-
    Send caught exceptions and handled errors to UXCam alongside session replays
    for better debugging context.
  robots: index
---
# Report Handled Exceptions

Send caught exceptions to UXCam so they appear alongside session replays. This gives you full context — see exactly what the user was doing when the error occurred.

## API Reference

```javascript
import RNUxcam from 'react-native-ux-cam';

RNUxcam.reportExceptionEvent(error);
```

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
    // Report to UXCam — this links the error to the session replay
    RNUxcam.reportExceptionEvent(error);

    // Optionally log additional context
    RNUxcam.logEvent('Component Error', {
      componentStack: errorInfo.componentStack?.substring(0, 200),
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

const defaultHandler = ErrorUtils.getGlobalHandler();

ErrorUtils.setGlobalHandler((error, isFatal) => {
  // Report to UXCam
  RNUxcam.reportExceptionEvent(error);

  // Call the default handler
  if (defaultHandler) {
    defaultHandler(error, isFatal);
  }
});
```

## Async Error Pattern

Report errors from API calls and async operations:

```javascript
const fetchUserData = async (userId) => {
  try {
    const response = await api.getUser(userId);
    return response.data;
  } catch (error) {
    RNUxcam.reportExceptionEvent(error);
    RNUxcam.logEvent('API Error', {
      endpoint: 'getUser',
      status: error.response?.status?.toString(),
    });
    throw error;
  }
};
```

## Best Practices

- **Report meaningful errors** — don't report expected conditions like "user not logged in"
- **Add context with events** — pair `reportExceptionEvent` with `logEvent` for richer debugging
- **Keep stack traces** — pass the original `Error` object, not just a string message
- **Don't swallow errors** — always re-throw or handle the error after reporting
