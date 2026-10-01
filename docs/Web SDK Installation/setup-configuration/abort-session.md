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

Stops recording and closes the connection. The session is not uploaded and does not appear in your UXCam dashboard. The method takes no parameters.

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

### Calling before the SDK has loaded

Once the SDK has loaded, `uxc.abort()` is always available. To call it earlier, for example from a consent script that runs before the UXCam script finishes loading, add `abort` to the `window.uxc` object in your snippet. The call is queued and applied as soon as the SDK starts.

```javascript
window.uxc = {
    __t: [],
    __ak: appKey,
    __o: opts,
    // ...event, setUserIdentity, setUserProperty and setUserProperties as in the standard snippet
    abort: function() {
        this.__t.push(['abort']);
    },
};
```
