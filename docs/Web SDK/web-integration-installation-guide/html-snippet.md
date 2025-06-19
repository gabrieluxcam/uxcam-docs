---
title: HTML Code snippet
deprecated: false
hidden: false
metadata:
  robots: index
---
<Accordion title="Supported technologies" icon="fa-duotone fa-solid fa-code">
  But not limited to..

  * Basic web (HTML CSS and Javascript)
  * React.js
  * Angular
  * Vue.js
  * Next.js
</Accordion>

<br />

**To integrate the Web SDK follow these steps:**

1. Replace `'Your_App_Key'` in the following HTML code with the App Key from your [UXCam platform](https://app.uxcam.com/integration)
2. Copy the snippet and include it in all your HTML files before the closing \</head> tag:

<br />

```javascript

<script type="text/javascript" defer="">
(function(appKey, opts) {
    window.uxc = {
        __t: [],
        __ak: appKey,
        __o: opts,
        event: function(n, p) {
            this.__t.push(['event', n, p]);
        },
        setUserIdentity: function(i) {
            this.__t.push(['setUserIdentity', i]);
        },
        setUserProperty: function(k, v) {
            this.__t.push(['setUserProperty', k, v]);
        },
        setUserProperties: function(p) {
            this.__t.push(['setUserProperties', p]);
        },
    };
    var head = document.getElementsByTagName('head')[0];
    var script = document.createElement('script');
    script.type = 'text/javascript';
    script.src = '//websdk-recording.uxcam.com/index.js';
    script.async = true;
    script.defer = true;
    script.id = 'uxcam-web-sdk';
    script.crossOrigin = 'anonymous';
    head.appendChild(script);
})('Your_App_Key', {}); 
</script>
```

<br />

## Next steps - Setup and configuration

| Step                 | Task                                                                                                    | Goal                                    |
| :------------------- | :------------------------------------------------------------------------------------------------------ | :-------------------------------------- |
| <strong>1</strong>   | <strong>Occlusion </strong>  <a href="occlusion#">Jump →</a>                                            | Hide sensitive data                     |
| <strong>3</strong>   | <strong>Tag Screens </strong>  <a href="#3-tag-screens">Jump →</a>                                      | Enable heat-maps & screen analytics     |
| <strong>2</strong>   | <strong>Mask PII & Sensitive Content </strong>  <a href="#2-mask-pii--sensitive-content">Jump →</a>     | Hide passwords and other GDPR/CCPA data |
| <strong>4 ★</strong> | <strong>Set User Identity & Properties </strong>  <a href="#4-set-user-identity--properties">Jump →</a> | Unify sessions, power funnels & cohorts |
| <strong>5 ★</strong> | <strong>Tag Events </strong>  <a href="#5-tag-events">Jump →</a>                                        | Measure key actions & run final QA      |

<GitHubCallout type="tip"> ★ **Optional but highly recommended.** Ship steps 1-3 to start getting replays and heat-maps, then add steps 4-5 for deeper analytics.</GitHubCallout>

<br />

## Next steps - Setup and configuration

* [Occlusion - Hide Sensitive Data](https://developer.uxcam.com/v2.0-draft/update/docs/occlusion#/)
* [Define Pages & Properties](https://developer.uxcam.com/v2.0-draft/update/docs/define-pages-properties-to-simplify-data)
* [Setting User Properties](https://developer.uxcam.com/v2.0-draft/update/docs/user-properties)
* [Logging Custom Event](https://developer.uxcam.com/v2.0-draft/update/docs/logging-custom-event#/)
* [Configure Content Security Policy (CSP)](https://developer.uxcam.com/v2.0-draft/update/docs/csp#/)
* [Define App version (Optional)](https://developer.uxcam.com/v2.0-draft/update/docs/app-version#/)

<br />

## Support

For questions or support, reach out to us at [team@uxcam.com](mailto:team@uxcam.com).