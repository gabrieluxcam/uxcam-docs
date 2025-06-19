---
title: Web Integration
excerpt: How to Get Started with UXCam for Web
deprecated: false
hidden: false
metadata:
  robots: index
---
Get up and running with UXCam in minutes - no complex setup required. This guide covers everything you need to integrate the UXCam Web SDK into your website or web app.

<br />

<GitHubCallout type="note">Need an account? \*\*<Anchor label="Start free" target="_blank" href="https://app.uxcam.com/signup">Start free</Anchor>            \*\* – most teams finish this setup in **\< 15 min**.</GitHubCallout>

<br />

\<Accordion title="Supported technologies" icon="fa-duotone fa-solid fa-code">
&#x20; But not limited to..

&#x20; \* Basic web (HTML CSS and Javascript)
&#x20; \* React.js
&#x20; \* Angular
&#x20; \* Vue.js
&#x20; \* Next.js

<GitHubCallout type="important">Flutter web is not supported</GitHubCallout>

\</Accordion>

<br />

# Choose your Method of Integration

| HTML Code snippet      | <a href="html-snippet#">Jump →</a>      |
| :--------------------- | :-------------------------------------- |
| **Google Tag Manager** | <a href="google-tag-manage#">Jump →</a> |
| **Shopify**            | <a href="shopify#">Jump →</a>           |
| **Wordpress**          | <a href="wordpress#">Jump →</a>         |

<br />

# What is automatically captured?

After the initial integration, UXCam automatically collects a variety of user interactions and technical properties without requiring additional setup.

* **User Events**\
  We automatically capture key interaction events such as taps, clicks, scrolls, and page navigations.
  ➜ [Learn how to add custom event tagging](logging-custom-event#)
* **Page Visits**\
  All page views are captured automatically.
  ➜ [Use code-less page definition to organise your pages without touching code](define-pages-properties-to-simplify-data#)
* **Sensitive Data Protection**\
  Inputs such as passwords and email fields are auto-occluded to protect user privacy.
  ➜ [Explore additional occlusion options](occlusion#)
* **User Identification**\
  We identify users based on anonymous browser/device IDs by default.
  ➜ [You can enhance this by using functions to set user identity and properties](user-properties#)

<br />

# Content Security Policy (CSP)

If your website uses a Content Security Policy (CSP), you’ll need to explicitly allow UXCam resources for the Web SDK to function properly. Without this, session recordings and script loading may be blocked. ➜ [Learn how to configure your CSP to safely enable UXCam in our Content Security Policy setup guide](csp#)

<br />

## Support

For questions or support, reach out to us at [team@uxcam.com](mailto:team@uxcam.com).