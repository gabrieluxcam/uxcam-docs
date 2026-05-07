---
title: Iframe Recording
deprecated: false
hidden: false
metadata:
  robots: index
---
# Iframe Recording Support

Record user interactions inside iframes with UXCam Web SDK.

**What's supported:**

| Scenario | Status | SDK in Iframe? |
|---|---|---|
| Same-site iframes (Level 1) | Stable | No |
| Cross-site iframes (Level 1) | **Experimental** | Yes |
| Nested iframes (Level 2+) | Not recommended | See [below](#3-nested-iframes-level-2) |

> **Note:** UXCam supports **Level 1 iframes only** — direct children of the main document. Deeper nesting is not officially supported.

---

## Prerequisites

- SDK loaded on the **parent page** (all scenarios)
- For cross-site iframes: SDK also loaded on the **child iframe page**
- A valid UXCam **App Key**

---

## 1. Same-Site Iframes

Same-site iframes share the same origin as the parent (same protocol, domain, and port). **Recording is enabled by default** — no extra setup needed.

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

<!-- Automatically recorded — no SDK needed inside the iframe -->
<iframe src="/checkout/payment-form.html"></iframe>
```

To **disable** same-site iframe recording use **recordIframes**:

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
    recordIframes: false
});
</script>
```

## 2. Cross-Site Iframes

> **[EXPERIMENTAL]** This feature is in beta. The API may change in future releases. Test thoroughly in staging before enabling in production.

> **Security Warning:** Before enabling cross-origin iframe recording, be aware that iframes communicating with the parent page via postMessage introduce potential security risks. Ensure your Content Security Policy (CSP) only permits trusted domains and correctly employs directives like `frame-src` or `frame-ancestors`. Failure to do so can lead to security breaches where an attacker embeds your website on their domain, gaining access to the recorded content. If unsure about CSP configurations, consult your security team or reach out to UXCam support.

Cross-site iframes are served from a **different origin** than the parent. The browser blocks direct DOM access, so UXCam uses a **postMessage bridge**: the child SDK captures DOM changes and sends them to the parent via `window.postMessage()`.

### Requirements

- SDK loaded on **both** parent and child pages with the **same App Key**
- `recordCrossOriginIframes: true` set **only in the child iframe**
- The parent automatically listens for cross-origin iframe messages — no extra config needed on the parent

### Parent Page

Standard SDK initialization — no cross-origin flags required:

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
    recordCrossOriginIframes: true,  // Required — enables postMessage mode
});
</script>
```

---

## 3. Nested Iframes (Level 2+)

### Iframe Hierarchy

```
MainDOM (Top-level document / Parent)
│
├── Iframe A  ← Level 1 (Child of MainDOM)       ✅ Supported
│   │
│   └── Iframe B  ← Level 2 (Child of Iframe A)  ⚠️ Not recommended
│       │
│       └── Iframe C  ← Level 3 ...              ⚠️ Not recommended
│
└── Iframe D  ← Level 1 (Child of MainDOM)       ✅ Supported
```

- **MainDOM** — Top-level document where the SDK is initialized
- **Level 1** — Direct children of MainDOM (Iframe A, Iframe D)
- **Level 2** — Iframes inside a Level 1 iframe (Iframe B inside Iframe A)
- **Level 3+** — Deeper nesting

**UXCam currently supports Level 1 iframes only.** Level 2+ may produce incomplete snapshots, unreliable behavior at cross-origin boundaries, and increased overhead. Support for nested iframes (Level 2+) is planned and will be available in a future release.

### Element Selectors and the `>>>` Boundary Delimiter

When recording iframe content, element selectors use `>>>` to represent an iframe document boundary. This delimiter separates the parent document path from the iframe document path, making it clear where one document ends and another begins.

**Example selectors:**

```
// Element in same-site iframe
body > div#app > iframe.checkout >>> body > form > input.email

// Element in nested iframe (Level 2)
body > div#app > iframe.outer >>> body > div > iframe.inner >>> body > div.content
```

Each `>>>` marks the crossing from one document into an iframe's document. This helps identify exactly which iframe boundary an element lives behind when reviewing session data.

---

## Configuration Reference

| Option | Type | Default | Description |
|---|---|---|---|
| `recordIframes` | `boolean` | `true` | Enable/disable same-origin iframe recording (parent page) |
| `recordCrossOriginIframes` | `boolean` | `false` | Enable cross-origin postMessage mode (**child iframe only**) [Experimental] |

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
| Iframe interactions not in replays | Verify SDK is on the parent page. For same-site: check `recordIframes` isn't `false`. For cross-site: ensure child has `recordCrossOriginIframes: true` and uses the same App Key. |
| Warning: "SDK detected in cross-origin iframe without recordCrossOriginIframes enabled" | Add `recordCrossOriginIframes: true` to the child iframe's SDK config. |

---

## Security

- **Origin validation** — Incoming `postMessage` events are checked against declared source origin
- **App Key validation** — Cross-origin messages must include a matching App Key
- **No direct DOM access** — Cross-origin iframes communicate exclusively via `postMessage`

**Best practices:**

- **Configure Content Security Policy (CSP)** — Use `frame-ancestors` to control which domains can embed your pages, and `frame-src` to restrict which origins your page can load in iframes. This prevents attackers from embedding your site on a malicious domain and intercepting recorded data.
  ```
  Content-Security-Policy: frame-ancestors 'self' https://trusted-parent.example.com;
  ```
- **Redact sensitive iframes** — Add `data-uxc="obfuscated"` to iframes containing sensitive content like payment forms, login pages, or personal data entry fields. This obscures the content in session replays while still capturing that the user interacted with the iframe.
- **Use the same App Key across parent and child** — Mismatched App Keys will cause the parent SDK to silently reject cross-origin messages. Verify both pages reference the same key.
- **Audit iframe sources regularly** — If your page dynamically loads iframes from external sources, periodically review which origins are being recorded to avoid unintentionally capturing data from untrusted domains.

---

## FAQ

**Do I need the SDK inside same-site iframes?**
No. The parent SDK records them automatically.

**Can I record third-party iframes I don't control?**
No. Cross-site recording requires the SDK inside the child iframe.

**Does each iframe create a separate session?**
No. All iframe events merge into the parent session.

**What if the parent SDK isn't loaded?**
The child SDK detects there's no parent and will not record.

**Are `srcdoc` iframes supported?**
Yes. They're treated as same-origin and recorded automatically.

**Are nested iframes supported?**
Only Level 1 (direct children of MainDOM). Level 2+ is not officially supported.