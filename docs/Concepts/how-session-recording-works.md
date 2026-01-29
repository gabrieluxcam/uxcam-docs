---
title: How Session Recording Works
excerpt: Technical overview of UXCam's screen capture and session reconstruction
deprecated: false
hidden: false
metadata:
  title: 'How Session Recording Works - UXCam'
  description: 'Understand the technical approach behind UXCam session recording'
  robots: index
next:
  description: ''
---

# How Session Recording Works

UXCam captures user sessions by recording screen content, touch interactions, and navigation events. This guide explains the technical approach so you can make informed decisions about privacy, performance, and integration.

---

## Overview

UXCam doesn't record video in the traditional sense. Instead, it captures screen snapshots at key moments and reconstructs the session as a video during playback.

```
Screen Snapshots + Touch Events + Metadata → Compressed Data → Upload → Session Reconstruction → Playback
```

This approach provides:
- **Smaller data sizes** than continuous video
- **Better privacy control** - specific elements can be excluded before capture
- **Lower performance impact** than screen recording

---

## What Gets Captured

### Screen Content

UXCam captures the visual state of screens through platform-specific mechanisms:

| Platform | Capture Method |
|----------|----------------|
| **Android** | View hierarchy rendering |
| **iOS** | UIWindow snapshots |
| **React Native** | Native bridge to underlying platform |
| **Flutter** | Platform channel to native layer |
| **Web** | DOM mutations and styling |

Captures happen at:
- Screen transitions
- User interactions
- Periodic intervals (configurable)

### Touch Interactions

All touch events are captured with:
- **Location** (x, y coordinates)
- **Type** (tap, swipe, scroll, long press)
- **Timestamp**
- **Target element** (when identifiable)

### Metadata

Each session includes:
- Device information (model, OS version, screen size)
- App version
- Session timing
- Custom events you log
- User properties you set

---

## Recording Modes

### Schematic Recording

Captures screen structure as wireframes without actual content.

```
┌─────────────────┐
│  ████████████   │  ← Title bar
├─────────────────┤
│  [Profile Image]│  ← Image placeholder
│  ██████████     │  ← Text placeholder
│  ████████       │
├─────────────────┤
│  [Button]       │  ← Button placeholder
└─────────────────┘
```

**Benefits:**
- Maximum privacy - no actual content captured
- Smaller data sizes
- Still shows layout, navigation, and interactions

**Enable with:**
```javascript
// This is required for iOS and Android to enable recordings
UXCam.optIntoSchematicRecordings();
```

### Full Recording

Captures actual screen content. Requires careful attention to sensitive data occlusion.

**Use when:**
- You need to see exact UI content
- Design/UX review requires visual fidelity
- You have proper occlusion configured

---

## Data Flow

### 1. Capture (On Device)

```
User Interaction → Screen Snapshot → Touch Event → Local Buffer
```

- Snapshots are compressed immediately
- Occlusion is applied before data leaves the device
- Data accumulates in memory buffer

### 2. Transmission

```
App Backgrounds → Buffer Serialized → HTTPS Upload → UXCam Servers
```

- Upload happens when app goes to background
- Uses HTTPS with certificate pinning
- Retries failed uploads on next session
- Data is compressed before transmission

### 3. Processing

```
Raw Data → Session Assembly → Video Encoding → Dashboard Availability
```

- Sessions appear in dashboard within minutes
- Video is reconstructed server-side
- Events and metadata are indexed for search

---

## Performance Considerations

### Memory

- UXCam maintains a rolling buffer
- Typical memory usage: 20-40 MB
- Configurable based on your needs

### CPU

- Capture happens on background threads
- Minimal impact during normal operation
- Brief spike during screen transitions

### Network

- Upload only on app background
- Typical session: 500 KB - 2 MB compressed
- Uses WiFi preferentially (configurable)

### Battery

- Negligible impact in testing
- No continuous background activity
- Upload uses efficient batch transmission

---

## What's NOT Captured

UXCam does NOT capture:

- **Audio** - No microphone access
- **Keyboard input** - Key presses aren't logged (only that typing occurred)
- **System UI** - Notification shade, control center, etc.
- **Other apps** - Only your app's content
- **Background activity** - Recording pauses when app is backgrounded

---

## Platform-Specific Details

### Android

- Uses `View.draw()` for capture
- Works with Activities, Fragments, and Jetpack Compose
- WebView content captured through special handling

### iOS

- Uses `UIWindow` layer rendering
- Works with UIKit and SwiftUI
- WebView content captured with cross-origin considerations

### Web

- Uses DOM observation and canvas rendering
- Captures CSS styling and layout
- Single Page App navigation tracked

---

## Troubleshooting Data Issues

| Symptom | Likely Cause | Solution |
|---------|--------------|----------|
| Sessions not appearing | App force-closed before upload | Send app to background normally |
| Missing screens | Automatic tagging not detecting | Use manual screen tagging |
| Blank areas in recording | Occlusion applied | Check occlusion configuration |
| Slow upload | Large session | Check for performance issues causing excessive captures |

---

## Further Reading

- [Understanding Occlusion](understanding-occlusion) - Privacy protection in recordings
- [Privacy and Compliance](privacy-and-compliance) - Data handling and regulations
- [Screen Tagging](/docs/screen-tagging) - Naming screens for analytics
