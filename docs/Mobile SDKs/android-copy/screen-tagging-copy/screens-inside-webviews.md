---
title: Screens inside Webviews
deprecated: false
hidden: false
metadata:
  robots: index
---
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