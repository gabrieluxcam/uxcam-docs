---
title: Opt-in / Opt-out Your Users
deprecated: false
hidden: false
metadata:
  robots: index
---
The SDK by default opts-in users for UXCam recording on app installs. You can now disable and enable recording at runtime by the following API.

## Opt out a User

This method stops and deletes the session in progress (if any) and disables the recording of future sessions from this user.

Use this if:

* The user has chosen not to enable analytics (screen recording)

```javascript
RNUxcam.optOutOverall();
```

> 📘
>
> The optIn/optOut setting resets to opt-In if the user un-installs and re-installs the app or clears app data.

## Opt in a User

The SDK by default opts-in users for UXCam recording on app installs. If the user has been disabled for UXCam recording by using the optOut method, you can use this method to enable recording at runtime.

```javascript
RNUxcam.optInOverall();
```

## Opt in Status

This method returns the status of the user indicating whether they are currently opted in or opted out.

```javascript
RNUxcam.optInOverallStatus();
```

## React Native Implementation Examples

### User Consent Management

```javascript
import React, { useState, useEffect } from 'react';
import { View, Switch, Text, Alert } from 'react-native';
import AsyncStorage from '@react-native-async-storage/async-storage';
import RNUxcam from 'react-native-ux-cam';

function ConsentScreen() {
  const [isOptedIn, setIsOptedIn] = useState(false);

  useEffect(() => {
    // Check current opt-in status
    const checkOptInStatus = async () => {
      try {
        const status = await AsyncStorage.getItem('uxcam_consent');
        const currentStatus = RNUxcam.optInOverallStatus();
        setIsOptedIn(currentStatus);
      } catch (error) {
        console.error('Error checking opt-in status:', error);
      }
    };

    checkOptInStatus();
  }, []);

  const handleConsentChange = async (value) => {
    try {
      if (value) {
        RNUxcam.optInOverall();
        await AsyncStorage.setItem('uxcam_consent', 'true');
      } else {
        RNUxcam.optOutOverall();
        await AsyncStorage.setItem('uxcam_consent', 'false');
      }
      setIsOptedIn(value);
    } catch (error) {
      console.error('Error updating consent:', error);
      Alert.alert('Error', 'Failed to update consent settings');
    }
  };

  return (
    <View style={{ flex: 1, padding: 20 }}>
      <Text style={{ fontSize: 18, marginBottom: 20 }}>Analytics Consent</Text>

      <View style={{ flexDirection: 'row', alignItems: 'center' }}>
        <Text>Allow session recording: </Text>
        <Switch value={isOptedIn} onValueChange={handleConsentChange} />
      </View>

      <Text style={{ fontSize: 12, marginTop: 10, color: '#666' }}>
        We use session recording to improve our app. You can change this setting
        anytime.
      </Text>
    </View>
  );
}
```

### Hook-Based Consent Management

```javascript
import React from 'react';
import { useCallback, useState, useEffect } from 'react';
import AsyncStorage from '@react-native-async-storage/async-storage';
import RNUxcam from 'react-native-ux-cam';

function useConsentManagement() {
  const [isOptedIn, setIsOptedIn] = useState(false);
  const [isLoading, setIsLoading] = useState(true);

  const checkConsentStatus = useCallback(async () => {
    try {
      const storedConsent = await AsyncStorage.getItem('uxcam_consent');
      const currentStatus = RNUxcam.optInOverallStatus();

      // If no stored consent, use current SDK status
      if (storedConsent === null) {
        await AsyncStorage.setItem('uxcam_consent', currentStatus.toString());
      }

      setIsOptedIn(currentStatus);
    } catch (error) {
      console.error('Error checking consent status:', error);
    } finally {
      setIsLoading(false);
    }
  }, []);

  const optIn = useCallback(async () => {
    try {
      RNUxcam.optInOverall();
      await AsyncStorage.setItem('uxcam_consent', 'true');
      setIsOptedIn(true);
    } catch (error) {
      console.error('Error opting in:', error);
      throw error;
    }
  }, []);

  const optOut = useCallback(async () => {
    try {
      RNUxcam.optOutOverall();
      await AsyncStorage.setItem('uxcam_consent', 'false');
      setIsOptedIn(false);
    } catch (error) {
      console.error('Error opting out:', error);
      throw error;
    }
  }, []);

  useEffect(() => {
    checkConsentStatus();
  }, [checkConsentStatus]);

  return {
    isOptedIn,
    isLoading,
    optIn,
    optOut,
    checkConsentStatus,
  };
}
```

### Settings Screen Implementation

```javascript
import React from 'react';
import { View, Text, Switch, StyleSheet } from 'react-native';
import { useConsentManagement } from './useConsentManagement';

function SettingsScreen() {
  const { isOptedIn, isLoading, optIn, optOut } = useConsentManagement();

  const handleToggle = async (value) => {
    try {
      if (value) {
        await optIn();
      } else {
        await optOut();
      }
    } catch (error) {
      console.error('Error toggling consent:', error);
    }
  };

  if (isLoading) {
    return (
      <View style={styles.container}>
        <Text>Loading...</Text>
      </View>
    );
  }

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Privacy Settings</Text>

      <View style={styles.settingRow}>
        <View style={styles.settingInfo}>
          <Text style={styles.settingTitle}>Session Recording</Text>
          <Text style={styles.settingDescription}>
            Allow UXCam to record your app usage to help improve the experience
          </Text>
        </View>

        <Switch value={isOptedIn} onValueChange={handleToggle} />
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
  },
  title: {
    fontSize: 24,
    fontWeight: 'bold',
    marginBottom: 20,
  },
  settingRow: {
    flexDirection: 'row',
    alignItems: 'center',
    justifyContent: 'space-between',
    paddingVertical: 15,
    borderBottomWidth: 1,
    borderBottomColor: '#eee',
  },
  settingInfo: {
    flex: 1,
    marginRight: 15,
  },
  settingTitle: {
    fontSize: 16,
    fontWeight: '500',
  },
  settingDescription: {
    fontSize: 14,
    color: '#666',
    marginTop: 4,
  },
});
```

## TypeScript Support

```typescript
import RNUxcam from 'react-native-ux-cam';

interface ConsentStatus {
  isOptedIn: boolean;
  isLoading: boolean;
}

interface ConsentManager {
  optIn: () => Promise<void>;
  optOut: () => Promise<void>;
  checkStatus: () => Promise<boolean>;
}

// Typed consent management
const useTypedConsentManagement = (): ConsentStatus & ConsentManager => {
  const [isOptedIn, setIsOptedIn] = useState<boolean>(false);
  const [isLoading, setIsLoading] = useState<boolean>(true);

  const optIn = async (): Promise<void> => {
    RNUxcam.optInOverall();
    setIsOptedIn(true);
  };

  const optOut = async (): Promise<void> => {
    RNUxcam.optOutOverall();
    setIsOptedIn(false);
  };

  const checkStatus = async (): Promise<boolean> => {
    return RNUxcam.optInOverallStatus();
  };

  return {
    isOptedIn,
    isLoading,
    optIn,
    optOut,
    checkStatus,
  };
};
```

## Best Practices

1. **Store consent locally**: Use AsyncStorage to persist user consent preferences
2. **Check status on app start**: Verify consent status when the app launches
3. **Provide clear explanations**: Explain what data is collected and why
4. **Allow easy changes**: Make it simple for users to change their consent
5. **Handle errors gracefully**: Provide fallbacks if consent operations fail
6. **Respect user choice**: Don't re-prompt if user has already made a choice

## Summary

The opt-in/opt-out APIs allow you to give users control over their privacy while maintaining compliance with data protection regulations. Implement these features thoughtfully to build user trust and ensure regulatory compliance.