---
title: FAQs
deprecated: false
hidden: false
metadata:
  robots: index
---
### 1. What defines the beginning and end of a session?

A session begins as soon as the page loads and the UXCam Web SDK starts recording. There’s no defined inactivity timeout, but if no data is received from the client for 3 minutes, the session is automatically ended and the recording is uploaded.

<br />

**“No data” can mean:**

* No internet connection
* The device is suspended (e.g., sleep mode)
* The browser or tab is closed

<br />

### 2. Can events be sent directly from the script?

Yes. While UXCam captures click gestures by default, you can send custom events manually using JavaScript event listeners. In this example, a custom event named my\_btn\_clicked is sent whenever the button is clicked.

```html
<button id="mybtn">Click me</button>

<script>
const button = document.querySelector('#mybtn');
button.addEventListener('click', () => uxc.event('my_btn_clicked'));
</script>
```

<br />

### 3. Why is my email not occluded automatically?

For automatic occlusion to work, the input field must have its type set to "email". If it’s set to "text" or another type, UXCam will not hide it by default.

For email or any other input type that should be occluded automatically, please verify that the type is set to any of the options mentioned in the occlusion section → [Occlusion - Hide Sensitive Data](https://developer.uxcam.com/v2.0-draft/docs/occlusion#/)

<br />

### 4. Why does the replay look broken, and CSS Styles or images are not loading?

UXCam relies on the CSS files that were available at the time when the session was recorded. If these assets become unavailable or restricted, the replay may not display styles correctly.

Common reasons why styles may not load:

* The resource no longer exists: It was part of a previous deployment and is no longer hosted on your server.
* The resource is inaccessible: It may require authentication or be restricted to an internal network.
* The resource is blocked by the browser due to CORS: Browsers enforce cross-origin security checks, preventing UXCam from loading your assets if CORS is not properly configured.

Learn how to fix CORS issues here → [Troubleshooting](\[https://developer.uxcam.com/v2.0-draft/update/docs/troubleshooting#/]\(https://developer.uxcam.com/v2.0-draft/update/docs/troubleshooting#/\))

<br />

## Support

For questions or support, reach out to us at [team@uxcam.com](mailto:team@uxcam.com).