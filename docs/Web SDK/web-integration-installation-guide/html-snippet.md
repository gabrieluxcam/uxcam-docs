---
title: HTML inclusion
deprecated: false
hidden: false
metadata:
  robots: index
---
To integrate the Web SDK follow these steps:

1. Replace `Your_App_Key` in the following HTML code with the App Key from your [UXCam platform](https://app.uxcam.com/integration)
2. Copy the edited HTML script and include it in all your HTML files before the closing \</head> tag:

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

> 🚧 You've Integrated but did not receive sessions?
>
> Please ensure that:
>
> * You've **replaced 'Your\_App\_Key'** at the bottom of the snippet with the app key located in your dashboard.
> * Your **plan includes web session recording.**\
>   You can request a **web trial** by clicking on Request Trial on your subscription page or simply by reaching out to our support team.

> 📘 Please note
>
> You will need to integrate this script in all of your HTML elements in order to get the session recording of your entire website.
>
> **Example of supported technologies:** Basic web (HTML, CSS and Javascript), React.js, Angular, Vue.js, Next.Js
>
> **Example of supported CMS:** Wordpress, Shopify and Magento