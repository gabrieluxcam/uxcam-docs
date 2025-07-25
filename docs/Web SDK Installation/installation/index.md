---
title: Web Installation
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
Get up and running with UXCam in minutes - no complex setup required. This guide covers everything you need to integrate the UXCam Web SDK into your website or web app.

<GitHubCallout type="note">Need an account? \*\*<Anchor label="Start free" target="_blank" href="https://app.uxcam.com/signup">Start free</Anchor>            \*\* – most teams finish this setup in **\< 15 min**.</GitHubCallout>

<Accordion title="Supported technologies" icon="fa-duotone fa-solid fa-code">
  But not limited to..

  * Basic web (HTML CSS and Javascript)
  * React.js
  * Angular
  * Vue.js
  * Next.js
</Accordion>

<GitHubCallout type="important">Flutter Web is at the moment not supported</GitHubCallout>

# Choose your Method of Integration

| HTML Code snippet      | <a href="html-code-snippet#">Jump →</a>       |
| :--------------------- | :--------------------------------------- |
| **Google Tag Manager** | <a href="google-tag-manager-1#">Jump →</a> |
| **Shopify**            | <a href="shopify-1#">Jump →</a>            |
| **Wordpress**          | <a href="wordpress-1#">Jump →</a>          |

# What is automatically captured?

After the initial integration, UXCam automatically collects a variety of user interactions and technical properties without requiring additional setup.

* **User Events**\
  We automatically capture key interaction events such as taps, clicks, scrolls, and page navigations.
  ➜ [Learn how to add custom event tagging](../setup-configuration/logging-custom-events#)
* **Page Visits**\
  All page views are captured automatically.
  ➜ [Use code-less page definition to organise your pages without touching code](../setup-configuration/define-pages-properties#)
* **Sensitive Data Protection**\
  Inputs such as passwords and email fields are auto-occluded to protect user privacy.
  ➜ [Explore additional occlusion options](../setup-configuration/occlusion-hide-sensitive-data#)
* **User Identification**\
  We identify users based on anonymous browser/device IDs by default.
  ➜ [You can enhance this by using functions to set user identity and properties](../setup-configuration/setting-user-properties#)

# Content Security Policy (CSP)

If your website uses a Content Security Policy (CSP), you’ll need to explicitly allow UXCam resources for the Web SDK to function properly. Without this, session recordings and script loading may be blocked. ➜ [Learn how to configure your CSP to safely enable UXCam in our Content Security Policy setup guide](../setup-configuration/configure-content-security-policy-csp#)

## Support

For questions or support, reach out to us at [team@uxcam.com](mailto:team@uxcam.com).