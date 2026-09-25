---
title: FAQ
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
### 1. What defines the beginning and end of a session?

A session begins as soon as the page loads and the UXCam Web SDK starts recording. There’s no defined inactivity timeout, but if no data is received from the client for 5 minutes, the session is automatically ended and the recording is uploaded.

**“No data” can mean:**

* No internet connection
* The device is suspended (e.g., sleep mode)
* The browser or tab is closed

### 2. Can events be sent directly from the script?

Yes. While UXCam captures click gestures by default, you can send custom events manually using JavaScript event listeners. In this example, a custom event named my\_btn\_clicked is sent whenever the button is clicked.

```html
<button id="mybtn">Click me</button>

<script>
const button = document.querySelector('#mybtn');
button.addEventListener('click', () => uxc.event('my_btn_clicked'));
</script>
```

### 3. Why is my email not occluded automatically?

For automatic occlusion to work, the input field must have its type set to "email". If it’s set to "text" or another type, UXCam will not hide it by default.

For email or any other input type that should be occluded automatically, please verify that the type is set to any of the options mentioned in the occlusion section → [Occlusion - Hide Sensitive Data](setup-configuration/occlusion-hide-sensitive-data#)

### 4. Why does the replay look broken, and CSS Styles or images are not loading?

UXCam relies on the CSS files that were available at the time when the session was recorded. If these assets become unavailable or restricted, the replay may not display styles correctly.

Common reasons why styles may not load:

* The resource no longer exists: It was part of a previous deployment and is no longer hosted on your server.
* The resource is inaccessible: It may require authentication or be restricted to an internal network.
* The resource is blocked by the browser due to CORS: Browsers enforce cross-origin security checks, preventing UXCam from loading your assets if CORS is not properly configured.

Learn how to fix CORS issues here → [Troubleshooting](troubleshooting#)

### 5. Does UXCam record bots, crawlers, or automated browsers?

No. Before it starts, the Web SDK checks whether the visitor is a bot, crawler, link-preview service, monitoring tool, or automated test browser. If it is, the SDK does not start. Nothing is recorded or sent to UXCam, so these visits never appear as sessions and don't count toward your session limit.

This check is on by default and cannot be turned off.

**User agents that are skipped**

The SDK skips any visitor whose user agent contains one of these keywords. Matching is not case-sensitive.

| Category | Keywords |
|---|---|
| Generic bots and crawlers | `bot`, `crawl`, `spider`, `slurp`, `mediapartners` |
| Search engines | `googlebot`, `bingbot`, `yandex`, `baidu`, `duckduck` |
| Social and messaging link previews | `facebookexternalhit`, `twitterbot`, `linkedinbot`, `whatsapp`, `slack`, `telegram`, `discord` |
| Headless browsers and test automation | `headless`, `phantom`, `selenium`, `puppeteer`, `playwright` |
| Performance and SEO tools | `prerender`, `lighthouse`, `pagespeed`, `gtmetrix` |
| Uptime monitoring | `pingdom`, `uptimerobot`, `statuscake` |

Because matching is by keyword, any user agent containing `bot` is skipped, including crawlers not named above (for example `AhrefsBot` or `GPTBot`).

**Other checks**

The SDK also skips the visit when:

* The browser reports that it is controlled by automation (`navigator.webdriver` is `true`). Selenium, Playwright, and Puppeteer set this by default.
* The browser is not Chrome-based, Safari, or Firefox, and reports no preferred languages (`navigator.languages` is empty).
* PhantomJS or Nightmare is detected (`window._phantom`, `window.callPhantom`, or `window.__nightmare` is present).

> 📘 **Testing your installation**
>
> Sessions from Playwright, Selenium, Puppeteer, Lighthouse, and similar tools are not recorded. To confirm that UXCam is working, open your site in a regular browser window.

## Support

For questions or support, reach out to us at [team@uxcam.com](mailto:team@uxcam.com).