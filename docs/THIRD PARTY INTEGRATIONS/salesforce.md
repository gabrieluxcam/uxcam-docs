---
title: Salesforce
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
## Overview

This guide explains how to create a direct link from Salesforce to UXCam that dynamically\
includes the User ID, allowing you to quickly access user session data in UXCam.

### Why Use This Approach?

* No coding required – implemented directly in Salesforce.
* Easy to maintain – uses a simple formula field in Salesforce.
* Quick access – enables one-click navigation from Salesforce to the relevant UXCam\
  session list.

### Implementation Steps

### 1. Create a Hyperlink Formula Field in Salesforce

1. Navigate to Salesforce Setup 
2. Go to Object Manager > Select the object where you want to add the UXCam link (e.g.,\
   User, Lead, Account). 
3. Click on Fields & Relationships > New Field 
4. Select Formula as the field type. 
5. Click Next, then set the Formula Return Type to Text. 

### 2. Define the Formula

In the formula editor, enter the following formula:

```Text Formula
HYPERLINK( 

"https://app.uxcam.com/app/YOURAPPID/sessions/list/1?user_custom_property={“o”:“in”,“pn”:“kUXCam_UserIdentity”,“pv”:[“YOUR_USER_ID”],“tb”:“appuser”}",  
  "Click Here"  
)
```

### 3. Explanation of the Formula

* The UXCam URL points to the session list page of the application.
* The YourApp User ID (YourApp\_User\_Id) is dynamically appended to the URL as\
  a query parameter.
* The HYPERLINK function displays a "Click Here" link in Salesforce, which, when\
  clicked, opens the corresponding UXCam session list. 

### 4. Save and Test

1. Click Save to create the formula field. 
2. Open a record where the YourApp User ID is available. 
3. Click on the "Click Here" link and verify that it correctly redirects to the relevant UXCam\
   session page. 

## Expected Output in Salesforce

* A clickable link in Salesforce that dynamically fetches the correct UXCam session data.
* Example of how it will appear in Salesforce:
  * *Click Here →[Redirects to UXCam session list filtered by User ID]*

## Troubleshooting & Notes

* Ensure that the YourApp User ID field (YourApp\_User\_Id\_\_c) is correctly populated\
  in Salesforce.
* Replace YOURAPPID in the URL with the actual UXCam App ID for your organization.
* If the link does not work, double-check the query string formatting and confirm that the\
  YourApp User ID exists in UXCam.
