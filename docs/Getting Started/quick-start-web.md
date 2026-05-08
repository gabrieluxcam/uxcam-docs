---
title: Quick Start - Web
excerpt: Get UXCam running on your website in 5 minutes
deprecated: false
hidden: false
metadata:
  title: 'Web Quick Start - UXCam'
  description: 'Fastest way to add UXCam session recording to your website'
  robots: index
next:
  description: ''
  pages:
    - type: basic
      slug: installation
      title: Full Web Guide
---

# Web Quick Start

Get session recording working on your website in under 5 minutes.

## Prerequisites

- A website or web app (HTML, React, Angular, Vue, Next.js, etc.)
- A [UXCam account](https://app.uxcam.com/signup) with an app key

---

## Step 1: Add the SDK Snippet

Copy this script and include it in your HTML before the closing `</head>` tag. Replace `'YOUR_APP_KEY'` with your key from the [UXCam dashboard](https://app.uxcam.com/integration):

```html
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
})('YOUR_APP_KEY', {});
</script>
```

> 📘 Include this snippet in **all** your HTML pages to record your entire website. SPAs (single-page apps) are supported automatically.

---

## Step 2: Verify It Works

1. Deploy or serve your site locally
2. Browse a few pages for at least 30 seconds
3. Check your [UXCam Dashboard](https://app.uxcam.com) — your session should appear within 60 seconds

---

## What's Captured Automatically

Once the snippet is live, UXCam captures these out of the box — no extra code needed:

- **User interactions** — clicks, scrolls, page navigations
- **Page visits** — all page views
- **Sensitive data protection** — password, email, tel, and credit card inputs are auto-occluded
- **User identification** — anonymous browser/device IDs

---

## Next Steps

You're recording sessions! Now customize your integration:

<Cards columns={2}>
  <Card title="Full Web Guide" href="/docs/installation" icon="fa-solid fa-globe">
    All installation methods (HTML, GTM, Shopify, WordPress)
  </Card>

  <Card title="Hide Sensitive Data" href="/docs/occlusion-hide-sensitive-data" icon="fa-solid fa-eye-slash">
    Configure occlusion for inputs, URLs, and HTML elements
  </Card>

  <Card title="Define Pages" href="/docs/define-pages-properties" icon="fa-solid fa-file">
    Group similar URLs under consistent page names
  </Card>

  <Card title="Track Events" href="/docs/logging-custom-events" icon="fa-solid fa-bolt">
    Log custom events and user properties
  </Card>
</Cards>
