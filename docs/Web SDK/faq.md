---
title: Troubleshooting
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
> 🚧 You've Installed UXCam but did not receive sessions yet?
>
> Please ensure that:
>
> * You've **replaced 'Your\_App\_Key'** at the bottom of the snippet with the app key located in your [UXCam platform](https://app.uxcam.com/integration)
> * Your **plan includes web session recording.**\
>   You can request a **web trial** by clicking on Request Trial on your subscription page or simply by reaching out to our support team via [team@uxcam.com](mailto:team@uxcam.com).

<br />

## Resolving CORS Issues for UXCam Session Replays

If you’re noticing missing fonts, broken icons, or layout issues in UXCam session replays, it’s likely due to CORS (Cross-Origin Resource Sharing) restrictions.

<br />

**What is CORS?**

CORS is a browser security mechanism that blocks resources (like fonts, images, or stylesheets) from loading if they’re not explicitly allowed to be accessed from another domain. This is meant to prevent unauthorized cross-site access.

<br />

**Why It Happens in UXCam?**

During session replay, your website is loaded inside a different domain ([https://app.uxcam.com](https://app.uxcam.com)) to simulate the user experience. If your assets are restricted to load only from your own domain, browsers may block them during playback.

<br />

**Typical browser console error:**

```
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at https://yourdomain.com/assets/file
```

This means the asset couldn’t load because it wasn’t allowed to be accessed from app.uxcam.com.

<br />

**How It Affects Replays**

Without proper CORS configuration:\
•	Fonts may not render (showing empty boxes)
•	Images and styles may not appear
•	Layouts can break

<br />

## How to fix it?

To ensure all assets load correctly in session replays

✅ **1. Enable CORS on your server**\
Update your server’s headers to allow requests from the replay domain:
Access-Control-Allow-Origin: [https://app.uxcam.com](https://app.uxcam.com)

If needed, also include:\
Access-Control-Allow-Credentials: true
Avoid using \* for Access-Control-Allow-Origin if your assets require credentials (like cookies).

✅ 2. Check Any Third-Party or CDN Assets\
If you're hosting fonts, images, or styles via a CDN or third-party service, make sure they also allow cross-origin requests from app.uxcam.com.

✅ 3. (Optional) Use a Proxy Server\
If you can’t change the CORS settings on the asset server, another approach is to serve those assets through your own server or a proxy that adds the required CORS headers.

For more help with CORS, check out [MDN Web Docs on CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS)

<br />

***

<br />

### How to Allow UXCam to Access Your Assets via CORS

You must configure your server or cloud storage to include this CORS rule:

```Text http
Access-Control-Allow-Origin: https://app.uxcam.com
```

<br />

### If Your Assets Are Hosted on AWS S3

For Amazon S3 buckets, update the CORS configuration:

1. Go to AWS S3 Console.
2. Navigate to Permissions > CORS Configuration.
3. Add this JSON rule

```Text JSON
[
    {
        "AllowedHeaders": ["*"],
        "AllowedMethods": ["GET", "HEAD"],
        "AllowedOrigins": ["https://app.uxcam.com"],
        "ExposeHeaders": []
    }
]
```

4. Save changes and test if styles now load in UXCam replays.

Reference: [https://repost.aws/knowledge-center/s3-configure-cors](https://repost.aws/knowledge-center/s3-configure-cors)

<br />

### If Your Assets Are Hosted on a Custom Web Server

If you host assets on your own web server (Apache or Node.js), modify the server settings:

✔ For Apache (.htaccess file)

```Text Apache
<IfModule mod_headers.c>
    Header set Access-Control-Allow-Origin "https://app.uxcam.com"
</IfModule>
```

✔ For Express.js (Node.js)

```javascript
const cors = require('cors');  
app.use(cors({ origin: "<https://app.uxcam.com"> }));
```

Reference: [https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)

<br />

<br />

## Support

For questions or support, reach out to us at [team@uxcam.com](mailto:team@uxcam.com).