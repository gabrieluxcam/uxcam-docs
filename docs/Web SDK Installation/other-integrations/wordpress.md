---
title: Wordpress
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
This guide will walk you through integrating the UXCam Web SDK into your WordPress website, tracking user events, and optionally integrating it via Google Tag Manager as well. Follow the steps below to start capturing valuable user interaction data.

# Step 1:Adding UXCam Web SDK to WordPress

## 1. Install a JavaScript Plugin

To add custom JavaScript globally to your WordPress site:

1. Log in to your WordPress admin dashboard. 
2. Navigate to Plugins > Add New.
3. Search for and install a plugin that allows you to add custom JavaScript and CSS, such as Insert Headers and Footers or similar plugins.
4. Activate the plugin.

## 2. Add the UXCam Script

1. Open the plugin settings (e.g., Settings > Insert Headers and Footers).
2. In the Global Header or Before <body> section, paste the following script:

```javascript
<script type="text/javascript" defer="">
  (function( appKey, opts ) {
    window.uxc = {
      __t: [],
      __ak: appKey,
      __o: opts,
      event: function( n, p ) {
        this.__t.push( [ 'event', n, p ] )
      },
      setUserIdentity: function( i ) {
        this.__t.push( [ 'setUserIdentity', i ] )
      },
      setUserProperty: function( k, v ) {
        this.__t.push( [ 'setUserProperty', k, v ] )
      },
      setUserProperties: function( p ) {
        this.__t.push( [ 'setUserProperties', p ] )
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
  })( 'YOUR_APP_KEY', {} );
</script>
```

<br />

3. Replace YOUR_APP_KEY with your UXCam App Key.
4. Save your changes.

# Step 2: Sending Events

## Example: Tracking Page Views

To track page views globally, use the following script:

```javascript
<script>
  document.addEventListener("DOMContentLoaded", function () {
    uxc.event("Page Viewed", {
      page_title: document.title,
      page_url: window.location.href
    });
    console.log("Page View Event Sent to UXCam");
  });
</script>
```

## Example: Tracking Button Clicks

To track button clicks (e.g., a "Buy Now" button), add this script globally:

```javascript
<script>
  document.addEventListener("DOMContentLoaded", function () {
    const buttons = document.querySelectorAll(".buy-now-button"); // Replace with your button's actual class or ID
    buttons.forEach(function (button) {
      button.addEventListener("click", function () {
        uxc.event("Button Clicked", {
          button_text: this.textContent,
          page_title: document.title
        });
        console.log("Button Click Event Sent to UXCam");
      });
    });
  });
</script>
```

# Step 3: Setting User Properties

If your website includes logged-in users, you can set user identity and properties dynamically:

```javascript
<script>
  document.addEventListener("DOMContentLoaded", function () {
    uxc.setUserIdentity("USER_ID"); // Replace USER_ID dynamically 
    uxc.setUserProperties({
      email: "USER_EMAIL", // Replace USER_EMAIL dynamically
      name: "USER_NAME" // Replace USER_NAME dynamically
    });
    console.log("User Identity and Properties Sent to UXCam");
  });
</script>
```

# Google Tag Manager

You can integrate Google Tag Manager (GTM) into your WordPress site by searching for a GTM plugin and adding your GTM container ID. Once the plugin is configured, your GTM scripts will be integrated with your website.

## Conclusion

This concludes the documentation for integrating the Web SDK. If you have any questions or need further assistance, please contact [team@uxcam.com](mailto:team@uxcam.com).