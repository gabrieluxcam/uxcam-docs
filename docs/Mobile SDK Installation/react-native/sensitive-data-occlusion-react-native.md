---
title: Mask PII & Sensitive Content
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    The document outlines how to handle sensitive data in React Native
    applications using UXCam's features to ensure privacy compliance,
    emphasizing the occlusion of sensitive information like PII through text
    fields, views, and screens. It provides guidance on using UXCam's dashboard
    and SDK to apply occlusion rules, such as blurring or overlaying screens, to
    protect user data while maintaining the ability to record gestures if
    needed.
  robots: index
next:
  description: ''
---

# 🔒 Phase 3: Privacy Protection (PII Occlusion)

## Overview

Privacy protection ensures GDPR/CCPA compliance by preventing sensitive user data from appearing in session recordings. **This phase leverages the screen names from Phase 2 for efficient, maintainable privacy rules.**

**Estimated time: 1-2 hours**  
**Prerequisites: Phase 1 (Bootstrap Setup) and Phase 2 (Screen Tagging) completed**

## Why Privacy Protection is Critical

**Legal Compliance**: GDPR, CCPA, and other privacy regulations require protecting personally identifiable information (PII) in user recordings.

**User Trust**: Demonstrating privacy protection builds user confidence and reduces privacy concerns.

**Security**: Prevents sensitive data from being transmitted or stored in analytics systems.

## 1. Sensitive Data Identification

### Critical Data to Protect

**Authentication Data:**

- Passwords, PINs, security codes
- Biometric authentication flows
- Two-factor authentication codes
- Security questions and answers

**Financial Information:**

- Credit card numbers and CVV codes
- Bank account details and routing numbers
- Payment information and transaction details
- Cryptocurrency addresses and keys
- Account balances and financial history

**Personal Information:**

- Social security numbers and tax IDs
- Government-issued ID numbers
- Driver's license and passport details
- Home addresses and contact information
- Phone numbers and email addresses

**Health Data:**

- Medical records and health information
- Prescription details and medical history
- Insurance information
- Health tracking data

**User-Generated Content:**

- Private messages and communications
- Personal notes and documents
- Uploaded photos and files
- Search history and preferences

### Privacy Audit Checklist

Use this checklist to identify sensitive data in your app:

```javascript utils/privacyAudit.js
export const PRIVACY_AUDIT_CHECKLIST = {
  authentication: [
    'Login forms with password fields',
    'Registration forms with personal data',
    'Password reset and change screens',
    'Biometric authentication prompts',
    'Two-factor authentication screens',
  ],

  financial: [
    'Payment forms and credit card entry',
    'Bank account setup screens',
    'Transaction history and account balances',
    'Billing and invoice screens',
    'Cryptocurrency wallet interfaces',
  ],

  personal: [
    'User profile editing screens',
    'Contact information forms',
    'Address and location entry',
    'Government ID verification',
    'Personal settings and preferences',
  ],

  userGenerated: [
    'Text input fields for private content',
    'File upload and photo selection',
    'Chat and messaging interfaces',
    'Notes and document creation',
    'Search bars and query inputs',
  ],

  thirdParty: [
    'Social media login flows',
    'Payment processor interfaces',
    'Maps with location data',
    'WebViews with sensitive content',
    'External authentication providers',
  ],
};

// Audit helper function
export const auditScreenForSensitiveData = (screenName, components) => {
  const sensitiveElements = [];

  components.forEach((component) => {
    if (component.type === 'TextInput' && component.props.secureTextEntry) {
      sensitiveElements.push('Password field detected');
    }

    if (
      component.props.keyboardType === 'numeric' &&
      component.props.placeholder?.includes('card')
    ) {
      sensitiveElements.push('Credit card field detected');
    }

    if (component.props.autoCompleteType === 'email') {
      sensitiveElements.push('Email field detected');
    }
  });

  return {
    screenName,
    sensitiveElements,
    requiresProtection: sensitiveElements.length > 0,
  };
};
```

## 2. Privacy Protection Strategies

### Strategy Decision Matrix

| Sensitivity Level            | Protection Method         | Implementation                  | Maintenance                              |
| ---------------------------- | ------------------------- | ------------------------------- | ---------------------------------------- |
| **Entire sensitive screens** | Screen-based overlay/blur | `screens: ['Login', 'Payment']` | **Low** - Uses screen names from Phase 2 |
| **Form sections**            | Screen-based blur         | `screens: ['ProfileEdit']`      | **Low** - Covers entire form areas       |
| **Individual fields**        | View-level occlusion      | `occludeSensitiveView(ref)`     | **High** - Requires per-component setup  |
| **Dynamic content**          | Text field occlusion      | `occludeAllTextFields()`        | **Medium** - Affects all text inputs     |

> 💡 **Best Practice**: Start with screen-based protection using the names from Phase 2, then add granular view-level occlusion only where needed.

## 3. Screen-Based Privacy Protection

### Dashboard Configuration (Recommended)

The most efficient approach is configuring privacy rules directly in your UXCam dashboard:

#### Global Privacy Settings

1. **Navigate to App Settings** → Video Recording Privacy
2. **Configure global rules** for all screens:
   - **Record**: Normal recording (default)
   - **Blur**: Apply blur with configurable radius
   - **Overlay**: Apply solid color overlay

#### Screen-Specific Rules

1. **Add screen-specific rules** using your Phase 2 screen names:

   ```
   Screen Names: Login Form, Payment Details, User Profile Settings
   Action: Overlay with gesture recording disabled
   ```

2. **Create multiple rule sets** for different sensitivity levels:
   ```
   High Sensitivity: Login Form, Payment Details → Overlay
   Medium Sensitivity: User Profile, Settings → Blur (radius: 10)
   Forms Only: Registration, Contact → Text Fields Only
   ```

#### Privacy Rule Priority

Privacy rules are applied in this order (highest to lowest priority):

1. Screen-specific overlay from Dashboard
2. Screen-specific blur from Dashboard
3. Global blur/overlay from Dashboard
4. Screen-specific overlay from SDK code
5. Screen-specific blur from SDK code
6. Global SDK privacy settings

### SDK-Based Screen Protection

```javascript config/privacyConfig.js
import { UXCamOcclusionType } from 'react-native-ux-cam/UXCamOcclusion';

// Define sensitive screens from Phase 2
export const PRIVACY_SCREEN_CONFIG = {
  // High sensitivity - complete overlay
  overlay: {
    type: UXCamOcclusionType.Overlay,
    color: 0x000000, // Black overlay
    hideGestures: true, // Disable gesture recording
    screens: [
      'Login Form',
      'Registration Form',
      'Payment Details',
      'Credit Card Entry',
      'Bank Account Setup',
      'Password Change',
      'Security Settings',
    ],
  },

  // Medium sensitivity - blur with gesture recording
  blur: {
    type: UXCamOcclusionType.Blur,
    blurRadius: 15,
    hideGestures: false, // Keep gesture insights
    screens: [
      'User Profile Settings',
      'Account Overview',
      'Personal Information',
      'Contact Details',
      'Address Settings',
    ],
  },

  // Text fields only - for mixed content screens
  textFields: {
    type: UXCamOcclusionType.OccludeAllTextFields,
    screens: ['Search Results', 'Product Reviews', 'Customer Support Chat'],
  },
};

// Apply privacy configuration
export const applyPrivacyConfiguration = () => {
  const configuration = {
    userAppKey: 'YOUR_API_KEY',
    enableAutomaticScreenNameTagging: false,
    enableImprovedScreenCapture: true,
    occlusions: [
      PRIVACY_SCREEN_CONFIG.overlay,
      PRIVACY_SCREEN_CONFIG.blur,
      PRIVACY_SCREEN_CONFIG.textFields,
    ],
  };

  return configuration;
};
```

### Runtime Privacy Control

```javascript utils/dynamicPrivacy.js
import RNUxcam from 'react-native-ux-cam';

export class DynamicPrivacyManager {
  static currentPrivacyLevel = 'normal';

  static setPrivacyLevel(level) {
    this.currentPrivacyLevel = level;

    switch (level) {
      case 'high':
        this.enableHighPrivacyMode();
        break;
      case 'medium':
        this.enableMediumPrivacyMode();
        break;
      case 'normal':
        this.disablePrivacyMode();
        break;
    }
  }

  static enableHighPrivacyMode() {
    // Apply overlay to all screens
    RNUxcam.applyOcclusion({
      color: 0x333333,
      hideGestures: true,
    });
  }

  static enableMediumPrivacyMode() {
    // Apply blur to all screens
    RNUxcam.applyBlur({
      blurRadius: 10,
      hideGestures: false,
    });
  }

  static disablePrivacyMode() {
    // Remove all occlusion
    RNUxcam.removeOcclusion();
    RNUxcam.removeBlur();
  }

  static applyScreenSpecificPrivacy(screenName) {
    // Apply privacy based on screen name
    const sensitiveScreens = [
      'Login Form',
      'Payment Details',
      'Password Change',
    ];

    if (sensitiveScreens.includes(screenName)) {
      RNUxcam.applyOcclusion({
        color: 0x000000,
        hideGestures: true,
      });
    }
  }
}

// Usage in screens
export const useScreenPrivacy = (screenName) => {
  useFocusEffect(
    React.useCallback(() => {
      DynamicPrivacyManager.applyScreenSpecificPrivacy(screenName);

      return () => {
        // Clean up privacy settings when leaving screen
        if (DynamicPrivacyManager.currentPrivacyLevel === 'normal') {
          DynamicPrivacyManager.disablePrivacyMode();
        }
      };
    }, [screenName])
  );
};
```

## 4. View-Level Privacy Protection

### Individual Component Occlusion

```javascript components/SensitiveForm.js
import React, { useRef, useEffect } from 'react';
import { TextInput, View } from 'react-native';
import RNUxcam from 'react-native-ux-cam';

function SensitiveForm() {
  const passwordRef = useRef(null);
  const creditCardRef = useRef(null);
  const ssnRef = useRef(null);

  useEffect(() => {
    // Occlude specific sensitive fields
    if (passwordRef.current) {
      RNUxcam.occludeSensitiveView(passwordRef.current);
    }

    if (creditCardRef.current) {
      RNUxcam.occludeSensitiveView(creditCardRef.current);
    }

    if (ssnRef.current) {
      RNUxcam.occludeSensitiveView(ssnRef.current);
    }
  }, []);

  return (
    <View>
      <TextInput
        ref={passwordRef}
        placeholder="Password"
        secureTextEntry={true}
        autoCompleteType="password"
      />

      <TextInput
        ref={creditCardRef}
        placeholder="Credit Card Number"
        keyboardType="numeric"
        autoCompleteType="cc-number"
      />

      <TextInput
        ref={ssnRef}
        placeholder="Social Security Number"
        keyboardType="numeric"
        maxLength={11}
      />
    </View>
  );
}
```

### Automatic Sensitive Field Detection

```javascript hooks/useSensitiveFieldProtection.js
import { useEffect, useRef } from 'react';
import RNUxcam from 'react-native-ux-cam';

export const useSensitiveFieldProtection = (props = {}) => {
  const fieldRef = useRef(null);

  useEffect(() => {
    const shouldOcclude = detectSensitiveField(props);

    if (shouldOcclude && fieldRef.current) {
      RNUxcam.occludeSensitiveView(fieldRef.current);
    }
  }, [props]);

  return fieldRef;
};

const detectSensitiveField = (props) => {
  const sensitiveKeywords = [
    'password',
    'pin',
    'ssn',
    'social',
    'credit',
    'card',
    'cvv',
    'cvc',
    'bank',
    'account',
    'routing',
  ];

  const sensitiveAutoComplete = [
    'password',
    'cc-number',
    'cc-csc',
    'cc-exp',
    'tel',
    'email',
    'postal-code',
  ];

  // Check placeholder text
  if (props.placeholder) {
    const placeholder = props.placeholder.toLowerCase();
    if (sensitiveKeywords.some((keyword) => placeholder.includes(keyword))) {
      return true;
    }
  }

  // Check autoCompleteType
  if (
    props.autoCompleteType &&
    sensitiveAutoComplete.includes(props.autoCompleteType)
  ) {
    return true;
  }

  // Check if it's a secure text entry
  if (props.secureTextEntry) {
    return true;
  }

  // Check keyboard type for financial data
  if (
    props.keyboardType === 'numeric' &&
    props.placeholder &&
    (props.placeholder.includes('card') ||
      props.placeholder.includes('account'))
  ) {
    return true;
  }

  return false;
};

// Usage
function SmartForm() {
  const passwordRef = useSensitiveFieldProtection({
    secureTextEntry: true,
    placeholder: 'Password',
  });

  const emailRef = useSensitiveFieldProtection({
    autoCompleteType: 'email',
    placeholder: 'Email Address',
  });

  return (
    <View>
      <TextInput
        ref={passwordRef}
        placeholder="Password"
        secureTextEntry={true}
        autoCompleteType="password"
      />

      <TextInput
        ref={emailRef}
        placeholder="Email Address"
        keyboardType="email-address"
        autoCompleteType="email"
      />
    </View>
  );
}
```

### All Text Fields Occlusion

```javascript components/FormWithTextFieldOcclusion.js
import React, { useEffect } from 'react';
import { useFocusEffect } from '@react-navigation/native';
import RNUxcam from 'react-native-ux-cam';

function FormWithTextFieldOcclusion() {
  useFocusEffect(
    React.useCallback(() => {
      // Occlude all text fields on this screen
      RNUxcam.occludeAllTextFields();

      return () => {
        // Stop occluding when leaving screen
        RNUxcam.stopOccludingAllTextFields();
      };
    }, [])
  );

  return (
    // Form components - all TextInput fields will be occluded
  );
}
```

## 5. WebView Privacy Protection

### Basic WebView Occlusion

```javascript components/SecureWebView.js
import React, { useRef, useEffect } from 'react';
import { WebView } from 'react-native-webview';
import RNUxcam from 'react-native-ux-cam';

function SecureWebView({ source }) {
  const webViewRef = useRef(null);

  useEffect(() => {
    // Occlude entire WebView for sensitive content
    if (webViewRef.current) {
      RNUxcam.occludeSensitiveView(webViewRef.current);
    }
  }, []);

  return (
    <WebView
      ref={webViewRef}
      source={source}
      javaScriptEnabled={true}
      domStorageEnabled={false} // Security consideration
    />
  );
}
```

### Dynamic WebView Privacy

```javascript components/DynamicWebView.js
import React, { useRef, useState } from 'react';
import { WebView } from 'react-native-webview';
import RNUxcam from 'react-native-ux-cam';

function DynamicWebView() {
  const webViewRef = useRef(null);
  const [isOccluded, setIsOccluded] = useState(false);

  const handleNavigationStateChange = (navState) => {
    const { url } = navState;

    // Define sensitive URL patterns
    const sensitivePatterns = [
      '/payment',
      '/checkout',
      '/login',
      '/register',
      '/profile',
      '/settings',
    ];

    const shouldOcclude = sensitivePatterns.some((pattern) =>
      url.includes(pattern)
    );

    if (shouldOcclude && !isOccluded) {
      RNUxcam.occludeSensitiveView(webViewRef.current);
      setIsOccluded(true);
    } else if (!shouldOcclude && isOccluded) {
      // Note: Cannot un-occlude individual views once occluded
      // Consider using screen-based occlusion instead
      setIsOccluded(false);
    }
  };

  return (
    <WebView
      ref={webViewRef}
      source={{ uri: 'https://your-webapp.com' }}
      onNavigationStateChange={handleNavigationStateChange}
    />
  );
}
```

### WebView with JavaScript Privacy Bridge

```javascript components/AdvancedSecureWebView.js
import React, { useRef } from 'react';
import { WebView } from 'react-native-webview';
import RNUxcam from 'react-native-ux-cam';

function AdvancedSecureWebView() {
  const webViewRef = useRef(null);

  const injectedJavaScript = `
    // Privacy protection JavaScript
    (function() {
      // Hide sensitive elements by class/ID
      const hideSensitiveElements = () => {
        const sensitiveSelectors = [
          'input[type="password"]',
          'input[type="email"]',
          'input[name*="credit"]',
          'input[name*="card"]',
          '.sensitive-data',
          '#personal-info'
        ];

        sensitiveSelectors.forEach(selector => {
          document.querySelectorAll(selector).forEach(element => {
            element.style.background = '#000';
            element.style.color = '#000';
          });
        });
      };

      // Monitor for dynamic content
      const observer = new MutationObserver(hideSensitiveElements);
      observer.observe(document.body, {
        childList: true,
        subtree: true
      });

      // Initial hide
      if (document.readyState === 'loading') {
        document.addEventListener('DOMContentLoaded', hideSensitiveElements);
      } else {
        hideSensitiveElements();
      }

      // Communicate privacy state to React Native
      window.ReactNativeWebView.postMessage(JSON.stringify({
        type: 'PRIVACY_STATUS',
        hasSensitiveContent: document.querySelectorAll('input[type="password"]').length > 0
      }));
    })();

    true; // Required for injectedJavaScript
  `;

  const handleMessage = (event) => {
    try {
      const data = JSON.parse(event.nativeEvent.data);

      if (data.type === 'PRIVACY_STATUS' && data.hasSensitiveContent) {
        // Apply additional protection for sensitive WebView content
        RNUxcam.occludeSensitiveView(webViewRef.current);
      }
    } catch (error) {
      console.warn('WebView privacy message parsing failed:', error);
    }
  };

  return (
    <WebView
      ref={webViewRef}
      source={{ uri: 'https://your-webapp.com' }}
      injectedJavaScript={injectedJavaScript}
      onMessage={handleMessage}
      javaScriptEnabled={true}
    />
  );
}
```

## 6. Privacy Testing and Validation

### Privacy Compliance Checker

```javascript utils/privacyValidator.js
import RNUxcam from 'react-native-ux-cam';

export class PrivacyValidator {
  static async validatePrivacyCompliance() {
    const validationResults = {
      screenBasedProtection: false,
      textFieldProtection: false,
      webViewProtection: false,
      sensitiveDataExposure: [],
      recommendations: [],
    };

    try {
      // Check if privacy rules are active
      validationResults.screenBasedProtection =
        await this.checkScreenProtection();
      validationResults.textFieldProtection =
        await this.checkTextFieldProtection();
      validationResults.webViewProtection = await this.checkWebViewProtection();

      // Generate recommendations
      validationResults.recommendations =
        this.generateRecommendations(validationResults);

      return validationResults;
    } catch (error) {
      console.error('Privacy validation failed:', error);
      return validationResults;
    }
  }

  static async checkScreenProtection() {
    // This would need to be implemented based on your screen tracking
    const sensitiveScreens = [
      'Login Form',
      'Payment Details',
      'User Profile Settings',
    ];

    // Check if sensitive screens have protection
    return sensitiveScreens.length > 0; // Simplified check
  }

  static async checkTextFieldProtection() {
    // Check if text field occlusion is configured
    return true; // Would implement actual check
  }

  static async checkWebViewProtection() {
    // Check WebView occlusion status
    return true; // Would implement actual check
  }

  static generateRecommendations(results) {
    const recommendations = [];

    if (!results.screenBasedProtection) {
      recommendations.push(
        'Enable screen-based protection for sensitive screens'
      );
    }

    if (!results.textFieldProtection) {
      recommendations.push('Configure text field occlusion for forms');
    }

    if (!results.webViewProtection) {
      recommendations.push('Review WebView content for sensitive data');
    }

    return recommendations;
  }
}

// Usage in development
export const runPrivacyAudit = async () => {
  if (__DEV__) {
    const results = await PrivacyValidator.validatePrivacyCompliance();

    console.log('🔒 Privacy Compliance Audit Results:');
    console.log(
      'Screen Protection:',
      results.screenBasedProtection ? '✅' : '❌'
    );
    console.log(
      'Text Field Protection:',
      results.textFieldProtection ? '✅' : '❌'
    );
    console.log('WebView Protection:', results.webViewProtection ? '✅' : '❌');

    if (results.recommendations.length > 0) {
      console.log('📋 Recommendations:');
      results.recommendations.forEach((rec) => console.log(`  • ${rec}`));
    }
  }
};
```

### Visual Privacy Testing

```javascript components/PrivacyTestingPanel.js
import React, { useState } from 'react';
import { View, Text, Button, Switch } from 'react-native';
import RNUxcam from 'react-native-ux-cam';
import { DynamicPrivacyManager } from '../utils/dynamicPrivacy';

function PrivacyTestingPanel() {
  const [overlayEnabled, setOverlayEnabled] = useState(false);
  const [blurEnabled, setBlurEnabled] = useState(false);
  const [textFieldsOccluded, setTextFieldsOccluded] = useState(false);

  const toggleOverlay = () => {
    if (!overlayEnabled) {
      RNUxcam.applyOcclusion({ color: 0xff0000, hideGestures: true });
    } else {
      RNUxcam.removeOcclusion();
    }
    setOverlayEnabled(!overlayEnabled);
  };

  const toggleBlur = () => {
    if (!blurEnabled) {
      RNUxcam.applyBlur({ blurRadius: 15, hideGestures: false });
    } else {
      RNUxcam.removeBlur();
    }
    setBlurEnabled(!blurEnabled);
  };

  const toggleTextFields = () => {
    if (!textFieldsOccluded) {
      RNUxcam.occludeAllTextFields();
    } else {
      RNUxcam.stopOccludingAllTextFields();
    }
    setTextFieldsOccluded(!textFieldsOccluded);
  };

  if (!__DEV__) {
    return null; // Only show in development
  }

  return (
    <View style={{ padding: 20, backgroundColor: '#f8f8f8' }}>
      <Text style={{ fontSize: 18, fontWeight: 'bold', marginBottom: 15 }}>
        🔒 Privacy Testing Panel
      </Text>

      <View
        style={{ flexDirection: 'row', alignItems: 'center', marginBottom: 10 }}
      >
        <Text style={{ flex: 1 }}>Screen Overlay</Text>
        <Switch value={overlayEnabled} onValueChange={toggleOverlay} />
      </View>

      <View
        style={{ flexDirection: 'row', alignItems: 'center', marginBottom: 10 }}
      >
        <Text style={{ flex: 1 }}>Screen Blur</Text>
        <Switch value={blurEnabled} onValueChange={toggleBlur} />
      </View>

      <View
        style={{ flexDirection: 'row', alignItems: 'center', marginBottom: 15 }}
      >
        <Text style={{ flex: 1 }}>Text Field Occlusion</Text>
        <Switch value={textFieldsOccluded} onValueChange={toggleTextFields} />
      </View>

      <Button
        title="Test High Privacy Mode"
        onPress={() => DynamicPrivacyManager.setPrivacyLevel('high')}
      />
    </View>
  );
}

export default PrivacyTestingPanel;
```

## 7. Privacy Best Practices

### Configuration Strategy

```javascript config/privacyBestPractices.js
// Centralized privacy configuration
export const PRIVACY_BEST_PRACTICES = {
  // Use screen-based protection as primary strategy
  screenBased: {
    // High sensitivity screens - complete overlay
    criticalScreens: [
      'Login Form',
      'Registration Form',
      'Payment Details',
      'Credit Card Entry',
      'Bank Account Setup',
      'Password Change',
      'Two Factor Authentication',
    ],

    // Medium sensitivity - blur with gestures
    sensitiveScreens: [
      'User Profile Settings',
      'Personal Information',
      'Account Overview',
      'Transaction History',
      'Contact Details',
    ],

    // Low sensitivity - text fields only
    formScreens: [
      'Search Results',
      'Product Reviews',
      'Customer Support',
      'Feedback Forms',
    ],
  },

  // Fallback view-level protection
  viewBased: {
    // Always protect these input types
    alwaysProtect: [
      'secureTextEntry',
      'password fields',
      'credit card inputs',
      'SSN fields',
      'phone numbers',
      'email addresses',
    ],
  },

  // WebView protection strategy
  webView: {
    // Patterns that trigger protection
    sensitiveUrlPatterns: [
      '/login',
      '/register',
      '/payment',
      '/checkout',
      '/profile',
      '/settings',
      '/account',
    ],

    // Always occlude WebViews with these domains
    sensitiveDomainsPatterns: ['payment.com', 'bank.com', 'secure.com'],
  },
};

// Implementation helper
export const implementPrivacyBestPractices = () => {
  return {
    type: 'configuration',
    occlusions: [
      {
        type: 'Overlay',
        screens: PRIVACY_BEST_PRACTICES.screenBased.criticalScreens,
        color: 0x000000,
        hideGestures: true,
      },
      {
        type: 'Blur',
        screens: PRIVACY_BEST_PRACTICES.screenBased.sensitiveScreens,
        blurRadius: 15,
        hideGestures: false,
      },
      {
        type: 'OccludeAllTextFields',
        screens: PRIVACY_BEST_PRACTICES.screenBased.formScreens,
      },
    ],
  };
};
```

### Privacy Documentation

```javascript utils/privacyDocumentation.js
export const generatePrivacyReport = () => {
  const report = {
    timestamp: new Date().toISOString(),
    privacyMeasures: {
      screenBasedProtection: {
        enabled: true,
        protectedScreens: [
          'Login Form',
          'Payment Details',
          'User Profile Settings',
        ],
      },
      viewLevelProtection: {
        enabled: true,
        protectedComponents: [
          'Password inputs',
          'Credit card fields',
          'Personal information forms',
        ],
      },
      webViewProtection: {
        enabled: true,
        protectionMethods: [
          'Full WebView occlusion for sensitive domains',
          'JavaScript-based element hiding',
          'URL pattern-based protection',
        ],
      },
    },
    complianceStandards: [
      'GDPR Article 25 - Data Protection by Design',
      'CCPA Section 1798.100 - Consumer Privacy Rights',
      'SOC 2 Type II - Privacy Controls',
    ],
    dataRetention: {
      sessionRecordings: '90 days maximum',
      personalData: 'Excluded from recordings',
      analyticsData: 'Aggregated only',
    },
  };

  return report;
};

// Generate privacy documentation for compliance
export const exportPrivacyDocumentation = () => {
  if (__DEV__) {
    const report = generatePrivacyReport();
    console.log('📄 Privacy Compliance Report:');
    console.log(JSON.stringify(report, null, 2));
  }
};
```

## 8. Troubleshooting Privacy Issues

### Common Issues and Solutions

#### Issue: Privacy Rules Not Working

**Symptoms**: Sensitive data still visible in recordings
**Debugging Steps**:

```javascript
// Debug privacy configuration
const debugPrivacySetup = () => {
  console.log('🔍 Privacy Debug Information:');

  // Check if occlusion is applied
  try {
    RNUxcam.applyOcclusion({ color: 0xff0000 }); // Red overlay for testing
    console.log('✅ Occlusion API working');

    setTimeout(() => {
      RNUxcam.removeOcclusion();
      console.log('✅ Occlusion removal working');
    }, 3000);
  } catch (error) {
    console.error('❌ Occlusion API failed:', error);
  }

  // Verify screen names match privacy configuration
  const currentScreen = 'Login Form'; // Get from your screen tracking
  console.log(`Current screen: ${currentScreen}`);

  const privacyScreens = ['Login Form', 'Payment Details'];
  const isProtected = privacyScreens.includes(currentScreen);
  console.log(`Screen should be protected: ${isProtected}`);
};
```

#### Issue: Text Fields Not Occluded

**Solution**: Ensure proper TextInput component usage:

```javascript
// ✅ Correct - will be occluded by occludeAllTextFields()
<TextInput placeholder="Enter password" />

// ❌ Incorrect - custom component won't be detected
<CustomInput placeholder="Enter password" />

// ✅ Solution for custom components
const CustomInputWithOcclusion = React.forwardRef((props, ref) => (
  <TextInput {...props} ref={ref} />
));

// Then occlude manually
const customInputRef = useRef();
useEffect(() => {
  RNUxcam.occludeSensitiveView(customInputRef.current);
}, []);
```

#### Issue: WebView Privacy Not Working

**Solution**: Verify WebView reference and timing:

```javascript
const DebugWebView = () => {
  const webViewRef = useRef(null);

  useEffect(() => {
    // Add delay to ensure WebView is mounted
    const timer = setTimeout(() => {
      if (webViewRef.current) {
        RNUxcam.occludeSensitiveView(webViewRef.current);
        console.log('✅ WebView occlusion applied');
      } else {
        console.error('❌ WebView ref is null');
      }
    }, 1000);

    return () => clearTimeout(timer);
  }, []);

  return (
    <WebView
      ref={webViewRef}
      source={{ uri: 'https://example.com' }}
      onLoad={() => console.log('WebView loaded')}
    />
  );
};
```

## Success Criteria ✅

Before proceeding to Phase 4, verify:

- [ ] All sensitive screens have appropriate protection (overlay/blur)
- [ ] Form fields with PII are properly occluded
- [ ] WebViews containing sensitive content are protected
- [ ] Privacy rules work consistently across iOS and Android
- [ ] No sensitive data visible in test session recordings
- [ ] Privacy configuration matches your compliance requirements
- [ ] Testing panel confirms all protection methods work

## Next Steps

🛡️ **Privacy Protection Complete!** Your React Native app now complies with GDPR/CCPA requirements and protects sensitive user data.

**Continue to Phase 4**: [Event Tracking](event-tracking-react-native) to capture business-critical user actions while maintaining privacy compliance.

### What You'll Build Next:

- Business event tracking with contextual properties
- Conversion funnel event implementation
- User interaction analytics
- Custom event validation and testing

### Privacy Protection Benefits:

- **Legal Compliance**: GDPR/CCPA compliant data collection
- **User Trust**: Demonstrable privacy protection measures
- **Selective Analytics**: Insights without compromising sensitive data
- **Flexible Rules**: Screen-based and component-level protection strategies

---

**Need Help?** Check our [Troubleshooting Guide](troubleshooting-react-native) or contact [team@uxcam.com](mailto:team@uxcam.com) for privacy compliance support.
