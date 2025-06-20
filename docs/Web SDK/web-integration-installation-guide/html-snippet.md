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

<GitHubCallout type="important">Flutter Web is at the moment not supported</GitHubCallout>

<br />

### **To integrate the Web SDK follow these steps:**

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

★ **Optional step**

| Step                 | Task                                                                                           | Goal                                       |
| :------------------- | :--------------------------------------------------------------------------------------------- | :----------------------------------------- |
| <strong>1</strong>   | <strong>Configure occlusion </strong>  <a href="occlusion#">Jump →</a>                         | Hide sensitive user data                   |
| <strong>2</strong>   | <strong>Define Pages </strong>  <a href="define-pages-properties-to-simplify-data#">Jump →</a> | Group similar URLs under consistent names  |
| <strong>3</strong>   | <strong>Set User Identity & Propertie </strong>  <a href="user-properties#">Jump →</a>         | Unify sessions, power funnels & cohorts    |
| <strong>4</strong>   | <strong>Add Custom Events </strong>  <a href="logging-custom-event#">Jump →</a>                | Track user interactions and key behaviours |
| <strong>5 </strong>  | <strong>Check Content Security Policy </strong>  <a href="csp#">Jump →</a>                     | Ensure the Web SDK functions correctly     |
| <strong>6 ★</strong> | <strong>Define App version </strong>  <a href="app-version#">Jump →</a>                        | Debug issues across different releases     |

<br />

<GitHubCallout type="note">Ship steps 1-5, to get the most value out of UXCam</GitHubCallout>

<br />

## Support

For questions or support, reach out to us at [team@uxcam.com](mailto:team@uxcam.com).