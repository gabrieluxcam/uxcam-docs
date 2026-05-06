---
title: React Native SDK Integration Guide
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    Complete guide to integrating the UXCam React Native SDK for session
    recordings, screen analytics, heat maps, and user journey insights.
  robots: index
next:
  description: ''
---
# React Native UXCam Integration Guide

Add session recordings, heat maps, and user journey analytics to your React Native app. This guide takes you from installation to your first recorded session.

<GitHubReleaseBadge owner="uxcam" repo="react-native-ux-cam" />

## Prerequisites

- React Native 0.68+ or Expo SDK 47+
- Node.js 16+ with npm or yarn
- UXCam account with app key ([sign up](https://uxcam.com))
- iOS deployment target 12.0+ / Android minSdkVersion 21+

> 📘 **Expo projects** require EAS Build for native modules. See our [Expo Installation Guide](expo-installation-react-native) for the complete setup.

***

## Step 1: Install the SDK

<Terminal>
  {`
          # Using npm
          npm install react-native-ux-cam

          # Using yarn
          yarn add react-native-ux-cam
          `}
</Terminal>

For iOS, install CocoaPods dependencies:

```bash
cd ios && pod install && cd ..
```

## Step 2: Initialize UXCam

```javascript App.js
import React, { useEffect } from 'react';
import RNUxcam from 'react-native-ux-cam';

const App = () => {
  useEffect(() => {
    // Enable video recording (required for both iOS and Android)
    RNUxcam.optIntoVideoRecordings();

    const configuration = {
      userAppKey: 'YOUR_API_KEY',
      enableAutomaticScreenNameTagging: false, // Manual tagging recommended for RN
      enableImprovedScreenCapture: true,
    };

    RNUxcam.startWithConfiguration(configuration);
  }, []);

  return (/* Your app */);
};

export default App;
```

## Step 3: Tag Your Screens

React Native requires manual screen tagging. Use `useFocusEffect` with React Navigation:

```javascript screens/HomeScreen.js
import { useFocusEffect } from '@react-navigation/native';
import RNUxcam from 'react-native-ux-cam';

function HomeScreen() {
  useFocusEffect(
    React.useCallback(() => {
      RNUxcam.tagScreenName('Home Dashboard');
    }, [])
  );

  return (/* Screen content */);
}
```

Or create a reusable hook:

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
```

## Step 4: Protect Sensitive Data

Configure privacy rules at initialization time:

```javascript
import { UXCamOcclusionType } from 'react-native-ux-cam/UXCamOcclusion';

const configuration = {
  userAppKey: 'YOUR_API_KEY',
  enableAutomaticScreenNameTagging: false,
  occlusions: [
    {
      type: UXCamOcclusionType.Overlay,
      color: 0x000000,
      hideGestures: true,
      screens: ['Login Form', 'Payment Details'],
    },
  ],
};
```

Or configure screen-level rules from the **UXCam Dashboard → Settings → Video Recording Privacy** (no code required).

## Step 5: Build & Test

1. **Run the app** on a device or simulator
2. **Navigate through 3-4 screens** for at least 30 seconds
3. **Background the app** — this triggers session upload
4. **Check the UXCam dashboard** — your session should appear within 60 seconds

> 👍 **Verification tip**: Enable `enableIntegrationLogging: true` in your config and check native IDE logs (Xcode for iOS, Android Studio for Android) to see `Verification successful` and `Session uploaded` messages.

***

## Configuration Options

```javascript
const configuration = {
  userAppKey: 'YOUR_API_KEY',

  // Screen tagging
  enableAutomaticScreenNameTagging: false,  // default: true (set false for RN)

  // Recording behavior
  enableImprovedScreenCapture: true,        // default: false
  enableMultiSessionRecord: true,           // default: true
  enableCrashHandling: true,                // default: true

  // Debugging (development only)
  enableIntegrationLogging: __DEV__,        // default: false

  // Privacy — applied at startup
  occlusions: [/* overlay, blur, or text field rules */],
};
```

***

## Development vs Production

```javascript
const getUXCamConfig = () => {
  const config = {
    userAppKey: __DEV__
      ? process.env.UXCAM_DEV_KEY
      : process.env.UXCAM_PROD_KEY,
    enableAutomaticScreenNameTagging: false,
  };

  if (__DEV__) {
    config.enableIntegrationLogging = true;
    config.enableMultiSessionRecord = true;
  }

  return config;
};
```

**Security best practices:**
- Never commit API keys to version control
- Use different keys for development and production
- Store production keys in CI/CD environment variables

***

## TypeScript Support

```typescript
import RNUxcam from 'react-native-ux-cam';

interface UXCamConfig {
  userAppKey: string;
  enableAutomaticScreenNameTagging?: boolean;
  enableImprovedScreenCapture?: boolean;
}

const config: UXCamConfig = {
  userAppKey: 'YOUR_API_KEY',
  enableAutomaticScreenNameTagging: false,
};

RNUxcam.optIntoVideoRecordings();
RNUxcam.startWithConfiguration(config);
```

***

## Next Steps

<Cards columns={3}>
  <Card title="Expo Installation" href="expo-installation-react-native" icon="fa-mobile">
    Complete Expo-specific setup with EAS Build configuration.
  </Card>

  <Card title="Screen Tagging" href="screen-tagging-react-native/" icon="fa-tags">
    Comprehensive screen tagging with React Navigation, Expo Router, and WebViews.
  </Card>

  <Card title="Mask PII Data" href="sensitive-data-occlusion-react-native/" icon="fa-eye-slash">
    Protect your users' privacy — mask or blur screens, views and fields.
  </Card>

  <Card title="Send Events" href="events" icon="fa-bolt">
    Capture business-critical user actions with custom events.
  </Card>

  <Card title="User Properties" href="user-properties" icon="fa-user">
    Identify users and track properties across sessions.
  </Card>

  <Card title="Advanced Configuration" href="advanced-configuration-and-apis-2/" icon="fa-cog">
    Recording control, crash handling, opt-in/opt-out, and more.
  </Card>
</Cards>

***

**Need Help?** Check our [Troubleshooting Guide](troubleshooting-react-native-uxcam-react-native) or contact [team@uxcam.com](mailto:team@uxcam.com).
