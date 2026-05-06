---
title: Pause & Resume Recording
deprecated: false
hidden: false
metadata:
  description: >-
    Temporarily pause UXCam screen recording during sensitive flows without
    stopping the entire session.
  robots: index
---
# Pause & Resume Recording

Temporarily pause screen recording for sensitive flows (e.g., payment entry, biometric authentication) without stopping the entire session. The session continues, but no frames are captured while paused.

## API Reference

```javascript
import RNUxcam from 'react-native-ux-cam';

// Pause recording — frames stop being captured
RNUxcam.pauseScreenRecording();

// Resume recording — frames captured again
RNUxcam.resumeScreenRecording();
```

## Use Cases

### Sensitive Flow Protection

```javascript screens/PaymentScreen.js
import { useFocusEffect } from '@react-navigation/native';
import RNUxcam from 'react-native-ux-cam';

function PaymentScreen() {
  useFocusEffect(
    React.useCallback(() => {
      // Pause recording while on payment screen
      RNUxcam.pauseScreenRecording();
      RNUxcam.tagScreenName('Payment Entry');

      return () => {
        // Resume when leaving the screen
        RNUxcam.resumeScreenRecording();
      };
    }, [])
  );

  return (/* Payment form */);
}
```

### Conditional Pause

```javascript
const handleSensitiveAction = (isSensitive) => {
  if (isSensitive) {
    RNUxcam.pauseScreenRecording();
  } else {
    RNUxcam.resumeScreenRecording();
  }
};
```

## When to Use Pause vs Occlusion

| Approach | What Happens | Best For |
|----------|-------------|----------|
| **Pause/Resume** | No frames captured at all | Highly sensitive flows where no visual data should exist |
| **Screen Occlusion** | Frames captured but blurred/overlaid | Analytics still needed (gestures, timing) but content hidden |
| **View-Level Occlusion** | Specific views hidden | Only certain fields are sensitive |

> 💡 **Tip**: Prefer screen-based occlusion (blur/overlay) when you still want gesture analytics. Use pause/resume only when absolutely no frames should be recorded.
