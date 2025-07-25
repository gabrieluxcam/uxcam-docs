---
title: Google Tag Manager
deprecated: false
hidden: false
metadata:
  robots: index
---
Using UXCam with Google Tag Manager (GTM) will allow you to efficiently manage and deploy the UXCam script across your website.

## Step 1: Adding the UXCam Script via Custom HTML Tag

1. Create a New Tag in GTM:

* Log in to your Google Tag Manager account.
* Go to the workspace for the website where you want to integrate UXCam.
* Click on "Tags" in the left-hand menu, then click on "New."

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

3. Set the Trigger:

* For the UXCam script to run on all pages, configure the trigger as "Page View - All Pages."
* This will ensure that the script is executed whenever a page is viewed on your website.

4. Save and Publish:

* After configuring the tag and trigger, save your changes.
* Click "Submit" to publish the changes, making the UXCam script active on your website.

***

## Step 2: Sending Events and properties to UXCam

You can use Google Tag Manager (GTM) to send custom events to UXCam. This includes sending `dataLayer` variables as event properties to help you track meaningful user actions with context. Whether you already have a `dataLayer` and variables configured, or you’re working with a basic setup, this guide covers both approaches:

**Using`dataLayer`**

To track user interactions using Google Tag Manager (GTM), there’s no need to manually implement a `dataLayer` if you’re only using GTM’s built-in triggers such as clicks, form submissions, scroll depth, element visibility, or timers. GTM handles these natively and can detect and fire tags based on these events without requiring any custom JavaScript or code changes. Manual `dataLayer.push()` are only necessary when tracking custom events or variables that aren’t detectable through GTM’s built-in functionality, such as backend-driven logic or specific user states (e.g user authentication, purchase confirmation, checkout completion etc.)

<GitHubCallout type="important">Apply one of the following approaches based on the set up of your product</GitHubCallout>

|       | Approaches                                                                                                                                                                                                                                                                                                                                                 |
| :---- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1** | **Teams Without a Data Layer Setup** - Need to [Push Data to Data Layer](https://developer.uxcam.com/docs/google-tag-manager#push-data-to-the-data-layer), [Define variables](https://developer.uxcam.com/docs/google-tag-manager#define-variables-in-gtm), and [Create event tags](https://developer.uxcam.com/docs/google-tag-manager#create-event-tags) |
| **2** | **Teams with Data Layer and User Variable already Setup** - Need to only [Create event tags](https://developer.uxcam.com/docs/google-tag-manager#create-event-tags)                                                                                                                                                                                        |
| **3** | **Using GMT built-in triggers** - Need to only [Create event tags](https://developer.uxcam.com/docs/google-tag-manager#create-event-tags)                                                                                                                                                                                                                  |

### Push Data to the Data Layer

`dataLayer.push()` is used to defines the event and supplies Data values to the GTM data layer when a user action happens.

```javascript
dataLayer.push({'variable_name': 'variable_value'})
```

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

### Define Variables in GTM

Variables pull specific pieces of information out of the `dataLayer` so they can be used inside Tags and Triggers.

* Click on "Variables" in the left-hand menu
* In the 'User-Defined Variables' section, click on 'New', and select 'Data Layer Variable'
* Give the variable a recognisable name in the top left of the screen (e.g. Item\_Price)
* Set the Data Layer Variable Name to match the key in the dataLayer.push (e.g., item, itemPrice)
* Save the Variable
* Repeat this for each data layer value you want to use

<Image align="center" src="https://files.readme.io/4252334dd396f15ddd8d9caff6aa569204f2f8caf9daf7606853f830be8e8a81-Cart_item_2.png" />

<GitHubCallout type="tip">Give your GTM variables clear names to stay organized. Read more about GTM variables [here](https://developers.google.com/tag-platform/tag-manager/datalayer)              .</GitHubCallout>

### Create Event Tags and set the triggers

The event tag defines the action of sending the event to UXCam

1. Create a New Tag for Events:

* Navigate to "Tags" in the left-hand menu, then click on "New."
* Select 'Tag configuration' - Tag Type: "Custom HTML."

2. Add Event Script inside the HTML box with the relevant values

> You can configure multiple events similarly by changing the event name and properties.

<HTMLBlock>{`
<script>
  uxc.event("Add to Cart", {
    "Item": {{cart_item}},
    "Item Price": {{cart_item_price}}
  });
</script>
`}</HTMLBlock>

<Image align="center" src="https://files.readme.io/19f54eaca76f3a5cbde508f13cabc2d44d84638d3044c4917763d565fc4759ca-tag_uxcam_.png" />

3. Set the Trigger:

* In case you want the event to fire when a user clicks a button; select from the list or create a new trigger:
* Click on 'Triggering' and add a new trigger by clicking on the plus (+) button
* Select 'Click - All Elements' from the list and select 'Some clicks' option on when the trigger fires
* Select the Click classes -> equals -> submitButton, and click 'Save'
* Select the new trigger to be added to the Tag, and Save

<GitHubCallout type="note">When referencing variable names, you refer to the name you gave to the variable in GTM (top-left-corner)</GitHubCallout>

<Image align="center" src="https://files.readme.io/61c35516267e9c55d2476e4d65e51008a0b1b81c7b05cabee2624f6ce80269f9-Cart_item.png" />

## Step 3: Test

Use Preview mode in GTM to trigger the event on your website. Steps to confirm:

* The tag fires when expected
* Variables are populated correctly
* Events show up in UXCam (in the session timeline or event page)
* Once confirmed, publish your GTM container.

## Step 4: Publish

* Publish your GTM container by clicking on 'Submit' button
* Review the changes and click on 'Publish'

<GitHubCallout type="note">Always sanitise and validate dynamic values being pushed to UXCam, and use clear naming for events and properties to stay consistent in UXCam reports.</GitHubCallout>

## Next steps - Setup and configuration

| Step                 | Task                                                                                           | Goal                                      |
| :------------------- | :--------------------------------------------------------------------------------------------- | :---------------------------------------- |
| <strong>1</strong>   | <strong>Configure occlusion </strong>  <a href="../setup-configuration/occlusion-hide-sensitive-data#">Jump →</a>                         | Hide sensitive user data                  |
| <strong>2</strong>   | <strong>Define Pages </strong>  <a href="../setup-configuration/define-pages-properties#">Jump →</a> | Group similar URLs under consistent names |
| <strong>3 </strong>  | <strong>Check Content Security Policy </strong>  <a href="../setup-configuration/configure-content-security-policy-csp#">Jump →</a>                     | Ensure the Web SDK functions correctly    |
| <strong>4 ★</strong> | <strong>Define App version </strong>  <a href="../setup-configuration/define-app-version-optional#">Jump →</a>                        | Debug issues across different releases    |

<GitHubCallout type="note">★ **Refer to optional steps**. Ship steps 1-3, to get the most value out of UXCam</GitHubCallout>

## Support

For questions or support, reach out to us at [team@uxcam.com](mailto:team@uxcam.com).