---
title: App Clips & App Extensions
excerpt: >-
  Record sessions from an App Clip or an app extension and hand them to your
  main app
deprecated: false
hidden: false
metadata:
  title: App Clips & App Extensions - UXCam iOS
  description: >-
    Set up UXCam inside an iOS App Clip or app extension, share storage with the
    main app through an App Group, and understand what is and is not captured.
  robots: index
---
> 📘 Requires UXCam iOS SDK **3.10.0 or later**. Check the [iOS changelog](https://developer.uxcam.com/docs/ios-sdk-change-log) for your version.

Since 3.10.0 the iOS SDK records sessions from two hosts besides your main app:

* **App Clips** — the lightweight version of your app launched from a link, QR code, NFC tag or Maps.
* **App extensions** — Share, Action, Notification Content, custom keyboard and similar targets that draw their own UI.

The SDK detects the host automatically from the target's `Info.plist`. You keep the same `startWithConfiguration` call; the differences are one or two configuration properties, a video opt-in, and an App Group so the main app can pick up sessions the short-lived host could not upload.

***

## What is captured

| | Main app | App Clip | App extension |
| --- | --- | --- | --- |
| Video recording | ✅ | ✅ | ✅ (the scene of the window you pass in `hostWindow`) |
| Gestures, screens, events, user properties | ✅ | ✅ | ✅ |
| Schematic (wireframe) recording | ✅ | ❌ | ❌ |
| Uploads from the host itself | Background session | In-process, while the clip is running | In-process, while the extension is running |
| Sessions the host could not upload | Retried in background | Kept in the clip's private storage for its next launch, or shared with the main app via an App Group | Handed to the main app via an App Group once upload processing settles; otherwise kept in the extension's private storage for its next run |

> 🚧 **Not supported:** WidgetKit widgets, Live Activities, watchOS and visionOS targets. Widgets are rendered by the system and have no window the SDK can capture. To attribute a widget or notification tap, log an event when the launch reaches your main app.

***

## Video recording must be enabled

Video recording is **off by default on a fresh installation**, in every host. The snippets below do not enable it on their own. Before `start`, either:

* call `UXCam.optIntoVideoRecordings()` in the clip or extension, or
* rely on a decision the main app already saved and shared through an App Group (see below).

```swift iOS
UXCam.optIntoVideoRecordings()
```

See [Opt-in / opt-out](https://developer.uxcam.com/docs/opt-in-opt-out-copy-ios) for the full API.

***

## App Clips

### 1. Add the SDK to the App Clip target

Add the UXCam package or pod to the App Clip target the same way you did for the main app. See [iOS integration](https://developer.uxcam.com/docs/ios).

```ruby
target 'YourAppClip' do
  pod 'UXCam' # Add within the App Clip target
end
```

### 2. Start UXCam in the clip

Use the **same app key** as the main app. A different key is not supported; sessions from the clip appear under the same app in the dashboard. Nothing else changes; the SDK sees `NSAppClip` in the clip's `Info.plist` and switches to clip mode.

```swift iOS
import UXCam

UXCam.optIntoVideoRecordings()
let config = UXCamConfiguration(appKey: "YOUR_APP_KEY")
config.appGroupIdentifier = "group.com.yourcompany.yourapp" // optional, see step 3
UXCam.start(with: config)
```

### 3. Share storage with the main app (recommended)

An App Clip runs briefly and may be removed by the system after a while. Two things depend on whether the clip shares storage with the main app:

* **Offline video allowance.** The number of sessions that may record video while the SDK cannot reach UXCam starts at **zero** and is replenished from your server settings after each successful online start. In a clip with private storage that allowance is **capped at 3**. With an App Group the clip uses the main app's storage and the clip-specific cap goes away; your plan's server-side limits still apply.
* **Pending sessions and consent.** With an App Group, sessions the clip could not finish uploading are uploaded by the main app once it is installed, and the opt-in / opt-out decision is shared between the clip and the main app.

To set it up:

1. In Xcode, add the **App Groups** capability to both the main app target and the App Clip target, using the same identifier (for example `group.com.yourcompany.yourapp`).
2. Set `appGroupIdentifier` to that identifier in **both** targets before calling `start`.

```swift iOS
let config = UXCamConfiguration(appKey: "YOUR_APP_KEY")
config.appGroupIdentifier = "group.com.yourcompany.yourapp"
UXCam.start(with: config)
```

If the group is missing from the entitlement, the SDK falls back to private storage and recording still works. From 3.10.2 the console also shows `App group … is unavailable; using this process's private session storage`.

***

## App extensions

### 1. Add the SDK to the extension target

Add the package or pod to the extension target and use the **same app key** as the main app. Extensions ship with a stricter memory budget than apps, so test on a real device.

### 2. Pass the extension's window

Extensions do not own a `UIApplication`, so the SDK cannot find the window on its own. Set `hostWindow` to the window your extension draws in **before** calling `start`. If it is missing, startup is rejected and the console shows:

```
UXCam: app extensions must set configuration.hostWindow before startWithConfiguration.
```

```swift iOS
import UXCam

class ShareViewController: UIViewController {
    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)
        guard let window = view.window else { return }

        UXCam.optIntoVideoRecordings() // or rely on shared consent, see step 3
        let config = UXCamConfiguration(appKey: "YOUR_APP_KEY")
        config.hostWindow = window
        config.appGroupIdentifier = "group.com.yourcompany.yourapp"
        UXCam.start(with: config)
    }
}
```

`hostWindow` is held weakly; keep the window alive for as long as the extension runs. The session ends when the host app backgrounds the extension.

### 3. Hand sessions to the main app

Extensions are short-lived and are often terminated before an upload finishes. Configure the same App Group on the extension and the main app (see the App Clip steps above) and set `appGroupIdentifier` in both. Then:

* The extension records into its own private storage and uploads what it can while it runs.
* After a session stops and pending upload processing settles, finished sessions that are still pending are moved to a hand-off folder inside the group container. If the extension is killed before that point, those sessions stay in the extension's private storage and are uploaded the next time the extension runs; the main app cannot recover them.
* The next time the main app starts UXCam, it adopts the handed-off sessions and uploads them like its own.
* When the extension's runtime starts, it **copies** the opt-in / opt-out decision the main app saved. It is a one-time copy per launch, not a live sync. Calling the opt-in / opt-out APIs inside the extension changes only the extension's private decision and never writes back to the shared one.

Without an App Group, pending sessions are not dropped: they stay in the extension's private storage and are uploaded the next time the extension runs. The extension keeps the 3-session offline video cap even with an App Group, because its recording storage stays private.

***

## Using `hostWindow` in a regular app

`hostWindow` is not only for extensions. In a multi-scene app you can set it to select which **scene** the SDK captures: the scene that owns the window you pass, which may include other windows in that scene. Leave it unset to keep the default behaviour.

***

## Troubleshooting

| Symptom | Cause | Fix |
| --- | --- | --- |
| Sessions have no video | Video recording is off by default on a fresh install | Call `UXCam.optIntoVideoRecordings()` before `start`, or share the main app's decision through an App Group |
| Extension sessions never appear | `hostWindow` not set → startup rejected | Set `hostWindow` before `start`; check the console for the rejection message |
| Extension sessions appear late or only sometimes | Extension killed before upload or hand-off completed | Add the App Group so settled sessions hand off to the main app; sessions killed mid-processing upload on the extension's next run |
| Clip sessions recorded offline have no video after the first 3 | Clip storage is private, so the offline video allowance is capped at 3 | Add the App Group so the clip shares the main app's storage and allowance |
| `App group … is unavailable` in the log (3.10.2+) | Identifier not in the target's App Groups entitlement, or typo | Match the identifier in Xcode → Signing & Capabilities on every target that sets it |
| No wireframe / schematic replay for clip or extension sessions | By design for short-lived hosts | Video recording is still captured |