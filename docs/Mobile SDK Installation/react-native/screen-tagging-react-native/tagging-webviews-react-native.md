---
title: WebView Screen Tagging
deprecated: false
hidden: false
metadata:
  description: >-
    Tag screens inside React Native WebViews using URL navigation state changes
    and JavaScript bridges for meaningful analytics.
  robots: index
---
# WebView Screen Tagging

WebViews contain dynamic content that rarely maps 1-to-1 to native screens. Use URL changes or a JavaScript bridge to tag logical pages within your WebView.

## URL-Based Tagging

Track navigation state changes to tag WebView screens by URL:

```javascript components/WebViewScreen.js
import { WebView } from 'react-native-webview';
import { useFocusEffect } from '@react-navigation/native';
import RNUxcam from 'react-native-ux-cam';

function WebViewScreen() {
  useFocusEffect(
    React.useCallback(() => {
      RNUxcam.tagScreenName('WebView Content');
    }, [])
  );

  const handleNavigationStateChange = (navState) => {
    const { url, title } = navState;

    if (url.includes('/checkout')) {
      RNUxcam.tagScreenName('WebView - Checkout');
    } else if (url.includes('/profile')) {
      RNUxcam.tagScreenName('WebView - Profile');
    } else if (title) {
      RNUxcam.tagScreenName(`WebView - ${title}`);
    }
  };

  return (
    <WebView
      source={{ uri: 'https://your-webapp.com' }}
      onNavigationStateChange={handleNavigationStateChange}
    />
  );
}
```

## JavaScript Bridge Approach

For single-page apps where URL doesn't change, inject JavaScript to detect page transitions:

```javascript components/AdvancedWebView.js
import { WebView } from 'react-native-webview';
import RNUxcam from 'react-native-ux-cam';

function AdvancedWebView() {
  const injectedJavaScript = `
    let lastPath = location.pathname;
    setInterval(() => {
      if (location.pathname !== lastPath) {
        lastPath = location.pathname;
        window.ReactNativeWebView.postMessage(JSON.stringify({
          type: 'SCREEN_TAG',
          screenName: 'WebView - ' + document.title
        }));
      }
    }, 1000);
    true;
  `;

  const handleMessage = (event) => {
    try {
      const data = JSON.parse(event.nativeEvent.data);
      if (data.type === 'SCREEN_TAG') {
        RNUxcam.tagScreenName(data.screenName);
      }
    } catch (error) {
      console.warn('WebView message parsing failed:', error);
    }
  };

  return (
    <WebView
      source={{ uri: 'https://your-webapp.com' }}
      injectedJavaScript={injectedJavaScript}
      onMessage={handleMessage}
      javaScriptEnabled={true}
    />
  );
}
```

## Best Practices

| Approach | When to Use | Complexity |
|----------|-------------|------------|
| **URL-based** (`onNavigationStateChange`) | Multi-page sites where URL changes on navigation | Low |
| **JavaScript bridge** (`postMessage`) | Single-page apps, hash routing, or complex transitions | Medium |
| **Combined** | When some navigation changes URL and some doesn't | Medium |

**Tips:**
- Tag the initial WebView screen with `useFocusEffect` as a fallback
- Avoid overly granular WebView screen names — group similar pages
- Test on both iOS and Android as WebView behaviour may differ slightly
