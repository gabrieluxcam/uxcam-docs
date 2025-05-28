---
title: Initialise the SDK
deprecated: false
hidden: false
metadata:
  robots: index
---
Let's get you started with the basics. With just a few lines of code, you'll be on your way to capturing first user sessions in your test app.

## Add dependencies for UXCam Library

Import UXCam using Maven in the **module's build.gradle** file:

```kotlin build.gradle (Java & Kotlin)
repositories {
  maven{ 
     url 'https://sdk.uxcam.com/android/' 
  } 
} 
dependencies { 
     implementation 'com.uxcam:uxcam:3.+' 
}
```

#### Store your UXCAM\_KEY safely

Find the UXCam App Key for your app integration - **TODO: Add screenshot of where to find the App Key**

1. **Pro tip:** create separate apps keys for your debug and production app in the UXCam dashboard (e.g. 'Your app - debug', 'Your app - production') to keep your data clean.

2. Add UXCAM\_KEY to your local.properties file. local.properties lives in the project root and is already in every default .gitignore, so it never reaches your VCS.
   ```Text local.properties (Java & Kotlin)
   UXCAM_KEY=your_app_key
   ```

3. Load the `UXCAM_KEY` in your build.gradle (or build.gradle.tks) files into your BuildConfig

```kotlin
// app/build.gradle.kts (Kotlin DSL)

val uxcamKey: String = project.findProperty("UXCAM_KEY") as? String ?: ""

android {
    defaultConfig {
        // Same result: BuildConfig.UXCAM_KEY
        buildConfigField("String", "UXCAM_KEY", "\"$uxcamKey\"")
    }
}
```
```groovy
// app/build.gradle (Groovy DSL)

def uxcamKey = project.findProperty("UXCAM_KEY") ?: ""

android {
    defaultConfig {
        // Make the key available as BuildConfig.UXCAM_KEY
        buildConfigField "String", "UXCAM_KEY", "\"${uxcamKey}\""
    }
}
```

#### Configure and initialise the UXCam SDK

1. Identify where to initialise the UXCam Android SDK

| **If you…**                                                 | **Put`UXCam.startWithConfiguration()` in…**                                              | **Why**                                                                                                              |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| Have (or can add) a custom **`Application`class**           | `MyApp : Application` → `override fun onCreate()`                                        | Runs before any `Activity`, so the first screen and uncaught crashes are always captured. Recommended for most apps. |
| Don’t have an `Application` class and don’t want to add one | Your **launcher`Activity`** → `onCreate()` (before `setContentView`)                     | Still early enough for the first screen; simplest drop-in as shown in the official quick-start.                      |
| Use a **single-Activity / Jetpack Compose** architecture    | Either of the above (preferred) **or** the top-level `@Composable` that’s first rendered | Works because Compose’s `setContent` is still in the launcher activity’s `onCreate()`.                               |
| Rely on **MultiDex**                                        | Call `MultiDex.install(this)` first, then UXCam, inside `Application.onCreate()`         | Guarantees the secondary dex files are loaded before the SDK.                                                        |

**Rule of thumb:** *Start the SDK once, at the earliest point where you have an`android.content.Context` that lives for the entire app lifecycle.*

2. Configure and start UXCam recording in Launcher Activity

```kotlin
// app/src/main/java/com/example/MyApp.kt
// import library
import com.uxcam.UXCam;
import com.uxcam.datamodel.UXConfig;
```
```java
import com.uxcam.UXCam;
import com.uxcam.datamodel.UXConfig;
```

```java Kotlin
// app/src/main/java/com/example/MyApp.kt
class MyApp : Application() {

    override fun onCreate() {
        super.onCreate()

        val builder = UXConfig.Builder(BuildConfig.UXCAM_KEY)
            .enableAutomaticScreenNameTagging(true)   // optional tweaks
            
        
        // Enable verbose integration logging only in debug builds
				if (BuildConfig.DEBUG) {
        	builder.enableIntegrationLogging(true);
				}

        UXCam.startWithConfiguration(config.build())
    }
}
```
```coffeescript Java
UXConfig config = new UXConfig.Builder("yourAppKey").build();
```

> 👍 As Simple As That!
>
> This will complete the integration process.\
> Your session will be shown on the dashboard within a few seconds after the app goes in the background.
>
> We recommend that after you've set this up and have reviewed some sessions from your tests, get to the customisation features UXCam offers, let's go to the next steps!

## Next Steps ➡️

You've successfully integrated UXCam and sent some sessions, great job! 🎉  But there's so much more you can do. Now, let's go further into setting things up.