---
title: Webview Tagging
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
This guide covers the steps to implement screen name tagging in webviews using UXCam for both Android and iOS platforms. Screen name tagging enhances the clarity of user journey visualisation within your app's webviews.

For tagging webviews, it is necessary to add some JS injection, here are some simplified examples you can check to accomplish this:

## Android Example:

### Step 1:  Create a Java WebAppInterface

To expose a method to JavaScript, create a Java class that acts as a bridge between the webview and the Android native code.

```java
public class WebAppInterface {
    @JavascriptInterface
    public void tagScreenName(String screenName) {
        UXCam.tagScreenName(screenName);
    }
}
```

### Step 2: Attach the Interface Class to the Webview

Attach the interface class to the webview, allowing JavaScript to call native methods.

```java
WebView webView = (WebView) findViewById(R.id.webview);
webView.addJavascriptInterface(new WebAppInterface(), "UXCamBridge");
```

### Step 3: Call the Java Method Through the Bridge

In your HTML code, call the Java method through the bridge you attached.

```html
<input type="button" value="Tag Screen" onClick="tagScreenName('screenName')" />

<script type="text/javascript">
    function tagScreenName(screenName) {
        UXCamBridge.tagScreenName(screenName);
    }
</script>

```

***

## iOS Example:

On iOS, we don't have easy solution for webview tagging as Android but there are some ways to do that. For this, first on ios, we need to observe the uxcam values using contentController.

### Step 1: Observe UXCam Values using WKUserContentController

In your iOS project, use WKUserContentController to observe the messages from web content. Create a WKWebViewConfiguration and attach it to the WKWebView.

```swift
let contentController = WKUserContentController()
contentController.add(self, name: "uxcam")
let config = WKWebViewConfiguration()
config.userContentController = contentController
self.webView = WKWebView(frame: .zero, configuration: config)

```

### Step 2: Pass Method Name and Parameters from JS to Native

Then on JS side, we need to pass method name and parameters that we need to use on native side and post that to native one using postMessage eg.

```swift
<script type="text/javascript">
     function tagScreenName(screenName) {
          const method = {
               name: "tagScreenName", // Method name
               param: "screenName" // Parameters for that method
          }
          window.webkit.messageHandlers["uxcam"].postMessage(method);
     }
</script>
```

### Step 3: Observe UXCam Values on the Webview Controller

And observe the uxcam values on the webview controller:

```swift
extension WebViewController: WKScriptMessageHandler {
    func userContentController(_ userContentController: WKUserContentController, didReceive message: WKScriptMessage) {
        guard message.name == "uxcam" else { return }
        guard let methodInfo = message.body as? [String: String] else { return }
        if let methodName = methodInfo["name"], methodName == "tagScreenName" {
            guard let screenName = methodInfo["param"] else { return }
            UXCam.tagScreenName(screenName)
        }
    }
}

```

## React Native Example:

There are two approaches to tag screens within a WebView:

<br />

### Option 1: Using URL path (recommended)

Automatically tag screens based on the URL path when navigation changes:

```javascript
import React from 'react';
import { WebView } from 'react-native-webview';
import RNUxcam from 'react-native-ux-cam';

const MyWebView = () => {
  const handleNavigationChange = (navState) => {
    if (navState.url) {
      const url = new URL(navState.url);
      const screenName = url.pathname || '/home';
      RNUxcam.tagScreenName(`WebView${screenName}`);
    }
  };

  return (
    <WebView
      source={{ uri: 'https://example.com/' }}
      onNavigationStateChange={handleNavigationChange}
    />
  );
};
```

<br />

### Option 2: Using page title

Tag screens based on the HTML document title:

```javascript
import React, { useRef } from 'react';
import { WebView } from 'react-native-webview';
import RNUxcam from 'react-native-ux-cam';

const MyWebView = () => {
  const webviewRef = useRef(null);

  const handleMessage = (event) => {
    const title = event.nativeEvent.data;
    RNUxcam.tagScreenName(title);
  };

  const handleNavigationChange = (navState) => {
    if (!navState.loading) {
      webviewRef.current?.injectJavaScript(`
        ReactNativeWebView.postMessage(document.title);
        true;
      `);
    }
  };

  return (
    <WebView
      ref={webviewRef}
      source={{ uri: 'https://example.com/' }}
      onNavigationStateChange={handleNavigationChange}
      onMessage={handleMessage}
    />
  );
};
```

<br />

## Flutter Example:

Webview has a delegate which gets called whenever the URL changes, which can be tracked to tag the screen name:

```javascript Dart
import 'package:flutter/material.dart';
import 'package:webview_flutter/webview_flutter.dart';
import 'package:uxcam_flutter/uxcam.dart';

class WebViewScreen extends StatefulWidget {
  @override
  _WebViewScreenState createState() => _WebViewScreenState();
}

class _WebViewScreenState extends State<WebViewScreen> {
  late WebViewController _controller;

  @override
  void initState() {
    super.initState();

    UXCam.startWithConfiguration(configuration);

    // Initialize WebView
    _controller = WebViewController()
      ..setJavaScriptMode(JavaScriptMode.unrestricted)
      ..setNavigationDelegate(
        NavigationDelegate(
          onPageStarted: (String url) {
            // Log the web page as a screen in UXCam
            UXCam.tagScreenName("WebView: $url");
            print("Navigated to: $url");
          },
        ),
      )
      ..loadRequest(Uri.parse("https://your-webpage.com"));
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("WebView Example")),
      body: WebViewWidget(controller: _controller),
    );
  }

```

<br />

This approach can be extended to other methods such as [sending events](https://developer.uxcam.com/docs/send-events) and occluding [sensitive views within the webview](https://developer.uxcam.com/docs/sensitive-views-inside-webviews).
