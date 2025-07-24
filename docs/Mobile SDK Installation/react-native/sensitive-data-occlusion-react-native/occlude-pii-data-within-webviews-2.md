---
title: Occlude PII Data within Webviews
deprecated: false
hidden: false
metadata:
  robots: index
---
# Occlude PII Data Within WebViews for React Native

WebViews in React Native can contain sensitive data that needs to be occluded before recording. This guide covers techniques for occluding Personally Identifiable Information (PII) within WebView content.

## Understanding WebView Occlusion Challenges

WebViews present unique challenges for data occlusion because:

* Content is loaded dynamically from web servers
* JavaScript can modify content after initial load
* Cross-platform differences in WebView behavior
* Content may contain sensitive forms and user data

## Basic WebView Occlusion

### Occluding the Entire WebView

```javascript
import React from 'react';
import { View } from 'react-native';
import { WebView } from 'react-native-webview';
import RNUxcam from 'react-native-ux-cam';

function SensitiveWebViewScreen() {
  return (
    <View style={{ flex: 1 }}>
      <WebView
        source={{ uri: 'https://example.com/sensitive-page' }}
        style={{ flex: 1 }}
        ref={(view) => RNUxcam.occludeSensitiveView(view)}
      />
    </View>
  );
}
```

### Using Configuration Object for WebView Occlusion

```javascript
import React from 'react';
import { WebView } from 'react-native-webview';
import { UXCamOcclusionType } from 'react-native-ux-cam/UXCamOcclusion';
import RNUxcam from 'react-native-ux-cam';

const configuration = {
  userAppKey: 'YOUR_API_KEY',
  occlusions: [
    {
      type: UXCamOcclusionType.Blur,
      blurRadius: 15,
      hideGestures: true,
      screens: ['SensitiveWebViewScreen'],
    },
  ],
};

RNUxcam.startWithConfiguration(configuration);
```

## JavaScript-Based Content Occlusion

### Injecting CSS to Hide Sensitive Elements

```javascript
import React from 'react';
import { View } from 'react-native';
import { WebView } from 'react-native-webview';
import RNUxcam from 'react-native-ux-cam';

function WebViewWithCSSOcclusion() {
  const injectedCSS = `
    /* Hide sensitive form fields */
    input[type="password"],
    input[name*="password"],
    input[name*="credit"],
    input[name*="card"],
    input[name*="ssn"],
    input[name*="social"],
    .sensitive-data,
    .private-info {
      -webkit-filter: blur(10px);
      filter: blur(10px);
      background-color: #f0f0f0 !important;
    }
    
    /* Hide sensitive text content */
    .sensitive-text {
      color: transparent !important;
      text-shadow: 0 0 8px rgba(0,0,0,0.5) !important;
    }
  `;

  const injectedJavaScript = `
    // Inject CSS for occlusion
    const style = document.createElement('style');
    style.textContent = \`${injectedCSS}\`;
    document.head.appendChild(style);
    
    // Notify React Native that occlusion is applied
    window.ReactNativeWebView.postMessage(JSON.stringify({
      type: 'OCCLUSION_APPLIED',
      timestamp: Date.now()
    }));
  `;

  return (
    <View style={{ flex: 1 }}>
      <WebView
        source={{ uri: 'https://example.com/sensitive-form' }}
        injectedJavaScript={injectedJavaScript}
        style={{ flex: 1 }}
      />
    </View>
  );
}
```

### Dynamic Content Occlusion

```javascript
import React from 'react';
import { View } from 'react-native';
import { WebView } from 'react-native-webview';
import RNUxcam from 'react-native-ux-cam';

function DynamicWebViewOcclusion() {
  const injectedJavaScript = `
    // Function to occlude sensitive content
    function occludeSensitiveContent() {
      // Hide password fields
      const passwordFields = document.querySelectorAll('input[type="password"]');
      passwordFields.forEach(field => {
        field.style.filter = 'blur(10px)';
        field.style.backgroundColor = '#f0f0f0';
      });
      
      // Hide credit card fields
      const cardFields = document.querySelectorAll('input[name*="card"], input[name*="credit"]');
      cardFields.forEach(field => {
        field.style.filter = 'blur(10px)';
        field.style.backgroundColor = '#f0f0f0';
      });
      
      // Hide SSN fields
      const ssnFields = document.querySelectorAll('input[name*="ssn"], input[name*="social"]');
      ssnFields.forEach(field => {
        field.style.filter = 'blur(10px)';
        field.style.backgroundColor = '#f0f0f0';
      });
      
      // Hide elements with sensitive classes
      const sensitiveElements = document.querySelectorAll('.sensitive, .private, .confidential');
      sensitiveElements.forEach(element => {
        element.style.filter = 'blur(8px)';
        element.style.backgroundColor = '#f0f0f0';
      });
    }
    
    // Apply occlusion on page load
    occludeSensitiveContent();
    
    // Apply occlusion when new content is added
    const observer = new MutationObserver(function(mutations) {
      mutations.forEach(function(mutation) {
        if (mutation.type === 'childList') {
          occludeSensitiveContent();
        }
      });
    });
    
    observer.observe(document.body, {
      childList: true,
      subtree: true
    });
    
    // Notify React Native
    window.ReactNativeWebView.postMessage(JSON.stringify({
      type: 'OCCLUSION_READY',
      timestamp: Date.now()
    }));
  `;

  return (
    <View style={{ flex: 1 }}>
      <WebView
        source={{ uri: 'https://example.com/dynamic-form' }}
        injectedJavaScript={injectedJavaScript}
        style={{ flex: 1 }}
      />
    </View>
  );
}
```

## Form-Specific Occlusion

### Occluding Login Forms

```javascript
import React from 'react';
import { View } from 'react-native';
import { WebView } from 'react-native-webview';
import RNUxcam from 'react-native-ux-cam';

function LoginWebViewScreen() {
  const injectedJavaScript = `
    // Occlude login form fields
    function occludeLoginForm() {
      const loginForm = document.querySelector('form[action*="login"], form[action*="signin"]');
      if (loginForm) {
        const inputs = loginForm.querySelectorAll('input');
        inputs.forEach(input => {
          if (input.type === 'password' || 
              input.name.toLowerCase().includes('password') ||
              input.name.toLowerCase().includes('passwd')) {
            input.style.filter = 'blur(10px)';
            input.style.backgroundColor = '#f0f0f0';
          }
        });
      }
    }
    
    // Apply on load and when forms are added
    occludeLoginForm();
    
    const observer = new MutationObserver(function(mutations) {
      mutations.forEach(function(mutation) {
        if (mutation.type === 'childList') {
          occludeLoginForm();
        }
      });
    });
    
    observer.observe(document.body, {
      childList: true,
      subtree: true
    });
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

### Occluding Payment Forms

```javascript
import React from 'react';
import { View } from 'react-native';
import { WebView } from 'react-native-webview';
import RNUxcam from 'react-native-ux-cam';

function PaymentWebViewScreen() {
  const injectedJavaScript = `
    // Occlude payment form fields
    function occludePaymentForm() {
      const sensitiveFields = [
        'input[name*="card"]',
        'input[name*="credit"]',
        'input[name*="cvv"]',
        'input[name*="cvc"]',
        'input[name*="expiry"]',
        'input[name*="expiration"]',
        'input[name*="account"]',
        'input[name*="routing"]',
        'input[name*="swift"]',
        'input[name*="iban"]'
      ];
      
      sensitiveFields.forEach(selector => {
        const fields = document.querySelectorAll(selector);
        fields.forEach(field => {
          field.style.filter = 'blur(12px)';
          field.style.backgroundColor = '#f0f0f0';
          field.style.color = 'transparent';
        });
      });
      
      // Also occlude any elements with payment-related classes
      const paymentElements = document.querySelectorAll('.payment, .card, .credit, .financial');
      paymentElements.forEach(element => {
        element.style.filter = 'blur(8px)';
        element.style.backgroundColor = '#f0f0f0';
      });
    }
    
    // Apply occlusion
    occludePaymentForm();
    
    // Watch for dynamic content
    const observer = new MutationObserver(function(mutations) {
      mutations.forEach(function(mutation) {
        if (mutation.type === 'childList') {
          occludePaymentForm();
        }
      });
    });
    
    observer.observe(document.body, {
      childList: true,
      subtree: true
    });
  `;

  return (
    <View style={{ flex: 1 }}>
      <WebView
        source={{ uri: 'https://example.com/payment' }}
        injectedJavaScript={injectedJavaScript}
        style={{ flex: 1 }}
      />
    </View>
  );
}
```

## Advanced Occlusion Techniques

### Selective Content Occlusion

```javascript
import React from 'react';
import { View } from 'react-native';
import { WebView } from 'react-native-webview';
import RNUxcam from 'react-native-ux-cam';

function SelectiveWebViewOcclusion() {
  const injectedJavaScript = `
    // Occlude specific content while keeping other content visible
    function selectiveOcclusion() {
      // Define sensitive patterns
      const sensitivePatterns = [
        /\\b\\d{4}[\\s-]?\\d{4}[\\s-]?\\d{4}[\\s-]?\\d{4}\\b/, // Credit card
        /\\b\\d{3}-\\d{2}-\\d{4}\\b/, // SSN
        /\\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Z|a-z]{2,}\\b/, // Email
        /\\b\\d{3}-\\d{3}-\\d{4}\\b/ // Phone
      ];
      
      // Function to occlude text nodes
      function occludeTextNodes(node) {
        if (node.nodeType === Node.TEXT_NODE) {
          const text = node.textContent;
          sensitivePatterns.forEach(pattern => {
            if (pattern.test(text)) {
              // Create a span with blurred text
              const span = document.createElement('span');
              span.style.filter = 'blur(8px)';
              span.style.backgroundColor = '#f0f0f0';
              span.textContent = text;
              node.parentNode.replaceChild(span, node);
            }
          });
        } else {
          node.childNodes.forEach(occludeTextNodes);
        }
      }
      
      // Apply to entire document
      occludeTextNodes(document.body);
    }
    
    // Apply selective occlusion
    selectiveOcclusion();
    
    // Watch for new content
    const observer = new MutationObserver(function(mutations) {
      mutations.forEach(function(mutation) {
        if (mutation.type === 'childList') {
          mutation.addedNodes.forEach(function(node) {
            if (node.nodeType === Node.ELEMENT_NODE) {
              occludeTextNodes(node);
            }
          });
        }
      });
    });
    
    observer.observe(document.body, {
      childList: true,
      subtree: true
    });
  `;

  return (
    <View style={{ flex: 1 }}>
      <WebView
        source={{ uri: 'https://example.com/mixed-content' }}
        injectedJavaScript={injectedJavaScript}
        style={{ flex: 1 }}
      />
    </View>
  );
}
```

## Cross-Platform Considerations

### Platform-Specific Occlusion

```javascript
import React from 'react';
import { View, Platform } from 'react-native';
import { WebView } from 'react-native-webview';
import RNUxcam from 'react-native-ux-cam';

function CrossPlatformWebViewOcclusion() {
  const platform = Platform.OS;

  const injectedJavaScript = `
    // Platform-specific occlusion
    const platform = '${platform}';
    
    function applyPlatformOcclusion() {
      if (platform === 'ios') {
        // iOS-specific occlusion
        document.body.style.webkitFilter = 'blur(5px)';
      } else {
        // Android-specific occlusion
        document.body.style.filter = 'blur(5px)';
      }
      
      // Common occlusion for both platforms
      const sensitiveElements = document.querySelectorAll('input[type="password"], .sensitive');
      sensitiveElements.forEach(element => {
        element.style.filter = 'blur(10px)';
        element.style.backgroundColor = '#f0f0f0';
      });
    }
    
    applyPlatformOcclusion();
  `;

  return (
    <View style={{ flex: 1 }}>
      <WebView
        source={{ uri: 'https://example.com/sensitive-content' }}
        injectedJavaScript={injectedJavaScript}
        style={{ flex: 1 }}
      />
    </View>
  );
}
```

## Error Handling and Fallbacks

```javascript
import React from 'react';
import { View } from 'react-native';
import { WebView } from 'react-native-webview';
import RNUxcam from 'react-native-ux-cam';

function RobustWebViewOcclusion() {
  const injectedJavaScript = `
    // Robust occlusion with error handling
    function robustOcclusion() {
      try {
        // Basic occlusion
        const passwordFields = document.querySelectorAll('input[type="password"]');
        passwordFields.forEach(field => {
          field.style.filter = 'blur(10px)';
          field.style.backgroundColor = '#f0f0f0';
        });
        
        // Fallback: occlude entire form if specific fields not found
        if (passwordFields.length === 0) {
          const forms = document.querySelectorAll('form');
          forms.forEach(form => {
            if (form.action && (form.action.includes('login') || form.action.includes('signin'))) {
              form.style.filter = 'blur(5px)';
            }
          });
        }
        
        // Notify success
        window.ReactNativeWebView.postMessage(JSON.stringify({
          type: 'OCCLUSION_SUCCESS',
          timestamp: Date.now()
        }));
        
      } catch (error) {
        // Fallback: occlude entire WebView content
        document.body.style.filter = 'blur(8px)';
        
        // Notify error
        window.ReactNativeWebView.postMessage(JSON.stringify({
          type: 'OCCLUSION_ERROR',
          error: error.message,
          timestamp: Date.now()
        }));
      }
    }
    
    // Apply robust occlusion
    robustOcclusion();
  `;

  const handleMessage = (event) => {
    try {
      const data = JSON.parse(event.nativeEvent.data);
      if (data.type === 'OCCLUSION_ERROR') {
        console.warn('WebView occlusion error:', data.error);
      }
    } catch (error) {
      console.error('Error parsing WebView message:', error);
    }
  };

  return (
    <View style={{ flex: 1 }}>
      <WebView
        source={{ uri: 'https://example.com/sensitive-content' }}
        injectedJavaScript={injectedJavaScript}
        onMessage={handleMessage}
        style={{ flex: 1 }}
      />
    </View>
  );
}
```

## TypeScript Support

```typescript
import React from 'react';
import { View } from 'react-native';
import { WebView, WebViewMessageEvent } from 'react-native-webview';
import RNUxcam from 'react-native-ux-cam';

interface WebViewOcclusionProps {
  url: string;
  onOcclusionApplied?: () => void;
}

function TypedWebViewOcclusion({
  url,
  onOcclusionApplied,
}: WebViewOcclusionProps) {
  const injectedJavaScript = `
    // TypeScript-friendly occlusion
    function applyOcclusion(): void {
      try {
        const sensitiveFields = document.querySelectorAll('input[type="password"], input[name*="card"]');
        sensitiveFields.forEach((field: Element) => {
          const input = field as HTMLInputElement;
          input.style.filter = 'blur(10px)';
          input.style.backgroundColor = '#f0f0f0';
        });
        
        window.ReactNativeWebView.postMessage(JSON.stringify({
          type: 'OCCLUSION_APPLIED',
          timestamp: Date.now()
        }));
      } catch (error) {
        console.error('Occlusion error:', error);
      }
    }
    
    applyOcclusion();
  `;

  const handleMessage = (event: WebViewMessageEvent) => {
    try {
      const data = JSON.parse(event.nativeEvent.data);
      if (data.type === 'OCCLUSION_APPLIED' && onOcclusionApplied) {
        onOcclusionApplied();
      }
    } catch (error) {
      console.error('Error parsing WebView message:', error);
    }
  };

  return (
    <View style={{ flex: 1 }}>
      <WebView
        source={{ uri }}
        injectedJavaScript={injectedJavaScript}
        onMessage={handleMessage}
        style={{ flex: 1 }}
      />
    </View>
  );
}
```

## Best Practices

1. **Always occlude sensitive forms**: Password, credit card, and personal information fields
2. **Use CSS filters**: More reliable than hiding elements completely
3. **Watch for dynamic content**: Use MutationObserver for content that loads after initial page load
4. **Provide fallbacks**: If specific occlusion fails, fall back to broader occlusion
5. **Test cross-platform**: Verify occlusion works on both iOS and Android
6. **Handle errors gracefully**: Don't let occlusion errors break the WebView functionality
7. **Consider performance**: Don't apply heavy occlusion to non-sensitive content

## Summary

WebView PII occlusion in React Native requires a combination of JavaScript injection, CSS filtering, and careful monitoring of dynamic content. Use the techniques outlined above to ensure sensitive data within WebViews is properly occluded while maintaining functionality.