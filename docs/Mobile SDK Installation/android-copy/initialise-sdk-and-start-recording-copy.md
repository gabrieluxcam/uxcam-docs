---
title: Initialise SDK and start recording
excerpt: Implement the SDK and record your first session.
deprecated: false
hidden: true
metadata:
  robots: index
next:
  pages:
    - slug: sensitive-data-occlusion-in-android-apps-copy
      title: 'Occlude sensitive data '
      type: basic
---
## 🚀 Initialise the UXCam SDK & Start Recording

Follow the four mini-steps below and you’ll have your first test session on the UXCam Dashboard in minutes.

---

### 1   Add the UXCam dependency

Add UXCam’s Maven repo **and** dependency in your *module-level* Gradle file.

<details>
<summary><code>build.gradle.kts</code> (Kotlin DSL)</summary>

```kotlin
repositories {
    maven { url = uri("https://sdk.uxcam.com/android/") }
}

dependencies {
    implementation("com.uxcam:uxcam:3.+")
}
````

</details>

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

---

### 2   Find & store your **UXCAM\_KEY** safely

1. **Grab the key** in **App Settings → App Key** on the [UXCam Dashboard](https://app.uxcam.com).

   > *Tip ✏️* Create separate keys for **debug** and **production** to keep data clean.
2. **Add the key to `local.properties`** (already in the default `.gitignore`):

   ```properties
   # local.properties
   UXCAM_KEY=your_app_key
   ```
3. **Expose the key via `BuildConfig`** so you never hard-code secrets.

   <details>
   <summary>Kotlin DSL</summary>

   ```kotlin
   // app/build.gradle.kts
   val uxcamKey: String = project.findProperty("UXCAM_KEY") as? String ?: ""

   android {
       defaultConfig {
           buildConfigField("String", "UXCAM_KEY", "\"$uxcamKey\"")
       }
   }
   ```

   </details>

   <details>
   <summary>Groovy DSL</summary>

   ```groovy
   // app/build.gradle
   def uxcamKey = project.findProperty("UXCAM_KEY") ?: ""

   android {
       defaultConfig {
           buildConfigField "String", "UXCAM_KEY", "\"${uxcamKey}\""
       }
   }
   ```

   </details>

---

### 3   Configure **and** initialise the SDK

#### 3.1  Pick the right spot

| **If you …**                                 | **Call `UXCam.startWithConfiguration()` in …**                    | **Why this spot?**                                                              |
| -------------------------------------------- | ----------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| Have (or can add) an **`Application` class** | `MyApp : Application → onCreate()`                                | Earliest lifecycle hook → captures the first screen & crashes. *(Recommended.)* |
| Don’t have an `Application` class            | **Launcher `Activity` → onCreate()**, **before** `setContentView` | Still early enough for the first screen; zero extra classes.                    |
| Use **single-Activity / Jetpack Compose**    | Either of the above **or** the first `@Composable` rendered       | Compose is launched from the activity’s `onCreate()`.                           |
| Rely on **MultiDex**                         | `Application.onCreate()` **after** `MultiDex.install(this)`       | Ensures secondary DEX files are loaded before UXCam.                            |

> **Rule of thumb:** *Start the SDK **once**, at the earliest `Context` that lives for the whole app.*

> ❌ **Never** call it from multiple places—this logs “SDK already started” warnings and may break uploads.

#### 3.2  Sample Kotlin setup (inside `Application`)

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
            .enableAutomaticScreenNameTagging(true)   // remove if you tag screens manually
            .apply {
                // Verbose integration logs only in debug
                if (BuildConfig.DEBUG) enableIntegrationLogging(true)
            }
            .build()

        UXCam.startWithConfiguration(config)
    }
}
```

Don’t forget to declare the class in **`AndroidManifest.xml`**:

```xml
<application
    android:name=".MyApp"
    … >
```

#### 3.3  Java fallback (launcher `Activity`)

```java
public class MainActivity extends AppCompatActivity {

    @Override protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        UXConfig config = new UXConfig.Builder(BuildConfig.UXCAM_KEY).build();

        // Must be *before* setContentView
        UXCam.startWithConfiguration(config);

        setContentView(R.layout.activity_main);
    }
}
```

---

### 4   Verify the integration

1. **Run the app** on a device/emulator, use it for \~20 seconds and open *Logcat* (filter by **uxcam**).
   You should see:

   * `Verification successful`
   * `Session recording started`
2. **Background the app** (don’t terminate the process).
   You should see:

   * `Session upload started.` and `Video upload started.`
   * `Session upload successful.` and `Video upload successful.`
3. Within **1–2 minutes** the session appears on your [UXCam Dashboard](https://app.uxcam.com). 🎉

---

### ➡️ Next steps

Great job—your first sessions are flowing!
Next, learn how to **occlude sensitive data** and **refine screen names** to unlock the full power of UXCam analytics.