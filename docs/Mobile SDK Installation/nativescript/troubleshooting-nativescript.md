---
title: NativeScript Troubleshooting Guide
excerpt: Common issues and solutions for UXCam NativeScript plugin integration
deprecated: false
hidden: false
metadata:
  title: 'NativeScript Troubleshooting - UXCam'
  description: 'Troubleshooting guide for UXCam NativeScript SDK integration'
  robots: index
next:
  description: ''
---

# NativeScript Troubleshooting Guide

Common issues and solutions when integrating UXCam with NativeScript applications.

## Quick Diagnostics Checklist

Before diving into specific issues:

- [ ] NativeScript CLI is installed and up to date
- [ ] UXCam plugin is latest version
- [ ] Valid UXCam app key is configured
- [ ] iOS deployment target is 12.0+ / Android minSdkVersion is 21+
- [ ] App module is properly configured

---

## Installation Issues

### Plugin Installation Fails

**Symptoms:**
```
Error: Failed to install nativescript-uxcam
```

**Solutions:**

1. **Update NativeScript CLI:**
```bash
npm install -g nativescript
ns --version
```

2. **Clear cache and reinstall:**
```bash
ns clean
npm install nativescript-uxcam
```

3. **For NativeScript 8+:**
```bash
npm install @nicothin/nativescript-uxcam
```

### Build Errors After Installation

**iOS CocoaPods Error:**
```bash
cd platforms/ios
pod install --repo-update
```

**Android Gradle Error:**
```bash
ns clean
ns build android
```

---

## Runtime Issues

### Sessions Not Appearing in Dashboard

**Symptoms:**
- App runs normally
- No sessions in UXCam dashboard

**Debugging Steps:**

1. **Verify initialization:**
```typescript
// app.ts or main.ts
import { Application } from '@nativescript/core';
import * as NSUXCam from 'nativescript-uxcam';

Application.on(Application.launchEvent, () => {
    console.log('Initializing UXCam...');

    NSUXCam.optIntoSchematicRecordings();
    NSUXCam.startWithKey('YOUR_APP_KEY');

    console.log('UXCam initialized');
});

Application.run({ moduleName: 'app-root' });
```

2. **Check logs:**
- Android: `adb logcat | grep UXCam`
- iOS: Xcode console

**Common Solutions:**

**Wrong import:**
```typescript
// ✅ Correct import
import * as NSUXCam from 'nativescript-uxcam';

// ❌ Incorrect
import { UXCam } from 'nativescript-uxcam';
```

**Late initialization:**
```typescript
// ❌ Too late - in a component
export class HomeComponent {
    ngOnInit() {
        NSUXCam.startWithKey(key); // May miss early screens
    }
}

// ✅ Correct - in app launch
Application.on(Application.launchEvent, () => {
    NSUXCam.startWithKey(key);
});
```

### NSUXCam is Undefined

**Symptoms:**
```
TypeError: Cannot read property 'startWithKey' of undefined
```

**Solutions:**

1. **Verify plugin installation:**
```bash
ns plugin list
# Should show: nativescript-uxcam
```

2. **Rebuild the app:**
```bash
ns clean
ns build android
ns build ios
```

---

## Screen Tagging Issues

### Screens Not Tagged Correctly

**Symptoms:**
- Generic screen names in dashboard
- Missing screen transitions

**Solution** - Manual screen tagging:

**Angular:**
```typescript
import { Component, OnInit } from '@angular/core';
import * as NSUXCam from 'nativescript-uxcam';

@Component({
    selector: 'home',
    templateUrl: './home.component.html'
})
export class HomeComponent implements OnInit {
    ngOnInit() {
        NSUXCam.tagScreenName('Home');
    }
}
```

**Vue:**
```typescript
export default {
    mounted() {
        NSUXCam.tagScreenName('Home');
    }
}
```

**Plain NativeScript:**
```typescript
export function onNavigatingTo(args) {
    NSUXCam.tagScreenName('Home');
}
```

**With Router:**
```typescript
// Use router events for automatic tagging
import { RouterExtensions } from '@nativescript/angular';

constructor(private router: RouterExtensions) {
    this.router.router.events.subscribe(event => {
        if (event instanceof NavigationEnd) {
            const screenName = event.urlAfterRedirects.replace('/', '') || 'Home';
            NSUXCam.tagScreenName(screenName);
        }
    });
}
```

---

## Occlusion Issues

### Sensitive Data Visible

**Solution** - Occlude views:

```typescript
import * as NSUXCam from 'nativescript-uxcam';
import { TextField } from '@nativescript/core';

// Get reference to sensitive field
const creditCardField = page.getViewById<TextField>('creditCard');

// Occlude it
NSUXCam.occludeSensitiveView(creditCardField);
```

**Occlude entire screen:**
```typescript
// When showing sensitive screen
NSUXCam.occludeSensitiveScreen(true);

// When leaving
NSUXCam.occludeSensitiveScreen(false);
```

---

## Platform-Specific Issues

### iOS: Recording Not Starting

**Solution:**
```typescript
// Required for iOS
NSUXCam.optIntoSchematicRecordings();
NSUXCam.startWithKey('YOUR_APP_KEY');
```

### iOS: Build Fails with Pod Errors

```bash
cd platforms/ios
pod deintegrate
pod install
```

### Android: ProGuard Issues

Add to `App_Resources/Android/proguard-rules.pro`:
```proguard
-keep class com.uxcam.** { *; }
-dontwarn com.uxcam.**
```

---

## TypeScript Type Issues

### Missing Type Definitions

**Solution** - Add declaration file `references.d.ts`:
```typescript
/// <reference path="./node_modules/nativescript-uxcam/index.d.ts" />
```

Or use any type:
```typescript
const NSUXCam: any = require('nativescript-uxcam');
```

---

## Debug Information Collection

When contacting support, include:

1. **Environment:**
```bash
ns --version
ns doctor
node --version
npm --version
```

2. **Plugin version:**
```bash
npm list nativescript-uxcam
```

3. **Device logs:**
- Android: `adb logcat | grep UXCam`
- iOS: Xcode console

4. **Configuration:**
- `package.json`
- Initialization code

---

## Contact Support

- **Email**: [team@uxcam.com](mailto:team@uxcam.com)
- **Documentation**: [NativeScript Integration Guide](index.md)
- **Changelog**: [Version History](nativescript-sdk-changelog.md)
