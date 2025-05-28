---
title: Initialise the SDK
excerpt: Implement the SDK and record your first session.
deprecated: false
hidden: false
metadata:
  robots: index
---
Let's get you started with the basics. With just a few lines of code, you'll be on your way to capturing first user sessions in your test app.

\<Tabs>\
\<Tab title="Second Tab">
AI Approach
\</Tab>
\<Tab title="Manual Integration">

\<Accordion title="Add the UXCam dependency">\
Add UXCam’s Maven repo and the dependency in **your module’s** `build.gradle` (Groovy) **or** `build.gradle.kts` (Kotlin DSL):

```
```

```
```

```
```

\<Accordion title="Store your **UXCAM\_KEY** safely" icon="fa-key">

## 2  Store your **UXCAM\_KEY** safely

1. **Find your App Key** in the UXCam dashboard\\\
   \<!-- TODO: Add screenshot of where to find the App Key -->

> **Pro-tip:** create separate keys for your *debug* and *production* apps (e.g. **“Your App – debug”**, **“Your App – production”**) to keep data clean.Add the key to **`local.properties`** (already ignored by Git):

2. **Add the key to local.properties** (already ignored by Git):

```
```

3. **Expose the key to code via BuildConfig**

```
```

val uxcamKey: String = project.findProperty("UXCAM\_KEY") as? String ?: ""

android \{\
defaultConfig \{
// Same result: BuildConfig.UXCAM\_KEY
buildConfigField("String", "UXCAM\_KEY", ""$uxcamKey"")
}
}````
```groovy app/build.gradle (Groovy)
// app/build.gradle (Groovy DSL)
````

def uxcamKey = project.findProperty("UXCAM\_KEY") ?: ""

android \{\
defaultConfig \{
// Make the key available as BuildConfig.UXCAM\_KEY
buildConfigField "String", "UXCAM\_KEY", ""$\{uxcamKey}""
}
}```
\</Accordion>
```

<br />

## 1. Add the UXCam dependency

## 2. Store your **UXCAM\_KEY** safely

1. **Find your App Key** in the UXCam dashboard\\

<br />

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