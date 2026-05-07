---
title: Quick Start - Web
excerpt: Get UXCam running on your website in 5 minutes
deprecated: false
hidden: true
metadata:
  title: Web Quick Start - UXCam
  description: Fastest way to add UXCam session recording to your website
  robots: index
next:
  description: ''
  pages:
    - type: basic
      slug: web-sdk-installation
      title: Full Web Guide
---

# Web Quick Start

Get session recording working on your website in under 5 minutes.

## Prerequisites

- A website or web application
- A [UXCam account](https://app.uxcam.com/signup) with an app key

<GitHubCallout type="note">Works with React, Angular, Vue, Next.js, and any HTML/JS site. Flutter Web is not currently supported.</GitHubCallout>

---

## Option 1: HTML Code Snippet (Recommended)

Add this script tag to your HTML `<head>`:

```html
<script type="text/javascript">
(function(p,l,o,w,i,n,g){if(!p[i]){p.GlobalSnowplowNamespace=p.GlobalSnowplowNamespace||[];
p.GlobalSnowplowNamespace.push(i);p[i]=function(){(p[i].q=p[i].q||[]).push(arguments)
};p[i].q=p[i].q||[];n=l.createElement(o);g=l.getElementsByTagName(o)[0];n.async=1;
n.src=w;g.parentNode.insertBefore(n,g)}}(window,document,"script","//websdk.uxcam.com/uxcam.min.js","uxcam"));

uxcam("newTracker", "uxcamTracker", {
  appKey: "YOUR_APP_KEY"
});
</script>
```

---

## Option 2: Google Tag Manager

1. Create a new **Custom HTML** tag
2. Paste the code snippet above
3. Set trigger to **All Pages**
4. Publish your container

See [Google Tag Manager setup](/docs/google-tag-manager-1) for detailed instructions.

---

## Option 3: npm Package

```bash
npm install @uxcam/web-sdk
```

```javascript
import uxcam from '@uxcam/web-sdk';

uxcam("newTracker", "uxcamTracker", {
  appKey: "YOUR_APP_KEY"
});
```

---

## Verify It Works

1. Load your website
2. Navigate through a few pages
3. Check your [UXCam Dashboard](https://app.uxcam.com) - your session should appear within 30 seconds

<GitHubCallout type="tip">Open browser DevTools Network tab and filter by "uxcam" to see SDK requests.</GitHubCallout>

---

## What's Captured Automatically?

- Page views and navigation
- Clicks, scrolls, and user interactions
- Password and email fields are auto-occluded

---

## Next Steps

You're recording sessions! Now customize your integration:

<Cards columns={2}>
  <Card title="Full Web Guide" href="/docs/web-sdk-installation" icon="fa-solid fa-globe">
    Complete setup with all configuration options
  </Card>

  <Card title="Define Pages" href="/docs/define-pages-properties" icon="fa-solid fa-file">
    Organize pages without code changes
  </Card>

  <Card title="Hide Sensitive Data" href="/docs/occlusion-hide-sensitive-data" icon="fa-solid fa-eye-slash">
    Additional privacy protection
  </Card>

  <Card title="Track Events" href="/docs/logging-custom-events" icon="fa-solid fa-bolt">
    Capture custom user actions
  </Card>
</Cards>

---

## Content Security Policy

If your site uses CSP, you'll need to allow UXCam resources. See [CSP configuration](/docs/configure-content-security-policy-csp).