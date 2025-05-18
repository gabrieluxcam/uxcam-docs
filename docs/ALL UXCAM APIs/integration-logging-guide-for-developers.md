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