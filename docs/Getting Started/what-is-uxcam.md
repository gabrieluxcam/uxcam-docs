---
title: What is UXCam?
excerpt: Understanding session recording, heatmaps, and user analytics
deprecated: false
hidden: false
metadata:
  title: 'What is UXCam?'
  description: 'Learn how UXCam session recording and analytics helps you understand user behavior'
  robots: index
next:
  description: ''
---

# What is UXCam?

UXCam is a user experience analytics platform that helps you understand how users interact with your mobile app or website through **session recordings**, **heatmaps**, and **behavioral analytics**.

---

## Core Features

### Session Recording

Watch video replays of real user sessions to see exactly what users experience - taps, swipes, navigation, and more.

- See the user's perspective, not just data points
- Identify UX issues and friction points
- Debug crashes with full context

### Heatmaps

Visualize user engagement patterns across your screens:

- **Tap heatmaps** - Where users interact most
- **Scroll heatmaps** - How far users scroll
- **Attention heatmaps** - What captures user focus

### User Analytics

Track user journeys, funnels, and behavior patterns:

- **Screen analytics** - Engagement and drop-off per screen
- **Conversion funnels** - Where users abandon flows
- **User segmentation** - Compare behavior across user groups

---

## Key Concepts

### Sessions

A **session** starts when a user opens your app and ends when they close it or it goes to background. Each session captures:

- Screen navigation sequence
- User interactions (taps, scrolls, gestures)
- Custom events you define
- Crashes and errors

### Screens

**Screens** are the individual pages or views users navigate through. Proper screen tagging enables:

- Heatmaps per screen
- Screen-level analytics
- Journey analysis

### Events

**Events** are specific user actions you want to track - like "Add to Cart" or "Complete Purchase". Events help you:

- Measure feature usage
- Build conversion funnels
- Segment users by behavior

### User Properties

**User properties** are attributes you assign to users - like subscription tier, account age, or device type. Properties enable:

- User segmentation
- Cohort analysis
- Personalized insights

---

## Privacy & Compliance

UXCam is designed with privacy in mind:

- **Automatic PII protection** - Passwords and sensitive fields are masked by default
- **Manual occlusion** - You control what's hidden in recordings
- **GDPR/CCPA compliant** - Tools for consent management and data deletion
- **SOC 2 Type II certified** - Enterprise-grade security

---

## Getting Your App Key

Every UXCam integration requires an app key:

1. Log in to your [UXCam Dashboard](https://app.uxcam.com)
2. Go to **App Settings** (click app name in top-left)
3. Copy your **App Key**

<GitHubCallout type="tip">Create separate keys for development and production to keep your data clean.</GitHubCallout>

---

## Ready to Start?

Choose your platform and integrate in under 5 minutes:

<Cards columns={3}>
  <Card title="Android" href="quick-start-android" icon="fa-brands fa-android">
    Native Android
  </Card>

  <Card title="iOS" href="quick-start-ios" icon="fa-brands fa-apple">
    Native iOS
  </Card>

  <Card title="React Native" href="quick-start-react-native" icon="fa-brands fa-react">
    Cross-platform
  </Card>

  <Card title="Flutter" href="quick-start-flutter" icon="fa-solid fa-feather">
    Cross-platform
  </Card>

  <Card title="Web" href="quick-start-web" icon="fa-solid fa-globe">
    Websites
  </Card>
</Cards>
