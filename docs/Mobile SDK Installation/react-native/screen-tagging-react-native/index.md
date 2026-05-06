---
title: Tagging Screens
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    Configure manual screen tagging in React Native to unlock heat-maps, funnels
    and journey analytics.
  robots: index
next:
  description: ''
---
Good screen names turn raw replays into **actionable heat-maps**, **screen analytics**, **conversion funnels** and **journey charts**. This guide walks you through choosing a tagging approach, implementing it, and verifying that every screen shows up with a meaningful duration.

## Automatic vs Manual Tagging

| Tagging Method | React Native Support | Recommendation |
|----------------|----------------------|----------------|
| **Automatic Screen Tagging** | ❌ Not reliable — React Native navigation doesn't expose native screen changes | Not recommended |
| **Manual Screen Tagging** | ✅ Fully supported and reliable | **Strongly recommended** |

> ⚠️ **Important**: For React Native, **manual tagging is strongly recommended**. Unlike native iOS/Android apps, React Native screens are JavaScript-based and don't trigger native screen lifecycle events that automatic tagging depends on.

```javascript
const configuration = {
  userAppKey: 'YOUR_API_KEY',
  enableAutomaticScreenNameTagging: false, // Manual control recommended
  enableImprovedScreenCapture: true,
};
```

## Basic Screen Tagging

Tag screens using `RNUxcam.tagScreenName()`:

```javascript
import RNUxcam from 'react-native-ux-cam';

// ✅ Good screen names — descriptive and human-readable
RNUxcam.tagScreenName('Home Dashboard');
RNUxcam.tagScreenName('Product Details');
RNUxcam.tagScreenName('Checkout Payment');

// ❌ Avoid technical names
RNUxcam.tagScreenName('HomeComponent');
RNUxcam.tagScreenName('Screen1');
```

**Naming tips:**
- Use descriptive, human-readable names
- Keep names consistent across similar screens
- Consider how names appear in analytics dashboards

## Centralized Screen Tagging Hook

Create a reusable hook for consistent tagging across your app:

```javascript hooks/useScreenTracking.js
import { useFocusEffect } from '@react-navigation/native';
import RNUxcam from 'react-native-ux-cam';
import { useCallback } from 'react';

export const useScreenTracking = (screenName) => {
  useFocusEffect(
    useCallback(() => {
      if (screenName) {
        RNUxcam.tagScreenName(screenName);
      }
    }, [screenName])
  );
};

// Usage in any screen
function ProductScreen() {
  useScreenTracking('Product Details');
  return (/* Screen content */);
}
```

## Expo Router Integration

```javascript app/_layout.js
import { useSegments } from 'expo-router';
import { useEffect } from 'react';
import RNUxcam from 'react-native-ux-cam';

export default function RootLayout() {
  const segments = useSegments();

  useEffect(() => {
    if (segments.length > 0) {
      const screenName = segments
        .filter(s => !s.startsWith('('))
        .join(' - ') || 'Home';
      RNUxcam.tagScreenName(screenName);
    }
  }, [segments]);

  return (/* Layout content */);
}
```

## Verify Your Tags

1. In Debug, navigate through every tagged screen, then background the app.
2. Once the session uploads, confirm:
   - Each screen appears **exactly once** with duration **> 0 s**
   - No "Unknown" or class-name entries remain
   - Names match your analytics terminology
3. Fix any issues by removing duplicate calls or adding a missing tag handler.

A solid screen-naming strategy makes **PII masking** and **heat-map analysis** far easier later on.

***

## Troubleshooting Cheat-Sheet

| Issue | Quick Diagnosis | Solution |
|-------|----------------|----------|
| **0 s screens** | Duplicate tag same frame | Debounce manual call or check re-renders |
| **Screen missing** | Navigation path not handled | Add `useScreenTracking()` to the screen |
| **Generic names** | Using auto-tagging | Set `enableAutomaticScreenNameTagging: false` |
| **Rapid duplicates** | Tab switching < 300 ms | Add debounce to tag logic |

***

## Next Steps

<Cards columns={3}>
  <Card title="React Navigation & Wix" href="react-navigation-tagging-react-native" icon="fa-route">
    Full integration guides for React Navigation v6, tab navigators, and Wix/React Native Navigation.
  </Card>

  <Card title="WebView Tagging" href="tagging-webviews-react-native" icon="fa-globe">
    Tag screens inside WebViews using URL changes and JavaScript bridges.
  </Card>

  <Card title="Mask PII Data" href="../sensitive-data-occlusion-react-native/" icon="fa-eye-slash">
    Protect your users' privacy — mask or blur screens, views and fields.
  </Card>
</Cards>
