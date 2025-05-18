---
title: Integration Logging Guide for Developers
deprecated: false
hidden: true
metadata:
  robots: index
---
This guide helps you, as a developer installing UXCam, to enable and interpret integration logs when setting up the SDK. It walks you through configuring logging, understanding key log messages, and troubleshooting upload issues.

<br />

When you integrate our SDK, sessions and data are recorded and uploaded automatically. If uploads fail (e.g., video/data/session issues), having detailed integration logs helps you and our Support/CS teams diagnose and resolve problems quickly.

<GitHubCallout type="warning">By default, integration logging is disabled. Follow the steps below to enable it and interpret the logs printed in your IDE’s console (Xcode, Android Studio, Visual Studio, etc.).</GitHubCallout>

### Enabling & Disabling Logs

Simply set to true the configuration parameter as such:

```coffeescript Code
UXCam.enableIntegrationLogging(true);
```

### Key Log Levels & Tags

<br />

All messages are prefixed with UXCam and categorized by level:

**INFO**: Normal operational messages

**WARNING**: Recoverable issues or state changes

**ERROR**: Failures requiring attention

Refer to the tables below for important messages you’ll see.

### 4. Important Logs During Startup

<br />

<GitHubCallout type="info">These logs appear during the SDK startup and verification phases.</GitHubCallout>

| Level    | Tag               | Message                                                                                  | When                                         |
|----------|-------------------|------------------------------------------------------------------------------------------|----------------------------------------------|
| INFO     | UXCam             | Integration log enabled                                                                  | On enabling logging                          |
| INFO     | UXCam             | Session Verification for Application key initiated with Request Parameters: `{…}`        | After `start(with:)`                         |
| ERROR    | UXCam: Verify     | Session Verification for app key failed due to missing parameters.                       | If required fields are missing               |
| ERROR    | UXCam: Verify     | Session Verification for app key failed with response: `{error_response}`               | On server error response                     |
| INFO     | UXCam: Verify     | Session Verification for app key succeeded                                               | On success                                   |
| WARNING  | UXCam: Session    | Session await upload remained: 1                                                         | After verification if un-uploaded sessions   |
| INFO     | UXCam: Session    | Started uploading session.                                                               | Upload begins                                |
| ERROR    | UXCam: Session    | Session upload failed with reason: New session await upload remained: X                  | On upload failure (timeout/network/etc.)     |
| INFO     | UXCam: Session    | Session upload success: New session await upload remained: 0                             | On upload success                            |
| INFO     | UXCam: Recording  | Session and Screen Recording has started                                                 | After a new session has been created         |

---

### 5. File‑Type Upload Logs

<br />

<GitHubCallout type="info">Watch individual file uploads for granular success/failure details.</GitHubCallout>

| Level | Tag                    | Message                                                                    | When                                          |
|-------|------------------------|----------------------------------------------------------------------------|-----------------------------------------------|
| ERROR | UXCam: Session Upload  | FileType upload has failed with reason and time. Host: `{host_response}`    | Video/DataFile/Icon/Background upload failure |
| INFO  | UXCam: Session Upload  | FileType upload has succeeded in `{elapsed_time}`. Host: `{host_response}` | On individual file upload success             |

---

### 6. Session & Screen Recording Events

<br />

<GitHubCallout type="info">These logs track session and screen recording lifecycles.</GitHubCallout>

| Level    | Tag                        | Message                                                                     | When                                                       |
|----------|----------------------------|-----------------------------------------------------------------------------|------------------------------------------------------------|
| WARNING  | UXCam: Session             | Session has paused due to `{reason}`                                         | Manual pause / app background / crash                      |
| INFO     | UXCam: Session             | Session has resumed                                                          | App foreground / manual resume                             |
| WARNING  | UXCam: Session             | Session has stopped                                                          | Session ended or app background                            |
| INFO     | UXCam: Screen Recording    | Screen Recording has started at `{time}`                                     | After session start                                        |
| WARNING  | UXCam: Screen Recording    | Screen Recording has paused at `{time}` with `{reason}`                      | Manual pause / background / crash                          |
| INFO     | UXCam: Screen Recording    | Screen Recording has resumed at `{time}` with `{reason}`                     | Resume after pause                                         |
| WARNING  | UXCam: Screen Recording    | Screen Recording has stopped due to `{reason}`                               | StopSession / background / manual stop                     |
| ERROR    | UXCam: Screen Recording    | Screen Recording has failed at `{time}` with `{reason}`                      | Technical errors (filter/write/encrypt/etc.)               |

---

### 7. Configuration Toggles

<br />

When you enable configurations, you'll see:
```
INFO UXCam  [ConfigurationName] has been enabled.
```

- `enableMultiSessionRecord()`
- `enableCrashHandling()`
- `enableAutomaticScreenNameTagging()`
- `enableAdvancedGestureRecognition()`
- `enableImprovedScreenCapture()`
- `enableNetworkLogging()`
- `occlusion()`
- `environment()`

---

### 8. User & Event Logs

<br />

<GitHubCallout type="info">Logs for updating user properties and custom events.</GitHubCallout>

| Level | Tag                  | Message                                         |
|-------|----------------------|-------------------------------------------------|
| INFO  | UXCam: UserProperties| User properties have been updated: `{…}`        |
| INFO  | UXCam: Event         | Event recorded: `{event_properties}`            |
| INFO  | UXCam: Occlusion     | Occlusion set on `{view/screenName}`            |
| INFO  | UXCam: Occlusion     | Occlusion removed on `{view/screenName}`        |

---

### 9. Troubleshooting Tips

<br />

1. **No logs appearing?**  
   - Confirm logging is enabled (API or launch argument).  
   - Check console filters in your IDE.  
2. **Verification failed?**  
   - Verify your `appKey` and SDK version match the dashboard.  
   - Ensure network connectivity and firewall allow SDK requests.  
3. **Uploads stuck or failing?**  
   - Inspect `Session await upload remained` warnings.  
   - Check individual file-type errors for more details.  
4. **Still stuck?**  
   Gather full log output and share with Support/CS for deeper analysis.
```