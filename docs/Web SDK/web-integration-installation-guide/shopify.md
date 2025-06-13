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
Integrate the UXCam Web SDK into your Shopify site and track custom events using Liquid tags by following the steps below. Liquid tags let you insert Shopify data (like product or customer info) into JavaScript. This ensures events sent to UXCam are relevant and personalized.

<br />

### Prerequisites

* Active Shopify account with theme editing access
* UXCam App Key (found in UXCam dashboard under Settings > Project Settings)

<br />

<br />

## Step 1: Adding UXCam Web SDK to Shopify

<br />

### Access the Shopify Theme Editor

1. Log in to your Shopify Admin.
2. Go to Online Store > Themes.
3. Select your active theme and click Actions > Edit Code.

<Image align="center" src="https://files.readme.io/be2dbfd5991354fef4ea0087b0f2c2e4067b59a0aac5d672a9734faf58f50af1-Screenshot_2025-01-08_at_12.18.13.png" />

<br />

### Add the Web SDK Script

1. Replace `'Your_App_Key'` in the following HTML code with the App Key from your [UXCam platform](https://app.uxcam.com/integration)
2. Navigate to the Layout folder, locate the theme.liquid file, and add the following script before the closing \</head> tag:

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

3. Save the changes.

<br />

> 📘 **Note**
>
> Adding this script to the **theme.liquid** file ensures that the UXCam functionality is applied to every screen on your Shopify website.

<br />

## Step 2: Sending Custom Events

You can use Shopify’s Liquid conditions to load JavaScript on specific pages and send custom events based on user actions.

<br />

## Example: Sending Events on Product Pages

To track interactions specific to product pages, use Liquid tags to dynamically insert product information:

1. Open the **product.liquid** file under Templates.
2. Add the following script:

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

This will send an event whenever a product page is viewed, including the product's name, ID, and price.

<br />

## Example: Sending Events on the Checkout Page

To track checkout-specific actions, modify the checkout.liquid template:

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

## Example: Sending a Page View Event on All Pages

You can conditionally load JavaScript for specific pages in the theme.liquid file. For example:

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

## Identify Users

If you have a customer login system, use Liquid tags to set the user identity dynamically:

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

# Conclusion

This concludes the documentation for integrating the Web SDK. If you have any questions or need further assistance, please contact [team@uxcam.com](mailto:team@uxcam.com).