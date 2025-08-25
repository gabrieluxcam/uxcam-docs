---
title: Custom Events and Properties
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    The document explains how to track user interactions in an application using
    events and properties, providing methods for logging these events in code,
    and emphasizes the importance of consistent naming and relevant properties
    for effective analytics.
  robots: index
next:
  description: ''
---
> 📘 Note:
>
> UXCam automatically detects[ UI Freezes ](https://help.uxcam.com/hc/en-us/articles/360045884471)and[ Rage taps](https://help.uxcam.com/hc/en-us/articles/360036136992) and logs them as an event; for example, Rage taps are registered as "Rage Tap" events.

Events are powerful tools for tracking user interactions within your application. By sending events, you can gain deeper insights into how users are interacting with your product and make data-driven decisions to improve the user experience.

<GitHubCallout type="important">Smart Events for Flutter SDK require an additional configuration from your code, see below for details.</GitHubCallout>

<JumpCallout to="#next">Jump to Regular Coded Events</JumpCallout>

## Smart Event for Flutter SDK:

In order to enable Smart Events in your UXCam Dashboard, it's required to add the following to your code: 

Wrap the root widget of your app with **UxCamGestureHandler**. Preferably, inside runApp()

```go Flutter
  runApp(
    UXCamGestureHandler(
      child: const YourApp(),
    ),
  );
```

Now every gesture should be tracked in the app in order to utilize smart events in the UXCam Dashboard.

### Working Principle

The SDK tracks widgets that users interact with and captures their class, type, ID, and value (when applicable).

The Element ID is generated from the widget’s class hierarchy within the widget tree.

### Developer Note

Because Flutter is highly flexible, there are scenarios where the SDK’s detection may not perfectly align with what you see in a session replay.

For example, if a developer wraps an entire Scaffold widget inside an InkWell, the SDK may classify the interaction as tapping an InkWell (or another button-like widget), even though the replay visually shows a Text or Image being clicked.

Since IDs, classes, and types are determined by the widget’s class hierarchy, this can occasionally lead to mismatches.
👉 As a best practice, double-check that any widget receiving gestures does not contain a button-like widget in its hierarchy unless that’s intended.

### Limitations

The SDK calculates interactivity based on widget bounds, making it heavily dependent on Box widgets.

As a result, the SDK cannot detect Sliver widgets.

## How to Send Events

To send an event, use the following method in your code:

```coffeescript Flutter
logEvent(String eventName)

// Example
FlutterUxcam.logEvent("purchased");
```

Replace `"EventName"` with a meaningful name that describes the action being tracked, such as `"ButtonClicked" `or` "UserLoggedIn"`. Naming your events consistently will make your analytics easier to understand.

## Sending Events with Properties

Events can also have properties associated with them to provide additional context. For example, if you have an event called "`Purchase`", you might include properties like the product ID, product category, or price.

To send an event with properties, use the following method:

```coffeescript Flutter
logEventWithProperties(String eventName, Map<String, dynamic> properties)

FlutterUxcam.logEvent("purchased"); // Event without properties
FlutterUxcam.logEventWithProperties("purchased", {
    "size": 7,
    "brand": "Nike"
});
```

This approach gives you a richer dataset and helps in better segmenting and understanding your users' behaviour.

### Important Considerations

**Properties Limit**: Each event can include up to 20 properties. If you exceed this limit, any extra properties will not be shown, and you'll receive a message indicating that there's an excess of properties.

**Case Sensitivity**: Event names and property keys are case sensitive. This means "purchase" and "Purchase" will be treated as two different events, so be consistent with naming to avoid confusion.

### Best Practices

**Be Consistent**: Use consistent naming conventions for event names and properties. This makes it easier to search and analyze your events in UXCam.

**Keep Properties Relevant:** Only add properties that provide meaningful context. Overloading events with unnecessary properties can make your analytics harder to interpret.

**Test Your Implementation**: Before rolling out your analytics to production, thoroughly test your events and properties to ensure they are correctly logged and appear as expected.

By leveraging events and properties effectively, you can unlock valuable insights into how users interact with your app, leading to better product decisions and enhanced user experiences.
