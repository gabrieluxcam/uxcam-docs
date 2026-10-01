---
title: Aborting a Session
deprecated: false
hidden: false
metadata:
  robots: index
---
To stop recording the current session and prevent it from being uploaded, call `uxc.abort()`. Use it when a user withdraws consent, for internal or test traffic, or on pages you never want recorded.

### Aborting the Current Session

**uxc.abort()**

Stops recording and closes the connection. The session is not uploaded, does not appear in your UXCam dashboard, and does not count towards your session quota. The method takes no parameters.

```javascript
uxc.abort();

//Example
<button id="decline">Decline analytics</button>

<script>
const button = document.querySelector('#decline');
button.addEventListener('click', () => uxc.abort());
</script>
```

### What to expect

* Recording does not restart on its own. After `uxc.abort()`, further clicks, scrolls or navigation within the same page load are not recorded.
* The next full page load (a refresh, or opening the site again) starts a new session as usual. If a user should stay unrecorded, call `uxc.abort()` again on each page load.
* Single-page apps navigate without a page load, so the abort lasts until the user reloads.

<GitHubCallout type="tip">Call `uxc.abort()` as early as possible, ideally before the user interacts with the page, so that nothing has been sent yet.</GitHubCallout>
