---
title: Google Tag Manager
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
Using UXCam with Google Tag Manager (GTM) will allow you to efficiently manage and deploy the UXCam script across your website.

<br />

## Step 1: Adding the UXCam Script via Custom HTML Tag

<br />

1. Create a New Tag in GTM:

* Log in to your Google Tag Manager account.
* Go to the workspace for the website where you want to integrate UXCam.
* Click on "Tags" in the left-hand menu, then click on "New."

<br />

2. Configure the Tag:

* Select tag configuration - Tag Type: "Custom HTML."
* Replace `'Your_App_Key'` in the following HTML code with the App Key from your [UXCam platform](https://app.uxcam.com/integration)
* Paste the UXCam script into the HTML section, as shown below:

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
})('yourAppKey', {});
</script>
```

<br />

3. Set the Trigger:

* For the UXCam script to run on all pages, configure the trigger as "Page View - All Pages."
* This will ensure that the script is executed whenever a page is viewed on your website.

<br />

4. Save and Publish:

* After configuring the tag and trigger, save your changes.
* Click "Submit" to publish the changes, making the UXCam script active on your website.

<br />

***

<br />

## Step 2: Sending Events and properties to UXCam

You can use Google Tag Manager (GTM) to send custom events to UXCam. Whether you already have a data layer and variables configured, or you’re working with a basic setup, this guide covers both approaches:

<br />

<GitHubCallout type="important">Choose on the foloowing approaches based on your product set up</GitHubCallout>

| Approaches                                                                                                                                 |
| :----------------------------------------------------------------------------------------------------------------------------------------- |
| **<strong>Teams with Data Layer and User Variable already Setup</strong>  <a href="define-pages-properties-to-simplify-data#">Jump →</a>** |
| **<strong>Teams Without a Data Layer Setup</strong>  <a href="define-pages-properties-to-simplify-data#">Jump →</a>**                      |

<br />

<br />

<br />

## Step 2: Push Data to the Data Layer

On your website, push values to the GTM data layer when a user action happens.

<br />

```javascript
dataLayer.push({'variable_name': 'variable_value'})
```

<br />

**Example**

`**event**`: The name used to trigger your tag in GTM (e.g., ‘AddToCart)\
`**Item, ItemPrice**`: Values you want to send as event properties

```javascript
window.dataLayer = window.dataLayer || [];
window.dataLayer.push({  
  event: 'AddToCart',  
  Item: 'Shoes',  
  ItemPrice: 49.99  
});
```

<br />

<br />

## Step 3: Define Variables in GTM

* Click on "Variables" in the left-hand menu
* In the 'User-Defined Variables' section, click on 'New', and select 'Data Layer Variable'
* Give the variable a recognisable name in the top left of the screen (e.g. Item\_Price)
* Set the Data Layer Variable Name to match the key in the dataLayer.push (e.g., item, itemPrice)
* Save the Variable
* Repeat this for each data layer value you want to use

<Image align="center" src="https://files.readme.io/4252334dd396f15ddd8d9caff6aa569204f2f8caf9daf7606853f830be8e8a81-Cart_item_2.png" />

<br />

<GitHubCallout type="tip">Give your GTM variables clear names to stay organized. Read more about GTM variables [here](https://developers.google.com/tag-platform/tag-manager/datalayer)        .</GitHubCallout>

<br />

<br />

## Step 4: Create the UXCam Event Tag

1. Create a New Tag for Events:

* Navigate to "Tags" in the left-hand menu, then click on "New."
* Select 'Tag configuration' - Tag Type: "Custom HTML."

<br />

2. Add Event Script inside the HTML box with the relevant values

> You can configure multiple events similarly by changing the event name and properties.

<br />

<HTMLBlock>{`
<script>
  uxc.event("Add to Cart", {
    "Item": {{cart_item}},
    "Item Price": {{cart_item_price}}
  });
</script>
`}</HTMLBlock>

<Image align="center" src="https://files.readme.io/19f54eaca76f3a5cbde508f13cabc2d44d84638d3044c4917763d565fc4759ca-tag_uxcam_.png" />

<br />

3. Set the Trigger:

* In case you want the event to fire when a user clicks a button; select from the list or create the trigger:
  * Click on 'Triggering' and add a new trigger by clicking on the plus (+) button
  * Select 'Click - All Elements' from the list and select 'Some clicks' option on when the trigger fires
  * Select the Click classes -> equals -> submitButton, and click 'Save'
  * Select the new trigger to be added to the Tag, and Save

<br />

<GitHubCallout type="note">When referencing variable names, you refer to the name you gave to the variable in GTM (top-left-corner)</GitHubCallout>

<br />

<br />

<Image align="center" src="https://files.readme.io/61c35516267e9c55d2476e4d65e51008a0b1b81c7b05cabee2624f6ce80269f9-Cart_item.png" />

<br />

<br />

## Step 4: Test

Use Preview mode in GTM to trigger the event on your website. Steps to confirm:

* The tag fires when expected
* Variables are populated correctly
* Events show up in UXCam (in the session timeline or event page)
* Once confirmed, publish your GTM container.

<br />

<br />

## Step 5: Publish

* Publish your GTM container by clicking on 'Submit' button
* Review the changes and click on 'Publish'

<br />

<GitHubCallout type="note">Make sure the UXCam script is loaded before the event fires.\\\\\\\\\\\\\\\\\
Always sanitise and validate dynamic values being pushed to UXCam.\
Use clear naming for events and properties to stay consistent in UXCam reports.</GitHubCallout>

<br />

<br />

## Next steps - Setup and configuration

| Step                 | Task                                                                                           | Goal                                      |
| :------------------- | :--------------------------------------------------------------------------------------------- | :---------------------------------------- |
| <strong>1</strong>   | <strong>Configure occlusion </strong>  <a href="occlusion#">Jump →</a>                         | Hide sensitive user data                  |
| <strong>2</strong>   | <strong>Define Pages </strong>  <a href="define-pages-properties-to-simplify-data#">Jump →</a> | Group similar URLs under consistent names |
| <strong>3 </strong>  | <strong>Check Content Security Policy </strong>  <a href="csp#">Jump →</a>                     | Ensure the Web SDK functions correctly    |
| <strong>4 ★</strong> | <strong>Define App version </strong>  <a href="app-version#">Jump →</a>                        | Debug issues across different releases    |

<GitHubCallout type="note">★ **Optional step**\\\\\\\\\\\\\\\\\
Ship steps 1-3, to get the most value out of UXCam</GitHubCallout>

<br />

<br />

## Support

For questions or support, reach out to us at [team@uxcam.com](mailto:team@uxcam.com).