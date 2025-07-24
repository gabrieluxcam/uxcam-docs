---
title: React Native SDK Integration Guide
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
# React Native UXCam Integration Guide

Transform your React Native app into a data-driven product with comprehensive user session analytics, heatmaps, and behavioral insights. This guide provides a complete integration roadmap from basic setup to advanced customization.

## What Does a Successful Integration Look Like?

With a properly integrated UXCam SDK, you'll have complete visibility into user behavior across your React Native app. From detailed session replays and screen analytics to conversion funnels and user journey mapping, you'll understand exactly how users interact with your product.

**Key Benefits:**

* **Session Recordings**: Visual replays of user interactions
* **Screen Analytics**: Heat maps and engagement metrics per screen
* **User Journey Analysis**: Complete flow tracking and funnel analytics
* **Privacy Compliance**: GDPR/CCPA compliant data collection
* **Cross-Platform Insights**: Unified analytics for iOS and Android

## Integration Complexity Assessment

**Effort Level**: Medium (4-6 hours total)\
**Technical Complexity**: Moderate
**Team Coordination**: Low (primarily mobile team)
**Risk Level**: Low (non-breaking additions)

### Prerequisites Checklist

* [ ] React Native 0.68+ or Expo SDK 47+
* [ ] Node.js 16+ with npm/yarn package manager
* [ ] UXCam account with app key
* [ ] iOS deployment target 12.0+ / Android minSdkVersion 21+
* [ ] Development environment configured (Xcode/Android Studio for debug logs)

### Project Type Decision Matrix

| Project Type         | Setup Approach            | Key Considerations                         | Integration Time |
| -------------------- | ------------------------- | ------------------------------------------ | ---------------- |
| **React Native CLI** | Standard npm installation | Full native access, all features available | 4-5 hours        |
| **Expo Managed**     | Requires EAS Build        | Limited to managed workflow features       | 5-6 hours        |
| **Expo Bare**        | Standard installation     | Full customization, requires ejection      | 4-5 hours        |

## Phase-by-Phase Integration Roadmap

### Phase 1: Foundation Setup (1-2 hours) ➡️

**Goal**: Get basic session recording working\
**Output**: First session visible in UXCam dashboard

**Key Tasks:**

* SDK installation and dependency setup
* Secure API key configuration
* Basic initialization and verification
* Performance validation

**Success Criteria:**

* Session appears in dashboard within 30 seconds
* No initialization errors in logs
* App performance remains unaffected

### Phase 2: Screen Analytics (1-2 hours) ➡️

**Goal**: Enable meaningful screen tracking\
**Output**: All major screens properly named and timed

**Key Tasks:**

* Configure screen tagging strategy
* Implement navigation framework integration
* Handle WebView and modal screens
* Validate screen flow accuracy

**Success Criteria:**

* All major app screens have descriptive names
* Screen durations > 0 seconds consistently
* Navigation flows make logical sense

### Phase 3: Privacy Protection (1-2 hours) ➡️

**Goal**: Ensure GDPR/CCPA compliance\
**Output**: All sensitive data properly occluded

**Key Tasks:**

* Identify sensitive data elements
* Implement occlusion strategies
* Configure WebView privacy
* Test compliance thoroughly

**Success Criteria:**

* No PII visible in session recordings
* Forms and sensitive screens properly masked
* Privacy audit passes completely

### Phase 4: Event Tracking (1 hour) ➡️

**Goal**: Capture business-critical user actions\
**Output**: Key conversion events tracked with context

**Key Tasks:**

* Define business-critical events
* Implement event tracking with properties
* Set up conversion funnels
* Validate event accuracy

**Success Criteria:**

* Business events captured reliably
* Event properties provide meaningful context
* Funnel analysis becomes possible

### Phase 5: User Analytics (30 minutes) ➡️

**Goal**: Enable user-level insights and segmentation\
**Output**: Users identified and tracked across sessions

**Key Tasks:**

* Implement user identity mapping
* Configure user properties
* Test cross-session tracking
* Validate user flows

**Success Criteria:**

* Users properly identified across sessions
* Key user attributes tracked consistently
* User journey analysis enabled

### Phase 6: Advanced Configuration (30 minutes)

**Goal**: Optimize performance and customize behavior\
**Output**: Production-ready configuration

**Key Tasks:**

* Configure session control settings
* Optimize performance parameters
* Set up third-party integrations
* Implement custom validation

**Success Criteria:**

* Performance optimized for production
* Advanced features configured properly
* Monitoring and alerting in place

## Quick Start: Basic Integration

<GitHubReleaseBadge owner="uxcam" repo="react-native-ux-cam" />

Get started with just a few lines of code:

### 1. Installation

<Terminal>
  {`
          # Using npm
          npm install react-native-ux-cam

          # Using yarn
          yarn add react-native-ux-cam
          `}
</Terminal>

### 2. Basic Setup

```javascript App.js
import RNUxcam from 'react-native-ux-cam';

// Enable screen recordings for both iOS and Android
RNUxcam.optIntoSchematicRecordings();

const configuration = {
  userAppKey: 'YOUR_API_KEY',
  enableAutomaticScreenNameTagging: false, // Manual tagging recommended
  enableImprovedScreenCapture: true,
  // enableCrashHandling: true by default - no need to specify
};

RNUxcam.startWithConfiguration(configuration);
```

### 3. Verification

Your first session will appear in the UXCam dashboard within 30 seconds after the app goes to background.

> 👍 **Integration Success!**\
> This basic setup gets you session recording immediately. Continue with the phase-by-phase approach below for complete functionality.

## Navigation Framework Support

### React Navigation v6 (Recommended)

```javascript
import { useFocusEffect } from '@react-navigation/native';

function HomeScreen() {
  useFocusEffect(() => {
    RNUxcam.tagScreenName('Home Screen');
  });

  return <YourScreenContent />;
}
```

### Expo Router

```javascript
import { useSegments } from 'expo-router';

export default function Layout() {
  const segments = useSegments();

  useEffect(() => {
    RNUxcam.tagScreenName(segments.join('/') || 'Home');
  }, [segments]);
}
```

## TypeScript Support

```typescript
import RNUxcam from 'react-native-ux-cam';

interface UXCamConfiguration {
  userAppKey: string;
  enableAutomaticScreenNameTagging?: boolean;
  enableImprovedScreenCapture?: boolean;
  enableMultiSessionRecord?: boolean;
  enableIntegrationLogging?: boolean;
}

const configuration: UXCamConfiguration = {
  userAppKey: 'YOUR_API_KEY',
  enableAutomaticScreenNameTagging: false,
  enableImprovedScreenCapture: true,
};

RNUxcam.optIntoSchematicRecordings();
RNUxcam.startWithConfiguration(configuration);
```

## Development vs Production Configuration

```javascript
const getUXCamConfig = () => {
  const config = {
    userAppKey: __DEV__
      ? process.env.UXCAM_DEV_KEY
      : process.env.UXCAM_PROD_KEY,
    enableAutomaticScreenNameTagging: false,
    enableImprovedScreenCapture: true,
  };

  if (__DEV__) {
    config.enableMultiSessionRecord = true;
    config.enableIntegrationLogging = true; // Debug logs visible in Xcode/Android Studio only
  }

  return config;
};
```

## Common Integration Patterns

### Error Handling

```javascript
const initializeUXCam = async () => {
  try {
    RNUxcam.optIntoSchematicRecordings();
    const config = getUXCamConfig();
    await RNUxcam.startWithConfiguration(config);
    console.log('UXCam initialized successfully');
  } catch (error) {
    console.error('UXCam initialization failed:', error);
    // Implement fallback behavior
  }
};
```

### Platform-Specific Setup

```javascript
import { Platform } from 'react-native';

// Required for both iOS and Android - enables screen recordings
RNUxcam.optIntoSchematicRecordings();

// Platform-specific configuration (if needed)
if (Platform.OS === 'ios') {
  // iOS-specific setup can go here
}

if (Platform.OS === 'android') {
  // Android-specific setup can go here
}
```

## What's Next?

🚀 **Ready to dive deeper?** Continue with our phase-by-phase integration:

1. **[Bootstrap Setup](set-up-the-sdk-and-start-recording)** - Secure configuration and environment setup
2. **[Screen Tagging](screen-tagging-react-native)** - Implement comprehensive screen analytics
3. **[Privacy Protection](sensitive-data-occlusion-react-native/)** - Ensure GDPR/CCPA compliance
4. **[Event Tracking](custom-events-and-properties-react-native)** - Capture business-critical user actions
5. **[User Analytics](custom-users-and-properties-react-native)** - Enable user-level insights
6. **[Advanced Configuration](advanced-configuration-and-apis-2/)** - Optimize and customize

### Quick Links

📖 **[Expo Integration Guide](expo-installation)** - Complete Expo-specific setup\
🔧 **[Troubleshooting Guide](troubleshooting-react-native)** - Common issues and solutions

***

**Need Help?** Our Customer Success team is available at [team@uxcam.com](mailto:team@uxcam.com) for integration support and best practices guidance.