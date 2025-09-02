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

## When to Use Each Approach

| Approach                   | When to Use                                 | Complexity  |
| -------------------------- | ------------------------------------------- | ----------- |
| **HTML Class**             | You control the HTML content                | ⭐ (Easiest) |
| **Inject CSS**             | Third-party content you can't modify        | ⭐⭐          |
| **Occlude Entire WebView** | Very sensitive content or unknown structure | ⭐⭐          |

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

1. **Start with HTML classes** - If you control the content, this is the simplest approach
2. **Use specific selectors** - Target only what you need to occlude
3. **Test thoroughly** - Verify occlusion works on both iOS and Android
4. **Keep it simple** - Avoid complex JavaScript unless absolutely necessary

## Summary

The HTML class approach (`uxcam-occlude`) is the simplest and most reliable method. Use CSS injection only when you don't have control over the HTML content, and occlude the entire WebView as a last resort for very sensitive content.