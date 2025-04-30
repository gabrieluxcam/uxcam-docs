---
title: Control Crash Handling
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
Crash handling is enabled by default, to let you capture all the sessions with crashes and see the corresponding crash logs. We provide this option to disable crash handling within UXCam as having two crash reporters at the same time is not recommended since they are very sensitive. 

By default, UXCam automatically detects crashes and ANRs to let you capture all the sessions with crashes and see the corresponding crash logs. You can also enable/disable crash handling, report non-fatal (handled) exceptions and events.

### Control Crash Handling Events

**If you want to disable UXCam from capturing sessions with crashes**, you will need to call this API** before startWithKey.** 

```swift iOS
UXCam.disableCrashHandling(_ disabled: Bool)
```
```java Android
UXCam.disableCrashHandling(bool disabled)
```
```csharp Xamarin
void UXCam.DisableCrashHandling(bool disable)
```
```javascript React Native
RNUxcam.enableCrashHandling(bool)
```

> 📘 
> 
> The API parameters are:  
> **disable**: Default **false**. Set **true** to disable crash recording.