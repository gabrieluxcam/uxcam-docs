---
title: Shopify
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
Integrate the UXCam Web SDK into your Shopify site and track custom events using Liquid tags by following the steps below. Liquid tags let you insert Shopify data (like product or customer info) into JavaScript. This ensures events sent to UXCam are relevant and personalised.

### Prerequisites

* Active Shopify account with theme editing access
* UXCam App Key (found in UXCam dashboard under Settings > Project Settings)

<br />

## Step 1: Adding UXCam Web SDK to Shopify

### Access the Shopify Theme Editor

1. Log in to your Shopify Admin.
2. Go to Online Store > Themes.
3. Select your active theme and click Actions > Edit Code.

<Image align="center" src="https://files.readme.io/be2dbfd5991354fef4ea0087b0f2c2e4067b59a0aac5d672a9734faf58f50af1-Screenshot_2025-01-08_at_12.18.13.png" />

<br />

### Add the Web SDK Script

1. Replace `'Your_App_Key'` in the following HTML code with the App Key from your [UXCam platform](https://app.uxcam.com/integration)
2. Navigate to the Layout folder, locate the theme.liquid file, and add the following script before the closing \</head> tag
3. Save the changes.

```javascript
<script type="text/javascript" defer="">
  (function( appKey, opts ) {
    window.uxc = {
      __t: [],
      __ak: appKey,
      __o: opts,
      event: function( n, p ) {
        this.__t.push( [ 'event', n, p ] );
      },
      setUserIdentity: function( i ) {
        this.__t.push( [ 'setUserIdentity', i ] );
      },
      setUserProperty: function( k, v ) {
        this.__t.push( [ 'setUserProperty', k, v ] );
      },
      setUserProperties: function( p ) {
        this.__t.push( [ 'setUserProperties', p ] );
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
  } )( 'YOUR_APP_KEY', {} );
</script>
```

> 📘 **Note**
>
> Adding this script to the **theme.liquid** file ensures that the UXCam functionality is applied to every screen on your Shopify website.

<br />

## Step 2: Sending Custom Events

To track user interactions across different pages on your Shopify store, use Shopify’s Liquid syntax to conditionally inject JavaScript that triggers UXCam custom events.

<br />

### Tracking Product Page Views

To send events when users view product pages, helps monitor whenever a product page is viewed, including the product's name, ID, and price:

1. Open the product.liquid template under the Templates directory.
2. Insert a script that sends a custom event using product-specific data, such as:

```javascript
<script>
  document.addEventListener("DOMContentLoaded", function() {
    uxc.event("Product Viewed", {
      product_name: "{{ product.title }}",
      product_id: "{{ product.id }}"
    });
  });
</script>
```

<br />

### Tracking Checkout Interactions

To capture user activity during checkout, helps monitor checkout behaviour, such as total value and number of items:

1. Open the checkout.liquid template.
2. Add a script to send an event with checkout information:

```javascript
<script>
  document.addEventListener("DOMContentLoaded", function() {
    uxc.event("Checkout Started", {
      total_price: {{ checkout.total_price | money_without_currency }},
      item_count: {{ checkout.line_items.size }}
    });
  });
</script>
```

<br />

### Tracking Page Views Based on Template

To send page view events depending on the type of page, helps to send different events for each page type, making your session recordings and analytics more actionable.

1. Open the theme.liquid layout file.
2. Add conditional JavaScript based on the current template:

```javascript
<script>
  document.addEventListener("DOMContentLoaded", function() {
    {% if template == 'index' %}
      uxc.event("Home Page Viewed", {
        page_title: "Home Page"
      });
    {% elsif template == 'product' %}
      uxc.event("Product Page Viewed", {
        product_name: "{{ product.title }}"
      });
    {% elsif template == 'cart' %}
      uxc.event("Cart Viewed", {
        item_count: {{ cart.item_count }}
      });
    {% endif %}
  });
</script>
```

<br />

## Step 3: User ID and Properties

### Identify Users

To associate session data with known users, you can dynamically set user identity and attributes using Liquid tags. This is especially useful if your Shopify store includes a customer login system. This ensures UXCam sessions are tied to individual users, enabling more precise filtering, retention tracking, and user-level insights.

**Setting User Identity and Properties**

1. In your theme file (e.g., theme.liquid), insert the following script inside the \<head> tag.
2. Use Liquid conditions to check for a logged-in customer and inject their details:

```javascript
<script>
  document.addEventListener("DOMContentLoaded", function() {
    {% if customer %}
      uxc.setUserIdentity("{{ customer.id }}");
      uxc.setUserProperties({
        email: "{{ customer.email }}",
        name: "{{ customer.first_name }} {{ customer.last_name }}"
      });
    {% endif %}
  });
</script>
```

<br />

## Support

For questions or support, reach out to us at [team@uxcam.com](mailto:team@uxcam.com).