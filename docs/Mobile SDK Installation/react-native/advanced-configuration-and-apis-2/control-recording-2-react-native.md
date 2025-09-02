---
title: Control Recording
deprecated: false
hidden: false
metadata:
  robots: index
---
The following API allows you to control when the session gets recorded in your React Native application.

## Start a New Recording

Start the UXCam application after having previously stopped it to verify the API key with the server, get the settings configurations and start capturing the data according to the configuration **if you previously stopped it**.

```javascript
RNUxcam.startNewSession();
```

## Stop Recording

UXCam automatically stops a session when the app goes to the background. Using this API you are going to be able to stop a session and upload it.

This is useful for:

* Recording a specific flow without sending the application to the background.
* Sending data (events, gestures, etc.) in a session without the closure of an app.

```javascript
// Stop and upload immediately
RNUxcam.stopSessionAndUploadData();

// Stop with completion handler
RNUxcam.stopSessionAndUploadData(() => {
  console.log('Session stopped and uploaded successfully');
});
```

## Pause and Resume Recording

### Pause Video Recording

Use this API in case you want to stop the screen recording of a session indefinitely. This can be helpful for:

* Avoid recording some parts of the app that are not relevant.
* Omit some screens and gestures that you want to avoid in your recordings.

```javascript
RNUxcam.pauseScreenRecording();
```

### Resume Video Recording

Use this API to resume the recording of the screen and gestures where the recording has been previously paused.

```javascript
RNUxcam.resumeScreenRecording();
```

## Session Management Examples

### Conditional Recording

```javascript
import React, { useState } from 'react';
import { View, Button, Switch } from 'react-native';
import RNUxcam from 'react-native-ux-cam';

function ConditionalRecordingScreen() {
  const [isRecording, setIsRecording] = useState(true);

  const toggleRecording = () => {
    if (isRecording) {
      RNUxcam.pauseScreenRecording();
      setIsRecording(false);
    } else {
      RNUxcam.resumeScreenRecording();
      setIsRecording(true);
    }
  };

  const stopAndUpload = () => {
    RNUxcam.stopSessionAndUploadData(() => {
      console.log('Session uploaded successfully');
    });
  };

  return (
    <View style={{ flex: 1, padding: 20 }}>
      <Switch value={isRecording} onValueChange={toggleRecording} />

      <Button title="Stop and Upload" onPress={stopAndUpload} />
    </View>
  );
}
```

### Flow-Specific Recording

```javascript
import React from 'react';
import { View, Button } from 'react-native';
import RNUxcam from 'react-native-ux-cam';

function FlowRecordingScreen() {
  const startCheckoutFlow = () => {
    // Start recording for checkout flow
    RNUxcam.startNewSession();
    RNUxcam.tagScreenName('Checkout Started');
  };

  const completeCheckout = () => {
    // Stop recording after checkout completion
    RNUxcam.stopSessionAndUploadData(() => {
      console.log('Checkout flow recorded and uploaded');
    });
  };

  return (
    <View style={{ flex: 1, padding: 20 }}>
      <Button title="Start Checkout" onPress={startCheckoutFlow} />

      <Button title="Complete Checkout" onPress={completeCheckout} />
    </View>
  );
}
```

## TypeScript Support

```typescript
import React from 'react';
import { View, Button } from 'react-native';
import RNUxcam from 'react-native-ux-cam';

interface RecordingControlsProps {
  onSessionComplete?: () => void;
}

function TypedRecordingControls({ onSessionComplete }: RecordingControlsProps) {
  const stopRecording = () => {
    RNUxcam.stopSessionAndUploadData(() => {
      if (onSessionComplete) {
        onSessionComplete();
      }
    });
  };

  return (
    <View style={{ flex: 1, padding: 20 }}>
      <Button title="Stop Recording" onPress={stopRecording} />
    </View>
  );
}
```

## Error Handling

```javascript
import React from 'react';
import { View, Button, Alert } from 'react-native';
import RNUxcam from 'react-native-ux-cam';

function RobustRecordingControls() {
  const stopRecording = () => {
    try {
      RNUxcam.stopSessionAndUploadData(() => {
        console.log('Session uploaded successfully');
      });
    } catch (error) {
      console.error('Error stopping session:', error);
      Alert.alert('Error', 'Failed to stop recording session');
    }
  };

  const pauseRecording = () => {
    try {
      RNUxcam.pauseScreenRecording();
      console.log('Recording paused');
    } catch (error) {
      console.error('Error pausing recording:', error);
      Alert.alert('Error', 'Failed to pause recording');
    }
  };

  return (
    <View style={{ flex: 1, padding: 20 }}>
      <Button title="Pause Recording" onPress={pauseRecording} />

      <Button title="Stop and Upload" onPress={stopRecording} />
    </View>
  );
}
```

## Best Practices

1. **Use startNewSession() sparingly**: Only when you need to start a completely new session
2. **Handle completion callbacks**: Always provide completion handlers for async operations
3. **Error handling**: Wrap recording control calls in try-catch blocks
4. **User feedback**: Provide visual feedback when recording state changes
5. **Session lifecycle**: Understand when sessions automatically start and stop

## Summary

The recording control APIs allow you to fine-tune when and how UXCam records your React Native application. Use these APIs to create more targeted recording sessions and better control over your analytics data.