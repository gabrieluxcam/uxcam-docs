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

# Integration methods at a Glance

| Task                                                                                                    | Goal                                    |
| :------------------------------------------------------------------------------------------------------ | :-------------------------------------- |
| <strong>Install Code Snippet </strong>  <a href="html-snippet#">Jump →</a>                              | Capture your first live session         |
| <strong>Tag Screens </strong>  <a href="#3-tag-screens">Jump →</a>                                      | Enable heat-maps & screen analytics     |
| <strong>Mask PII & Sensitive Content </strong>  <a href="#2-mask-pii--sensitive-content">Jump →</a>     | Hide passwords and other GDPR/CCPA data |
| <strong>Set User Identity & Properties </strong>  <a href="#4-set-user-identity--properties">Jump →</a> | Unify sessions, power funnels & cohorts |
| <strong>Tag Events </strong>  <a href="#5-tag-events">Jump →</a>                                        | Measure key actions & run final QA      |

<GitHubCallout type="tip"> ★ **Optional but highly recommended.** Ship steps 1-3 to start getting replays and heat-maps, then add steps 4-5 for deeper analytics.</GitHubCallout>

<br />

## Events Captured by Default

By default, the following events are captured:

* **`uxc_input`**: Triggered when an input loses focus after it has been modified.
* **`uxc_submit`**: Triggered when a form is submitted.
* **`uxc_u_turn`**: Triggered when a user goes back to the same page. The event is triggered on the page they went back to. Example: If a user navigates Home > About Us > Home, it will be triggered on Home.
* **`uxc_page_refresh`**: Triggered when refreshing the page.

<br />

## Choose your methods of integration

* [AI Agent](https://developer.uxcam.com/v2.0-draft/update/docs/ai-agent-integration#/)
* [HTML inclusion](https://developer.uxcam.com/v2.0-draft/update/docs/html-snippet#/)
* [Google Tag Manager](https://developer.uxcam.com/v2.0-draft/update/docs/google-tag-manager#/)
* [Shopify](https://developer.uxcam.com/v2.0-draft/update/docs/shopify#/)
* [Wordpress](https://developer.uxcam.com/v2.0-draft/update/docs/wordpress#/)