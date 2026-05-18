---
title: Android SDK Changelog
excerpt: Release history and version notes for the UXCam Android SDK
deprecated: false
hidden: false
icon: fab fa-android
metadata:
  title: Android SDK Changelog
  description: >-
    The UXCam Android Changelog details various fixes and improvements across
    multiple versions, including resolving crashes, enhancing occlusion
    handling, and adding new features like app launch time metrics and wrapper
    info on tagged screen data.
  robots: index
---
# UXCam Android Changelog

<br />

### V. 3.10.1 - May 18, 2026

* Fix (tagging): Initial fragment tagging race condition on offline cold-start
* Fix (uxcamlib): Synchronize timeline event attribution
* Fix (uxcamlib): Align session screen timeline state
* Fix (screenshot): Preserve compose occlusion across transient tags
* Fix (screenshot): Keep compose occlusion during transient detach
* Fix (file): Cache JSON byte[] once in DataFile write path


### V. 3.10.0 - May 5, 2026

* **New**: Readable fragment screen tagging — fragment screens now show as `Activity/Fragment` (e.g. `MainActivity/HomeFragment`) instead of hex hashes (e.g. `MainActivity/0xa1b2c3`) in the dashboard timeline
* **New**: Opt-out available via `UXConfig.Builder.enableReadableFragmentNames(false)` for customers with analytics tied to legacy hash names
* **Note**: Existing occlusion rules using old hex hash names continue to work — the SDK matches against both names
* Feat (frame-sync): marker-based occlusion via `FrameSyncManager`
* Fix (occlusion): evict detached Compose occlusions on read
* Fix (capture): skip captures when activity view tree isn't laid out yet
* Fix (occlusion): key `autoByRoot` by `View` to prevent silent bucket loss
* Fix (logcat): use `ProcessBuilder` to process logcat data across all API levels

<br />

### V. 3.9.1 - Apr 9, 2026

* Fix: ANR in `ReflectionUtil.findField` caused by `getDeclaredFields()` iterating all fields per class level
* Fix (rage-tap): use server rage-click config instead of hardcoded defaults
* Fix: skip `canScrollVertically` on `AndroidComposeView` to prevent `ConcurrentModificationException` in Compose semantics tree traversal
* Fix: resolve ANR caused by checkpoint writing on main thread during `logEvent()` calls
* Fix (uxcamlib): make cancel lifecycle-owned
* Fix (thread-safety): use `ConcurrentHashMap` and `ConcurrentSkipListSet` for public API data structures
* Fix (ANR): prevent ANR by moving blocking public APIs off main thread
* Fix: ANR in `ScreenActionTracker` due to repeated reflection on every view scan

<br />

### V. 3.9.0 - Mar 31, 2026

* Feat (identity): user identity is now in-memory only, decoupled from session restart
* Fix: simplified deferred startup lifecycle handling
* Fix (screenshot): centralized blur backend routing
* Fix: redact screen action details for occluded `EditText` views
* Fix: allow gesture tracking on auto-occluded `EditText` views
* Fix (Cordova): text fields get automatically occluded
* Fix: add support for blur radius greater than 25px

<br />

### V. 3.8.14 - Mar 13, 2026

* Fix (response): prevent NPE when null data is received from server
* Fix: session cancelling due to `OutOfMemoryException`
* Fix: allow per-view occlusion to apply alongside full-screen occlusion

<br />

### V. 3.8.13 - Feb 26, 2026

* fix(pixelcopy): capture surface view for overlay window
* fix: initialize gesture detector in main looper
* fix: defer orientation state update until session is running to preserve initial orientation
* fix(memory-leak): prevent memory leak due to retaining activity context
* fix(session-lifecycle): start session when app comes to foreground after failed state
* fix: resolve orientation-related occlusion issues

<br />

### V. 3.8.12 - Feb 10, 2026

* Fix: excludeMentionedScreens not working on applyOcclusion
* Fix: cancel session if app goes to background before verification response received
* Fix: video not uploading on Force Close
* Fix: batch upload progress flag not resetting on exceptions
* Fix: log formatting issues
* Fix: memory leaks from unclaimed frames, H.264 encoder, and runBlocking operations
* Fix: UI thread blocking when closing debug log files
* Improve: non-blocking verification cancel
* Refactor: session upload pipeline with centralized lifecycle state machine and single gateway
* Refactor: frame capture using callback-based approach instead of complex pooling mechanism
* Refactor: removed VerificationUtil, redundant backend session recovery files, deprecated Gradle properties, and unused bitmap/webview occlusion code

### V. 3.8.11 - Jan 27, 2026

* Fix: isRecording() now uses SessionStartGateway with explicit state checks
* Fix: Handle null values before creating JSON object
* Fix: Handle non-fragment activity when fragment tagging is enabled
* Fix: Add null check for context_ before upload to prevent crash
* Improve logging: Added new parameter debugMode to enableIntegrationLogging function to display debug logs
* Improve: removed Timber logging infrastructure and migrated all logs to Verbose logging

### V. 3.8.10 - Jan 21, 2026

* Fix: ANR on app startup by deferring CoreModule initialization and using IO dispatcher
* Fix: Prevent app crashes when preconditions are not met
* Improve: Upload debug log file after successful verification with configurable pause toggle
* Refactor: Removed Xamarin wrapper, unused HttpPostService and redundant permission/storage checks

### V. 3.8.9 - Jan 14, 2026

* Fix: Integration logs not showing in release mode
* Improve: Added two-level logging system for cleaner debug and diagnostic log separation
* Fix: Active session being accidentally deleted during cleanup operations
* Fix: Video encoder failures and orphan cleanup causing crashes
* Fix: WebView content exposed when alert dialogs appear over WebView
* Fix: Toolbar key not appearing in Flutter datafile for non-navigation scenarios
* Fix: WorkManager crash when uploading sessions from older SDK versions
* Fix: Missing zip extensions on uploaded data and video files

### V. 3.8.8 - Jan 6, 2026

* Improve: Synchronously stop session to ensure crash data and crash video are properly captured
* Fix: Total session time and video length mismatch

### V. 3.8.7 - Dec 29, 2025

* Fix: Screen recording on full-screen occlusion
* Fix: Crashed sessions uploading when filtered by dashboard settings

### V. 3.8.6 - Dec 22, 2025

* Fix: ANR issues caused by blocking SharedPreferences operations
* Fix: Manual occlusion being lost in RecyclerView detach/reattach cycles
* Fix: Flutter occlusion rect alignment with native coordinates

### V. 3.8.5 - Dec 18, 2025

* Fix: RecyclerView occlusion disappearing
* Fix: Manual occlusion being lost
* Fix: Cross-platform occlusion rects transformation issues
* Fix: Bitmap dimensions don't match on rotation
* Fix: Flutter bar cutout and release compile issue
* Fix: Dex errors (localiza)
* Fix: Screen tagging skipped when screen recording is paused (reverted)
* Improve: Reduce delta when fast scrolling

### V. 3.8.4 - Dec 3, 2025

* Fix: Skip KeyboardInsetsTracker reinstall if already attached to activity
* Fix: Manual screen name being reset on session reset
* Fix: Video orientation not changing correctly
* Fix: Timeline not matching video playback
* Fix: Alert dialogs not appearing in video recordings
* Fix: Session upload not rescheduling when app returns to foreground with WiFi
* Improve: AI text occlusion feature flag auto-enabled when added to config
* Improve: New upload architecture for better reliability
* Improve: Flutter initial screen tagging and navigation handling
* Improve: Remove WorkManager dependency - apps can upload without it
* Improve: Unified occlusion state management across SDK

### V. 3.8.3 - Nov 21, 2025

* Fix: Skip KeyboardInsetsTracker reinstall if already attached to activity
* Fix: Delete session if s3 token is missing from backend
* Fix: Crash due to R8 inlining code
* Improve: Flutter occlusion handling
* Improve: Add implementation for flutter occlusion
* Improve: Add support for app key in ae region

### V. 3.8.2 - Nov 19, 2025

* Improve: Add UploadService fallback when WorkManager unavailable
* Improve: Add session clock for data/video synchronization
* Refactor: Unify session stop into single path
* Refactor: Replace bitmap with FramePacket for better frame handling
* Fix: Deadlock when app going to background
* Fix: Flutter not drawing issue on PixelCopy
* Fix: Video duration not matching actual session time
* Fix: Session not uploading on Wi-Fi only mode
* Fix: Occlusion issue in screen and view

### V. 3.8.1 - Nov 7, 2025

* Refactored Android Project to improve: Session Upload, Screen Tagging, Video Recording, Compose Occlusion, Frame Pipeline, ServiceHandler and many more.

### V. 3.7.3 - Sept 17, 2025

* Fix: resolve session upload issue in release mode

### V. 3.7.2 - Aug 21, 2025

* Fix: Red screen appearing when pause/resuming recording and when short breaks are created
* Fix: Occlusion skipping on WebView
* Improve: Shipping AI Text Occlusion as a new optional module

### V. 3.7.1 - Jul 29, 2025

* Added: Screen Action / Smart Events support for Jetpack Compose
* Fix: Google Maps ClassNotFoundException

### V. 3.7.0 - Jul 1, 2025

* Added: AI Occlusion feature

### V. 3.6.43 - Jun 11, 2025

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
