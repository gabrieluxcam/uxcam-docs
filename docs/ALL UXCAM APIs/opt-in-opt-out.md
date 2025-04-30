---
title: Opt-in / Opt-out
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: noindex
next:
  description: ''
---
The SDK by default opts-in users for UXCam recording on app installs. You can now disable and enable recording at runtime by the following API.

### Opt out a User

This method stops and deletes the session in progress (if any) and disables the recording of future sessions from this user.

Use this if:

- The user has chosen not to enable analytics (screen recording)

```swift iOS
UXCam.optOutOverall()
```
```java Android
UXCam.optOutOverall()
```
```javascript React Native
RNUxcam.optOutOverall: () => void
```
```javascript Flutter
void optOutOverall()
```
```javascript Xamarin
void UXCam.OptOutOverall()
```
```javascript Cordova
UXCam.optOutOverall: () => void
```

> 📘 
> 
> The optIn/optOut setting resets to opt-In if the user un-installs and re-installs the app or clears app data.

### Opt in a User

The SDK by default opts-in users for UXCam recording on app installs. If the user has been disabled for UXCam recording by using the optOut method, you can use this method to enable recording at runtime.

```swift iOS
UXCam.optInOverall()
```
```javascript Android
UXCam.optInOverall()
```
```javascript React Native
RNUxcam.optInOverall: () => void
```
```javascript Flutter
void optInOverall()
```
```javascript Xamarin
void UXCam.OptInOverall()
```
```javascript Cordova
UXCam.optInOverall: () => void
```

### Opt in Status

This method returns the status of the user indicating whether they are currently opted in or opted out.

```swift iOS
UXCam.optInOverallStatus()->Bool
```
```java Android
boolean UXCam.optInOverallStatus()
```
```javascript React Native
const status = await RNUxcam.optInOverallStatus(); ==> boolean
```
```javascript Flutter
Future<bool> optInOverallStatus()
```
```csharp Xamarin
bool UXCam.optInOverallStatus()
```
```javascript Cordova
UXCam.optInOverallStatus: () => boolean
```