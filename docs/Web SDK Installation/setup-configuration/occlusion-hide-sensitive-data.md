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
      </td>

      <td>
        'password'
        'cc-'
        'email'
        'phone'
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

Password and hidden fields, and fields whose name or autocomplete contains `password` or `cc-`, are always masked. The other fields in the table can be shown with an `unmask` on the field itself; see [Sensitive input fields](#sensitive-input-fields).

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

***

### Masking page content

Choose what to hide with the `data-uxc` attribute in your HTML, or with CSS selectors in the `occlusion` option. Both use the same two values:

| Value | Effect |
|:------|:-------|
| `mask` | Hides the element and everything inside it. |
| `unmask` | Shows the element and everything inside it again, inside a masked area. |

`data-uxc="obfuscated"` still works. It means the same as `mask`.

A masked element looks like this in the replay:

- **Text:** every letter and digit becomes `*`. Spaces and punctuation stay, so the layout is kept.
- **Input values:** `******`, or `000000` for number fields.
- **Attributes:** `placeholder`, `title`, `aria-label`, `aria-description`, `alt` and `label`, and dropdown option values, are masked too.
- **Images and videos:** an empty box of the same size. Their URLs aren't recorded.
- **Everything else:** layout, styles, clicks and scrolling are still recorded, so replays and heatmaps keep working.

Masking happens in the browser, so masked content never reaches UXCam.

#### Mask specific elements

Add the attribute to the element:

```html
<div data-uxc="mask">Account balance: $1,234.56</div>
```

Or list CSS selectors, without changing your HTML:

```javascript
occlusion: {
  mask: ['.account-balance', '#checkout-summary']
}
```

#### Mask the whole page, then show selected parts

Mask everything with the `html` selector, then list the parts that should stay visible:

```javascript
occlusion: {
  mask: ['html'],
  unmask: ['header', 'nav', '.product-list']
}
```

The same with attributes:

```html
<html data-uxc="mask">
  <body>
    <header data-uxc="unmask">...</header>
  </body>
</html>
```

Use `html` rather than `body`, so the page title is masked too.

#### Which rule wins

UXCam checks the element, then each of its parents in turn. The first `mask` or `unmask` it finds, from an attribute or a selector, decides:

- An `unmask` inside a masked area shows that part.
- A `mask` inside an unmasked area hides that part again.
- If an element has both a `mask` and an `unmask`, for example because it matches selectors in both lists, it's masked.
- Content with no `mask` or `unmask` above it is recorded as usual, apart from the fields in the table above.

```html
<section data-uxc="mask">
  <p>Jane Doe</p>                             <!-- masked -->
  <div data-uxc="unmask">
    <p>Order status: shipped</p>              <!-- shown -->
    <p data-uxc="mask">Card ending 4242</p>   <!-- masked -->
  </div>
</section>
```

#### Sensitive input fields

- **Always masked:** password fields, hidden fields, and fields whose name or autocomplete contains `password` or `cc-`. No `unmask` can show them.
- **Masked unless you target the field:** email, phone and number fields, and fields whose name or autocomplete suggests an email, phone number or address. They stay masked inside an unmasked area. To show one, put the `unmask` on the field itself:

```javascript
occlusion: {
  mask: ['html'],
  unmask: ['input[type="number"]']  // show quantity fields
}
```

> 🚧 **Broad unmask selectors**
>
> A selector such as `input`, `p` or `*` matches many elements directly, including fields, and elements inside areas marked `data-uxc="mask"` or `data-uxc="obfuscated"`. Email, phone and number fields matched this way are shown. Prefer specific selectors, such as `#promo-code` or `.product-list`.

#### Iframes and shadow DOM

- Masking works inside shadow DOM: a mask on a component's host masks everything it renders, and an `unmask` inside the component still shows that part.
- Masking reaches into same-site iframes. `mask: ['html']` masks them too, because each iframe page has its own `<html>`.
- A cross-site iframe is recorded by the SDK in the iframe page, so it follows that page's own attributes and `occlusion` options. See [Iframe Recording: Privacy](/docs/iframe-recording#privacy).

#### Things to know

- Put the attributes in your HTML, or add them before the UXCam script loads. Adding or changing `data-uxc` later doesn't change what was already recorded.
- Invalid CSS selectors are ignored, and the SDK logs a warning in the browser console (unless `silent` is on).