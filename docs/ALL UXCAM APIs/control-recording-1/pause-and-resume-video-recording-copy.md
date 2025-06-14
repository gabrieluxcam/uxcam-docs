---
title: Pause and Resume Video Recording (COPY)
deprecated: false
hidden: false
metadata:
  robots: index
---
> 📘 Important Note:
>
> * **Pausing Session Recording:** This action stops UXCam from capturing session data temporarily. Use this when you want to preserve your free session limits or need to disable all session recording for compliance reasons. You can do this from the dashboard itself.
> * **Pausing Video Recording:** This only stops UXCam from recording videos during sessions, but session data will still be captured unless session recording is also paused.
>
> In case of any questions, feel free to reach out to our amazing support team at [team@uxcam.com](mailto:team@uxcam.com)

### Pause Video Recording

Use this API in case you want to stop the screen recording of a session indefinitely. This can be helpful for:

* Avoid recording some parts of the app that are not relevant.
* Omit some screens and gestures that you want to avoid in your recordings.

```swift iOS
UXCam.pauseScreenRecording()
```
```java Android
UXCam.pauseScreenRecording();
```
```javascript React Native
RNUxcam.pauseScreenRecording: () => void
```
```javascript Flutter
void pauseScreenRecording()
```
```csharp Xamarin
void UXCam.PauseScreenRecording()
```
```javascript Cordova
UXCam.pauseScreenRecording: () => void
```

### Resume Video Recording

Use this API to resume the recording of the screen and gestures where the recording has been previously paused.

```swift iOS
UXCam.resumeScreenRecording()
```
```java Android
UXCam.resumeScreenRecording();
```
```javascript React Native
RNUxcam.resumeScreenRecording: () => void
```
```javascript Flutter
void resumeScreenRecording()
```
```csharp Xamarin
void UXCam.ResumeScreenRecording()
```
```javascript Cordova
UXCam.resumeScreenRecording: () => void
```