---
title: Other 3rd party integrations
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
UXCam offers seamless integrations with products you already use by tagging UXCam’s session and user’s URL, extending UXCam capabilities and enhancing your current workflow. Follow along for things to consider when integrating UXCam with 3rd party services. Furthermore, use this guide to integrate UXCam with any other 3rd party service.

## Start UXCam with verification callback/listener

***

UXCam needs to be verified successfully to get user and session URLs. It is recommended to start UXCam along with a completion handler, callback or verification listener to ensure that session was started.

> 📘
>
> This is an optional step. You can conditionally attach UXCam URL later. For instance, you may only want to attach URLs for users that purchased your product or performed specific action.

```swift iOS
UXCam.start(with: config) {(status) in
    if (status) {
      //Get user or session URL
      //Add user or session URL to your other services
   }
}


```
```java Android
UXCam.startWithConfiguration(config);
    
UXCam.addVerificationListener(new OnVerificationListener() {
    @Override
    public void onVerificationSuccess() {
      //Get user or session URL
      //Add user or session URL to your other services
    }

    @Override
    public void onVerificationFailed(String s) { }
});
```
```typescript Xamarin
//IOS:
UXCam.OptIntoSchematicRecordings();
UXCam.StartWithKey("your app key", null, (status)=>
{
    if (status){
      //Get user or session URL
      //Add user or session URL to your other services
    }
});
    
//Android:
UXCam.startWithConfiguration(config);
UXCam.AddVerificationListener(new IOnVerificationListener());
    
//Add this within your class or namespace
class IOnVerificationListener : Java.Lang.Object, Com.UXCam.IOnVerificationListener
{
    public void OnVerificationFailed(string p0)
    {
    }

    public void OnVerificationSuccess()
    {
        //Get user or session URL
        //Add user or session URL to your other services
    }
}
```
```javascript React Native
import { NativeModules, NativeEventEmitter } from 'react-native';
    
RNUxcam.optIntoSchematicRecordings();
RNUxcam.startWithConfiguration(config);
    
//Setup listener
const emitter = new NativeEventEmitter(NativeModules.RNUxcam);
//Use this.uxcamEvent.remove() to remove listener
this.uxcamEvent = emitter.addListener('UXCam_Verification_Event', async () => {
    //Get user or session URL
    //Add user or session URL to your other services
});
```
```javascript Cordova
UXCam.optIntoSchematicRecordings();
UXCam.startWithKey("your app key", success, failure);
Example:
UXCam.startWithKey("your app key", ()=>{
    //Get user or session URL
    //Add user or session URL to your other services
});
```
```go Flutter
//Supported version - 1.2.1 or higher
FlutterUxcam.startWithConfiguration(config);.then((value) async {
    if (value == true) {
      //Get user or session URL
      //Add user or session URL to your other services
    }
});
```

For conditionally adding UXCam.

```text Code
if (itemPurchased){
    //log UXCam URL to other analytics tools
}
```

## UXCam User URLs

***

This returns a URL path for showing all the current users sessions. This can be used for tying in the current user with other analytics systems.

```swift iOS
urlForCurrentUser() -> String
```
```java Android
String urlForCurrentUser();
```
```typescript Xamarin
string UXCam.UrlForCurrentUser;
```
```javascript React Native
Promise RNUxcam.urlForCurrentUser();
Example:
    const userURL = await RNUxcam.urlForCurrentUser();
```
```javascript Cordova
UXCam.urlForCurrentUser(success, failure);
Example:
    UXCam.urlForCurrentUser((url)=>{
      //Attach UXCam user URL to other services
    });
```
```go Flutter
Future<String> urlForCurrentUser();
Example:
    String userUrl = await FlutterUxcam.urlForCurrentUser();
```

## UXCam Session URLs

***

This returns a URL path that shows the current session when it completes. This can be used for tying in the current session with other analytics systems

```swift iOS
urlForCurrentSession() -> String
```
```java Android
String urlForCurrentSession();
```
```typescript Xamarin
string UXCam.UrlForCurrentSession;
```
```javascript React Native
Promise RNUxcam.urlForCurrentSession();
Example:
    const sessionURL = await RNUxcam.urlForCurrentSession();
```
```javascript Cordova
UXCam.urlForCurrentSession(success, failure);
Example:
    UXCam.urlForCurrentSession((url)=>{
      //Attach UXCam session URL to other services
    });
```
```go Flutter
Future<String> urlForCurrentSession();
Example:
    String sessionUrl = await FlutterUxcam.urlForCurrentSession();
```

## Integrating with UXCam

***

Use UXCam events API to integrate other analytics metadata to UXCam. You can view your events directly within session replay or filter them for specific events.

```swift iOS
UXCam.logEvent("3rd-party-service-name", withProperties: ["key" : "value"])
```
```java Android
final Map<String, String> metaData = new HashMap<String, String>();
metaData.put("key", "value");
UXCam.logEvent("3rd-party-service-name", metaData);
```
```typescript Xamarin
var properties = new NSDictionary("key1", "value1", "key2", "value2");
UXCam.LogEvent("3rd-party-service-name", NSObject.FromObject(properties));
```
```javascript React Native
RNUxcam.logEvent("3rd-party-service-name", {key: "value"})
```
```javascript Cordova
UXCam.logEventWithProperties("3rd-party-service-name", {key: "value"});
```
```go Flutter
FlutterUxcam.logEventWithProperties("3rd-party-service-name", {"key": "value"});
```

## Tips and best practices

***

### Always use completion handler when starting UXCam

UXCam needs to be verified successfully to get user and session URLs. Always start UXCam with a completion handler, callback or verification listener when integrating with 3rd party libraries.

### Check URL before integrating

Offline session recording will not have URLs. While this is optional, it is always best to check for an empty session URL.
