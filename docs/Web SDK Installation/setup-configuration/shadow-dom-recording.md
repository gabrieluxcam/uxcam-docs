---
title: Shadow DOM Recording
deprecated: false
hidden: false
metadata:
  robots: index
---
# Shadow DOM Recording

Record content that lives inside shadow roots, such as Web Components built with Lit, Stencil, or Angular's `ViewEncapsulation.ShadowDom`.

## Why you need it

A shadow root is a separate DOM tree attached to an element. Standard DOM observers cannot see into it, so without this feature any component that uses shadow DOM shows up empty in session replay. Common signs:

- Custom elements (for example `<my-button>`) render as blank boxes in replays
- Clicks land on elements that appear to have no content
- Carousels, date pickers, or design-system components are missing

Shadow DOM recording is **off by default**. Turn it on when your site uses Web Components.

---

## Setup

Set `recordShadowDom` to `true` in the options object at the end of the SDK snippet:

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
})('Your_App_Key', {
    recordShadowDom: true
});
</script>
```

No other changes are needed. The SDK finds shadow roots that already exist at page load and any that are attached later.

---

## Supported frameworks

Any library that uses the browser's native shadow DOM is supported. This includes:

| Framework or library | Notes |
|---|---|
| Vanilla Web Components (`attachShadow()`) | Open and closed roots |
| Lit | |
| Stencil | Includes Ionic components |
| Angular | Components using `ViewEncapsulation.ShadowDom` |
| Vue | Custom elements built with `defineCustomElement()` |
| Svelte | Components compiled with the `customElement` option |
| Microsoft FAST and Fluent UI Web Components | |
| Shoelace and Web Awesome | |
| Vaadin | |
| Salesforce LWC | Native shadow only. Synthetic shadow is not recorded, see [Limitations](#limitations) |
| Declarative shadow DOM (`<template shadowrootmode="open">`) | Server-rendered components |

Nested components, styles inside shadow roots (including `adoptedStyleSheets`), and DOM changes after page load are all recorded.

Occlusion works the same way inside shadow roots. Elements marked with `data-uxc="obfuscated"` and sensitive input types are hidden as usual. See [Occlusion - Hide Sensitive Data](occlusion-hide-sensitive-data#).

---

## Limitations

- **Closed declarative shadow DOM** cannot be captured. The browser attaches these roots without calling `attachShadow()`, so the SDK never gets a chance to see them. Use `shadowrootmode="open"` if you need them in replays.
- **Manual slot assignment** (`slot.assign()`) is not replayed. Slotted content that uses the default `slot="name"` attribute replays correctly.
- **Polyfilled or synthetic shadow DOM** is not recorded. Pages that replace the browser's native shadow DOM accessors (for example Salesforce LWC synthetic shadow or ShadyDOM) are detected automatically and shadow recording is skipped for that page. The rest of the session records normally.

---

## Configuration Reference

| Option | Type | Default | Description |
|---|---|---|---|
| `recordShadowDom` | `boolean` | `false` | Capture content inside shadow roots |

---

## Troubleshooting

| Problem | Solution |
|---|---|
| Custom elements are blank in replays | Confirm `recordShadowDom: true` is set on every page that uses Web Components. |
| One specific component is still blank | Check whether it uses `<template shadowrootmode="closed">` or `slot.assign()`. See [Limitations](#limitations). |
| Nothing inside shadow roots records on a page | The page may use a shadow DOM polyfill. Shadow recording is disabled on those pages by design. |

---

## FAQ

**Does this slow down my page?**
No. The performance impact is minimal, the same as recording the main document.

**Do I need it for Angular, React, or Vue apps?**
Only if your components use real shadow DOM. Angular's default `Emulated` encapsulation, React, and Vue do not, so most apps built with them work without this option.

**Does it work with iframe recording?**
Yes. Shadow roots inside same-origin iframes are captured on the parent page. For cross-origin iframes, set `recordShadowDom: true` in the child iframe's own SDK config.

## Support

For questions or support, reach out to us at [team@uxcam.com](mailto:team@uxcam.com).
