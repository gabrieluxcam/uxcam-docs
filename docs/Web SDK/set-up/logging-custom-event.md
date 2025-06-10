---
title: Logging Custom Event
deprecated: false
hidden: false
metadata:
  robots: index
---
The SDK provides methods to log events and set user properties.

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