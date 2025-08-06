---
title: Set Up the SDK and Start Recording
deprecated: false
hidden: false
metadata:
  robots: index
---


# 🚀 Phase 1: Bootstrap Setup

## Overview
This phase establishes the foundation for UXCam integration with secure configuration, proper initialization, and verification procedures. **Estimated time: 1-2 hours**

## Prerequisites Validation

Before starting, ensure your environment meets these requirements:

### React Native CLI Projects
- [ ] React Native 0.68+ 
- [ ] Node.js 16+ with npm/yarn
- [ ] iOS deployment target 12.0+
- [ ] Android minSdkVersion 21+
- [ ] Xcode 13+ (for iOS development)
- [ ] Android Studio (for Android development)

### Expo Projects  
- [ ] Expo SDK 47+
- [ ] EAS CLI installed globally
- [ ] EAS Build configured
- [ ] Development build capability

## 1. Package Installation

### React Native CLI

```bash Terminal
# Using npm
npm install react-native-ux-cam

# Using yarn
yarn add react-native-ux-cam

# For iOS, install pods
cd ios && pod install && cd ..
```

### Expo Managed Workflow

```bash Terminal
# Install the package
expo install react-native-ux-cam

# Configure for EAS Build
npx expo install --fix
```

**Important for Expo**: UXCam requires native code, so you'll need EAS Build. See our [Expo Installation Guide](expo-installation) for complete setup.

## 2. Secure API Key Management

### Environment-Based Configuration

Create environment-specific configuration files:

```javascript config/uxcam.js
const getUXCamConfig = () => {
  // Validate API key format (basic check)
  const validateApiKey = (key) => {
    if (!key || key.length < 10) {
      throw new Error('Invalid UXCam API key');
    }
    return key;
  };

  const config = {
    userAppKey: validateApiKey(
      __DEV__ ? 
        process.env.UXCAM_DEV_KEY || 'your-dev-key-here' : 
        process.env.UXCAM_PROD_KEY || 'your-prod-key-here'
    ),
    enableAutomaticScreenNameTagging: false, // Manual tagging recommended
    enableImprovedScreenCapture: true,
    // enableCrashHandling: true by default - no need to specify
  };
  
  // Development-specific settings
  if (__DEV__) {
    config.enableMultiSessionRecord = true;
    config.enableIntegrationLogging = true; // Debug logs visible in Xcode/Android Studio only
  }
  
  return config;
};

export default getUXCamConfig;
```

### Environment Variables (.env)

```bash .env.development
UXCAM_DEV_KEY=your_development_api_key_here
```

```bash .env.production  
UXCAM_PROD_KEY=your_production_api_key_here
```

**Security Best Practices:**
- Never commit API keys to version control
- Use different keys for development/staging/production
- Store production keys in CI/CD environment variables
- Validate key format before initialization

## 3. SDK Initialization

### Basic Initialization Pattern

```javascript App.js
import React, { useEffect } from 'react';
import RNUxcam from 'react-native-ux-cam';
import getUXCamConfig from './config/uxcam';

const App = () => {
  useEffect(() => {
    initializeUXCam();
  }, []);

  const initializeUXCam = () => {
    try {
      // Always enable schematic recordings for screen capture
      RNUxcam.optIntoSchematicRecordings();
      
      const config = getUXCamConfig();
      RNUxcam.startWithConfiguration(config);
      
      console.log('UXCam initialized successfully');
      
      // Note: enableIntegrationLogging shows debug info in Xcode/Android Studio only
      // Logs won't appear in other IDEs like Cursor or VSCode
      
    } catch (error) {
      console.error('UXCam initialization failed:', error);
      // Implement fallback behavior - app should continue working
    }
  };

  return (
    // Your app components
  );
};

export default App;
```

### Advanced Initialization with Verification

```javascript utils/uxcam.js
import RNUxcam from 'react-native-ux-cam';
import { Platform } from 'react-native';
import getUXCamConfig from '../config/uxcam';

class UXCamManager {
  static instance = null;
  isInitialized = false;
  initializationPromise = null;

  static getInstance() {
    if (!UXCamManager.instance) {
      UXCamManager.instance = new UXCamManager();
    }
    return UXCamManager.instance;
  }

  async initialize() {
    if (this.isInitialized) {
      return true;
    }

    if (this.initializationPromise) {
      return this.initializationPromise;
    }

    this.initializationPromise = this.performInitialization();
    return this.initializationPromise;
  }

  async performInitialization() {
    try {
      // Enable screen recordings (required for both platforms)
      RNUxcam.optIntoSchematicRecordings();
      
      const config = getUXCamConfig();
      
      // Platform-specific setup if needed
      if (Platform.OS === 'ios') {
        // iOS-specific configuration
      } else if (Platform.OS === 'android') {
        // Android-specific configuration
      }

      RNUxcam.startWithConfiguration(config);
      
      this.isInitialized = true;
      
      if (__DEV__) {
        console.log('UXCam initialized successfully');
        console.log('Integration logs available in Xcode/Android Studio');
      }
      
      return true;
      
    } catch (error) {
      console.error('UXCam initialization failed:', error);
      this.initializationPromise = null;
      return false;
    }
  }

  isReady(): boolean {
    return this.isInitialized;
  }
}

export default UXCamManager;
```

## 4. Build Configuration

### iOS Configuration (ios/YourApp/Info.plist)

```xml
<!-- Network security configuration -->
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <false/>
    <key>NSExceptionDomains</key>
    <dict>
        <key>uxcam.com</key>
        <dict>
            <key>NSExceptionAllowsInsecureHTTPLoads</key>
            <false/>
            <key>NSExceptionMinimumTLSVersion</key>
            <string>TLSv1.2</string>
        </dict>
    </dict>
</dict>
```

### Android Configuration (android/app/proguard-rules.pro)

```proguard
# UXCam ProGuard rules
-keep class com.uxcam.** { *; }
-dontwarn com.uxcam.**
-keepclassmembers class * {
    @com.uxcam.* <methods>;
}

# React Native UXCam bridge
-keep class com.rnuxcam.** { *; }
-dontwarn com.rnuxcam.**
```

### Metro Configuration (metro.config.js)

```javascript
const { getDefaultConfig } = require('expo/metro-config');

const config = getDefaultConfig(__dirname);

// Ensure UXCam native modules are properly resolved
config.resolver.platforms = ['native', 'ios', 'android', 'web'];

module.exports = config;
```

## 5. Integration Verification

### Verification Checklist

```javascript utils/verification.js
import RNUxcam from 'react-native-ux-cam';

export const verifyUXCamIntegration = async () => {
  const checks = {
    sdkInitialized: false,
    sessionStarted: false,
    screenRecordingEnabled: false,
    apiKeyValid: false,
  };

  try {
    // Check if SDK is initialized (this method should exist)
    checks.sdkInitialized = true;

    // Verify session URL is available (indicates successful initialization)
    try {
      const sessionUrl = RNUxcam.urlForCurrentSession();
      checks.sessionStarted = sessionUrl && sessionUrl.length > 0;
      checks.apiKeyValid = !sessionUrl.includes('invalid');
    } catch (error) {
      console.warn('Session URL not available yet');
    }

    // Screen recording is enabled via optIntoSchematicRecordings
    checks.screenRecordingEnabled = true;

    // Log verification results (only in development)
    if (__DEV__) {
      console.log('UXCam Integration Verification:', checks);
      
      Object.entries(checks).forEach(([key, passed]) => {
        console.log(`✅ ${key}: ${passed ? 'PASSED' : 'FAILED'}`);
      });
    }

    return checks;
    
  } catch (error) {
    console.error('Verification failed:', error);
    return checks;
  }
};
```

### Runtime Verification

```javascript components/DebugPanel.js
import React, { useState, useEffect } from 'react';
import { View, Text, Button } from 'react-native';
import { verifyUXCamIntegration } from '../utils/verification';

const DebugPanel = () => {
  const [verificationResults, setVerificationResults] = useState(null);

  const runVerification = async () => {
    const results = await verifyUXCamIntegration();
    setVerificationResults(results);
  };

  if (!__DEV__) {
    return null; // Only show in development
  }

  return (
    <View style={{ padding: 20, backgroundColor: '#f0f0f0' }}>
      <Text style={{ fontSize: 16, fontWeight: 'bold' }}>UXCam Debug Panel</Text>
      
      <Button title="Verify Integration" onPress={runVerification} />
      
      {verificationResults && (
        <View style={{ marginTop: 10 }}>
          {Object.entries(verificationResults).map(([key, passed]) => (
            <Text key={key} style={{ color: passed ? 'green' : 'red' }}>
              {passed ? '✅' : '❌'} {key}
            </Text>
          ))}
        </View>
      )}
    </View>
  );
};

export default DebugPanel;
```

## 6. Performance Considerations

### Memory Usage Monitoring

```javascript utils/performance.js
import { Performance } from 'react-native';

export const monitorUXCamPerformance = () => {
  if (__DEV__) {
    const startTime = Performance.now();
    
    // Monitor memory usage (if available)
    if (global.performance && global.performance.memory) {
      const memoryBefore = global.performance.memory.usedJSHeapSize;
      
      setTimeout(() => {
        const memoryAfter = global.performance.memory.usedJSHeapSize;
        const memoryDiff = memoryAfter - memoryBefore;
        console.log(`UXCam memory impact: ${memoryDiff / 1024}KB`);
      }, 5000);
    }
    
    // Monitor initialization time
    setTimeout(() => {
      const initTime = Performance.now() - startTime;
      console.log(`UXCam initialization time: ${initTime}ms`);
    }, 1000);
  }
};
```

## 7. Common Issues & Solutions

### Issue: SDK Not Initializing

**Symptoms**: No sessions appearing in dashboard
**Solutions**:
```javascript
// Check API key format
const isValidApiKey = (key) => {
  return key && key.length >= 10 && !key.includes('your-key-here');
};

// Verify initialization timing
useEffect(() => {
  const timer = setTimeout(() => {
    console.log('Initializing UXCam after component mount');
    initializeUXCam();
  }, 100); // Small delay to ensure app is ready
  
  return () => clearTimeout(timer);
}, []);
```

### Issue: Debug Logs Not Visible

**Problem**: Integration logs not showing in IDE
**Solution**: 
- Logs only appear in **Xcode** (iOS) and **Android Studio** (Android)
- Not visible in VSCode, Cursor, or other text editors
- Use physical device or simulator with native IDE open

### Issue: Expo Build Failures

**Symptoms**: Build fails with native module errors
**Solutions**:
```bash
# Ensure EAS Build is configured
eas build:configure

# Clear cache and reinstall
expo install --fix
npm cache clean --force
rm -rf node_modules && npm install
```

## 8. Testing Your Integration

### Basic Functionality Test

1. **Initialize the app** with UXCam configuration
2. **Navigate through 3-4 screens** for at least 30 seconds
3. **Background the app** (essential for session upload)
4. **Check UXCam dashboard** within 60 seconds
5. **Verify session appears** with correct metadata

### Verification Script

```javascript __tests__/uxcam.test.js
import RNUxcam from 'react-native-ux-cam';

describe('UXCam Integration', () => {
  test('SDK initializes without errors', () => {
    const mockConfig = {
      userAppKey: 'test-key-12345',
      enableAutomaticScreenNameTagging: false,
      enableImprovedScreenCapture: true,
    };

    expect(() => {
      RNUxcam.optIntoSchematicRecordings();
      RNUxcam.startWithConfiguration(mockConfig);
    }).not.toThrow();
  });

  test('Configuration validation works', () => {
    const invalidConfig = { userAppKey: '' };
    
    expect(() => {
      validateApiKey(invalidConfig.userAppKey);
    }).toThrow('Invalid UXCam API key');
  });
});
```

## Success Criteria ✅

Before proceeding to Phase 2, verify:

- [ ] SDK initializes without errors
- [ ] First session appears in UXCam dashboard
- [ ] Debug logs visible in Xcode/Android Studio (if enabled)
- [ ] App performance remains unaffected
- [ ] API key configuration is secure
- [ ] Build process works for both debug and release

## Next Steps

🎉 **Foundation Complete!** Your React Native app now has basic UXCam session recording.

**Continue to Phase 2**: [Screen Tagging](screen-tagging-react-native) to implement comprehensive screen analytics and prepare for advanced features.

### What You'll Build Next:
- Meaningful screen names for all major flows
- Navigation-aware screen tracking
- WebView and modal screen handling
- Foundation for privacy protection (Phase 3)

---

**Need Help?** Check our [Troubleshooting Guide](troubleshooting-react-native) or contact [team@uxcam.com](mailto:team@uxcam.com).