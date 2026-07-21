---
title: Occlude PII Data within Webviews
deprecated: false
hidden: false
metadata:
  robots: index
---
# Occlude PII Data Within WebViews for React Native

WebViews in React Native can contain sensitive data that needs to be occluded before recording. Here are the simplest and most effective ways to handle this.

## Option 1: HTML Class (Easiest)

If you have access to the HTML content, simply add the `uxcam-occlude` class to sensitive elements:

```html
<input
  type="password"
  class="uxcam-occlude"
  name="password"
  placeholder="Password"
/>
<input
  type="text"
  class="uxcam-occlude"
  name="credit_card"
  placeholder="Credit Card"
/>
<div class="uxcam-occlude">Sensitive information here</div>
```

That's it! UXCam will automatically occlude any element with this class.

> 🚧 Element-level masking has timing limitations
>
> The SDK masks `uxcam-occlude` elements by measuring their on-screen position on every captured frame. This means the mask can only exist while the element exists and has layout — see [Known Limitations](#known-limitations-of-element-level-occlusion) below before relying on this approach for PHI, payment, or other regulated data.

## Option 2: Inject CSS (When You Don't Have HTML Access)

```javascript
import React from 'react';
import { View } from 'react-native';
import { WebView } from 'react-native-webview';

function WebViewWithOcclusion() {
  const injectedCSS = `
    input[type="password"],
    input[name*="password"],
    input[name*="credit"],
    input[name*="card"] {
      filter: blur(10px) !important;
      background-color: #f0f0f0 !important;
    }
  `;

  const injectedJavaScript = `
    const style = document.createElement('style');
    style.textContent = \`${injectedCSS}\`;
    document.head.appendChild(style);
  `;

  return (
    <View style={{ flex: 1 }}>
      <WebView
        source={{ uri: 'https://example.com/login' }}
        injectedJavaScript={injectedJavaScript}
        style={{ flex: 1 }}
      />
    </View>
  );
}
```

## Option 3: Occlude the Entire WebView

```javascript
import React from 'react';
import { View } from 'react-native';
import { WebView } from 'react-native-webview';
import RNUxcam from 'react-native-ux-cam';

function SensitiveWebViewScreen() {
  return (
    <View style={{ flex: 1 }}>
      <WebView
        source={{ uri: 'https://example.com/payment' }}
        style={{ flex: 1 }}
        ref={(view) => RNUxcam.occludeSensitiveView(view)}
      />
    </View>
  );
}
```

The occlusion is applied natively to the WebView container, independently of the page content — it stays in place across every page load and navigation inside the WebView.

> 👍 Tip: keep your funnel visibility
>
> Occluding the whole WebView hides the video, not your analytics. Fire a custom event per step of the flow (`RNUxcam.logEvent('consult_step_completed')`) and tag your screens — you keep full drop-off visibility with zero sensitive data in the recording.

## Known Limitations of Element-Level Occlusion

Options 1 and 2 mask *elements inside the page*, so they are inherently tied to the page's DOM lifecycle. Two situations can expose content that you expect to be masked:

1. **Page navigations inside the WebView.** When the WebView navigates to a new document, the old page's elements are torn down and the new page's elements don't have layout yet. For roughly a second there is nothing to measure, so the mask drops and re-applies once the new page settles. If a sensitive value is visible during that window, it will appear in the recording. This is inherent to element-level masking — no SDK version or configuration eliminates it.
2. **Cross-origin iframes.** The SDK's scanner runs in the page's main frame only. Content rendered inside a cross-origin `<iframe>` (payment widgets, SSO forms, embedded checkouts) cannot be measured or masked at the element level — it fails silently.

**For PHI, payment, or other regulated flows, use Option 3 (occlude the entire WebView) as your primary protection.** It is applied natively at the container level, is immune to both cases above, and can be combined with `uxcam-occlude` as a defense-in-depth second layer. Register the occlusion in the WebView's `ref` callback (as shown above) so it is active from the very first captured frame.

## When to Use Each Approach

| Approach                   | When to Use                                                            | Complexity  |
| -------------------------- | ---------------------------------------------------------------------- | ----------- |
| **HTML Class**             | You control the HTML content; single-page, non-regulated data          | ⭐ (Easiest) |
| **Inject CSS**             | Third-party content you can't modify                                   | ⭐⭐          |
| **Occlude Entire WebView** | PHI/payment/regulated data, multi-page flows, iframes, unknown structure | ⭐⭐          |

## Common CSS Selectors

If you're injecting CSS, here are the most common selectors you'll need:

```css
/* Password fields */
input[type='password'],
input[name*='password'] {
  filter: blur(10px) !important;
}

/* Credit card fields */
input[name*='card'],
input[name*='credit'],
input[name*='cvv'],
input[name*='cvc'] {
  filter: blur(10px) !important;
}

/* Social security numbers */
input[name*='ssn'],
input[name*='social'] {
  filter: blur(10px) !important;
}
```

## TypeScript Support

```typescript
import React from 'react';
import { View } from 'react-native';
import { WebView } from 'react-native-webview';

interface WebViewOcclusionProps {
  url: string;
  cssSelectors?: string[];
}

function TypedWebViewOcclusion({
  url,
  cssSelectors = [],
}: WebViewOcclusionProps) {
  const defaultSelectors = [
    'input[type="password"]',
    'input[name*="card"]',
    'input[name*="credit"]',
  ];

  const allSelectors = [...defaultSelectors, ...cssSelectors];
  const css = allSelectors.join(',\n') + ' { filter: blur(10px) !important; }';

  const injectedJavaScript = `
    const style = document.createElement('style');
    style.textContent = \`${css}\`;
    document.head.appendChild(style);
  `;

  return (
    <View style={{ flex: 1 }}>
      <WebView
        source={{ uri: url }}
        injectedJavaScript={injectedJavaScript}
        style={{ flex: 1 }}
      />
    </View>
  );
}
```

## Best Practices

1. **Match the approach to the sensitivity** - Element-level masking for convenience; whole-WebView occlusion for PHI, payment, and regulated flows
2. **Use specific selectors** - Target only what you need to occlude
3. **Test navigations, not just pages** - Record a test session, navigate through the full flow (including page transitions inside the WebView), and review the replay frame by frame before sign-off
4. **Keep it simple** - Avoid complex JavaScript unless absolutely necessary

## Summary

The HTML class approach (`uxcam-occlude`) is the simplest method for lightly sensitive, single-page content. Use CSS injection when you don't control the HTML. For PHI, payment, or any flow where even a momentary exposure is unacceptable, occlude the entire WebView — element-level masking briefly drops during in-WebView page navigations and cannot see inside cross-origin iframes.
