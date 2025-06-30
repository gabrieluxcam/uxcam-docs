---
title: iOS
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    The UXCam iOS Changelog documents various updates from March 2022 to October
    2024, including numerous bug fixes, performance improvements, and new
    features such as support for app logs in release builds, privacy manifest
    files, and enhanced session recording capabilities.
  robots: index
next:
  description: ''
---
# UXCam iOS Changelog

<br />

### V. 3.6.26 - June 17, 2025

* Fixed crash due to unhandled exception while parsing ANR stacktrace

### V. 3.6.25 - June 4, 2025

* While calling allowshortbreak api, 0 sec screen is being shown in the timeline
* Crash fixes
* App Crashes on page with Youtube Link
* Improved ANR stacktrace

### V. 3.6.24 - April 10, 2025

* **Fix** Fixed crash issue due to invalid json in data file
* **Fix** Fixed crash issue if video recording is cancelled by system
* **Fix** Fixed large values for app start metrics due to sdk starting in background state
* **Fix** Fixed 0 secs screens for UIKit controllers hosting SwiftUI views
* **Improvement** Handled long and failed session if sdk is started in background
* **Improvement** Handled errors for cancelled sessions

### V. 3.6.23 - March 20, 2025

* **Fix** Occlusion not working for few offline session during startup
* **Fix** Prevent capturing occluded screens element information
* **Fix** Crash on iOS 3.6.20
* **Fix** Crashed sessions is not being uploaded
* **Improvement** Use API for cancelling session instead of sending it as data file

### V. 3.6.22 - February 20, 2025

* **Fix**: Modal makes occluded text fields disappear
* **Fix**: UXCam Memory Issue
* **Fix**: Video Playback Issue
* **Fix**: Crash on React Native
* **Fix**: Schematic recordings in UXCam not displaying text after updating to React Native 0.76
* **Fix**: Specific fixes to ANR issue for a few customers

### V. 3.6.21 - January 31, 2025

* **New**: Implement storing session information in file instead of preferences
* **Fix**: App Launch Metric fixes for large and negative values
* **Improvement**: flutter occlusion

### V. 3.6.20 - December 12, 2024

* **New**: Add Integration log api\
  -configuration.enableIntegrationLogging=true (By default, it will be disabled)
* **Fix**: crash on iOS due to failed video recording
* **Improvement**: Show actual reason for technical error for failed video recording
* **Fix**: negative timeline of screens

### V. 3.6.19 - November 22, 2024

* **Fix**: Fixed build issue due to error in upload crash dsym script

### V. 3.6.18 - November 20, 2024

* **New**: App load time.
* **Fix**: Ignore mobile data limit and allow uploading data files on mobile data
* **New**: Crash symbolication.
* **Fix**: Occlusion issue while entering specific screens in flutter.
* **Fix**: App crash while merging video in app background

### V. 3.6.17 - October 4, 2024

* **Fix**: Occlusion issue while navigating to different screens on SwiftUI.
* **Fix**: Crash issue due to empty session key while uploading offline sessions.
* **Fix**: Background thread exception when verifying after coming to foreground.
* **Fix**: Fixed sessions cancelling incorrectly due to background verify.
* **Improvement**: Improved console logs not capturing ASCII characters.

***

### V. 3.6.16 - September 3, 2024

* **Improvement**: Improved uploading sessions logic to reduce missing sessions.
* **New**: Added support for app logs in release build.
* **Fix**: Fixed app language changing on first run after installing UXCam.
* **Fix**: Fixed random crash issue at the beginning due to null value in logs captured.
* **Fix**: Fixed UI freeze issue due to incorrect bit rate while capturing session videos.

***

### V. 3.6.15 - July 23, 2024

* **Fix**: Fixed sessions not being uploaded on mobile data.
* **Fix**: Fixed irregular crash due to semaphore when app goes to background.
* **Fix**: Fixed screen tag mismatch for child controllers.

***

### V. 3.6.14 - June 20, 2024

* **New**: Added support for webview occlusion in native and cross-platform plugins.
* **Fix**: Fixed occlusion misplacement on SwiftUI views.
* **Fix**: Fixed occlusion issue while screen transition in Flutter.

***

### V. 3.6.13 - May 17, 2024

* **Fix**: Fixed occlusion not working for schematic recording.
* **Fix**: Fixed unknown name showing on dashboard when app comes to foreground for manual tagging.
* **Fix**: Fixed app crash due to deadlock condition while cancelling session.

***

### V. 3.6.12 - April 23, 2024

* **Fix**: Fixed negative session issue in timeline.
* **Fix**: Fixed occlusion not being removed issue when screen is changed in Flutter.
* **Fix**: Fixed crash issue when multi-session record is disabled.
* **Fix**: Fixed crash issue when tabbar consists of more navigation controller.

***

### V. 3.6.11 - March 14, 2024

* **Fix**: Fixed 0 sec length issue in dashboard for few sessions.
* **Fix**: Fixed app upload failing issue due to SDK in Xcode 15.3.
* **New**: Added privacy manifest file declaring the information we capture as required by Apple.
* **Fix**: Fixed crash issue when session starts before screen is loaded for some customers.
* **Fix**: Fixed crash issue occurring on schematic recording due to custom fonts used in NSAttributedString.
* **Fix**: Removed capture of mobile carrier information from iOS 16 as Apple has restricted the gathering of mobile carrier information.

***

### V. 3.6.10 - February 28, 2024

* **New**: Automatically occlude all textfields based on `UITextContentType` property.
* **New**: Allow screens to be recorded for third-party frameworks.
* **Fix**: Fixed zero seconds sessions showing in dashboard for sessions that have longer video duration in video replay.
* **Improvement**: Improved occlusions related logic.

***

### V. 3.6.9 - January 29, 2024

* **Fix**: Fixed crash issue due to uploading large sessions in background.
* **Improvement**: Modified verification request in SDK to include pending sessions count.
* **New**: Expose method in SDK to handle caught exceptions from cross-platform plugins.
* **New**: Expose new Flutter module to handle Flutter-specific methods.

***

### V. 3.6.8 - December 8, 2023

* **Fix**: Fixed crash issue due to ineffective screen rendering.

***

### V. 3.6.7 - November 10, 2023

* **Fix**: Fixed issue related to app language changed after adding UXCam.
* **Fix**: Fixed crash issue due to uploading of large video session.
* **Fix**: Fixed issue related to occluded elements information shown in dashboard.
* **Fix**: Fixed an issue in Flutter where last frame was visible after applying occlusion.
* **Fix**: Fixed text detection not working in table view.
* **Fix**: Fixed text detection not working for compound button.
* **Fix**: Fixed occlusion not being removed after navigating to next screen.
* **Fix**: Fixed elements for bottom navigation bar showing different name for each screen.
* **New**: Enabled multi-region verify functionality in SDK.

***

### V. 3.6.6 - September 25, 2023

* **Fix**: Fixed app crash issue due to uploading large videos in Flutter.
* **Fix**: Fixed issue related to SCNView not being captured in iOS.
* **Fix**: Fixed different crash issues occurring on iOS.
* **Fix**: Fixed text detection not working for double tap and rage tap in Flutter.
* **New**: Added new configuration key `enableImprovedScreenCapture` to give option to switch between old and new rendering methods.

***

### V. 3.6.5 - August 23, 2023

* **Fix**: Fixed Flutter occlusion flickering issue.
* **Fix**: Handled performance issue for custom camera views.
* **Fix**: Prevent capturing animated views using old rendering method.
* **Fix**: Fixed issue related to non-interactable buttons when added directly to table and collection view cells.
* **Fix**: Fixed issue related to showing gestures in top left corner for Flutter apps.
* **New**: Updated code to handle zoomed Flutter views.
* **Fix**: Fixed crash issue related to rendering empty views.
* **Fix**: Fixed blur removing issue on React Native before screen changes.

***

### V. 3.6.4 - July 6, 2023

* **Fix**: Fixed performance issue in Cordova and other webview-related frameworks.
* **Fix**: Fixed random flickering occlusion screen showing in Cordova framework.

***

### V. 3.6.3 - June 13, 2023

* **Fix**: Fixed gesture override issue in React Native gesture SDK.
* **Fix**: Fixed time discrepancy between device time and recorded time.
* **Fix**: Fixed random crash issue due to enabling network log.
* **Fix**: Fixed crash issue due to nullable value being sent on a non-null parameter while uploading session.
* **Fix**: Fixed Cordova crash due to nullable sync group.
* **Fix**: Fixed random crash issue while stopping sessions.
* **Fix**: Fixed event time greater than session length issue for some sessions.
* **Fix**: Fixed app termination issue due to not properly ending the background task.

***

### V. 3.6.2 - May 5, 2023

* **Fix**: Fixed occlusion not correctly happening while scrolling.
* **Improvement**: Prevent screen recording of manually tagged screens if disabled from the dashboard.
* **Fix**: Fixed crash if segment control segments are less than the total number of segments.
* **Improvement**: Prevent tagging of blank screen name in manual tagging.

***

### V. 3.6.1 - April 19, 2023

* **Improvement**: Improved handling session upload errors causing missing videos.
* **New**: Capture different types of UI elements that were missing previously.
* **New**: Add a unique identifier for different UI elements.
* **New**: Accept null values as event properties.
* **Fix**: Fixed an issue where typing quickly in `UITextView` makes the app unresponsive.
* **Improvement**: Improved performance for apps using `CAMetalLayer` (used in game engines, Flutter).
* **Fix**: Fixed overlapping screens in the session when a dialog box is shown.
* **Improvement**: Prevent occlusion of screens if there are no tagged screens in manual tagging.
* **New**: Show rage tap on the session only if there are more than 2 consecutive taps in the dashboard.
* **Improvement**: Reset rage tap if the user navigates to a new screen.
* **Improvement**: Improved showing correct screens for cross-platform plugins.

***

### V. 3.6.0 - March 24, 2023

* **Fix**: Fixed an issue where a black screen was shown in session recording when a dialog box was clicked.
* **Fix**: Fixed session length mismatch in the dashboard while calling the pause screen recording API.
* **Fix**: Fixed an issue where device size was not correctly calculated for some applications.
* **Fix**: Fixed an issue where blurring was not applied in SwiftUI if no screens are tagged.
* **Fix**: Fixed an issue where `excludeMentionedScreens` was not working while occluding some of the screens.
* **Improvement**: Now occlusion applied from SDK can’t be removed from the dashboard.
* **Fix**: Fixed an issue related to gesture being not recorded for occluded screens on cross-platforms (Flutter).
* **Fix**: Fixed a crash issue due to the SDK while using custom `UIContentConfiguration` objects in the app.
* **Improvement**: Improved automatic screen name capturing for `UIPageViewController` (Now child screens are shown instead of `UIPageViewController`).

***

### V. 3.5.3 - February 9, 2023

* **Fix**: Fix upside-down video orientation in the dashboard.
* **Fix**: Fixed random crash issue related to metal layer.
* **Improvement**: Improved unresponsive gesture implementation in the SDK.
* **Improvement**: Improved implementation of UI elements capture in the SDK.
* **Improvement**: Show parent controller name instead of the child one to avoid 0 secs screens.
* **New**: Added UXCam prefix to avoid the plcrash reporter namespace clash with apps.
* **Fix**: Fixed an issue related to app logs unable to parse some of the UTF-8 characters.

***

### V. 3.5.2 - January 16, 2023

* **New**: Added a configuration option for environment types (alpha, beta, release).
* **Fix**: Fixed occlusion issue for manually tagged screens in automatic tagging.
* **Improvement**: Updated crash reporting logic to show correct crash information in the dashboard.

***

### V. 3.5.1 - December 26, 2022

* **Improvement**: Improved screen tagging for automatically tagged screens - Show "ScreenName" instead of "AppName.ScreenName".
* **New**: Allow manual tagging for screens in automatically tagged screens.
* **Fix**: Fixed a crash occurring in Apple/Google maps.

***

### V. 3.5.0 - November 14, 2022

* **Improvement**: Improved app performance on native session recording.
* **Fix**: Fixed hitching issue while scrolling in native recording.

***

### V. 3.4.5 - October 5, 2022

* **Fix**: Fixed app freezing and random crash issue due to enabling app logs.

***

### V. 3.4.4 - September 2, 2022

* **Fix**: Revert multiple windows video record logic to prevent the app from freezing on iPad.
* **Fix**: Fixed issue regarding gestures not captured in paused state on the same screen.
* **Improvement**: Refactor native screen logic to decrease CPU usage.

***

### V. 3.4.3 - August 17, 2022

* **Fix**: Fixed random crash issue while saving heatmap.

***

### V. 3.4.2 - July 15, 2022

* **Improvement**: Improvements in CPU usage while the app is in ANR.
* **Improvement**: Performance improvements in the app when going to the background.
* **Fix**: Fixed an issue where blurring without providing radius would not blur in the app.
* **Fix**: Fixed an issue where a webview was occluded as a sensitive view on `occludeAllTextFields`.
* **Fix**: Fixed an issue where certain crashes were not uploading to the dashboard.

***

### V. 3.4.1 - April 13, 2022

* **New**: Blur/Occlusion support.
* **Fix**: 4XX error fallback to offline session recordings.
* **New**: App log in JSON format.

***

### V. 3.4.0 - March 16, 2022

* **Fix**: Allow short break improvements and issue fixes.
* **New**: PLCrashReporter.
* **Improvement**: ANR uses PLCrashReporter better stack frames.
* **New**: Network logs.
* **New**: SDK settings via configuration object rather than multiple API calls.