---
title: Occlusion - Hide Sensitive Data
deprecated: false
hidden: false
metadata:
  robots: index
---
Occlusion is a data privacy technique used to mask or hide sensitive information from being recorded or exposed during analytics tracking. In the context of the UXCam SDK, occlusion ensures that user inputs—such as passwords, credit card numbers, emails, and other personal data—are automatically or manually hidden from session recordings and logs. This helps protect user privacy and maintain compliance with data protection regulations like GDPR and CCPA.

<GitHubCallout type="note">Occluded input field texts are replaced with asterisks (\*\*\*). Occluded input field numbers are replaced with zeros (000)</GitHubCallout>

### Elements that are occluded by default

Inputs will be occluded by default if they meet any of the following criteria:

<Table align={["left","left","left"]}>
  <thead>
    <tr>
      <th>
        Input Types
      </th>

      <th>
        Input Names Containing
      </th>

      <th>
        Autocomplete Properties Containing
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        'password'
        'email'
        'tel'
        'hidden'
        'number'
        'hidden'
      </td>

      <td>
        'password'
        'cc-'
        'email'
      </td>

      <td>
        'cc-'
        'address'
        'phone'
        'email'
        'password'
      </td>
    </tr>
  </tbody>
</Table>

***

### Enabling Occlusion

Enables occlusion of sensitive data in URLs and query parameters.

**Occluding Query Parameters**\
Query parameters to be occluded should be listed under queryParams.

```javascript
occlusion: {  
  queryParams: ['product', 'userId']  
}

// Example  
// Input: http://www.uxcam.com/query?product=shoes&userId=321  
// Output:http://www.uxcam.com/query?product=_occluded_&userId=_occluded
```

**Occluding URLs**\
A custom function can be used to occlude parts of the URL before the query parameters.

```javascript
occlusion: {  
  url: function(url) {  
    // Custom logic to modify the URL  
    return url.replace(/\/invite\/\w+/, '/invite/:inviteId');  
  }  
}  
// Example  
// Input: http://www.uxcam.com/invite/12345  
// Output: http://www.uxcam.com/invite/:inviteId
```

**Occlusion of HTML Elements**\
To occlude specific HTML elements, add the attribute data-uxc="obfuscated" to the elements you want to occlude.

```javascript
<div data-uxc="obfuscated">Sensitive Content</div>
```