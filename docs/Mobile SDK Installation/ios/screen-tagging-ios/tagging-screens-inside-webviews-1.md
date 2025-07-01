---
title: Tagging Screens Inside Webviews
deprecated: false
hidden: false
metadata:
  robots: index
---
# Create a JavaScript bridge in Swift

```swift
import WebKit
import UXCam

/// Handles `window.webkit.messageHandlers.UXCamBridge.postMessage("Name")`.
final class UXCamWebBridge: NSObject, WKScriptMessageHandler {
    func userContentController(_ userContentController: WKUserContentController,
                               didReceive message: WKScriptMessage) {
        guard message.name == "UXCamBridge",
              let screen = message.body as? String else { return }
        UXCam.tagScreenName(screen)
    }
}
```

> 🛠️ **Tip** – Keep one shared instance (e.g. static `let shared = UXCamWebBridge()`) so multiple web views reuse the same handler.

***

# Attach the bridge to your `WKWebView`

```swift
let webView = WKWebView(frame: .zero, configuration: {
    let config = WKWebViewConfiguration()
    let controller = WKUserContentController()
    controller.add(UXCamWebBridge(), name: "UXCamBridge")
    config.userContentController = controller
    return config
}())
```

*SwiftUI?* Wrap the above in `UIViewRepresentable` or use `WKWebView` inside a `UIViewControllerRepresentable`.

***

# Call the native bridge from JavaScript

```html
<input type="button" value="Tag Screen"
       onclick="UXCamBridge.tag('Checkout Step 1')" />

<script>
  window.UXCamBridge = {
    tag: function (name) {
      if (window.webkit && window.webkit.messageHandlers &&
          window.webkit.messageHandlers.UXCamBridge) {
        window.webkit.messageHandlers.UXCamBridge.postMessage(name);
      }
    }
  };
</script>
```

> 📘 **Best practice** – Call `UXCamBridge.tag()` on **route changes** (hash or history API) and after in‑page transitions to avoid 0 s duplicates.

***

# Alternative: evaluate JavaScript from Swift

If you own the page source, the above is simplest. If you *don’t* (third‑party checkout, CMS), push the tag from Swift:

```swift
webView.evaluateJavaScript("document.title") { result, _ in
    if let title = result as? String {
        UXCam.tagScreenName("WebView: \(title)")
    }
}
```

Run this logic in `webView(_:didFinish:)` or via `WKNavigationDelegate` whenever the URL changes.

***

# Verification checklist

1. Record a session through the hybrid flow.
2. Ensure each WebView step appears **once** with > 0 s duration.
3. Confirm names match the order of user navigation.
4. Rename in **Dashboard → Screens** if needed.

***

# Troubleshooting

| Symptom                      | Cause                               | Fix                                                               |
| ---------------------------- | ----------------------------------- | ----------------------------------------------------------------- |
| **Bridge ‘undefined’** in JS | Message handler name mismatch       | Use the same string –`"UXCamBridge"`– on both sides               |
| No name change on URL hash   | Single‑page app doesn’t reload view | Trigger `UXCamBridge.tag()` on hashchange/history listeners       |
| 0 s duplicate screens        | Tag called twice for same visual    | Debounce calls or check last sent name in JavaScript              |
| Crashes in WebKit on reload  | Handler added twice                 | Call `controller.removeScriptMessageHandler(forName:)` on dispose |

***