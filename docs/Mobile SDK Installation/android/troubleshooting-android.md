---
title: Android Troubleshooting Guide
excerpt: Common issues and solutions for UXCam Android SDK integration
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---

# Android Troubleshooting Guide

This guide covers common issues encountered when integrating UXCam with Android applications and their solutions.

## Quick Diagnostics Checklist

Before diving into specific issues, verify these basics:

- [ ] Android Studio version 4.0+ is installed
- [ ] UXCam Android SDK is latest version (Check [changelog](android-sdk-changelog-android.md))
- [ ] Valid UXCam app key is configured
- [ ] minSdkVersion is 21+ (Android 5.0 Lollipop)
- [ ] Internet connectivity is available
- [ ] App permissions are granted (if required)

---

## Installation & Setup Issues

### Gradle Sync Failures

**Symptoms:**
```
Could not resolve com.uxcam:uxcam:x.x.x
```

**Solutions:**

1. **Check repository configuration** in `build.gradle` (project level):
```gradle
allprojects {
    repositories {
        google()
        mavenCentral()
        // Ensure these are present
    }
}
```

2. **Update Gradle version** in `gradle/wrapper/gradle-wrapper.properties`:
```properties
distributionUrl=https\://services.gradle.org/distributions/gradle-8.0-all.zip
```

3. **Sync and clean**:
```bash
./gradlew clean
./gradlew --refresh-dependencies
```

### Build Failures After Adding UXCam

**MultiDex Issues:**

**Symptoms:**
```
Cannot fit requested classes in a single dex file
```

**Solution** - Enable MultiDex in `app/build.gradle`:
```gradle
android {
    defaultConfig {
        minSdkVersion 21
        multiDexEnabled true
    }
}

dependencies {
    implementation 'androidx.multidex:multidex:2.0.1'
}
```

**Minimum SDK Version Error:**

**Symptoms:**
```
Manifest merger failed : uses-sdk:minSdkVersion X cannot be smaller than version 21
```

**Solution** - Update `app/build.gradle`:
```gradle
android {
    defaultConfig {
        minSdkVersion 21  // UXCam requires API 21+
        targetSdkVersion 34
    }
}
```

---

## Runtime Issues

### Sessions Not Appearing in Dashboard

**Symptoms:**
- App seems to work normally
- No sessions visible in UXCam dashboard after 5+ minutes
- "Waiting for data" message persists

**Debugging Steps:**

1. **Enable verbose logging in your Application class**:
```kotlin
import com.uxcam.UXCam
import com.uxcam.datamodel.UXConfig

class MyApplication : Application() {
    override fun onCreate() {
        super.onCreate()

        val config = UXConfig.Builder("YOUR_APP_KEY")
            .enableAutomaticScreenNameTagging(true)
            .build()

        UXCam.startWithConfiguration(config)
    }
}
```

2. **Check Logcat output**:
```bash
adb logcat | grep -E "UXCam|UXCAM"
```

Look for these messages:
- ✅ "Verification successful" - SDK initialized correctly
- ✅ "Session started" - Recording began
- ✅ "Session/Video uploaded" - Data sent to server
- ❌ "Invalid app key" - Check your app key
- ❌ "Network error" - Check internet connectivity

**Common Solutions:**

**Wrong App Key:**
```kotlin
// Verify the key matches your UXCam project
// Ensure no extra spaces or characters
val appKey = "YOUR_APP_KEY" // Check this carefully
```

**Network Issues:**
- Verify internet permission in `AndroidManifest.xml`:
```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

**Initialization Timing:**
```kotlin
class MyApplication : Application() {
    override fun onCreate() {
        super.onCreate()
        // Initialize UXCam as early as possible
        UXCam.startWithConfiguration(config)

        // Don't wait for other initialization
    }
}
```

### Application Class Not Registered

**Symptoms:**
- UXCam initialization code not running
- No Logcat messages from UXCam

**Solution** - Register Application class in `AndroidManifest.xml`:
```xml
<application
    android:name=".MyApplication"  <!-- Add this line -->
    android:label="@string/app_name"
    android:icon="@mipmap/ic_launcher">
    <!-- ... -->
</application>
```

---

## Screen Tagging Issues

### Activities Not Being Tagged Automatically

**Symptoms:**
- Sessions recorded but no screen names visible
- All screens show as "Unknown" or class names

**Solutions:**

1. **Enable automatic screen tagging**:
```kotlin
val config = UXConfig.Builder("YOUR_APP_KEY")
    .enableAutomaticScreenNameTagging(true)  // Enable this
    .build()
```

2. **For Fragments** - Enable dashboard option or use manual tagging:
```kotlin
// In your Fragment
override fun onResume() {
    super.onResume()
    UXCam.tagScreenName("Home Fragment")
}
```

3. **For Jetpack Compose** - Use manual tagging:
```kotlin
@Composable
fun HomeScreen() {
    // Tag screen when composable enters composition
    DisposableEffect(Unit) {
        UXCam.tagScreenName("Home Screen")
        onDispose { }
    }

    // Your UI code
}
```

### Duplicate Screen Names or 0s Duration

**Symptoms:**
- Same screen tagged multiple times
- Screens showing 0 seconds duration

**Solutions:**

1. **Avoid tagging in onCreate** - Use onResume instead:
```kotlin
// ❌ Wrong - fires too early
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    UXCam.tagScreenName("Home")  // Too early
}

// ✅ Correct - fires when screen is visible
override fun onResume() {
    super.onResume()
    UXCam.tagScreenName("Home")  // Right timing
}
```

2. **Guard against multiple tags**:
```kotlin
private var screenTagged = false

override fun onResume() {
    super.onResume()
    if (!screenTagged) {
        UXCam.tagScreenName("Home")
        screenTagged = true
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
```xml
<!-- Automatically occluded -->
<EditText
    android:inputType="textPassword" />  <!-- Works -->
<EditText
    android:inputType="number|textPassword" />  <!-- Works -->
```

2. **Manual occlusion for specific views**:
```kotlin
// Kotlin
import com.uxcam.UXCam

// Hide a specific view
UXCam.occludeSensitiveView(findViewById(R.id.sensitiveView))

// Hide all views with a tag
UXCam.occludeAllTextFields(true)
```

3. **For Jetpack Compose**:
```kotlin
// Use the KTX helper
import com.uxcam.compose.UxcamOcclude

@Composable
fun PaymentScreen() {
    Column {
        TextField(
            value = cardNumber,
            onValueChange = { cardNumber = it },
            modifier = Modifier.uxcamOcclude()  // Hide this field
        )
    }
}
```

For more details, see: [Jetpack Compose Occlusion Guide](https://developer.uxcam.com/docs/jetpack-compose-occlusion)

### Entire Screen Being Blurred

**Symptoms:**
- Whole screen appears blurred in replays
- Unable to see any content

**Solution** - Check if screen is marked as sensitive:
```kotlin
// Remove accidental screen hiding
override fun onResume() {
    super.onResume()
    // UXCam.occludeSensitiveScreen(this)  // Remove this line
    UXCam.tagScreenName("Home")
}
```

---

## Performance Issues

### App Startup Slowdown

**Symptoms:**
- Noticeable delay in app startup after adding UXCam
- ANR (Application Not Responding) errors

**Solutions:**

1. **Async initialization**:
```kotlin
class MyApplication : Application() {
    override fun onCreate() {
        super.onCreate()

        // Initialize UXCam asynchronously
        lifecycleScope.launch(Dispatchers.IO) {
            val config = UXConfig.Builder("YOUR_APP_KEY").build()
            UXCam.startWithConfiguration(config)
        }
    }
}
```

2. **Lazy initialization**:
```kotlin
class MyApplication : Application() {
    override fun onCreate() {
        super.onCreate()

        // Initialize after first activity starts
        registerActivityLifecycleCallbacks(object : ActivityLifecycleCallbacks {
            override fun onActivityCreated(activity: Activity, savedInstanceState: Bundle?) {
                if (!uxcamInitialized) {
                    initializeUXCam()
                    uxcamInitialized = true
                }
            }
            // ... other callbacks
        })
    }
}
```

### Memory Usage Issues

**Symptoms:**
- OutOfMemoryError crashes
- Increased memory usage visible in profiler

**Solutions:**

1. **Limit recording quality** (if available in your SDK version):
```kotlin
val config = UXConfig.Builder("YOUR_APP_KEY")
    .enableAdvancedGestureRecognition(false)  // Reduce memory usage
    .build()
```

2. **Stop recording in background**:
```kotlin
class MyApplication : Application() {
    override fun onCreate() {
        super.onCreate()

        registerActivityLifecycleCallbacks(object : ActivityLifecycleCallbacks {
            override fun onActivityPaused(activity: Activity) {
                // Stop recording when app goes to background
                if (!isInForeground()) {
                    UXCam.stopSessionAndUploadData()
                }
            }
            // ... other callbacks
        })
    }
}
```

---

## ProGuard / R8 Issues

### SDK Not Working in Release Build

**Symptoms:**
- Works in debug build
- Fails or crashes in release build
- ClassNotFoundException or NoSuchMethodError

**Solution** - Add ProGuard rules in `app/proguard-rules.pro`:
```proguard
# UXCam SDK
-keep class com.uxcam.** { *; }
-keep interface com.uxcam.** { *; }
-dontwarn com.uxcam.**

# OkHttp (used by UXCam)
-dontwarn okhttp3.**
-dontwarn okio.**
-keep class okhttp3.** { *; }
-keep interface okhttp3.** { *; }

# Kotlin coroutines
-keepnames class kotlinx.coroutines.internal.MainDispatcherFactory {}
-keepnames class kotlinx.coroutines.CoroutineExceptionHandler {}
```

---

## Crash Reporting Issues

### Crashes Not Appearing in Dashboard

**Symptoms:**
- App crashes but no crash reports in UXCam
- Crash logs missing

**Solutions:**

1. **Verify crash handling is enabled**:
```kotlin
val config = UXConfig.Builder("YOUR_APP_KEY")
    .enableAutomaticScreenNameTagging(true)
    .build()

UXCam.startWithConfiguration(config)
// Crash handling is enabled by default
```

2. **Check for conflicting crash handlers**:
```kotlin
// If using other crash reporting tools (Crashlytics, Sentry, etc.)
// Ensure they don't interfere with UXCam

// Initialize UXCam BEFORE other crash handlers
UXCam.startWithConfiguration(config)  // First
FirebaseCrashlytics.getInstance()  // After
```

3. **Test crash reporting**:
```kotlin
// Add test button to trigger crash
button.setOnClickListener {
    throw RuntimeException("Test crash for UXCam")
}
```

---

## Platform-Specific Issues

### Android 12+ (API 31+) Issues

**Exported Component Error:**

**Symptoms:**
```
Apps targeting Android 12 and higher are required to specify an explicit value for android:exported
```

**Solution** - Update `AndroidManifest.xml`:
```xml
<activity
    android:name=".MainActivity"
    android:exported="true">  <!-- Add this -->
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```

### Android 14+ (API 34+) Issues

**Foreground Service Types:**

If your app uses foreground services, ensure proper types are declared:
```xml
<service
    android:name=".MyService"
    android:foregroundServiceType="dataSync|location" />
```

---

## Getting Additional Help

### Debug Information to Collect

When contacting support, include:

1. **Environment Info**:
```bash
./gradlew --version
```

2. **Dependency Tree**:
```bash
./gradlew app:dependencies > dependencies.txt
```

3. **UXCam Logs**:
```bash
adb logcat -d > logcat.txt
# Or filtered
adb logcat -d | grep -E "UXCam|UXCAM" > uxcam_logs.txt
```

4. **Build Configuration**:
- `build.gradle` (project level)
- `app/build.gradle`
- `AndroidManifest.xml`

5. **Minimal Reproduction**:
   - Create simple test app demonstrating the issue
   - Include relevant code snippets
   - Specify exact SDK version

### Contact Support

- **Email**: [team@uxcam.com](mailto:team@uxcam.com)
- **Documentation**: [Android Integration Guide](index.md)
- **Changelog**: [Version History](android-sdk-changelog-android.md)
- **Advanced Configuration**: [Advanced APIs](advanced-configuration-and-apis/index.md)

### Related Documentation

- [Screen Tagging Guide](screen-tagging/index.md)
- [Sensitive Data Occlusion](sensitive-data-occlusion/index.md)
- [Users and Properties](users-and-properties-android.md)
- [Sending Events](sending-events-android.md)

---

*Last updated: January 2025*
