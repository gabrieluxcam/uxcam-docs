---
title: Integration Logging
deprecated: false
hidden: false
metadata:
  description: >-
    Enable verbose SDK logs to debug and verify your UXCam React Native
    integration.
  robots: index
---
# Integration Logging

Enable verbose SDK logs to debug and verify your UXCam integration. Integration logging outputs detailed information about SDK initialization, session recording, and data upload.

## Enable Logging

Set `enableIntegrationLogging: true` in your configuration:

```javascript config/uxcam.js
import RNUxcam from 'react-native-ux-cam';

const configuration = {
  userAppKey: 'YOUR_API_KEY',
  enableAutomaticScreenNameTagging: false,
  enableIntegrationLogging: true, // Enable verbose logs
};

RNUxcam.optIntoVideoRecordings();
RNUxcam.startWithConfiguration(configuration);
```

## Where Logs Appear

| IDE / Tool | iOS Logs | Android Logs |
|------------|----------|--------------|
| **Xcode** | ✅ Console output | N/A |
| **Android Studio** | N/A | ✅ Logcat output |
| **VSCode / Cursor** | ❌ Not visible | ❌ Not visible |
| **Metro bundler** | ❌ Not visible | ❌ Not visible |

> ⚠️ **Important**: Integration logs are **native-level logs** and only appear in native IDEs. They will **not** appear in your JavaScript console, Metro bundler, or editors like VSCode or Cursor.

### Viewing iOS Logs
1. Open your project in **Xcode**
2. Run the app on a simulator or device
3. Open the **Console** panel (⇧⌘C)
4. Filter by `UXCam` to see relevant logs

### Viewing Android Logs
1. Open your project in **Android Studio**
2. Run the app on an emulator or device
3. Open **Logcat** panel
4. Filter by `UXCam` tag

## What to Look For

| Log Message | Meaning |
|-------------|---------|
| `Verification successful` | SDK initialized correctly with a valid API key |
| `Session started` | Recording has begun |
| `Session uploaded` | Session data sent to UXCam servers |
| `Screen tagged: [name]` | A screen tag was registered |
| `Occlusion applied` | Privacy rules are active |

## Debug vs Release Behavior

Integration logging should only be enabled during development:

```javascript
const configuration = {
  userAppKey: 'YOUR_API_KEY',
  enableAutomaticScreenNameTagging: false,
  enableIntegrationLogging: __DEV__, // Auto-disable in production
};
```

> 💡 **Tip**: Integration logging adds minimal overhead, but it's good practice to disable it in production builds to keep logs clean.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| No logs visible | Ensure you're using Xcode (iOS) or Android Studio (Android), not a JS-only IDE |
| `Verification failed` | Check that your API key is correct and the app key matches your UXCam dashboard |
| `Session not started` | Verify `optIntoVideoRecordings()` is called before `startWithConfiguration()` |
| Logs stop appearing | The app may have been backgrounded — recording pauses until the app returns to foreground |
