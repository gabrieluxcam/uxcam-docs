---
title: React Navigation & Wix Integration
deprecated: false
hidden: false
metadata:
  description: >-
    Integrate UXCam screen tagging with React Navigation v6, tab navigators,
    stack navigators with parameters, and Wix React Native Navigation.
  robots: index
---
# Navigation Framework Integration

This guide covers full screen tagging integration for the most popular React Native navigation libraries.

## React Navigation v6 (Recommended)

### Basic Screen Tagging

Use `useFocusEffect` to tag screens when they become visible:

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

  return (/* Your screen content */);
}
```

### Tab Navigator Handling

Tag each tab screen separately so analytics distinguish between tabs:

```javascript navigation/TabNavigator.js
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { useFocusEffect } from '@react-navigation/native';
import RNUxcam from 'react-native-ux-cam';

const Tab = createBottomTabNavigator();

function TabScreen({ route }) {
  useFocusEffect(
    React.useCallback(() => {
      const tabName = route.name;
      RNUxcam.tagScreenName(`${tabName} Tab`);
    }, [route.name])
  );

  return (/* Tab content */);
}
```

### Stack Navigator with Parameters

Include relevant context in screen names for richer analytics:

```javascript screens/ProductDetailsScreen.js
import { useFocusEffect, useRoute } from '@react-navigation/native';
import RNUxcam from 'react-native-ux-cam';

function ProductDetailsScreen() {
  const route = useRoute();

  useFocusEffect(
    React.useCallback(() => {
      const { category } = route.params || {};
      const screenName = category
        ? `Product Details - ${category}`
        : 'Product Details';

      RNUxcam.tagScreenName(screenName);
    }, [route.params])
  );

  return (/* Screen content */);
}
```

> ⚠️ **Avoid dynamic IDs in screen names** — `Product Details - Electronics` is useful for analytics; `Product Details - 12345` creates thousands of unique screens and fragments your data.

### NavigationContainer Global Handler

For apps that want a centralized tagging approach:

```javascript App.js
import { NavigationContainer } from '@react-navigation/native';
import RNUxcam from 'react-native-ux-cam';

function App() {
  const routeNameRef = React.useRef();
  const navigationRef = React.useRef();

  return (
    <NavigationContainer
      ref={navigationRef}
      onStateChange={() => {
        const currentRouteName = navigationRef.current?.getCurrentRoute()?.name;
        if (currentRouteName && currentRouteName !== routeNameRef.current) {
          RNUxcam.tagScreenName(currentRouteName);
          routeNameRef.current = currentRouteName;
        }
      }}
    >
      {/* Navigator tree */}
    </NavigationContainer>
  );
}
```

***

## React Native Navigation (Wix)

Use `componentDidAppear` for screen tagging with Wix navigation:

```javascript screens/HomeScreen.js
import { Navigation } from 'react-native-navigation';
import RNUxcam from 'react-native-ux-cam';

class HomeScreen extends Component {
  constructor(props) {
    super(props);
    Navigation.events().bindComponent(this);
  }

  componentDidAppear() {
    RNUxcam.tagScreenName('Home Dashboard');
  }

  render() {
    return (/* Screen content */);
  }
}
```

For functional components with Wix navigation:

```javascript
import { NavigationFunctionComponent } from 'react-native-navigation';
import RNUxcam from 'react-native-ux-cam';
import { useEffect } from 'react';

const HomeScreen: NavigationFunctionComponent = ({ componentId }) => {
  useEffect(() => {
    const listener = Navigation.events().registerComponentDidAppearListener(
      ({ componentName }) => {
        if (componentName === componentId) {
          RNUxcam.tagScreenName('Home Dashboard');
        }
      }
    );
    return () => listener.remove();
  }, [componentId]);

  return (/* Screen content */);
};
```

***

## Advanced Patterns

### Modal and Overlay Screens

Tag modals as separate screens so they appear in analytics:

```javascript components/ModalScreen.js
import { useEffect } from 'react';
import { Modal } from 'react-native';
import RNUxcam from 'react-native-ux-cam';

function ModalScreen({ visible, onClose }) {
  useEffect(() => {
    if (visible) {
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

When screen content loads asynchronously, tag after data arrives:

```javascript screens/DynamicScreen.js
import { useFocusEffect } from '@react-navigation/native';
import { useState, useEffect } from 'react';
import RNUxcam from 'react-native-ux-cam';

function DynamicScreen() {
  const [screenTitle, setScreenTitle] = useState(null);

  useFocusEffect(
    React.useCallback(() => {
      if (screenTitle) {
        RNUxcam.tagScreenName(`Category - ${screenTitle}`);
      }
    }, [screenTitle])
  );

  useEffect(() => {
    fetchCategoryData().then(data => setScreenTitle(data.name));
  }, []);

  return (/* Screen content */);
}
```

***

## Troubleshooting

### Duplicate Screen Tags

**Issue**: Same screen tagged multiple times with 0 s duration

```javascript
// ✅ Correct — tag once on focus, no cleanup tag
useFocusEffect(
  React.useCallback(() => {
    RNUxcam.tagScreenName('Home Dashboard');
  }, [])
);
```

### Navigation Timing Issues

**Issue**: Screen tagged before navigation animation completes

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

### Rapid Tab Switching

**Issue**: Tags < 300 ms apart collapse to 0 s duration

```javascript
// Debounced screen tagging
const useDebouncedScreenTag = (screenName, delay = 300) => {
  const timeoutRef = useRef(null);

  useFocusEffect(
    useCallback(() => {
      if (timeoutRef.current) clearTimeout(timeoutRef.current);
      timeoutRef.current = setTimeout(() => {
        RNUxcam.tagScreenName(screenName);
      }, delay);
      return () => {
        if (timeoutRef.current) clearTimeout(timeoutRef.current);
      };
    }, [screenName, delay])
  );
};
```
