---
title: Send Events and Properties
excerpt: 'API reference for logging custom events in session recordings'
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
Track user interactions by logging custom events. Events appear in session timelines and can be used for funnels, filtering, and analytics.

## Use Cases

- Create granular funnels combining Events and Screens
- Track registration flow drop-offs
- Measure feature adoption
- Identify errors in conversion processes

> **Auto-detected Events**: UXCam automatically logs [UI Freezes](https://help.uxcam.com/en/articles/10222575-ui-freeze-s-events) and [Rage Taps](https://help.uxcam.com/en/articles/10222528-session-analytics).

---

## Log Event

Log an event at the current timestamp.

```swift iOS
UXCam.logEvent("purchased")
```
```java Android
UXCam.logEvent("purchased");
```
```dart Flutter
FlutterUxcam.logEvent("purchased");
```
```javascript React Native
RNUxcam.logEvent("purchased");
```
```csharp Xamarin
UXCam.LogEvent("purchased");
```
```javascript Cordova
UXCam.logEvent("purchased");
```

---

## Log Event with Properties

Add context to events with key-value properties.

```swift iOS
UXCam.logEvent("purchased", withProperties: [
    "size": 7,
    "brand": "Nike"
])
```
```java Android
Map<String, Object> properties = new HashMap<>();
properties.put("size", 7);
properties.put("brand", "Nike");
UXCam.logEvent("purchased", properties);
```
```dart Flutter
FlutterUxcam.logEventWithProperties("purchased", {
    "size": 7,
    "brand": "Nike"
});
```
```javascript React Native
RNUxcam.logEvent("purchased", {
    "size": 7,
    "brand": "Nike"
});
```
```csharp Xamarin
// Android
UXCam.LogEvent("purchased", jsonObject);
// iOS
UXCam.LogEvent("purchased", nsObject);
```
```javascript Cordova
UXCam.logEventWithProperties("purchased", {
    "size": 7,
    "brand": "Nike"
});
```

---

## API Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `eventName` | String | Yes | Name to identify the event |
| `properties` | Dictionary | No | Key-value pairs (max 20 per event) |

**Property value types**: String and Number only.

---

## Limits and Considerations

| Limit | Value | Behavior |
|-------|-------|----------|
| Properties per event | 20 | Exceeding logs `_UXCam_Overload` instead |
| Property value types | String, Number | Other types may not display correctly |

---

## Date Properties

When sending dates as event properties, use these formats:

- **Recommended**: UTC format `YYYY-MM-DDTHH:MM:SSZ`
- **Alternatives**: `YYYY-MM-DD HH:MM:SS`, `YYYY/MM/DD`, `YYYY-MM-DD`
- **Unix timestamp**: Seconds since 1970 (integer comparison works)

---

## Platform Implementation Guides

| Platform | Guide |
|----------|-------|
| Android | [Events](/docs/events) |
| iOS | [Events](/docs/events-ios) |
| Flutter | [Events](/docs/events-flutter) |
| React Native | [Events](/docs/events-react-native) |
