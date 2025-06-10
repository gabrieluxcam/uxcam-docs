---
title: Content Security Policy (CSP)
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
If your website uses a Content Security Policy (CSP), you need to allow UXCam’s resources in your CSP configuration to ensure the Web SDK functions correctly. CSP blocks external resources unless explicitly allowed, which might prevent UXCam from recording sessions or loading its scripts.

## Why It’s Important

CSP ensures the security of your website by controlling which external resources are allowed. By adding UXCam to your CSP configuration, you enable the SDK to record user sessions without compromising your security settings.

## Required CSP Directives

To allow UXCam, add the following to your CSP configuration:

```javascript
content-security-policy: default-src https://*.uxcam.com wss://*.uxcam.com;
```

This directive allows:

* https\://\*.uxcam.com: To load UXCam’s scripts and resources.
* wss\://\*.uxcam.com: To enable WebSocket connections used by the SDK.

### Example CSP

If you already have a CSP, update it to include UXCam alongside your other allowed resources. Here’s an example:

```javascript
content-security-policy: default-src 'self' https://*.intercom.io https://*.uxcam.com wss://*.uxcam.com;
```

This example includes UXCam alongside other tools like Intercom.