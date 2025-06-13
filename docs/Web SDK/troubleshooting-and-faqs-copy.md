---
title: FAQs
deprecated: false
hidden: false
metadata:
  robots: index
---
### 1. What defines the beginning and end of a session?

The session starts as soon as the page loads and the WebSDK begins recording. There is no inactivity period per se; however, if there is no **incoming data**\* from the client for 3 minutes, the session is considered finished, and the recording is uploaded.

\*Incoming data = No internet connection, the OS being suspended or the browser/tab being closed.

<br />

### 2. Can events be sent directly from the script?

While clicks are captured as gestures by default, you can send custom events directly from the script by adding event listeners. For example:

```html
<button id="mybtn">Click me</button>

<script>
const button = document.querySelector('#mybtn');
button.addEventListener('click', () => uxc.event('my_btn_clicked'));
</script>
```

In this example, when the button with the ID mybtn is clicked, a custom event named my\_btn\_clicked is sent to UXCam.

<br />

### 3. Why is my email not occluded automatically?

The email input will only be occluded if the input type is explicitly set to email. If the input field has its type set to text instead of email, it will not be occluded by default.

For email or any other input type that should be occluded automatically, please verify that the type is set to any of the options mentioned in the occlusion section. [Auto-occlusion](https://developer.uxcam.com/docs/installation#default-occlusion)

<br />

### 4. Why does the replay look broken, and CSS Styles or images are not loading?

UXCam relies on the CSS files that were available at the time when the session was recorded. If these assets become unavailable or restricted, the replay may not display styles correctly.

Common reasons why styles may not load:

* The resource no longer exists: It was part of a previous deployment and is no longer hosted on your server.
* The resource is inaccessible: It may require authentication or be restricted to an internal network.
* The resource is blocked by the browser due to CORS: Browsers enforce cross-origin security checks, preventing UXCam from loading your assets if CORS is not properly configured.