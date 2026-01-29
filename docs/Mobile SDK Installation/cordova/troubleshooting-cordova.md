---
title: Cordova Troubleshooting Guide
excerpt: Common issues and solutions for UXCam Cordova plugin integration
deprecated: false
hidden: false
metadata:
  title: 'Cordova Troubleshooting - UXCam'
  description: 'Troubleshooting guide for UXCam Cordova SDK integration'
  robots: index
next:
  description: ''
---

# Cordova Troubleshooting Guide

Common issues and solutions when integrating UXCam with Apache Cordova applications.

## Quick Diagnostics Checklist

Before diving into specific issues:

- [ ] Cordova CLI is installed and up to date
- [ ] UXCam plugin is latest version
- [ ] Valid UXCam app key is configured
- [ ] iOS deployment target is 12.0+ / Android minSdkVersion is 21+
- [ ] `deviceready` event is properly handled

---

## Installation Issues

### Plugin Installation Fails

**Symptoms:**
```
Error: Failed to install 'cordova-uxcam'
```

**Solutions:**

1. **Update Cordova CLI:**
```bash
npm install -g cordova
cordova --version
```

2. **Remove and re-add the plugin:**
```bash
cordova plugin remove cordova-uxcam
cordova plugin add cordova-uxcam
```

3. **Clear Cordova cache:**
```bash
cordova platform remove android
cordova platform remove ios
cordova platform add android
cordova platform add ios
```

### iOS Build Fails

**Symptoms:**
```
error: Signing for "YourApp" requires a development team
```

**Solution:**
Open `platforms/ios/YourApp.xcworkspace` in Xcode and configure signing.

**Minimum iOS Version Error:**
```
The iOS deployment target is set to 10.0, but the minimum is 12.0
```

**Solution** - Update `config.xml`:
```xml
<platform name="ios">
    <preference name="deployment-target" value="12.0" />
</platform>
```

### Android Build Fails

**Minimum SDK Error:**
```
Manifest merger failed : uses-sdk:minSdkVersion 19 cannot be smaller than version 21
```

**Solution** - Update `config.xml`:
```xml
<platform name="android">
    <preference name="android-minSdkVersion" value="21" />
</platform>
```

---

## Runtime Issues

### Sessions Not Appearing in Dashboard

**Symptoms:**
- App runs normally
- No sessions visible in UXCam dashboard

**Debugging Steps:**

1. **Verify initialization timing:**
```javascript
document.addEventListener('deviceready', function() {
    console.log('Device ready - initializing UXCam');

    UXCam.optIntoSchematicRecordings();
    UXCam.startWithConfiguration({
        userAppKey: 'YOUR_APP_KEY'
    });

    console.log('UXCam initialized');
}, false);
```

2. **Check console logs** (Chrome DevTools for Android, Safari for iOS):
```
UXCam initialized
Session started
```

3. **Verify app key** - Ensure no typos or extra spaces

**Common Solutions:**

**Initialization too early:**
```javascript
// ❌ Wrong - before deviceready
UXCam.startWithConfiguration(config);

// ✅ Correct - after deviceready
document.addEventListener('deviceready', function() {
    UXCam.startWithConfiguration(config);
}, false);
```

**App not sent to background:**
- Sessions upload when app goes to background
- Don't force-close the app; press Home button instead

### UXCam is Undefined

**Symptoms:**
```
ReferenceError: UXCam is not defined
```

**Solutions:**

1. **Wait for deviceready:**
```javascript
document.addEventListener('deviceready', function() {
    // UXCam is available here
    if (typeof UXCam !== 'undefined') {
        UXCam.startWithConfiguration(config);
    } else {
        console.error('UXCam plugin not loaded');
    }
}, false);
```

2. **Verify plugin installation:**
```bash
cordova plugin list
# Should show: cordova-uxcam
```

---

## Screen Tagging Issues

### Screens Not Tagged Correctly

**Symptoms:**
- All screens show same name
- Generic names like "index.html"

**Solution** - Manual screen tagging:
```javascript
// Tag screens when navigating
function navigateToHome() {
    UXCam.tagScreenName('Home');
    // ... navigation code
}

function navigateToProfile() {
    UXCam.tagScreenName('Profile');
    // ... navigation code
}
```

**For SPA frameworks (Angular, Vue, React):**
```javascript
// Example with simple routing
window.addEventListener('hashchange', function() {
    var screenName = window.location.hash.replace('#/', '') || 'Home';
    UXCam.tagScreenName(screenName);
});
```

---

## Occlusion Issues

### Sensitive Data Visible

**Symptoms:**
- Password fields visible in recordings
- Sensitive data not hidden

**Solutions:**

1. **Occlude specific elements by ID:**
```javascript
// After element is in DOM
document.addEventListener('deviceready', function() {
    UXCam.startWithConfiguration(config);

    // Occlude sensitive input
    var sensitiveInput = document.getElementById('credit-card');
    if (sensitiveInput) {
        UXCam.occludeSensitiveView(sensitiveInput);
    }
}, false);
```

2. **Occlude entire screen:**
```javascript
function showPaymentScreen() {
    UXCam.occludeSensitiveScreen(true);
    // Show payment form
}

function hidePaymentScreen() {
    UXCam.occludeSensitiveScreen(false);
    // Navigate away
}
```

---

## Platform-Specific Issues

### iOS: Recording Not Starting

**Solution** - Ensure schematic recordings are enabled:
```javascript
document.addEventListener('deviceready', function() {
    // Required for iOS screen capture
    UXCam.optIntoSchematicRecordings();

    UXCam.startWithConfiguration({
        userAppKey: 'YOUR_APP_KEY'
    });
}, false);
```

### Android: ProGuard Issues

**Symptoms:**
- Works in debug, fails in release

**Solution** - Add to ProGuard rules:
```proguard
-keep class com.uxcam.** { *; }
-dontwarn com.uxcam.**
```

---

## Debug Information Collection

When contacting support, include:

1. **Environment:**
```bash
cordova --version
cordova platform list
cordova plugin list
```

2. **Device logs:**
- Android: `adb logcat | grep UXCam`
- iOS: Xcode console or Safari Web Inspector

3. **Configuration:**
- `config.xml`
- Initialization code

---

## Contact Support

- **Email**: [team@uxcam.com](mailto:team@uxcam.com)
- **Documentation**: [Cordova Integration Guide](index.md)
- **Changelog**: [Version History](cordova-sdk-changelog.md)
