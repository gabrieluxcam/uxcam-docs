---
title: Web Installation guide
deprecated: false
hidden: false
metadata:
  robots: index
---
This document will guide you through the process of integrating the Web SDK into your website. The SDK allows for tracking user events, setting user identities, page views, and configuring user properties, with additional options for occlusion of sensitive data in URLs and query parameters.

<br />

## What is Captured by Default

### Default Events

By default, the following events are captured:

* uxc\_input: Triggered when an input loses focus after it has been modified.
* uxc\_submit: Triggered when a form is submitted.
* uxc\_u\_turn: Triggered when a user goes back to the same page. The event is triggered on the page they went back to.\
  Example: If a user navigates Home > About Us > Home, it will be triggered on Home.
* uxc\_page\_refresh: Triggered when refreshing the page.

<br />

## Choose your methods of integration

* [AI Agent](https://developer.uxcam.com/v2.0-draft/update/docs/ai-agent-integration#/)
* [HTML inclusion](https://developer.uxcam.com/v2.0-draft/update/docs/html-snippet#/)
* [Google Tag Manager](https://developer.uxcam.com/v2.0-draft/update/docs/google-tag-manager#/)
* [Shopify](https://developer.uxcam.com/v2.0-draft/update/docs/shopify#/)
* [Wordpress](https://developer.uxcam.com/v2.0-draft/update/docs/wordpress#/)