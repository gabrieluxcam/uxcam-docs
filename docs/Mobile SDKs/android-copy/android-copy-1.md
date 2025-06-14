---
title: Android SDK Changelog
deprecated: false
hidden: false
metadata:
  description: >-
    The UXCam Android Changelog details various fixes and improvements across
    multiple versions, including resolving crashes, enhancing occlusion
    handling, and adding new features like app launch time metrics and wrapper
    info on tagged screen data.
  robots: index
---
### V. 3.6.42 - Jun 11, 2025

* Fix: Google ads not being clickable
* Fix: Fixed tag on Integration log
* Fix: Crash due to PixelCopyRequest
* Fix: Session shows 00 time stamp
* Fix: Delayed Occlusion elements make PII Data show in screen transitions
* Fix: Flutter Occluded view is being visible while scrolling or navigating

### V. 3.6.41 - April 29, 2025

* Fixed occlusion being removed if bottom sheet is presented
* Fixed crash issue for map view in React Native

### V. 3.6.40 - April 16, 2025

* **Fix:** Prevent capturing occluded screens information.
* **Fix:** ANRs on Android.
* **Fix:** Flutter crash.
* **Improvement**: Implement cancel Api.

### V. 3.6.39 - March 5, 2025

* **Fix:** Flutter crash issue due to v2 embedding.
* **Fix:** Debug logs not reporting correctly.
* **Fix:** Black screen on few frames.

### V. 3.6.38 - February 14, 2025

* **Fix**: Fixed App Launch Metrics
* **Fix**: OutOfMemoryError
* **Improvement**: Excessive logging when parsing HTML

### V. 3.6.37 - January 21, 2025

* **Added**: Added Integration Log
* **Fix**: crash on Google ad view.
* **Fix**: Occlusion persists in React Native

### V. 3.6.36 - December 17, 2024

* **Fix**: Flutter Occlusion Skip Issue
* **Improvement**: Send the video of debug session in new kinesis pipeline to make it available in dashboard sooner.

### V. 3.6.35 - December 6, 2024

* **Fix**: App crash on launch in multidex projects
* **Fix**: Handled NoSuchMethodError when using reflection

***

### V. 3.6.34 - November 14, 2024

* **Fix**: Jetpack Compose Views were tagged with incorrect information. SDK now detects clicks on Compose View and tag with null information.
* **Fix**: Flutter Android - Crash when applying multiple occlusions.

***

### V. 3.6.33 - October 29, 2024

* **Improvement**: Ignore mobile data limit for data file.
* **Fix**: Resolve manually tagged screen not being recorded on first start.
* **New**: Added App launch time metrics.

***

### V. 3.6.32 - September 20, 2024

* **Fix**: Fixed issue where force-closed session was written with empty timeline data.
* **Fix**: Handled `DeadSystemException` when retrieving app version name.
* **Fix**: Resolved issue where user properties were not registered.

***

### V. 3.6.31 - September 11, 2024

* **Fix**: WebView element occlusion support for native and cross-platform wrappers.
* **Fix**: Prevented `SurfaceView` rendering in `androidx.camera.view.PreviewView` to avoid recording the camera preview.
* **Fix**: Fixed video filter issues where sessions satisfying filter conditions were not discarded.
* **Improvement**: Reduced main thread usage during SDK initialization to fix ANR.
* **Fix**: Fixed issue where session ended after set time, even with `allowShortBreak` set to false.

***

### V. 3.6.30 - August 7, 2024

* **Fix**: Cache verification response to prevent re-verification before expiration.
* **Fix**: Ignored screen set on dashboard was not reflected on warm start of the app.
* **Fix**: Safely handle fatal crash, `ArrayIndexOutOfBoundsException @ ScreenTagManagerImpl`.

***

### V. 3.6.29 - July 19, 2024

* **Fix**: Session was not stopped correctly when `androidx.startup.InitializationProvider` is removed from the manifest. SDK now falls back to the legacy approach for app lifecycle detection when `InitializationProvider` is unavailable.

***

### V. 3.6.28 - June 27, 2024

* **Fix**: Fixed issue where the first frame after a view appears was not occluded in Jetpack Compose.
* **Improvement**: Improved occlusion synchronization during view movement by drawing the occlusion rectangle before and after screenshots in Jetpack Compose.
* **Fix**: Resolved WebView rendering black session video frames in Flutter apps.
* **Fix**: Fixed dialogs not rendering correctly in Flutter hybrid applications.

***

### V. 3.6.27 - May 31, 2024

* **Improvement**: Force close support for data file.
* **Improvement**: Fragment tagging improvements.
* **Improvement**: Improved exception handling on screen action implementation.
* **Fix**: Handled `FileNotFoundException`.

***

### V. 3.6.26 - May 13, 2024

* **Fix**: Fixed an issue where sessions were not stopping correctly from cross-platform plugins, which was present in the previous version.

***

### V. 3.6.24 - April 1, 2024

* **Fix**: Accurate Screen Action detection when interacting with elements that would result in layout updates.
* **Fix**: Button text change when clicked was not captured accurately.
* **Fix**: UI properties were tracked on full-screen occlusion.
* **Fix**: Screen Action for `ListPreference` data was not captured on click.

***

### V. 3.6.23 - March 18, 2024

* **Fix**: Prevent `NoSuchElementException` due to race condition on preparing screen bitmap.
* **Fix**: Get Screen action of responsive view.

***

### V. 3.6.22 - February 29, 2024

* **Fix**: `PixelCopy` error log printed while navigating through activities.
* **Fix**: Previous screen action details were registered for new screen actions in some specific scenarios.

***

### V. 3.6.21 - February 1, 2024

* **Fix**: View within `BottomSheetFragment` was not occluded when shown navigating to a new activity.
* **Fix**: View type was sent for occluded view.
* **New**: Ability to add wrapper info on the tagged screen data.

***

### V. 3.6.20 - January 23, 2024

* **Fix**: Rage tap session filter did not work.
* **Fix**: OutOfMemory (OOM) error while encrypting very large videos.
* **Fix**: Fatal crash with `AssertionError` while looping through layout for screen action.
* **Fix**: Not disabling screen action when accessibility service is enabled.
* **Fix**: Occluding parent view did not prevent screen action tracking of its child view.

***

### V. 3.6.19 - December 13, 2023

* **Fix**: Crash - Null cannot be cast to non-null type `android.app.Activity`.
* **Fix**: Exception while navigating between Activities.
* **Fix**: Main thread blocked causing ANR on Android API level 21 and 22.
* **New**: Added pending session count to verification request.
* **Fix**: Internal debug logs were only cleared when verification response has status true.

***

### V. 3.6.18 - November 9, 2023

* **Fix**: Data and video time inconsistency, skipped frame.
* **Fix**: Screen action data was visible for the occluded view.
* **Fix**: `Surface` isn't valid, `IllegalArgumentException` when SDK tries to take screenshot of invalid surface.
* **Fix**: Fatal crash `ArrayIndexOutOfBoundsException` and `ConcurrentModificationException` on `ScreenActionViewsRepositoryImpl`.

***

### V. 3.6.17 - October 20, 2023

* **Fix**: OutOfMemoryError on low-end devices.
* **Fix**: View time of screen being 0 with gesture time being greater than view arrival time.
* **Fix**: GLSurfaceView rendered black.

***

### V. 3.6.16 - October 6, 2023

* **Fix**: Occlusion drawing shift on some devices.
* **Fix**: Occlusion affecting dialogs.
* **Fix**: Fatal crash "IllegalStateException: Underflow in restore - more restores than saves".
* **Fix**: Multi-region infrastructure support.

***

### V. 3.6.15 - September 15, 2023

* **Fix**: Veracode flagging for "Improper Resource Shutdown or Release".
* **Fix**: Compose UI dependency updated to version 1.4.3.
* **Fix**: React Native - `MapView` not recorded on screen video.
* **New**: Send app environment variable in the verification request.

***

### V. 3.6.14 - August 28, 2023

* **Fix**: Android: No video uploaded in session if user presses device home button.
* **Fix**: Android: Missing sessions/SDK not starting if minification is added to app.
* **Fix**: React Native - Android Native OutOfMemory (OOM) crashes.