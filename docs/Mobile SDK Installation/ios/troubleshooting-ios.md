---
title: iOS Troubleshooting Guide
excerpt: Common issues and solutions for UXCam iOS SDK integration
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---

# iOS Troubleshooting Guide

This guide covers common issues encountered when integrating UXCam with iOS applications and their solutions.

## Quick Diagnostics Checklist

Before diving into specific issues, verify these basics:

- [ ] Xcode version 14.0+ is installed
- [ ] UXCam iOS SDK is latest version (Check [changelog](ios-sdk-change-log-ios.md))
- [ ] Valid UXCam app key is configured
- [ ] Deployment target is iOS 12.0+
- [ ] Internet connectivity is available
- [ ] App permissions are granted (if required)

---

## Installation & Setup Issues

### CocoaPods Installation Failures

**Symptoms:**
```bash
[!] Unable to find a specification for `UXCam`
```

**Solutions:**

1. **Update CocoaPods repository**:
```bash
pod repo update
pod install
```

2. **Check Podfile configuration**:
```ruby
platform :ios, '12.0'  # Minimum iOS 12.0

target 'MyApp' do
  use_frameworks!  # Required for Swift projects

  pod 'UXCam'
end
```

3. **Clean and reinstall**:
```bash
rm -rf Pods/ Podfile.lock
pod deintegrate
pod install
```

### Swift Package Manager (SPM) Issues

**Symptoms:**
- Package resolution fails
- Build errors after adding UXCam via SPM

**Solutions:**

1. **Reset package cache**:
   - File → Packages → Reset Package Caches
   - File → Packages → Update to Latest Package Versions

2. **Verify package URL**:
```
https://github.com/uxcam/uxcam-ios-sdk
```

3. **Check minimum iOS version** in project settings:
   - Project → Deployment Info → iOS Deployment Target: 12.0+

### Build Failures After Adding UXCam

**Module Not Found Error:**

**Symptoms:**
```swift
No such module 'UXCam'
```

**Solutions:**

1. **For CocoaPods** - Ensure you're opening `.xcworkspace`:
```bash
# ✅ Correct
open MyApp.xcworkspace

# ❌ Wrong
open MyApp.xcodeproj
```

2. **Clean build folder**:
   - Product → Clean Build Folder (⇧⌘K)
   - Quit Xcode
   - Delete `~/Library/Developer/Xcode/DerivedData`
   - Reopen and rebuild

3. **Check import statement**:
```swift
// For Swift
import UXCam

// For Objective-C
#import <UXCam/UXCam.h>
```

**Linker Errors:**

**Symptoms:**
```
Undefined symbols for architecture arm64
```

**Solutions:**

1. **Verify framework is embedded** - Project Settings → General → Frameworks, Libraries, and Embedded Content
   - UXCam.framework should be "Embed & Sign"

2. **Check build settings**:
   - Build Settings → Framework Search Paths should include Pod paths

---

## Runtime Issues

### Sessions Not Appearing in Dashboard

**Symptoms:**
- App seems to work normally
- No sessions visible in UXCam dashboard after 5+ minutes
- "Waiting for data" message persists

**Debugging Steps:**

1. **Enable debug logging**:

**Swift:**
```swift
import UXCam

// In AppDelegate or SwiftUI App
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {

    let configuration = UXCamConfiguration(appKey: "YOUR_APP_KEY")
    configuration.enableAutomaticScreenNameTagging = true

    UXCam.start(with: configuration)

    return true
}
```

**SwiftUI:**
```swift
import SwiftUI
import UXCam

@main
struct MyApp: App {
    init() {
        let config = UXCamConfiguration(appKey: "YOUR_APP_KEY")
        config.enableAutomaticScreenNameTagging = true
        UXCam.start(with: config)
    }

    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```

2. **Check console output**:
   - Look for "Verification successful" message
   - Look for "Session started" message
   - Look for "Session/Video uploaded" message

**Common Solutions:**

**Wrong App Key:**
```swift
// Verify the key matches your UXCam project
// Ensure no extra spaces or characters
let appKey = "YOUR_APP_KEY"  // Check this carefully
```

**Network Issues:**
- Verify App Transport Security settings in `Info.plist`
- Check if firewall is blocking connections
- Test on cellular data vs WiFi

**Initialization Timing:**
```swift
// ✅ Correct - Initialize as early as possible
class AppDelegate: UIResponder, UIApplicationDelegate {
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // Initialize UXCam FIRST
        UXCam.start(withKey: "YOUR_APP_KEY")

        // Other initialization after
        return true
    }
}
```

### SwiftUI App Not Recording

**Symptoms:**
- SwiftUI app shows no sessions
- UIKit apps work fine

**Solutions:**

1. **Use dedicated SwiftUI SDK**:
```swift
// Add to Package.swift or use CocoaPods
dependencies: [
    .package(url: "https://github.com/uxcam/uxcam-swiftui-sdk", from: "1.0.0")
]
```

2. **Initialize in App struct**:
```swift
import SwiftUI
import UXCamSwiftUI

@main
struct MyApp: App {
    init() {
        let config = Configuration(appKey: "YOUR_APP_KEY")
        config.enableAutomaticScreenNameTagging = true
        UXCamSwiftUI.start(with: config)
    }

    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```

For more details, see: [SwiftUI Integration Guide](swiftui-ios.md)

---

## Screen Tagging Issues

### Screens Not Being Tagged Automatically

**Symptoms:**
- Sessions recorded but no screen names visible
- All screens show as "Unknown" or class names

**Solutions:**

**For UIKit:**

Automatic tagging works for ViewControllers by default. Ensure it's enabled:
```swift
let configuration = UXCamConfiguration(appKey: "YOUR_APP_KEY")
configuration.enableAutomaticScreenNameTagging = true  // Enable this
UXCam.start(with: configuration)
```

**For SwiftUI:**

1. **Enable automatic tagging** with configuration:
```swift
let config = Configuration(appKey: "YOUR_APP_KEY")
config.enableAutomaticScreenNameTagging = true  // Required for SwiftUI
UXCamSwiftUI.start(with: config)
```

2. **Use navigationTitle** for automatic tagging:
```swift
struct HomeView: View {
    var body: some View {
        Text("Welcome")
            .navigationTitle("Home")  // This will be tagged automatically
    }
}
```

3. **Manual tagging for edge cases**:
```swift
struct ProfileView: View {
    var body: some View {
        Text("Profile")
            .onAppear {
                UXCamCore.tagScreenName("User Profile")
            }
    }
}
```

For more details, see: [SwiftUI Automatic Screen Tagging](screen-tagging-ios/automatic-screen-tagging-swiftui-ios.md)

### Duplicate Screen Names or 0s Duration

**Symptoms:**
- Same screen tagged multiple times
- Screens showing 0 seconds duration

**Solutions:**

1. **Avoid tagging in viewDidLoad** - Use viewDidAppear instead:
```swift
// ❌ Wrong - fires too early
override func viewDidLoad() {
    super.viewDidLoad()
    UXCam.tagScreenName("Home")  // Too early
}

// ✅ Correct - fires when screen is visible
override func viewDidAppear(_ animated: Bool) {
    super.viewDidAppear(animated)
    UXCam.tagScreenName("Home")  // Right timing
}
```

2. **For SwiftUI** - Use onAppear carefully:
```swift
struct HomeView: View {
    @State private var hasTagged = false

    var body: some View {
        Text("Home")
            .onAppear {
                if !hasTagged {
                    UXCamCore.tagScreenName("Home")
                    hasTagged = true
                }
            }
    }
}
```

---

## Occlusion & Privacy Issues

### Sensitive Views Not Being Occluded

**Symptoms:**
- Password fields visible in session replay
- Sensitive data not hidden despite configuration

**Solutions:**

1. **Verify automatic occlusion** - These are hidden by default:

**UIKit:**
```swift
// Automatically occluded
let passwordField = UITextField()
passwordField.textContentType = .password  // Auto-occluded
passwordField.textContentType = .newPassword  // Auto-occluded
passwordField.textContentType = .creditCardNumber  // Auto-occluded
passwordField.textContentType = .oneTimeCode  // Auto-occluded
```

2. **Manual occlusion for specific views**:
```swift
// Hide a specific view
UXCam.occludeSensitiveView(sensitiveView)

// Hide entire screen
UXCam.occludeSensitiveScreen()
```

3. **For SwiftUI**:
```swift
import UXCamSwiftUI

struct PaymentView: View {
    var body: some View {
        VStack {
            TextField("Card Number", text: $cardNumber)
                .uxcamOcclude()  // Hide this field

            SecureField("CVV", text: $cvv)
                .uxcamOcclude()  // Hide this field
        }
    }
}
```

For more details, see: [Sensitive Data Occlusion](sensitive-data-occlusion-ios/index.md)

### Entire Screen Being Blurred

**Symptoms:**
- Whole screen appears blurred in replays
- Unable to see any content

**Solution** - Check if screen is marked as sensitive:
```swift
// Remove accidental screen hiding
override func viewDidAppear(_ animated: Bool) {
    super.viewDidAppear(animated)
    // UXCam.occludeSensitiveScreen()  // Remove this line
    UXCam.tagScreenName("Home")
}
```

---

## Performance Issues

### App Startup Slowdown

**Symptoms:**
- Noticeable delay in app startup after adding UXCam
- Watchdog timeout warnings

**Solutions:**

1. **Async initialization**:
```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {

    // Initialize UXCam asynchronously
    DispatchQueue.global(qos: .utility).async {
        let configuration = UXCamConfiguration(appKey: "YOUR_APP_KEY")
        UXCam.start(with: configuration)
    }

    return true
}
```

2. **Lazy initialization after first screen**:
```swift
var uxcamInitialized = false

func initializeUXCamOnce() {
    guard !uxcamInitialized else { return }

    let configuration = UXCamConfiguration(appKey: "YOUR_APP_KEY")
    UXCam.start(with: configuration)
    uxcamInitialized = true
}

// Call in first ViewController's viewDidAppear
override func viewDidAppear(_ animated: Bool) {
    super.viewDidAppear(animated)
    initializeUXCamOnce()
}
```

### Memory Usage Issues

**Symptoms:**
- Memory warnings
- App crashes with low memory
- Increased memory usage visible in Instruments

**Solutions:**

1. **Stop recording in background**:
```swift
class AppDelegate: UIResponder, UIApplicationDelegate {
    func applicationDidEnterBackground(_ application: UIApplication) {
        // Stop recording when app goes to background
        UXCam.stopSessionAndUploadData()
    }

    func applicationWillEnterForeground(_ application: UIApplication) {
        // Restart recording when app returns
        UXCam.startNewSession()
    }
}
```

2. **Monitor memory usage**:
```swift
// Use Instruments to monitor memory
// Product → Profile → Allocations
```

---

## Crash Reporting Issues

### Crashes Not Appearing in Dashboard

**Symptoms:**
- App crashes but no crash reports in UXCam
- Crash logs missing

**Solutions:**

1. **Verify crash handling is enabled** (enabled by default):
```swift
let configuration = UXCamConfiguration(appKey: "YOUR_APP_KEY")
// Crash handling is enabled by default
UXCam.start(with: configuration)
```

2. **Check for conflicting crash handlers**:
```swift
// Initialize UXCam BEFORE other crash handlers
UXCam.start(withKey: "YOUR_APP_KEY")  // First
FirebaseCrashlytics.crashlytics()  // After
```

3. **Ensure crash symbolication is set up**:
   - Upload dSYM files to UXCam
   - Check Build Settings → Debug Information Format → DWARF with dSYM File

For more details, see: [Crash Symbolication Guide](advanced-configuration-and-apis-1/crash-symbolication-ios.md)

---

## Platform-Specific Issues

### iOS 14+ App Tracking Transparency

**Symptoms:**
- Sessions not recording on iOS 14+
- Tracking permission issues

**Solution** - Add tracking description to `Info.plist`:
```xml
<key>NSUserTrackingUsageDescription</key>
<string>We use tracking to improve your app experience and analyze crashes.</string>
```

Then request permission:
```swift
import AppTrackingTransparency

func requestTrackingPermission() {
    if #available(iOS 14, *) {
        ATTrackingManager.requestTrackingAuthorization { status in
            // Handle permission result
        }
    }
}
```

### iOS 17+ Privacy Manifests

If your app needs a privacy manifest, ensure UXCam's tracking is documented:
```xml
<!-- PrivacyInfo.xcprivacy -->
<dict>
    <key>NSPrivacyTracking</key>
    <true/>
    <key>NSPrivacyTrackingDomains</key>
    <array>
        <string>uxcam.com</string>
    </array>
</dict>
```

### Archive/Distribution Issues

**Symptoms:**
- Build succeeds but archive fails
- App crashes on TestFlight but not in development

**Solutions:**

1. **Clean build folder**:
   - Product → Clean Build Folder (⇧⌘K)

2. **Check code signing**:
   - Verify provisioning profiles are valid
   - Ensure UXCam framework is properly signed

3. **Validate archive**:
   - Window → Organizer
   - Select archive → Validate App

4. **Check Build Settings**:
   - Build Settings → Enable Bitcode → NO (if using older SDK versions)
   - Build Settings → Strip Debug Symbols During Copy → YES (for Release)

---

## Getting Additional Help

### Debug Information to Collect

When contacting support, include:

1. **Environment Info**:
   - Xcode version
   - iOS deployment target
   - Swift/Objective-C version
   - Installation method (CocoaPods/SPM/Manual)

2. **Console Logs**:
   - UXCam initialization messages
   - Any error messages
   - Crash logs

3. **Build Configuration**:
   - Podfile (if using CocoaPods)
   - Package.swift (if using SPM)
   - Build Settings screenshots

4. **Minimal Reproduction**:
   - Create simple test app demonstrating the issue
   - Include relevant code snippets
   - Specify exact SDK version

### Contact Support

- **Email**: [team@uxcam.com](mailto:team@uxcam.com)
- **Documentation**: [iOS Integration Guide](index.md)
- **Changelog**: [Version History](ios-sdk-change-log-ios.md)
- **Advanced Configuration**: [Advanced APIs](advanced-configuration-and-apis-1/index.md)

### Related Documentation

- [SwiftUI Guide](swiftui-ios.md)
- [Screen Tagging Guide](screen-tagging-ios/index.md)
- [Sensitive Data Occlusion](sensitive-data-occlusion-ios/index.md)
- [User Properties](user-properties.md)
- [Events](events.md)

---

*Last updated: January 2025*
