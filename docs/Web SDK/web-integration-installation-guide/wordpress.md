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
Integrate the UXCam Web SDK into your WordPress site and start tracking custom user events by following the steps below. You can implement the SDK directly or via Google Tag Manager, depending on your setup. This allows you to capture relevant user interactions and gain deeper insights into visitor behaviour.

<br />

## Step 1: Adding UXCam Web SDK to WordPress

You can inject code snippets into your WordPress site by either installing a JavaScript injection plugin or using a Google Tag Manager integration plugin.

<br />

### Option 1: Install a JavaScript Plugin

To add custom JavaScript globally to your WordPress site:

1. Log in to your WordPress admin dashboard.
2. Navigate to Plugins
3. Search for and install a plugin that allows you to add custom JavaScript and CSS, such as Insert Headers and Footers or similar plugins.
4. Activate the plugin.

<br />

### Option 2: Install a Google Tag Manager Plugin

To add Google Tab Manager integration plugin to your WordPress site:

1. Log in to your WordPress admin dashboard.
2. Navigate to Plugins
3. Install a plugin like “Insert Headers and Footers” or “Site Kit by Google”.
4. Activate the plugin.

<br />

<GitHubCallout type="note">For setting up UXCam through Google Tag Manager, make sure you have a Google Tag Manager account set up with your WordPress site, and continue that installation and set up steps described in the [Google Tag Manager page](https://developer.uxcam.com/v2.0-draft/update/docs/google-tag-manager) </GitHubCallout>

<br />

### Add the UXCam Script - Through JavaScript Plugin

1. Open the plugin settings (e.g., Settings > Insert Headers and Footers).
2. Replace `'Your_App_Key'` in the following HTML code with the App Key from your [UXCam platform](https://app.uxcam.com/integration)
3. In the Global Header or Before \<body> section, paste the following script, and save the changes:

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

## Step 2: Sending Events through the JavaScript Plugin

**Tracking Page Views**

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

<br />

**Tracking Button Clicks**

To track when users click a specific button (e.g., “Buy Now”), use this script and adjust the selector to match your button, and add this script globally:

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

<br />

## Step 3: Setting User Properties

To associate session data with known users, you can dynamically set user identity and attributes using Liquid tags. This is especially useful if your Shopify store includes a customer login system. This ensures UXCam sessions are tied to individual users, enabling more precise filtering, retention tracking, and user-level insights.

<br />

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

<br />

## Support

For questions or support, reach out to us at [team@uxcam.com](mailto:team@uxcam.com).