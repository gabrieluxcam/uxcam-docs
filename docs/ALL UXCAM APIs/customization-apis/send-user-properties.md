---
title: Send User Properties
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
  pages:
    - type: basic
      slug: send-events
      title: Send Events and Properties
---
By default, UXCam generates a random alias name to identify your users based on the Install ID. We also show you other properties about the user such as location, device used, network type, and app version. However, you can send up to 100 additional user properties with our API. 

**Sending additional user properties will help you:**

* Have a better understanding of your users’ actions, create segments, and discover how behavior changes between different audiences.
* Easily identify your users with a unique User ID to pinpoint their issues and provide better support.
* Identify your users across multiple devices.

We recommend not sending **PII data**, such as email address or phone number, and instead, send your own and unique User ID to UXCam. However, if you do need to send PII data, you will need to sign a DPA agreement with us. Please contact [team@uxcam.com](mailto:team@uxcam.com) to discuss it.

### Set user Identity

You can replace the default alias with your own user ID using this method:

```swift iOS
UXCam.setUserIdentity(_ userIdentity: String)
```
```java Android
UXCam.setUserIdentity(String userIdentity);
```
```javascript React Native
RNUxcam.setUserIdentity: (userIdentity: string) => void
```
```javascript Flutter
void setUserIdentity(String userIdentity)
```
```csharp Xamarin
void UXCam.SetUserIdentity(string userIdentity)
```
```javascript Cordova
UXCam.setUserIdentity: (userIdentity: string) => void
```

> 📘 The API parameters are:\
> **userIdentity**: The new name for the user.

### Send Additional User Information

You can send additional user properties to gather more insights or to easily filter or group sessions, users, or events by specific user properties.

> 📘 E.g.  Role, Company name, Acquisition source, Subscription type, Loyalty membership, NPS score or rating, etc.

```swift iOS
setUserProperty(_ propertyName: String, value: Any)
    
//Example
UXCam.setUserProperty("role", value: "your-role")
UXCam.setUserProperty("subscription_type", value: "premium")
UXCam.setUserProperty("company_name", value: "your-company")
```
```java Android
setUserProperty(String propertyName, String value)
    
//Example
UXCam.setUserProperty("role","your-role")
UXCam.setUserProperty("subscription_type","premium")
UXCam.setUserProperty("company_name","your-company")
```
```javascript React Native
RNUxcam.setUserProperty: (propertyName: string, value: string | number) => void
    
//Example
RNUxcam.setUserProperty("role","your-role")
RNUxcam.setUserProperty("subscription_type","premium")
RNUxcam.setUserProperty("company_name","your-company")
```
```javascript Flutter
void setUserProperty(String key, String value)
    
//Example
FlutterUxcam.setUserProperty("role","your-role")
FlutterUxcam.setUserProperty("subscription_type","premium")
FlutterUxcam.setUserProperty("company_name","your-company")
```
```csharp Xamarin
void UXCam.SetUserProperty(string propertyName, string value)
    
//Example
UXCam.setUserProperty("role","your-role")
UXCam.setUserProperty("subscription_type","premium")
UXCam.setUserProperty("company_name","your-company")
```
```swift Cordova
setUserProperty(_ propertyName: String, value: Any)
    
//Example
UXCam.setUserProperty("role","your-role")
UXCam.setUserProperty("subscription_type","premium")
UXCam.setUserProperty("company_name","your-company")
```

> 📘 The API parameters are:\
> **propertyName**: The name of the property to attach to the user.\
> **value**: A value to associate with the property. String or Number are accepted for value.
