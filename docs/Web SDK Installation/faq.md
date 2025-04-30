---
title: FAQ
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
### 1. What defines the beginning and end of a session? 

The session starts as soon as the page loads and the WebSDK begins recording. There is no inactivity period per se; however, if there is no **incoming data\*** from the client for 3 minutes, the session is considered finished, and the recording is uploaded. 

\*Incoming data = No internet connection, the OS being suspended or the browser/tab being closed. 



### 2. Can events be sent directly from the script?

While clicks are captured as gestures by default, you can send custom events directly from the script by adding event listeners. For example:

```html
<button id="mybtn">Click me</button>

<script>
const button = document.querySelector('#mybtn');
button.addEventListener('click', () => uxc.event('my_btn_clicked'));
</script>
```

In this example, when the button with the ID mybtn is clicked, a custom event named my_btn_clicked is sent to UXCam.



### 3. Why is my email not occluded automatically?

The email input will only be occluded if the input type is explicitly set to email. If the input field has its type set to text instead of email, it will not be occluded by default. 

For email or any other input type that should be occluded automatically, please verify that the type is set to any of the options mentioned in the occlusion section. [Auto-occlusion](https://developer.uxcam.com/docs/installation#default-occlusion)



### 4. Why does the replay look broken, and CSS Styles or images are not loading?

UXCam relies on the CSS files that were available at the time when the session was recorded. If these assets become unavailable or restricted, the replay may not display styles correctly.

Common reasons why styles may not load:

- The resource no longer exists: It was part of a previous deployment and is no longer hosted on your server.
- The resource is inaccessible: It may require authentication or be restricted to an internal network.
- The resource is blocked by the browser due to CORS: Browsers enforce cross-origin security checks, preventing UXCam from loading your assets if CORS is not properly configured.





# Fixing CORS Issues for UXCam

If you're seeing missing fonts, broken icons, or layout issues in your session replays, it’s likely due to a CORS (Cross-Origin Resource Sharing) issue.

CORS is a browser security feature that restricts how resources can be shared between websites. It’s designed to protect users by preventing one site from accessing sensitive content from another without permission.

**What’s causing this issue?**  
During session replay, your website is loaded from a different domain — app.uxcam.com — so we can show you what the user experienced. But if your fonts, images, or styles are only allowed to load from your own domain, the browser may block them when the session is replayed.

You might see errors like this in your browser's console:

> ❗️ Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at <https://yourdomain.com/assets/something>

This means the asset couldn’t load because it wasn’t allowed to be accessed from app.uxcam.com.

**How does it affect session replays?**

If your website doesn’t explicitly allow assets to be loaded from app.uxcam.com, fonts may appear as empty boxes, images may not display, and some styles may break during playback.

## How to fix it?

To ensure all assets load correctly in session replays

✅ **1. Enable CORS on your server**  
Update your server’s headers to allow requests from the replay domain:  
Access-Control-Allow-Origin: <https://app.uxcam.com>

If needed, also include:  
Access-Control-Allow-Credentials: true  
Avoid using \* for Access-Control-Allow-Origin if your assets require credentials (like cookies).

✅ 2. Check Any Third-Party or CDN Assets  
If you're hosting fonts, images, or styles via a CDN or third-party service, make sure they also allow cross-origin requests from app.uxcam.com.

✅ 3. (Optional) Use a Proxy Server  
If you can’t change the CORS settings on the asset server, another approach is to serve those assets through your own server or a proxy that adds the required CORS headers.

For more help with CORS, check out [MDN Web Docs on CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS)

***



### How to Allow UXCam to Access Your Assets via CORS

You must configure your server or cloud storage to include this CORS rule:

```Text http
Access-Control-Allow-Origin: https://app.uxcam.com
```

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

Reference: <https://repost.aws/knowledge-center/s3-configure-cors>

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

Reference: <https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS>