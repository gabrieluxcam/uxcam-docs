---
title: Web Integration
excerpt: How to Get Started with UXCam for Web
deprecated: false
hidden: false
metadata:
  robots: index
---
This document will guide you through the process of integrating the Web SDK into your website. Through the setup and configuration phase, the SDK allows for tracking user events, setting user identities, page views, and configuring user properties, with additional options for occlusion of sensitive data in URLs and query parameters.

<br />

<GitHubCallout type="note">Need an account? \*\*<Anchor label="Start free" target="_blank" href="https://app.uxcam.com/signup">Start free</Anchor>            \*\* – most teams finish this setup in **\< 15 min**.</GitHubCallout>

<br />

<Cards columns={4}>
  <Card title="HTML CSS & Javascript" href="https://readme.com" icon="fa-duotone fa-solid fa-code" target="_blank" />
</Cards>

<br />

<br />

**Supported technologies**

* Basic web (HTML CSS and Javascript)
* React.js
* Angular
* Vue.js
* Next.js

<br />

## Events Captured by Default

By default, the following events are captured:

* **`uxc_input`**: Triggered when an input loses focus after it has been modified.
* **`uxc_submit`**: Triggered when a form is submitted.
* **`uxc_u_turn`**: Triggered when a user goes back to the same page. The event is triggered on the page they went back to. Example: If a user navigates Home > About Us > Home, it will be triggered on Home.
* **`uxc_page_refresh`**: Triggered when refreshing the page.

<br />

<br />

# Choose your methods of integration

| Install Code Snippet   | <a href="html-snippet#">Jump →</a>      |
| :--------------------- | :-------------------------------------- |
| **Google Tag Manager** | <a href="google-tag-manage#">Jump →</a> |
| **Shopify**            | <a href="shopify#">Jump →</a>           |
| **Wordpress**          | <a href="wordpress#">Jump →</a>         |