---
title: Send User Properties
excerpt: 'API reference for identifying users and setting custom properties'
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
Identify users with custom IDs and attach properties for filtering, segmentation, and analytics.

## Use Cases

- Identify users across sessions and devices
- Segment users by role, subscription, or other attributes
- Filter sessions by user properties
- Provide better support by finding specific users

> **Privacy Note**: Avoid sending PII (email, phone) unless you have a DPA with UXCam. Use internal user IDs instead. Contact [team@uxcam.com](mailto:team@uxcam.com) for DPA details.

---

## Set User Identity

Replace UXCam's auto-generated alias with your own user ID.

```swift iOS
UXCam.setUserIdentity("user-123")
```
```java Android
UXCam.setUserIdentity("user-123");
```
```dart Flutter
FlutterUxcam.setUserIdentity("user-123");
```
```javascript React Native
RNUxcam.setUserIdentity("user-123");
```
```csharp Xamarin
UXCam.SetUserIdentity("user-123");
```
```javascript Cordova
UXCam.setUserIdentity("user-123");
```

**Parameter**: `userIdentity` (String) - Your unique identifier for the user.

---

## Set User Property

Attach custom properties to users for filtering and segmentation.

```swift iOS
UXCam.setUserProperty("subscription_type", value: "premium")
UXCam.setUserProperty("company_name", value: "Acme Inc")
```
```java Android
UXCam.setUserProperty("subscription_type", "premium");
UXCam.setUserProperty("company_name", "Acme Inc");
```
```dart Flutter
FlutterUxcam.setUserProperty("subscription_type", "premium");
FlutterUxcam.setUserProperty("company_name", "Acme Inc");
```
```javascript React Native
RNUxcam.setUserProperty("subscription_type", "premium");
RNUxcam.setUserProperty("company_name", "Acme Inc");
```
```csharp Xamarin
UXCam.SetUserProperty("subscription_type", "premium");
UXCam.SetUserProperty("company_name", "Acme Inc");
```
```javascript Cordova
UXCam.setUserProperty("subscription_type", "premium");
UXCam.setUserProperty("company_name", "Acme Inc");
```

---

## API Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `propertyName` | String | Name of the property |
| `value` | String or Number | Value to associate |

---

## Limits

| Limit | Value |
|-------|-------|
| User properties per user | 100 |
| Property value types | String, Number |

---

## Example Properties

Common user properties to consider:

| Property | Example |
|----------|---------|
| `role` | `admin`, `user`, `guest` |
| `subscription_type` | `free`, `premium`, `enterprise` |
| `company_name` | Company identifier |
| `acquisition_source` | `organic`, `paid`, `referral` |
| `nps_score` | Numeric rating |

---

## Platform Implementation Guides

| Platform | Guide |
|----------|-------|
| Android | [Users and Properties](/docs/users-and-properties-android) |
| iOS | [Custom Users and Properties](/docs/custom-users-and-properties-ios) |
| Flutter | [Users and Properties](/docs/users-and-properties-flutter) |
| React Native | [Custom Users and Properties](/docs/custom-users-and-properties-react-native) |
