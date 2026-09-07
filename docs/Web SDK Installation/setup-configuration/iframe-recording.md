---
title: Iframe Recording
deprecated: false
hidden: false
metadata:
  robots: index
---
# Iframe Recording Support

Record user interactions inside iframes with the UXCam Web SDK.

**What's supported:**

| Scenario | Status | SDK in iframe? |
|---|---|---|
| Same-site iframes (Level 1) | Stable | No |
| Cross-site iframes (Level 1) | Stable | Yes |
| Nested iframes (Level 2+) | Not recommended | See [below](#3-nested-iframes-level-2) |

> **Note:** UXCam supports **Level 1 iframes only**, meaning direct children of the main document. Deeper nesting is not officially supported.

---

## Prerequisites

- SDK loaded on the **parent page** (all scenarios)
- For cross-site iframes: SDK also loaded on the **child iframe page**
- A valid UXCam **App Key**

---

## 1. Same-Site Iframes

Same-site iframes share the parent's origin (same protocol, domain, and port). **Recording is enabled by default**, so no extra setup is needed.

### Setup

Initialize the SDK on the parent page. Same-site iframes are recorded automatically:

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
})('Your_App_Key', {});
</script>

<!-- Recorded automatically. No SDK needed inside the iframe. -->
<iframe src="/checkout/payment-form.html"></iframe>
```

To **disable** same-site iframe recording, set `recordIframes` to `false` in the options object at the end of the snippet:

```javascript
})('Your_App_Key', {
    recordIframes: false
});
```

## 2. Cross-Site Iframes

> **Security Warning:** Before enabling cross-origin iframe recording, be aware that iframes communicating with the parent page via postMessage introduce potential security risks. Ensure your Content Security Policy (CSP) only permits trusted domains and correctly employs directives like `frame-src` or `frame-ancestors`. Otherwise an attacker could embed your website on their domain and gain access to the recorded content. If you are unsure about CSP configuration, consult your security team or reach out to UXCam support.

Cross-site iframes are served from a **different origin** than the parent. The browser blocks direct DOM access, so UXCam uses a **postMessage bridge**. The child SDK captures DOM changes and sends them to the parent with `window.postMessage()`. The parent merges them into its own session.

### Requirements

- SDK loaded on **both** parent and child pages with the **same App Key**
- `recordCrossOriginIframes: true` set **only in the child iframe**
- No extra config on the parent. It listens for cross-origin iframe messages automatically.

### Parent Page

Use the standard SDK snippet. No cross-origin flags are required:

```html
<!-- Parent: https://app.example.com -->
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
})('Your_App_Key', {});
</script>

<iframe src="https://third-party.example.com/widget.html"></iframe>
```

### Child Iframe Page

The child **must** set `recordCrossOriginIframes: true`.

```html
<!-- Child: https://third-party.example.com/widget.html -->
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
    recordCrossOriginIframes: true  // Required. Enables postMessage mode.
});
</script>
```

> **Note:** `recordCrossOriginIframes` only affects a page that is loaded inside a cross-origin iframe. Please don't add it at the top-level page as it will shutdown the SDK recording.

---

## Configuration Reference

| Option | Type | Default | Description |
|---|---|---|---|
| `recordIframes` | `boolean` | `true` | Enable or disable same-origin iframe recording (set on the parent page) |
| `recordCrossOriginIframes` | `boolean` | `false` | Enable cross-origin postMessage mode (set in the **child iframe only**) |

---

## Privacy

Redact sensitive iframe content with the `data-uxc` attribute:

```html
<iframe src="/payment-form.html" data-uxc="obfuscated"></iframe>
```

---

## Troubleshooting

| Problem | Solution |
|---|---|
| Iframe interactions not in replays | Verify the SDK is on the parent page. For same-site: check `recordIframes` is not `false`. For cross-site: ensure the child has `recordCrossOriginIframes: true` and uses the same App Key. |
| Warning: "SDK detected in cross-origin iframe without recordCrossOriginIframes enabled" | Add `recordCrossOriginIframes: true` to the child iframe's SDK config. |

---

## Security

- **Origin validation**: incoming `postMessage` events are checked against the declared source origin
- **App Key validation**: cross-origin messages must include a matching App Key
- **No direct DOM access**: cross-origin iframes communicate only through `postMessage`

**Best practices:**

- **Configure Content Security Policy (CSP).** Use `frame-ancestors` to control which domains can embed your pages, and `frame-src` to restrict which origins your page can load in iframes. This prevents attackers from embedding your site on a malicious domain and intercepting recorded data.
  ```
  Content-Security-Policy: frame-ancestors 'self' https://trusted-parent.example.com;
  ```
- **Redact sensitive iframes.** Add `data-uxc="obfuscated"` to iframes containing payment forms, login pages, or personal data entry fields. The content is obscured in session replays while the interaction itself is still captured.
- **Use the same App Key across parent and child.** Mismatched App Keys cause the parent SDK to silently reject cross-origin messages. Verify both pages reference the same key.
- **Audit iframe sources regularly.** If your page dynamically loads iframes from external sources, periodically review which origins are being recorded to avoid capturing data from untrusted domains.

---

## FAQ

**Do I need the SDK inside same-site iframes?**
No. The parent SDK records them automatically.

**Can I record third-party iframes I don't control?**
No. Cross-site recording requires the SDK inside the child iframe.

**Does each iframe create a separate session?**
No. All iframe events merge into the parent session.

**What if the parent SDK isn't loaded?**
The child SDK detects there is no parent and does not record.

**Are `srcdoc` iframes supported?**
Yes. They are treated as same-origin and recorded automatically.

**Are nested iframes supported?**
Only Level 1 (direct children of MainDOM). Level 2+ is not officially supported.
