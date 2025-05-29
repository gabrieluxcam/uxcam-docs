---
title: Initialise SDK and start recording
excerpt: Implement the SDK and record your first session.
deprecated: false
hidden: true
metadata:
  robots: index
---
Let's get you started with the basics. With just a few lines of code, you'll be on your way to capturing first user sessions in your test app.

## 1  Add the UXCam dependency

Add UXCam’s Maven repo and the dependency in **your module’s** `build.gradle` (Groovy) **or** `build.gradle.kts` (Kotlin DSL):

```kotlin build.gradle.kts (Kotlin DSL)
repositories {
    maven { url 'https://sdk.uxcam.com/android/' }
}

dependencies {
    implementation 'com.uxcam:uxcam:3.+'
}
```
```groovy build.gradle (Groovy)
repositories {
    maven { url 'https://sdk.uxcam.com/android/' }
}

dependencies {
    implementation 'com.uxcam:uxcam:3.+'
}
```

## 2  Store your **UXCAM\_KEY** safely

The app key is the identifier for your integration, and you can find it in the App settings page on the [UXCam dashboard](app.uxcam.com).

**Tip** create separate apps in the UXCam dashboard for your *debug* and *production* apps (e.g. **“*Your App* – debug”**, **“*Your App* – production”**) to keep data clean.

<Accordion title="How to find your app key on the UXCam dashboard" icon="fa-info-circle">
  1. **Go to the App settings of the app you created on the UXCam dashboard.**

  <Image align="center" border={true} src="https://files.readme.io/74a9f2d53acc063c2fcf45c3dbce3c3783c752a09b05a50f98534358dc394bc7-Screenshot_2025-05-29_at_14.26.02.png" width="70% " />

  2. **Copy the app key from the top right corner**

  <Image align="center" border={true} src="https://files.readme.io/6cc92db41a0fb9b3a0cfe90d4a6a8944366df95727cc764c1cd41d1b62a139c2-Screenshot_2025-05-29_at_14.27.15.png" width="70% " />
</Accordion>

<br />

To make it easier to manage different keys and to not expose the app key in your VCS it is recommendable to manage the app key in the environment files. To do this  you just need to add the key to local.properties (already ignored by Git) like:

```Text local.properties
UXCAM_KEY=your_app_key
```

Then you just need to expose the key to code via BuildConfig:

```kotlin app/build.gradle.kts (Kotlin DSL)
// app/build.gradle.kts (Kotlin DSL)

val uxcamKey: String = project.findProperty("UXCAM_KEY") as? String ?: ""

android {
    defaultConfig {
        // Same result: BuildConfig.UXCAM_KEY
        buildConfigField("String", "UXCAM_KEY", "\"$uxcamKey\"")
    }
}
```
```groovy app/build.gradle (Groovy)
// app/build.gradle (Groovy DSL)

def uxcamKey = project.findProperty("UXCAM_KEY") ?: ""

android {
    defaultConfig {
        // Make the key available as BuildConfig.UXCAM_KEY
        buildConfigField "String", "UXCAM_KEY", "\"${uxcamKey}\""
    }
}
```

<br />

## 3 Configure and initialise the UXCam SDK

To start the recording

### 3.1 Pick the right place to start UXCam

| **If you…**                                                 | **Put`UXCam.startWithConfiguration()` in…**                                              | **Why**                                                                                                              |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| Have (or can add) a custom **`Application`class**           | `MyApp : Application` → `override fun onCreate()`                                        | Runs before any `Activity`, so the first screen and uncaught crashes are always captured. Recommended for most apps. |
| Don’t have an `Application` class and don’t want to add one | Your **launcher`Activity`** → `onCreate()` (before `setContentView`)                     | Still early enough for the first screen; simplest drop-in as shown in the official quick-start.                      |
| Use a **single-Activity / Jetpack Compose** architecture    | Either of the above (preferred) **or** the top-level `@Composable` that’s first rendered | Works because Compose’s `setContent` is still in the launcher activity’s `onCreate()`.                               |
| Rely on **MultiDex**                                        | Call `MultiDex.install(this)` first, then UXCam, inside `Application.onCreate()`         | Guarantees the secondary dex files are loaded before the SDK.                                                        |

**Rule of thumb:** *Start the SDK once, at the earliest point where you have an`android.content.Context` that lives for the entire app lifecycle.*

### 3.2 Sample setup (inside Application)

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
```java Java fallback
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

***

## 4  Verify the integration

1. **Run the app in an emulator or device**, interact with it for 20s or so and watch *Logcat* (filter by `uxcam`). You should see:

* `Verification successful`
* `Session recording started`

2. **Background the app** (don’t just kill the emulator).
3. \*\*Within 1–2 minutes you’ll see the session on your [UXCam Dashboard](\[https://app.uxcam.com]\(https://app.uxcam.com\)) . \*\*\
   \<!-- TODO: Add screenshot of the first session in the dashboard -->

## Next Steps ➡️

You've successfully integrated UXCam and sent some sessions, great job! 🎉  But there's so much more you can do. Now, let's go further into setting things up.