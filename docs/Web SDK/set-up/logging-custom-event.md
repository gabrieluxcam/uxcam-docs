---
title: Logging Custom Event
deprecated: false
hidden: false
metadata:
  robots: index
---
To track user interactions and key behaviours within your application, the SDK allows you to log custom events. By capturing event names and optional properties, you can generate actionable insights tied to specific user actions.

<br />

### Logging an Event

* eventName (string): The name of the event.
* properties (object, optional): A JSON object with event properties.
* Example: **uxc.event(eventName, properties)**

<br />

```javascript
uxc.event("your_amazing_event", { key: "value" });

//Example
<button id="mybtn">Click me</button>

<script>
const button = document.querySelector('#mybtn');
button.addEventListener('click', () => uxc.event("your_amazing_event", { key: "value" }));
</script>
```