---
title: Quick Start - React Native
excerpt: Get UXCam running in your React Native app in 5 minutes
deprecated: false
hidden: false
metadata:
  title: 'React Native Quick Start - UXCam'
  description: 'Fastest way to add UXCam session recording to your React Native app'
  robots: index
next:
  description: ''
  pages:
    - type: basic
      slug: react-native
      title: Full React Native Guide
---

# React Native Quick Start

Get session recording working in your React Native app in under 5 minutes.

## Prerequisites

- React Native 0.68+ or Expo SDK 47+
- iOS deployment target 12.0+ / Android minSdkVersion 21+
- A [UXCam account](https://app.uxcam.com/signup) with an app key

<GitHubCallout type="note">**Using Expo?** Managed workflow requires EAS Build. See our [Expo installation guide](/docs/expo-installation-react-native) for details.</GitHubCallout>

---

## Step 1: Install the Package

```bash
# npm
npm install react-native-ux-cam

# yarn
yarn add react-native-ux-cam
```

For iOS, install pods:

```bash
cd ios && pod install
```

---

## Step 2: Initialize UXCam

In your `App.js` or entry file:

```javascript
import RNUxcam from 'react-native-ux-cam';

// Enable screen recordings (required)
RNUxcam.optIntoSchematicRecordings();

// Start UXCam
RNUxcam.startWithConfiguration({
  userAppKey: 'YOUR_APP_KEY',
  enableAutomaticScreenNameTagging: false,
  enableImprovedScreenCapture: true,
});
```

<Accordion title="TypeScript version">
```typescript
import RNUxcam from 'react-native-ux-cam';

interface UXCamConfig {
  userAppKey: string;
  enableAutomaticScreenNameTagging?: boolean;
  enableImprovedScreenCapture?: boolean;
}

const config: UXCamConfig = {
  userAppKey: 'YOUR_APP_KEY',
  enableAutomaticScreenNameTagging: false,
  enableImprovedScreenCapture: true,
};

RNUxcam.optIntoSchematicRecordings();
RNUxcam.startWithConfiguration(config);
```
</Accordion>

---

## Step 3: Verify It Works

1. Run your app on a device or emulator
2. Navigate through a few screens
3. Send the app to background
4. Check your [UXCam Dashboard](https://app.uxcam.com) - your session should appear within 30 seconds

<GitHubCallout type="tip">Check Xcode console (iOS) or Android Studio logcat for UXCam initialization messages.</GitHubCallout>

---

## Next Steps

You're recording sessions! Now customize your integration:

<Cards columns={2}>
  <Card title="Full React Native Guide" href="/docs/react-native" icon="fa-brands fa-react">
    Complete setup with all configuration options
  </Card>

  <Card title="Tag Screens" href="/docs/screen-tagging-react-native" icon="fa-solid fa-mobile">
    Enable heatmaps and screen analytics
  </Card>

  <Card title="Mask Sensitive Data" href="/docs/sensitive-data-occlusion-react-native" icon="fa-solid fa-eye-slash">
    Protect passwords and PII
  </Card>

  <Card title="Track Events" href="/docs/react-native/events" icon="fa-solid fa-bolt">
    Capture custom user actions
  </Card>
</Cards>
