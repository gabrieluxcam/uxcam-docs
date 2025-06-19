---
title: Tagging Screens Inside Webviews
deprecated: false
hidden: false
metadata:
  robots: index
---
### Step 1:  Create a Native JavaScript Interface

First, create a class in your native Android code that will act as a bridge to your WebView. This interface will contain the methods you want to expose to JavaScript.

```java
public class WebAppInterface {
    @JavascriptInterface
    public void tagScreenName(String screenName) {
        UXCam.tagScreenName(screenName);
    }
}
```

### Step 2: Attach the Interface to the WebView

Next, attach an instance of your interface class to the WebView, giving it a name (e.g., "UXCamBridge") that your JavaScript code will use to call it.

```java
WebView webView = (WebView) findViewById(R.id.webview);
webView.addJavascriptInterface(new WebAppInterface(), "UXCamBridge");
```

### Step 3: Call the Native Method from JavaScript

Finally, within your web content's JavaScript, you can now call the native method using the bridge name you defined in the previous step.

```html
<input type="button" value="Tag Screen" onClick="tagScreenName('screenName')" />

<script type="text/javascript">
    function tagScreenName(screenName) {
        UXCamBridge.tagScreenName(screenName);
    }
</script>

```

***