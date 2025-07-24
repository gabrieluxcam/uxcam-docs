---
title: Troubleshooting React Native UXCam
deprecated: false
hidden: false
metadata:
  robots: index
---
# 🔧 React Native UXCam Troubleshooting Guide

## Quick Diagnostic Checklist

Before diving into specific issues, run through this quick checklist:

* [ ] UXCam SDK version is compatible with your React Native version
* [ ] API key is valid and properly configured
* [ ] `optIntoSchematicRecordings()` is called before initialization
* [ ] App backgrounds for at least 5 seconds to trigger session upload
* [ ] Network connection is available during session upload
* [ ] Debug logs are checked in **Xcode/Android Studio** (not other IDEs)

## 1. SDK Initialization Issues

### Issue: "UXCam not initializing" / No sessions in dashboard

**Symptoms:**

* No sessions appearing in UXCam dashboard after 5+ minutes
* No initialization success logs
* App appears to work normally

**Debugging Steps:**

```javascript
// 1. Add detailed initialization logging
const debugUXCamInit = async () => {
  console.log('🔍 UXCam Debug: Starting initialization');
  
  try {
    // Verify API key format
    const apiKey = 'YOUR_API_KEY';
    console.log(`API Key length: ${apiKey.length}`);
    console.log(`API Key starts with: ${apiKey.substring(0, 5)}...`);
    
    if (apiKey === 'YOUR_API_KEY' || apiKey.length < 10) {
      throw new Error('Invalid API key detected');
    }

    // Always call this first
    RNUxcam.optIntoSchematicRecordings();
    console.log('✅ Schematic recordings enabled');

    const config = {
      userAppKey: apiKey,
      enableAutomaticScreenNameTagging: false,
      enableImprovedScreenCapture: true,
      enableIntegrationLogging: __DEV__, // Only in development
    };

    console.log('🔍 Configuration:', JSON.stringify(config, null, 2));

    await RNUxcam.startWithConfiguration(config);
    console.log('✅ UXCam initialization completed');

    // Verify session URL (may not be available immediately)
    setTimeout(async () => {
      try {
        const sessionUrl = await RNUxcam.urlForCurrentSession();
        console.log(`Session URL: ${sessionUrl}`);
      } catch (error) {
        console.log('Session URL not available yet (normal during initialization)');
      }
    }, 2000);

  } catch (error) {
    console.error('❌ UXCam initialization failed:', error);
  }
};
```

**Common Solutions:**

1. **Invalid API Key**
   ```javascript
   // ❌ Common mistakes
   const config = {
     userAppKey: 'YOUR_API_KEY', // Not replaced
     userAppKey: '', // Empty string
     userAppKey: undefined, // Undefined variable
   };

   // ✅ Correct format
   const config = {
     userAppKey: 'ux_xxxxxxxxxxxxxxxxxxxxx', // Actual key from dashboard
   };
   ```

2. **Missing optIntoSchematicRecordings()**
   ```javascript
   // ✅ Always call before startWithConfiguration
   RNUxcam.optIntoSchematicRecordings();
   RNUxcam.startWithConfiguration(config);
   ```

3. **Initialization Timing Issues**
   ```javascript
   // ✅ Initialize after app component mounts
   useEffect(() => {
     const timer = setTimeout(() => {
       initializeUXCam();
     }, 100); // Small delay ensures app is ready
     
     return () => clearTimeout(timer);
   }, []);
   ```

### Issue: "Integration logs not visible"

**Symptoms:**

* No UXCam logs in IDE console
* Unable to see initialization status

**Solution:**

* **Integration logs only appear in Xcode (iOS) and Android Studio (Android)**
* **Will NOT appear** in VSCode, Cursor, or other text editors
* Must run the app using the native IDE to see logs

```javascript
// Enable integration logging for development
const config = {
  userAppKey: 'YOUR_API_KEY',
  enableIntegrationLogging: __DEV__, // Shows logs in Xcode/Android Studio only
};
```

## 2. Session Recording Issues

### Issue: "Sessions not uploading" / "Session appears but no video"

**Symptoms:**

* Session metadata in dashboard but no video content
* Very short session durations (\< 5 seconds)
* "Processing" status that never completes

**Debugging Steps:**

```javascript
// Session upload debugging
const debugSessionUpload = () => {
  let sessionStartTime = Date.now();
  
  // Track session lifecycle
  const trackSessionEvents = () => {
    console.log('📱 App state monitoring started');
    
    // Monitor app state changes
    AppState.addEventListener('change', (nextAppState) => {
      console.log(`App state changed to: ${nextAppState}`);
      
      if (nextAppState === 'background') {
        const sessionDuration = Date.now() - sessionStartTime;
        console.log(`Session duration: ${sessionDuration}ms`);
        
        if (sessionDuration < 5000) {
          console.warn('⚠️ Session too short for reliable upload');
        } else {
          console.log('✅ Session duration sufficient for upload');
        }
      }
    });
  };
  
  trackSessionEvents();
};
```

**Common Solutions:**

1. **App Must Background for Upload**
   ```javascript
   // Sessions only upload when app goes to background
   // Ensure test flow includes:
   // 1. Navigate through app (30+ seconds)
   // 2. Press home button or switch apps
   // 3. Wait 30-60 seconds
   // 4. Check dashboard
   ```

2. **Network Issues**
   ```javascript
   // Check network connectivity
   import NetInfo from '@react-native-async-storage/async-storage';

   const checkNetworkForUXCam = async () => {
     const netInfo = await NetInfo.fetch();
     
     console.log('Network Status:', {
       isConnected: netInfo.isConnected,
       type: netInfo.type,
       isInternetReachable: netInfo.isInternetReachable
     });
     
     if (!netInfo.isConnected) {
       console.warn('⚠️ No network connection - sessions will upload when connection restored');
     }
   };
   ```

3. **iOS Simulator Issues**
   ```javascript
   // iOS Simulator may have recording limitations
   // Test on physical device for reliable results
   if (__DEV__ && Platform.OS === 'ios') {
     console.log('📱 Testing on iOS - use physical device for best results');
   }
   ```

### Issue: "Black screen recordings" / "No visual content captured"

**Symptoms:**

* Sessions upload but show black/blank screens
* Audio/gestures captured but no visual content

**Solutions:**

1. **Verify Schematic Recordings**
   ```javascript
   // Must be called before initialization
   const initWithProperRecording = () => {
     // ✅ Correct order
     RNUxcam.optIntoSchematicRecordings(); // FIRST
     RNUxcam.startWithConfiguration(config); // SECOND
     
     // ❌ Wrong order
     // RNUxcam.startWithConfiguration(config);
     // RNUxcam.optIntoSchematicRecordings(); // Too late
   };
   ```

2. **Check Screen Capture Settings**
   ```javascript
   const config = {
     userAppKey: 'YOUR_API_KEY',
     enableImprovedScreenCapture: true, // Essential for visual recording
   };
   ```

3. **iOS Privacy Settings**
   ```javascript
   // For iOS apps, ensure proper privacy permissions
   // Check Info.plist for required usage descriptions
   ```

## 3) Screen Tagging Issues

### Issue: "Screen names not appearing" / "Generic screen names"

**Symptoms:**

* Sessions show "Unknown Screen" or component names
* Screen durations show as 0 seconds
* Heat maps don't work properly

**Debugging Steps:**

```javascript
// Screen tagging debugger
const debugScreenTagging = (screenName) => {
  console.log(`🏷️ Attempting to tag screen: "${screenName}"`);
  
  // Validate screen name
  if (!screenName || screenName.trim().length === 0) {
    console.error('❌ Invalid screen name: empty or null');
    return;
  }
  
  if (screenName.length > 100) {
    console.warn(`⚠️ Screen name very long: ${screenName.length} characters`);
  }
  
  // Check for technical names
  const technicalPatterns = [
    /Component$/,
    /Screen$/,
    /View$/,
    /\d+$/,
    /^[a-z]/
  ];
  
  technicalPatterns.forEach((pattern, index) => {
    if (pattern.test(screenName)) {
      const warnings = [
        'Ends with "Component"',
        'Ends with "Screen"', 
        'Ends with "View"',
        'Ends with numbers',
        'Starts with lowercase'
      ];
      console.warn(`⚠️ Screen name issue: ${warnings[index]} - "${screenName}"`);
    }
  });
  
  // Actually tag the screen
  RNUxcam.tagScreenName(screenName);
  console.log(`✅ Screen tagged: "${screenName}"`);
  
  // Verify after delay
  setTimeout(() => {
    console.log(`🔍 Screen tagging completed for: "${screenName}"`);
  }, 100);
};

// Usage with React Navigation
useFocusEffect(
  React.useCallback(() => {
    debugScreenTagging('Home Dashboard');
  }, [])
);
```

**Common Solutions:**

1. **Proper useFocusEffect Usage**
   ```javascript
   // ✅ Correct - tags on every focus
   useFocusEffect(
     React.useCallback(() => {
       RNUxcam.tagScreenName('Product Details');
     }, [])
   );

   // ❌ Wrong - only tags on mount
   useEffect(() => {
     RNUxcam.tagScreenName('Product Details');
   }, []);
   ```

2. **Fix Rapid Screen Changes**
   ```javascript
   // Debounce rapid screen changes
   const useDebouncedScreenTag = (screenName, delay = 200) => {
     const timeoutRef = useRef(null);
     
     useFocusEffect(
       React.useCallback(() => {
         if (timeoutRef.current) {
           clearTimeout(timeoutRef.current);
         }
         
         timeoutRef.current = setTimeout(() => {
           RNUxcam.tagScreenName(screenName);
         }, delay);
         
         return () => {
           if (timeoutRef.current) {
             clearTimeout(timeoutRef.current);
           }
         };
       }, [screenName, delay])
     );
   };
   ```

3. **Handle Tab Navigation**
   ```javascript
   // For tab navigation, ensure unique names
   const TabScreen = ({ route, navigation }) => {
     useFocusEffect(
       React.useCallback(() => {
         const tabName = route.name;
         const parentRoute = navigation.getState().routeNames[0];
         RNUxcam.tagScreenName(`${tabName} Tab`);
       }, [route.name])
     );
   };
   ```

### Issue: "Duplicate screen tags" / "0 second screen durations"

**Symptoms:**

* Same screen appears multiple times in session
* Screens show 0-second durations
* Navigation analytics are incorrect

**Solutions:**

```javascript
// Prevent duplicate tagging
const useUniqueScreenTag = (screenName) => {
  const lastTaggedRef = useRef(null);
  
  useFocusEffect(
    React.useCallback(() => {
      if (lastTaggedRef.current !== screenName) {
        RNUxcam.tagScreenName(screenName);
        lastTaggedRef.current = screenName;
        console.log(`🏷️ New screen tagged: ${screenName}`);
      } else {
        console.log(`🔄 Screen already tagged: ${screenName}`);
      }
    }, [screenName])
  );
};
```

## 4. Privacy Protection Issues

### Issue: "Sensitive data still visible" / "Occlusion not working"

**Symptoms:**

* PII visible in session recordings
* Privacy overlays/blurs not appearing
* Text fields not occluded

**Debugging Steps:**

```javascript
// Privacy debugging helper
const debugPrivacyProtection = () => {
  console.log('🔒 Privacy Protection Debug');
  
  // Test overlay
  const testOverlay = () => {
    console.log('Testing overlay...');
    RNUxcam.applyOcclusion({ 
      color: 0xFF0000, // Bright red for visibility
      hideGestures: true 
    });
    
    setTimeout(() => {
      console.log('Removing test overlay...');
      RNUxcam.removeOcclusion();
    }, 3000);
  };
  
  // Test blur
  const testBlur = () => {
    console.log('Testing blur...');
    RNUxcam.applyBlur({ 
      blurRadius: 20, // High radius for visibility
      hideGestures: false 
    });
    
    setTimeout(() => {
      console.log('Removing test blur...');
      RNUxcam.removeBlur();
    }, 3000);
  };
  
  // Test text field occlusion
  const testTextFields = () => {
    console.log('Testing text field occlusion...');
    RNUxcam.occludeAllTextFields();
    
    setTimeout(() => {
      console.log('Removing text field occlusion...');
      RNUxcam.stopOccludingAllTextFields();
    }, 3000);
  };
  
  return { testOverlay, testBlur, testTextFields };
};
```

**Common Solutions:**

1. **Screen Name Mismatch**
   ```javascript
   // Ensure privacy screen names match tagged screen names exactly

   // ❌ Mismatch
   RNUxcam.tagScreenName('Login Screen'); // Tagged name
   const privacyConfig = {
     screens: ['Login Form'] // Different name - won't work
   };

   // ✅ Correct match
   RNUxcam.tagScreenName('Login Form'); // Tagged name
   const privacyConfig = {
     screens: ['Login Form'] // Matching name - will work
   };
   ```

2. **View Reference Issues**
   ```javascript
   // Ensure view reference is valid before occluding
   const SensitiveInput = () => {
     const inputRef = useRef(null);
     
     useEffect(() => {
       // Add delay to ensure ref is set
       const timer = setTimeout(() => {
         if (inputRef.current) {
           RNUxcam.occludeSensitiveView(inputRef.current);
           console.log('✅ View occluded');
         } else {
           console.error('❌ View ref is null');
         }
       }, 100);
       
       return () => clearTimeout(timer);
     }, []);
     
     return (
       <TextInput
         ref={inputRef}
         placeholder="Sensitive data"
         secureTextEntry={true}
       />
     );
   };
   ```

3. **Custom Component Occlusion**
   ```javascript
   // For custom components, ensure proper ref forwarding
   const CustomInput = React.forwardRef((props, ref) => (
     <View ref={ref}>
       <TextInput {...props} />
     </View>
   ));

   // Then use it normally
   const parentRef = useRef();
   useEffect(() => {
     if (parentRef.current) {
       RNUxcam.occludeSensitiveView(parentRef.current);
     }
   }, []);

   return <CustomInput ref={parentRef} />;
   ```

## 5) Expo-Specific Issues

### Issue: "UXCam not working in Expo" / "Native module not found"

**Symptoms:**

* "Native module RNUxcam not found" error
* Build failures with UXCam dependency
* Runtime crashes on UXCam method calls

**Solutions:**

1. **Ensure EAS Build is Configured**
   ```bash Terminal
   # Install EAS CLI globally
   npm install -g eas-cli

   # Login to Expo account
   eas login

   # Configure EAS build
   eas build:configure

   # Create development build
   eas build --platform ios --profile development
   eas build --platform android --profile development
   ```

2. **Check eas.json Configuration**
   ```json eas.json
   {
     "cli": {
       "version": ">= 0.52.0"
     },
     "build": {
       "development": {
         "developmentClient": true,
         "distribution": "internal"
       },
       "preview": {
         "distribution": "internal"
       },
       "production": {}
     }
   }
   ```

3. **Verify Package Installation**
   ```bash
   # Ensure proper installation
   expo install react-native-ux-cam

   # Clear cache and reinstall if needed
   expo install --fix
   rm -rf node_modules
   npm cache clean --force
   npm install
   ```

### Issue: "Expo Go compatibility"

**Important:** UXCam requires native code and **cannot run in Expo Go**. You must use EAS Build.

```javascript
// Add this check in your app
import Constants from 'expo-constants';

const isExpoGo = Constants.appOwnership === 'expo';

if (isExpoGo) {
  console.warn('⚠️ UXCam requires EAS Build - not compatible with Expo Go');
} else {
  // Initialize UXCam normally
  initializeUXCam();
}
```

## 6. Performance Issues

### Issue: "App performance degradation" / "High memory usage"

**Symptoms:**

* App feels slower after UXCam integration
* Increased memory usage
* Battery drain on device

**Debugging Steps:**

```javascript
// Performance monitoring
const monitorUXCamPerformance = () => {
  const startTime = performance.now();
  let initialMemory = null;
  
  // Monitor memory usage (if available)
  if (global.performance?.memory) {
    initialMemory = global.performance.memory.usedJSHeapSize;
  }
  
  // Track initialization time
  const trackInitTime = () => {
    const initTime = performance.now() - startTime;
    console.log(`🚀 UXCam initialization time: ${initTime.toFixed(2)}ms`);
    
    if (initTime > 1000) {
      console.warn('⚠️ UXCam initialization took longer than expected');
    }
  };
  
  // Track memory impact
  const trackMemoryImpact = () => {
    if (initialMemory && global.performance?.memory) {
      const currentMemory = global.performance.memory.usedJSHeapSize;
      const memoryDiff = (currentMemory - initialMemory) / 1024 / 1024;
      console.log(`📊 UXCam memory impact: ${memoryDiff.toFixed(2)}MB`);
    }
  };
  
  setTimeout(trackInitTime, 1000);
  setTimeout(trackMemoryImpact, 5000);
};
```

**Optimization Solutions:**

1. **Optimize Configuration**
   ```javascript
   // Performance-optimized configuration
   const config = {
     userAppKey: 'YOUR_API_KEY',
     enableAutomaticScreenNameTagging: false, // Reduces overhead
     enableImprovedScreenCapture: true,
     enableMultiSessionRecord: false, // Disable in production
     enableIntegrationLogging: false, // Disable in production
   };
   ```

2. **Efficient Screen Tagging**
   ```javascript
   // Use cached screen names
   const SCREEN_NAMES = Object.freeze({
     HOME: 'Home Dashboard',
     PRODUCT: 'Product Details',
     CART: 'Shopping Cart'
   });

   // Batch rapid screen changes
   const debouncedTagScreen = debounce((screenName) => {
     RNUxcam.tagScreenName(screenName);
   }, 100);
   ```

3. **Memory Management**
   ```javascript
   // Clean up references in components
   useEffect(() => {
     return () => {
       // Clean up any UXCam references
       // (Most cleanup is handled automatically)
     };
   }, []);
   ```

## 7) Build and Deployment Issues

### Issue: "ProGuard/R8 obfuscation conflicts"

**Symptoms:**

* Release builds crash with UXCam methods
* "Method not found" errors in production
* Works in debug but fails in release

**Solution:**

```proguard android/app/proguard-rules.pro
# UXCam ProGuard rules
-keep class com.uxcam.** { *; }
-dontwarn com.uxcam.**
-keepclassmembers class * {
    @com.uxcam.* <methods>;
}

# React Native UXCam bridge
-keep class com.rnuxcam.** { *; }
-dontwarn com.rnuxcam.**

# Keep UXCam native methods
-keepclassmembers class * {
    native <methods>;
}
```

### Issue: "Metro bundler conflicts"

**Symptoms:**

* Build fails during Metro bundling
* "Unable to resolve module" errors
* Dependency resolution issues

**Solution:**

```javascript metro.config.js
const { getDefaultConfig } = require('metro-config');

module.exports = (async () => {
  const {
    resolver: { sourceExts, assetExts },
  } = await getDefaultConfig();
  
  return {
    resolver: {
      assetExts: assetExts.filter(ext => ext !== 'svg'),
      sourceExts: [...sourceExts, 'svg'],
      platforms: ['native', 'ios', 'android', 'web'],
    },
    transformer: {
      babelTransformerPath: require.resolve('react-native-svg-transformer'),
    },
  };
})();
```

## 8. Event Tracking Issues

### Issue: "Events not appearing" / "Event properties missing"

**Symptoms:**

* Custom events don't show in dashboard
* Event properties are null or undefined
* Events appear with wrong timestamps

**Debugging Steps:**

```javascript
// Event tracking debugger
const debugEventTracking = (eventName, properties = {}) => {
  console.log(`📊 Tracking event: "${eventName}"`);
  console.log('Properties:', JSON.stringify(properties, null, 2));
  
  // Validate event name
  if (!eventName || eventName.trim().length === 0) {
    console.error('❌ Invalid event name');
    return;
  }
  
  // Validate properties
  Object.entries(properties).forEach(([key, value]) => {
    if (value === undefined || value === null) {
      console.warn(`⚠️ Property "${key}" is ${value}`);
    }
    
    if (typeof value === 'object' && value !== null) {
      console.warn(`⚠️ Property "${key}" is an object - should be primitive value`);
    }
  });
  
  // Track the event
  RNUxcam.logEvent(eventName, properties);
  console.log(`✅ Event tracked: "${eventName}"`);
};

// Usage
debugEventTracking('Product Viewed', {
  productId: 'abc123',
  category: 'Electronics',
  price: 299.99,
  userId: user.id
});
```

**Common Solutions:**

1. **Event Property Types**
   ```javascript
   // ✅ Correct property types
   RNUxcam.logEvent('Purchase Completed', {
     orderId: 'order_123', // string
     amount: 99.99, // number
     currency: 'USD', // string
     itemCount: 3, // number
     isFirstPurchase: true // boolean
   });

   // ❌ Incorrect property types
   RNUxcam.logEvent('Purchase Completed', {
     orderData: { id: 'order_123' }, // object - will be ignored
     amount: undefined, // undefined - will be ignored
     items: ['item1', 'item2'] // array - will be ignored
   });
   ```

2. **Event Timing**
   ```javascript
   // Ensure events are tracked after UXCam initialization
   const trackEventSafely = async (eventName, properties) => {
     try {
       // Wait for UXCam to be ready
       await new Promise(resolve => setTimeout(resolve, 1000));
       RNUxcam.logEvent(eventName, properties);
     } catch (error) {
       console.error('Event tracking failed:', error);
     }
   };
   ```

## 9) Platform-Specific Issues

### iOS-Specific Issues

1. **Simulator Recording Issues**
   ```javascript
   // iOS Simulator may have limited recording capabilities
   if (__DEV__ && Platform.OS === 'ios') {
     console.log('🍎 iOS Development - test on physical device for best results');
   }
   ```

2. **Privacy Permissions**
   ```xml Info.plist
   <!-- Add if using location or other sensitive data -->
   <key>NSLocationWhenInUseUsageDescription</key>
   <string>This app uses location for analytics purposes.</string>
   ```

### Android-Specific Issues

1. **Multiple Activity Issues**
   ```javascript
   // Ensure UXCam initialization in main activity
   // Check native Android documentation for multi-activity setup
   ```

2. **Background Processing**
   ```xml AndroidManifest.xml
   <!-- Ensure proper background processing permissions -->
   <uses-permission android:name="android.permission.INTERNET" />
   <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
   ```

## 10) Validation and Testing Tools

### Integration Testing Script

```javascript utils/integrationTest.js
export const runUXCamIntegrationTest = async () => {
  const results = {
    initialization: false,
    screenTagging: false,
    eventTracking: false,
    privacyProtection: false,
    sessionRecording: false
  };

  console.log('🧪 Running UXCam Integration Test...');

  try {
    // Test 1: Initialization
    RNUxcam.optIntoSchematicRecordings();
    await RNUxcam.startWithConfiguration({
      userAppKey: 'test-key-12345',
      enableIntegrationLogging: true
    });
    results.initialization = true;
    console.log('✅ Initialization test passed');

    // Test 2: Screen Tagging
    RNUxcam.tagScreenName('Integration Test Screen');
    results.screenTagging = true;
    console.log('✅ Screen tagging test passed');

    // Test 3: Event Tracking
    RNUxcam.logEvent('Integration Test Event', { testProp: 'testValue' });
    results.eventTracking = true;
    console.log('✅ Event tracking test passed');

    // Test 4: Privacy Protection
    RNUxcam.applyOcclusion({ color: 0x000000 });
    setTimeout(() => RNUxcam.removeOcclusion(), 1000);
    results.privacyProtection = true;
    console.log('✅ Privacy protection test passed');

    // Test 5: Session Recording (basic check)
    results.sessionRecording = true;
    console.log('✅ Session recording setup test passed');

  } catch (error) {
    console.error('❌ Integration test failed:', error);
  }

  // Summary
  const passedTests = Object.values(results).filter(Boolean).length;
  const totalTests = Object.keys(results).length;
  
  console.log(`\n📊 Integration Test Results: ${passedTests}/${totalTests} passed`);
  
  Object.entries(results).forEach(([test, passed]) => {
    console.log(`${passed ? '✅' : '❌'} ${test}`);
  });

  return results;
};
```

### Health Check Component

```javascript components/UXCamHealthCheck.js
import React, { useState, useEffect } from 'react';
import { View, Text, Button, ScrollView } from 'react-native';
import { runUXCamIntegrationTest } from '../utils/integrationTest';

const UXCamHealthCheck = () => {
  const [testResults, setTestResults] = useState(null);
  const [isRunning, setIsRunning] = useState(false);

  const runHealthCheck = async () => {
    setIsRunning(true);
    const results = await runUXCamIntegrationTest();
    setTestResults(results);
    setIsRunning(false);
  };

  if (!__DEV__) {
    return null; // Only show in development
  }

  return (
    <View style={{ padding: 20, backgroundColor: '#f5f5f5' }}>
      <Text style={{ fontSize: 18, fontWeight: 'bold', marginBottom: 15 }}>
        🏥 UXCam Health Check
      </Text>

      <Button
        title={isRunning ? "Running Tests..." : "Run Health Check"}
        onPress={runHealthCheck}
        disabled={isRunning}
      />

      {testResults && (
        <ScrollView style={{ marginTop: 15, maxHeight: 200 }}>
          {Object.entries(testResults).map(([test, passed]) => (
            <Text key={test} style={{ 
              color: passed ? 'green' : 'red',
              fontSize: 16,
              marginBottom: 5
            }}>
              {passed ? '✅' : '❌'} {test.replace(/([A-Z])/g, ' $1').trim()}
            </Text>
          ))}
        </ScrollView>
      )}
    </View>
  );
};

export default UXCamHealthCheck;
```

## Getting Additional Help

### Before Contacting Support

1. **Run the integration test** to identify specific failing components
2. **Check logs in Xcode/Android Studio** for detailed error messages
3. **Test on physical device** rather than simulator when possible
4. **Verify API key** and account status in UXCam dashboard
5. **Review recent code changes** that might have affected the integration

### Support Information

* **Email**: [team@uxcam.com](mailto:team@uxcam.com)
* **Include in your request**:
  * React Native version
  * UXCam SDK version
  * Platform (iOS/Android)
  * Error logs from native IDEs
  * Integration test results

### Useful Debug Information

When reporting issues, include this debug information:

```javascript
const getDebugInfo = () => {
  const info = {
    platform: Platform.OS,
    platformVersion: Platform.Version,
    reactNativeVersion: '0.73.0', // Update with your version
    uxcamVersion: '6.0.0', // Update with your version
    isDebugBuild: __DEV__,
    timestamp: new Date().toISOString()
  };
  
  console.log('🔍 Debug Information:');
  console.log(JSON.stringify(info, null, 2));
  
  return info;
};
```

This comprehensive troubleshooting guide should help resolve most common UXCam integration issues. Remember to check the specific platform documentation (iOS/Android) for platform-specific solutions.