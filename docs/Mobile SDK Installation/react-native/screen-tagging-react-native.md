---
title: Screen Tagging
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    The document explains the importance of manual screen tagging in React
    Native for better analytics, recommending the use of `useFocusEffect()` for
    tagging screens and providing guidance on tagging WebViews, as automatic
    tagging is inconsistent in React Native.
  robots: index
next:
  description: ''
---

# 📊 Phase 2: Screen Analytics (Tagging)

## Overview

Screen tagging transforms raw session recordings into actionable analytics by providing meaningful names for each user interface. **Proper screen tagging is essential for Phase 3 privacy protection and enables heat maps, funnels, and journey analysis.**

**Estimated time: 1-2 hours**  
**Prerequisites: Phase 1 (Bootstrap Setup) completed**

## Why Screen Tagging Matters

Good screen names unlock powerful UXCam features:

- **Heat Maps**: Understand user interaction patterns per screen
- **Conversion Funnels**: Track user progress through key flows
- **Journey Analytics**: Visualize complete user paths
- **Privacy Protection**: Enable screen-based occlusion rules (Phase 3)
- **Performance Analysis**: Measure screen load times and engagement

## 1. Screen Tagging Strategy

### Manual vs Automatic Tagging

> ⚠️ **Important**: For React Native, **manual tagging is strongly recommended** over automatic tagging due to React Native's navigation architecture.

```javascript
// Always set this in your configuration
const configuration = {
  userAppKey: 'YOUR_API_KEY',
  enableAutomaticScreenNameTagging: false, // Manual control recommended
  enableImprovedScreenCapture: true,
};
```

**Why Manual Tagging?**

- React Native navigation doesn't map 1:1 with native Activities/ViewControllers
- Provides consistent naming across iOS and Android
- Enables meaningful screen names instead of technical component names
- Required for effective privacy protection in Phase 3

### Screen Naming Best Practices

```javascript
// ✅ Good screen names
RNUxcam.tagScreenName('Home Dashboard');
RNUxcam.tagScreenName('Product Details');
RNUxcam.tagScreenName('Checkout Payment');
RNUxcam.tagScreenName('User Profile Settings');

// ❌ Avoid technical names
RNUxcam.tagScreenName('HomeComponent');
RNUxcam.tagScreenName('Screen1');
RNUxcam.tagScreenName('com.app.ProductView');
```

**Naming Conventions:**

- Use descriptive, human-readable names
- Include the primary function or content type
- Keep names consistent across similar screens
- Avoid special characters and numbers when possible
- Consider how names will appear in analytics dashboards

## 2. Navigation Framework Integration

### React Navigation v6 (Recommended)

React Navigation is the most popular navigation library and integrates seamlessly with UXCam:

```javascript screens/HomeScreen.js
import React from 'react';
import { useFocusEffect } from '@react-navigation/native';
import RNUxcam from 'react-native-ux-cam';

function HomeScreen() {
  useFocusEffect(
    React.useCallback(() => {
      RNUxcam.tagScreenName('Home Dashboard');
    }, [])
  );

  return (
    // Your screen content
  );
}

export default HomeScreen;
```

#### Centralized Screen Tagging Hook

```javascript hooks/useScreenTracking.js
import { useFocusEffect } from '@react-navigation/native';
import RNUxcam from 'react-native-ux-cam';
import { useCallback } from 'react';

export const useScreenTracking = (screenName) => {
  useFocusEffect(
    useCallback(() => {
      if (screenName) {
        RNUxcam.tagScreenName(screenName);
      }
    }, [screenName])
  );
};

// Usage in screens
function ProductScreen() {
  useScreenTracking('Product Details');

  return (
    // Screen content
  );
}
```

#### Tab Navigator Handling

```javascript navigation/TabNavigator.js
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { useFocusEffect } from '@react-navigation/native';

const Tab = createBottomTabNavigator();

function TabScreen({ route }) {
  useFocusEffect(
    React.useCallback(() => {
      // Tag based on active tab
      const tabName = route.name;
      RNUxcam.tagScreenName(`${tabName} Tab`);
    }, [route.name])
  );

  return (
    // Tab content
  );
}
```

#### Stack Navigator with Parameters

```javascript screens/ProductDetailsScreen.js
import { useFocusEffect, useRoute } from '@react-navigation/native';

function ProductDetailsScreen() {
  const route = useRoute();

  useFocusEffect(
    React.useCallback(() => {
      // Include relevant parameters in screen name
      const { productId, category } = route.params || {};
      const screenName = productId ?
        `Product Details - ${category}` :
        'Product Details';

      RNUxcam.tagScreenName(screenName);
    }, [route.params])
  );

  return (
    // Screen content
  );
}
```

### Expo Router Integration

```javascript app/(tabs)/home.js
import { useSegments, usePathname } from 'expo-router';
import { useEffect } from 'react';
import RNUxcam from 'react-native-ux-cam';

export default function HomeScreen() {
  const segments = useSegments();
  const pathname = usePathname();

  useEffect(() => {
    // Convert file-based routing to meaningful screen names
    const getScreenName = (path, segments) => {
      if (path === '/') return 'Home Dashboard';
      if (path.includes('/profile')) return 'User Profile';
      if (path.includes('/products')) return 'Product Catalog';

      // Fallback to segments
      return segments.join(' ').replace(/[()]/g, '') || 'Unknown Screen';
    };

    const screenName = getScreenName(pathname, segments);
    RNUxcam.tagScreenName(screenName);
  }, [pathname, segments]);

  return (
    // Screen content
  );
}
```

#### Expo Router Layout Integration

```javascript app/_layout.js
import { useSegments } from 'expo-router';
import { useEffect } from 'react';
import RNUxcam from 'react-native-ux-cam';

export default function RootLayout() {
  const segments = useSegments();

  useEffect(() => {
    // Global screen tracking for all routes
    if (segments.length > 0) {
      const screenName = formatScreenName(segments);
      RNUxcam.tagScreenName(screenName);
    }
  }, [segments]);

  const formatScreenName = (segments) => {
    // Convert file paths to readable names
    const nameMap = {
      '(tabs)': '',
      'home': 'Home Dashboard',
      'profile': 'User Profile',
      'settings': 'Settings',
      '[id]': 'Details'
    };

    return segments
      .map(segment => nameMap[segment] || segment)
      .filter(Boolean)
      .join(' - ') || 'Home';
  };

  return (
    // Layout content
  );
}
```

### React Native Navigation (Wix)

```javascript screens/HomeScreen.js
import { Navigation } from 'react-native-navigation';
import RNUxcam from 'react-native-ux-cam';

class HomeScreen extends Component {
  constructor(props) {
    super(props);

    // Bind navigation events
    Navigation.events().bindComponent(this);
  }

  componentDidAppear() {
    // Tag screen when it appears
    RNUxcam.tagScreenName('Home Dashboard');
  }

  componentDidDisappear() {
    // Optional: Handle screen disappearance
  }

  render() {
    return (
      // Screen content
    );
  }
}
```

## 3. Advanced Screen Tagging Patterns

### Modal and Overlay Screens

```javascript components/ModalScreen.js
import { useEffect } from 'react';
import { Modal } from 'react-native';
import RNUxcam from 'react-native-ux-cam';

function ModalScreen({ visible, onClose }) {
  useEffect(() => {
    if (visible) {
      // Tag modal as a separate screen
      RNUxcam.tagScreenName('Settings Modal');
    }
  }, [visible]);

  return (
    <Modal visible={visible} onRequestClose={onClose}>
      {/* Modal content */}
    </Modal>
  );
}
```

### Dynamic Screen Names

```javascript screens/DynamicScreen.js
import { useFocusEffect } from '@react-navigation/native';
import { useState, useEffect } from 'react';

function DynamicScreen({ route }) {
  const [screenTitle, setScreenTitle] = useState('Loading...');

  useFocusEffect(
    React.useCallback(() => {
      // Wait for dynamic content to load
      if (screenTitle !== 'Loading...') {
        RNUxcam.tagScreenName(`Dynamic - ${screenTitle}`);
      }
    }, [screenTitle])
  );

  useEffect(() => {
    // Simulate API call for dynamic content
    fetchScreenData().then(data => {
      setScreenTitle(data.title);
    });
  }, []);

  return (
    // Screen content
  );
}
```

### Conditional Screen Tagging

```javascript utils/screenTagging.js
import RNUxcam from 'react-native-ux-cam';

export const conditionalScreenTag = (baseScreenName, conditions = {}) => {
  let screenName = baseScreenName;

  // Add user state information
  if (conditions.isLoggedIn) {
    screenName += ' - Authenticated';
  } else {
    screenName += ' - Guest';
  }

  // Add feature flags
  if (conditions.premiumUser) {
    screenName += ' - Premium';
  }

  // Add A/B test variants
  if (conditions.variant) {
    screenName += ` - ${conditions.variant}`;
  }

  RNUxcam.tagScreenName(screenName);
};

// Usage
conditionalScreenTag('Product Details', {
  isLoggedIn: user.isAuthenticated,
  premiumUser: user.isPremium,
  variant: abTestVariant,
});
```

## 4. WebView Screen Tagging

### Basic WebView Integration

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
    // Tag based on WebView URL changes
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

### Advanced WebView Integration with JavaScript Bridge

```javascript components/AdvancedWebView.js
import { WebView } from 'react-native-webview';
import RNUxcam from 'react-native-ux-cam';

function AdvancedWebView() {
  const injectedJavaScript = `
    // Inject UXCam screen tagging into WebView
    window.tagUXCamScreen = function(screenName) {
      window.ReactNativeWebView.postMessage(JSON.stringify({
        type: 'SCREEN_TAG',
        screenName: screenName
      }));
    };

    // Auto-tag based on page changes
    let lastPath = location.pathname;
    setInterval(() => {
      if (location.pathname !== lastPath) {
        lastPath = location.pathname;
        window.tagUXCamScreen('WebView - ' + document.title);
      }
    }, 1000);

    true; // Required for injectedJavaScript
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

## 5. Screen Lifecycle Management

### Timing Considerations

```javascript utils/screenLifecycle.js
import RNUxcam from 'react-native-ux-cam';

class ScreenLifecycleManager {
  static currentScreen = null;
  static screenStartTime = null;

  static enterScreen(screenName) {
    // Track screen entry time
    this.screenStartTime = Date.now();
    this.currentScreen = screenName;

    // Tag the screen
    RNUxcam.tagScreenName(screenName);

    if (__DEV__) {
      console.log(`Entered screen: ${screenName}`);
    }
  }

  static exitScreen() {
    if (this.currentScreen && this.screenStartTime) {
      const duration = Date.now() - this.screenStartTime;

      if (__DEV__) {
        console.log(`Exited screen: ${this.currentScreen} (${duration}ms)`);
      }

      // Log screen duration as event
      RNUxcam.logEvent('Screen Duration', {
        screen: this.currentScreen,
        duration: duration
      });
    }

    this.currentScreen = null;
    this.screenStartTime = null;
  }
}

// Usage in screens
function ScreenWithLifecycle() {
  useFocusEffect(
    React.useCallback(() => {
      ScreenLifecycleManager.enterScreen('Product Details');

      return () => {
        ScreenLifecycleManager.exitScreen();
      };
    }, [])
  );

  return (
    // Screen content
  );
}
```

### Debounced Screen Tagging

```javascript hooks/useDebouncedScreenTag.js
import { useRef, useCallback } from 'react';
import { useFocusEffect } from '@react-navigation/native';
import RNUxcam from 'react-native-ux-cam';

export const useDebouncedScreenTag = (screenName, delay = 300) => {
  const timeoutRef = useRef(null);

  const debouncedTag = useCallback(() => {
    if (timeoutRef.current) {
      clearTimeout(timeoutRef.current);
    }

    timeoutRef.current = setTimeout(() => {
      RNUxcam.tagScreenName(screenName);
    }, delay);
  }, [screenName, delay]);

  useFocusEffect(
    useCallback(() => {
      debouncedTag();

      return () => {
        if (timeoutRef.current) {
          clearTimeout(timeoutRef.current);
        }
      };
    }, [debouncedTag])
  );
};
```

## 6. Screen Tagging Validation

### Development Tools

```javascript utils/screenValidation.js
import RNUxcam from 'react-native-ux-cam';

class ScreenValidator {
  static taggedScreens = new Set();
  static screenDurations = new Map();
  static duplicateDetector = new Map();

  static tagScreen(screenName) {
    // Validate screen name format
    if (!screenName || screenName.trim().length === 0) {
      console.warn('Invalid screen name: empty or null');
      return;
    }

    if (screenName.length > 100) {
      console.warn(`Screen name too long: ${screenName.length} characters`);
    }

    // Detect rapid duplicate tagging
    const now = Date.now();
    const lastTagged = this.duplicateDetector.get(screenName) || 0;

    if (now - lastTagged < 100) {
      // Less than 100ms
      console.warn(`Rapid duplicate screen tagging: ${screenName}`);
    }

    this.duplicateDetector.set(screenName, now);

    // Track for analytics
    this.taggedScreens.add(screenName);

    // Actually tag the screen
    RNUxcam.tagScreenName(screenName);

    if (__DEV__) {
      console.log(`Tagged screen: ${screenName}`);
    }
  }

  static getValidationReport() {
    return {
      totalScreensTagged: this.taggedScreens.size,
      screenNames: Array.from(this.taggedScreens),
      potentialIssues: this.findPotentialIssues(),
    };
  }

  static findPotentialIssues() {
    const issues = [];

    this.taggedScreens.forEach((screen) => {
      if (screen.includes('Screen') || screen.includes('Component')) {
        issues.push(`Technical name detected: ${screen}`);
      }

      if (/\d+$/.test(screen)) {
        issues.push(`Numbered screen name: ${screen}`);
      }
    });

    return issues;
  }
}

// Enhanced tagging function
export const tagScreen = (screenName) => {
  if (__DEV__) {
    ScreenValidator.tagScreen(screenName);
  } else {
    RNUxcam.tagScreenName(screenName);
  }
};
```

### Testing Screen Flow

```javascript __tests__/screenFlow.test.js
import { tagScreen } from '../utils/screenValidation';

describe('Screen Flow Testing', () => {
  test('Common user journey screens are tagged', () => {
    const commonFlow = [
      'Home Dashboard',
      'Product Catalog',
      'Product Details',
      'Shopping Cart',
      'Checkout Payment',
      'Order Confirmation',
    ];

    commonFlow.forEach((screen) => {
      expect(() => tagScreen(screen)).not.toThrow();
    });
  });

  test('Screen names follow naming convention', () => {
    const validNames = [
      'Home Dashboard',
      'User Profile Settings',
      'Product Details - Electronics',
    ];

    const invalidNames = ['Screen1', 'HomeComponent', 'com.app.ProductView'];

    validNames.forEach((name) => {
      expect(name).toMatch(/^[A-Z][a-zA-Z\s-]+$/);
    });
  });
});
```

## 7. Integration with Privacy Protection

### Preparing for Phase 3

Good screen names enable efficient privacy protection:

```javascript
// Phase 2: Screen tagging
RNUxcam.tagScreenName('Login Form');
RNUxcam.tagScreenName('Payment Details');
RNUxcam.tagScreenName('User Profile Settings');

// Phase 3: Privacy protection can target these screens
const privacyConfig = {
  type: 'blur',
  screens: ['Login Form', 'Payment Details', 'User Profile Settings'],
  blurRadius: 15,
};
```

### Screen-Based Privacy Rules

```javascript config/privacyScreens.js
// Define sensitive screens for Phase 3
export const SENSITIVE_SCREENS = [
  'Login Form',
  'Registration Form',
  'Payment Details',
  'Credit Card Entry',
  'Bank Account Setup',
  'Personal Information',
  'Password Change',
  'Security Settings',
];

export const BLUR_SCREENS = [
  'User Profile Settings',
  'Account Overview',
  'Transaction History',
];

// Use in screen tagging
export const tagScreenWithPrivacy = (screenName) => {
  RNUxcam.tagScreenName(screenName);

  // Log privacy classification for Phase 3 setup
  if (__DEV__) {
    if (SENSITIVE_SCREENS.includes(screenName)) {
      console.log(`🔒 Sensitive screen tagged: ${screenName}`);
    } else if (BLUR_SCREENS.includes(screenName)) {
      console.log(`🌫️ Blur screen tagged: ${screenName}`);
    }
  }
};
```

## 8. Performance Optimization

### Efficient Screen Tagging

```javascript utils/performantTagging.js
import RNUxcam from 'react-native-ux-cam';

// Cache screen names to avoid string operations
const SCREEN_NAMES = Object.freeze({
  HOME: 'Home Dashboard',
  PRODUCTS: 'Product Catalog',
  PRODUCT_DETAIL: 'Product Details',
  CART: 'Shopping Cart',
  CHECKOUT: 'Checkout Payment',
  PROFILE: 'User Profile',
});

// Batch screen transitions for rapid navigation
class ScreenTaggingBatch {
  static pendingTag = null;
  static batchTimeout = null;

  static tagScreen(screenName) {
    // Cancel any pending tag
    if (this.batchTimeout) {
      clearTimeout(this.batchTimeout);
    }

    this.pendingTag = screenName;

    // Batch rapid screen changes
    this.batchTimeout = setTimeout(() => {
      if (this.pendingTag) {
        RNUxcam.tagScreenName(this.pendingTag);
        this.pendingTag = null;
      }
    }, 50); // 50ms debounce
  }
}

export { SCREEN_NAMES, ScreenTaggingBatch };
```

## 9. Troubleshooting Common Issues

### Screen Names Not Appearing

**Issue**: Sessions show no screen names or generic names
**Solutions**:

```javascript
// Verify tagging is called
const debugTagScreen = (screenName) => {
  console.log(`Attempting to tag: ${screenName}`);
  RNUxcam.tagScreenName(screenName);

  // Verify after delay
  setTimeout(() => {
    console.log('Screen tagging completed');
  }, 100);
};
```

### Duplicate Screen Tags

**Issue**: Same screen tagged multiple times with 0 duration
**Solutions**:

```javascript
// Use focus/blur pattern correctly
useFocusEffect(
  React.useCallback(() => {
    RNUxcam.tagScreenName('Home Dashboard');

    // Don't tag in cleanup
    // return () => { /* cleanup */ };
  }, [])
);
```

### Navigation Timing Issues

**Issue**: Screen tagged before navigation completes
**Solutions**:

```javascript
// Add small delay for navigation completion
useFocusEffect(
  React.useCallback(() => {
    const timer = setTimeout(() => {
      RNUxcam.tagScreenName('Product Details');
    }, 50);

    return () => clearTimeout(timer);
  }, [])
);
```

## Success Criteria ✅

Before proceeding to Phase 3, verify:

- [ ] All major user flows have meaningful screen names
- [ ] No screens show 0-second duration consistently
- [ ] Screen names are human-readable and descriptive
- [ ] Navigation between screens creates logical flow
- [ ] Modal and overlay screens are properly tagged
- [ ] WebView screens have appropriate names
- [ ] No duplicate rapid screen tagging occurs

## Next Steps

🎯 **Screen Analytics Complete!** Your React Native app now provides meaningful screen-level insights.

**Continue to Phase 3**: [Privacy Protection](sensitive-data-occlusion-react-native/) to implement GDPR/CCPA compliant data collection using the screen names you've established.

### What You'll Build Next:

- Screen-based privacy rules using your tagged screen names
- Form field occlusion for sensitive data
- WebView privacy protection
- Compliance verification procedures

### Benefits of Your Screen Tagging:

- **Efficient Privacy Rules**: Use screen names for targeted occlusion
- **Better Analytics**: Heat maps and funnels now have meaningful labels
- **User Journey Insights**: Clear understanding of user flow patterns
- **Performance Tracking**: Screen-level engagement and timing metrics

---

**Need Help?** Check our [Troubleshooting Guide](troubleshooting-react-native) or contact [team@uxcam.com](mailto:team@uxcam.com).
