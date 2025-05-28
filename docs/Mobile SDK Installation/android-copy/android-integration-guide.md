---
title: '# Android Integration Guide'
deprecated: false
hidden: false
metadata:
  robots: index
---
# Android Integration Guide

Let’s get you started with the basics. With just a few lines of code, you’ll be on your way to capturing user sessions in your test app.

---

## 1  Add the UXCam dependency

Add UXCam’s Maven repo and the dependency in **your module’s** `build.gradle` (Groovy) **or** `build.gradle.kts` (Kotlin DSL).

<details>
<summary><code>build.gradle</code> (Groovy)</summary>

```groovy
repositories {
    maven { url 'https://sdk.uxcam.com/android/' }
}

dependencies {
    implementation 'com.uxcam:uxcam:3.+'
}
```
</details>

<details>
<summary><code>build.gradle.kts</code> (Kotlin DSL)</summary>

```kotlin
repositories {
    maven { url = uri("https://sdk.uxcam.com/android/") }
}

dependencies {
    implementation("com.uxcam:uxcam:3.+")
}
```
</details>

---

## 2  Store your **UXCAM_KEY** safely

1. **Find your App Key** in the UXCam dashboard  
    TODO: Add screenshot of where to find the App Key

   **Pro-tip:** create separate keys for your *debug* and *production* apps (e.g. **“Your App – debug”**, **“Your App – production”**) to keep data clean.

2. Add the key to **`local.properties`** (already ignored by Git):

   ```properties
   # local.properties
   UXCAM_KEY=your_app_key
   ```

3. Expose the key to code via **`BuildConfig`**.

<details>
<summary><code>app/build.gradle.kts</code> (Kotlin DSL)</summary>

```kotlin
// app/build.gradle.kts
val uxcamKey: String = project.findProperty("UXCAM_KEY") as? String ?: ""

android {
    defaultConfig {
        buildConfigField("String", "UXCAM_KEY", ""$uxcamKey"")
    }
}
```
</details>

<details>
<summary><code>app/build.gradle</code> (Groovy)</summary>

```groovy
// app/build.gradle
def uxcamKey = project.findProperty("UXCAM_KEY") ?: ""

android {
    defaultConfig {
        buildConfigField "String", "UXCAM_KEY", ""${uxcamKey}""
    }
}
```
</details>

---

## 3  Configure **and** initialise the SDK

### 3.1  Pick the right place to start UXCam

| **If you …** | **Call `UXCam.startWithConfiguration()` in …** | **Why this spot?** |
|--------------|-----------------------------------------------|--------------------|
| Have (or can add) a custom **`Application` class** | `class MyApp : Application → override fun onCreate()` | Runs before any `Activity`, so the **very first screen and uncaught crashes** are captured. *(Recommended.)* |
| Don’t have an `Application` class and don’t want to add one | Your **launcher `Activity` → onCreate()`**, **before** `setContentView` | Still early enough for the first screen; simplest drop-in. |
| Use a **single-Activity / Jetpack Compose** architecture | Either of the above (preferred) **or** the first `@Composable` rendered | Compose’s `setContent` is triggered from the launcher `Activity`’s `onCreate()`, so both locations work. |
| Rely on **MultiDex** | `Application.onCreate()` **after** `MultiDex.install(this)` | Ensures secondary Dex files are loaded before UXCam. |

> **Rule of thumb:** *Start the SDK **once**, at the earliest point where you have a `Context` that lives for the entire app lifecycle.*

### 3.2  Sample Kotlin setup (inside `Application`)

```kotlin
// app/src/main/java/com/example/MyApp.kt
package com.example

import android.app.Application
import com.uxcam.UXCam
import com.uxcam.datamodel.UXConfig

class MyApp : Application() {

    override fun onCreate() {
        super.onCreate()

        val config = UXConfig.Builder(BuildConfig.UXCAM_KEY)
            .enableAutomaticScreenNameTagging(true) // remove if you tag screens manually
            .apply {
                // Enable verbose integration logs only in debug builds
                if (BuildConfig.DEBUG) {
                    enableIntegrationLogging(true)
                }
            }
            .build()

        UXCam.startWithConfiguration(config)
    }
}
```

Add the class to **`AndroidManifest.xml`**:

```xml
<application
    android:name=".MyApp"
    ... >
```

### 3.3  Java fallback (launcher `Activity`)

```java
public class MainActivity extends AppCompatActivity {

    @Override protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        UXConfig config = new UXConfig
                .Builder(BuildConfig.UXCAM_KEY)
                .build();

        // Call BEFORE setContentView
        UXCam.startWithConfiguration(config);

        setContentView(R.layout.activity_main);
    }
}
```

---

## 4  Verify the integration

1. **Run the app in an emulator or device** and watch *Logcat* (filter by `uxcam`). You should see:  
   * `Verification successful`  
   * `Session recording started`
2. **Background the app** (don’t just kill the emulator).  
3. Within 1–2 minutes you’ll see the session on your [UXCam Dashboard](https://app.uxcam.com).  
    TODO: Add screenshot of the first session in the dashboard

---

## 5  Troubleshooting

* **No “verification successful” log?**  
  * Check your key, network connectivity and ProGuard/R8 rules (the SDK is already obfuscation-safe, but verify custom rules).
* **Session not uploaded?**  
  * Ensure the app is backgrounded gracefully; a force-kill stops the upload on emulators. Real devices retry later.
* **Multiple initialisation warnings?**  
  * Confirm `UXCam.startWithConfiguration()` is called exactly *once*.

---

> 👍 **As simple as that!**  
> Once you confirm your first sessions, explore UXCam’s customisation features to get even deeper insights.
