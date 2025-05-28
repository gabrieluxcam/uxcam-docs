---
title: Initialise the SDK
excerpt: Implement the SDK and record your first session.
deprecated: false
hidden: false
metadata:
  robots: index
---
/

Let's get you started with the basics. With just a few lines of code, you'll be on your way to capturing first user sessions in your test app.

<Accordion title="1. Add the UXCam dependency" icon="fa-info-circle">
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

  ##
</Accordion>

## 1. Add the UXCam dependency

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

<br />

## 2. Store your **UXCAM\_KEY** safely

1. **Find your App Key** in the UXCam dashboard\
   \<!-- TODO: Add screenshot of where to find the App Key -->

> **Pro-tip:** create separate keys for your *debug* and *production* apps (e.g. **“Your App – debug”**, **“Your App – production”**) to keep data clean.Add the key to **`local.properties`** (already ignored by Git):

2. **Add the key to local.properties** (already ignored by Git):

```Text local.properties
UXCAM_KEY=your_app_key
```

3. **Expose the key to code via BuildConfig**

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

## 3. Configure and initialise the UXCam SDK

### 3.1 Pick the right place to start UXCam

| **If you…**                                                 | **Put`UXCam.startWithConfiguration()` in…**                                              | **Why**                                                                                                              |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| Have (or can add) a custom **`Application`class**           | `MyApp : Application` → `override fun onCreate()`                                        | Runs before any `Activity`, so the first screen and uncaught crashes are always captured. Recommended for most apps. |
| Don’t have an `Application` class and don’t want to add one | Your **launcher`Activity`** → `onCreate()` (before `setContentView`)                     | Still early enough for the first screen; simplest drop-in as shown in the official quick-start.                      |
| Use a **single-Activity / Jetpack Compose** architecture    | Either of the above (preferred) **or** the top-level `@Composable` that’s first rendered | Works because Compose’s `setContent` is still in the launcher activity’s `onCreate()`.                               |
| Rely on **MultiDex**                                        | Call `MultiDex.install(this)` first, then UXCam, inside `Application.onCreate()`         | Guarantees the secondary dex files are loaded before the SDK.                                                        |

**Rule of thumb:** *Start the SDK once, at the earliest point where you have an`android.content.Context` that lives for the entire app lifecycle.*

### 3.2 Sample  setup (inside Application)

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

## 4. Verify the integration

1. **Run the app in an emulator or device**, interact with it for 20s or so and watch *Logcat* (filter by `uxcam`). You should see:

* `Verification successful`
* `Session recording started`

2. **Background the app** (don’t just kill the emulator).
3. \*\*Within 1–2 minutes you’ll see the session on your [UXCam Dashboard](\[https://app.uxcam.com]\(https://app.uxcam.com\)) . \*\*\
   \<!-- TODO: Add screenshot of the first session in the dashboard -->

<br />

## Next Steps ➡️

You've successfully integrated UXCam and sent some sessions, great job! 🎉  But there's so much more you can do. Now, let's go further into setting things up.