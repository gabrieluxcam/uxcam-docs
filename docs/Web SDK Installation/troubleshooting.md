---
title: Troubleshooting
deprecated: false
hidden: false
metadata:
  robots: index
---
> 🚧 You've Installed UXCam but did not receive sessions yet?
>
> Please ensure that:
>
> * You've **replaced 'Your\_App\_Key'** at the bottom of the snippet with the app key located in your [UXCam platform](https://app.uxcam.com/integration)
> * Your **plan includes web session recording.**\
>   You can request a **web trial** by clicking on Request Trial on your subscription page or simply by reaching out to our support team via [team@uxcam.com](mailto:team@uxcam.com).
> * You're **testing in a regular browser**, not an automated or headless one such as Playwright, Selenium, Puppeteer, or Lighthouse.\
>   The SDK does not run for bots, crawlers, or automated browsers. See the full list in the [FAQ](faq#).

## Resolving CORS Issues for UXCam Session Replays

If you’re noticing missing fonts, broken icons, or layout issues in UXCam session replays, it’s likely due to CORS (Cross-Origin Resource Sharing) restrictions.

**What is CORS?**

CORS is a browser security mechanism that blocks resources (like fonts, images, or stylesheets) from loading if they’re not explicitly allowed to be accessed from another domain. This is meant to prevent unauthorized cross-site access.

**Why It Happens in UXCam?**

During session replay, your website is loaded inside a different domain ([https://app.uxcam.com](https://app.uxcam.com)) to simulate the user experience. If your assets are restricted to load only from your own domain, browsers may block them during playback.

**Typical browser console error**

```
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at https://yourdomain.com/assets/file
```

This means the asset couldn’t load because it wasn’t allowed to be accessed from app.uxcam.com.

**How It Affects Replays**

Without proper CORS configuration:\
•	Fonts may not render (showing empty boxes)
•	Images and styles may not appear
•	Layouts can break

## How to fix it?

**1. Enable CORS on your server**\
Set the following header to allow asset access from UXCam:

```
Access-Control-Allow-Origin: https://app.uxcam.com
```

If your assets require credentials (like cookies), also include:

```
Access-Control-Allow-Credentials: true
```

Avoid using \* for Access-Control-Allow-Origin if your assets require credentials (like cookies).

**2. Update Third-Party or CDN Asset Settings**\
Ensure any CDN or external services hosting your fonts, images, or styles also permit access from [https://app.uxcam.com](https://app.uxcam.com).

**3. Use a Proxy Server (Optional)**\
If CORS settings can’t be changed on the asset server, you can proxy the assets through your own server and attach the necessary headers.

<GitHubCallout type="note">For more help with CORS, check out [MDN Web Docs on CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS)   </GitHubCallout>

***

## CORS Setup Examples

### Using AWS S3

For Amazon S3 buckets, update the CORS configuration:

1. Open your AWS S3 Console
2. Navigate to: Permissions > CORS Configuration
3. Add the following JSON:

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

4. Save and verify if assets now load in replays.

Reference: [https://repost.aws/knowledge-center/s3-configure-cors](https://repost.aws/knowledge-center/s3-configure-cors)

### Using a Custom Web Server

In case you host assets on your own web server (Apache or Node.js), modify the server settings:

**For Apache (.htaccess file)**

```Text Apache
<IfModule mod_headers.c>
    Header set Access-Control-Allow-Origin "https://app.uxcam.com"
</IfModule>
```

**Node.js (Express)**

```javascript
const cors = require('cors');  
app.use(cors({ origin: "<https://app.uxcam.com"> }));
```

Reference: [https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)

<GitHubCallout type="note">For more help with CORS, check out [MDN Web Docs on CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS)  </GitHubCallout>

## Support

For questions or support, reach out to us at [team@uxcam.com](mailto:team@uxcam.com).