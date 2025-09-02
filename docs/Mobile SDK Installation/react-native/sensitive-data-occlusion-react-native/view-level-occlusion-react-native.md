---
title: View Level Occlusion
deprecated: false
hidden: false
metadata:
  robots: index
---
# View-Level Occlusion for React Native

View-level occlusion allows you to hide specific components or views that contain sensitive information. This guide covers how to implement component-level occlusion in React Native applications.

## Understanding View-Level Occlusion

View-level occlusion is useful when you need to:

* Hide specific form fields or components
* Occlude sensitive information within a screen
* Protect user data while keeping other content visible
* Apply occlusion to dynamically created components

## Basic View Occlusion

### Occluding Individual Components

```javascript
import React from 'react';
import { View, TextInput, Button } from 'react-native';
import RNUxcam from 'react-native-ux-cam';

function SensitiveFormScreen() {
  return (
    <View style={{ flex: 1, padding: 20 }}>
      <TextInput
        placeholder="Username"
        style={{ height: 40, borderWidth: 1, marginBottom: 10 }}
      />

      <TextInput
        placeholder="Password"
        secureTextEntry
        style={{ height: 40, borderWidth: 1, marginBottom: 10 }}
        ref={(view) => RNUxcam.occludeSensitiveView(view)}
      />

      <Button title="Login" onPress={() => console.log('Login pressed')} />
    </View>
  );
}
```

### Occluding Multiple Components

```javascript
import React from 'react';
import { View, TextInput, Text } from 'react-native';
import RNUxcam from 'react-native-ux-cam';

function PaymentFormScreen() {
  return (
    <View style={{ flex: 1, padding: 20 }}>
      <Text style={{ fontSize: 18, marginBottom: 20 }}>
        Payment Information
      </Text>

      <TextInput
        placeholder="Card Number"
        style={{ height: 40, borderWidth: 1, marginBottom: 10 }}
        ref={(view) => RNUxcam.occludeSensitiveView(view)}
      />

      <View style={{ flexDirection: 'row', justifyContent: 'space-between' }}>
        <TextInput
          placeholder="MM/YY"
          style={{ height: 40, borderWidth: 1, width: '45%' }}
          ref={(view) => RNUxcam.occludeSensitiveView(view)}
        />

        <TextInput
          placeholder="CVV"
          style={{ height: 40, borderWidth: 1, width: '45%' }}
          ref={(view) => RNUxcam.occludeSensitiveView(view)}
        />
      </View>
    </View>
  );
}
```

## Functional Component Occlusion

### Using useRef for Occlusion

```javascript
import React, { useRef, useEffect } from 'react';
import { View, TextInput, Button } from 'react-native';
import RNUxcam from 'react-native-ux-cam';

function SensitiveComponent() {
  const passwordRef = useRef(null);
  const cardRef = useRef(null);

  useEffect(() => {
    // Apply occlusion when component mounts
    if (passwordRef.current) {
      RNUxcam.occludeSensitiveView(passwordRef.current);
    }
    if (cardRef.current) {
      RNUxcam.occludeSensitiveView(cardRef.current);
    }
  }, []);

  return (
    <View style={{ flex: 1, padding: 20 }}>
      <TextInput
        ref={passwordRef}
        placeholder="Password"
        secureTextEntry
        style={{ height: 40, borderWidth: 1, marginBottom: 10 }}
      />

      <TextInput
        ref={cardRef}
        placeholder="Credit Card Number"
        style={{ height: 40, borderWidth: 1, marginBottom: 10 }}
      />

      <Button title="Submit" onPress={() => console.log('Form submitted')} />
    </View>
  );
}
```

### Conditional Occlusion

```javascript
import React, { useState, useRef, useEffect } from 'react';
import { View, TextInput, Switch, Text } from 'react-native';
import RNUxcam from 'react-native-ux-cam';

function ConditionalOcclusionScreen() {
  const [isSensitive, setIsSensitive] = useState(false);
  const sensitiveRef = useRef(null);

  useEffect(() => {
    if (isSensitive && sensitiveRef.current) {
      RNUxcam.occludeSensitiveView(sensitiveRef.current);
    }
  }, [isSensitive]);

  return (
    <View style={{ flex: 1, padding: 20 }}>
      <View
        style={{ flexDirection: 'row', alignItems: 'center', marginBottom: 20 }}
      >
        <Text>Hide sensitive data: </Text>
        <Switch value={isSensitive} onValueChange={setIsSensitive} />
      </View>

      <TextInput
        ref={sensitiveRef}
        placeholder="Sensitive Information"
        style={{ height: 40, borderWidth: 1, marginBottom: 10 }}
      />
    </View>
  );
}
```

## Class Component Occlusion

### Using componentDidMount

```javascript
import React, { Component } from 'react';
import { View, TextInput, Button } from 'react-native';
import RNUxcam from 'react-native-ux-cam';

class SensitiveClassComponent extends Component {
  componentDidMount() {
    // Apply occlusion after component mounts
    if (this.passwordInput) {
      RNUxcam.occludeSensitiveView(this.passwordInput);
    }
    if (this.cardInput) {
      RNUxcam.occludeSensitiveView(this.cardInput);
    }
  }

  render() {
    return (
      <View style={{ flex: 1, padding: 20 }}>
        <TextInput
          ref={(input) => (this.passwordInput = input)}
          placeholder="Password"
          secureTextEntry
          style={{ height: 40, borderWidth: 1, marginBottom: 10 }}
        />

        <TextInput
          ref={(input) => (this.cardInput = input)}
          placeholder="Credit Card"
          style={{ height: 40, borderWidth: 1, marginBottom: 10 }}
        />

        <Button title="Submit" onPress={() => console.log('Form submitted')} />
      </View>
    );
  }
}
```

## Dynamic Component Occlusion

### Occluding Dynamically Created Components

```javascript
import React, { useState, useRef, useEffect } from 'react';
import { View, TextInput, Button, FlatList } from 'react-native';
import RNUxcam from 'react-native-ux-cam';

function DynamicOcclusionScreen() {
  const [fields, setFields] = useState([]);
  const fieldRefs = useRef({});

  const addSensitiveField = () => {
    const newField = {
      id: Date.now(),
      placeholder: `Sensitive Field ${fields.length + 1}`,
      isSensitive: true,
    };
    setFields([...fields, newField]);
  };

  useEffect(() => {
    // Apply occlusion to all sensitive fields
    fields.forEach((field) => {
      if (field.isSensitive && fieldRefs.current[field.id]) {
        RNUxcam.occludeSensitiveView(fieldRefs.current[field.id]);
      }
    });
  }, [fields]);

  const renderField = ({ item }) => (
    <TextInput
      ref={(ref) => (fieldRefs.current[item.id] = ref)}
      placeholder={item.placeholder}
      style={{ height: 40, borderWidth: 1, marginBottom: 10 }}
    />
  );

  return (
    <View style={{ flex: 1, padding: 20 }}>
      <Button title="Add Sensitive Field" onPress={addSensitiveField} />

      <FlatList
        data={fields}
        renderItem={renderField}
        keyExtractor={(item) => item.id.toString()}
        style={{ marginTop: 20 }}
      />
    </View>
  );
}
```

## Advanced Occlusion Patterns

### Occluding Custom Components

```javascript
import React, { forwardRef, useImperativeHandle } from 'react';
import { View, TextInput } from 'react-native';
import RNUxcam from 'react-native-ux-cam';

const SensitiveInput = forwardRef(({ placeholder, style, ...props }, ref) => {
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => ({
    applyOcclusion: () => {
      if (inputRef.current) {
        RNUxcam.occludeSensitiveView(inputRef.current);
      }
    },
  }));

  return (
    <TextInput
      ref={inputRef}
      placeholder={placeholder}
      style={style}
      {...props}
    />
  );
});

function CustomComponentScreen() {
  const passwordRef = useRef(null);
  const cardRef = useRef(null);

  useEffect(() => {
    // Apply occlusion to custom components
    if (passwordRef.current) {
      passwordRef.current.applyOcclusion();
    }
    if (cardRef.current) {
      cardRef.current.applyOcclusion();
    }
  }, []);

  return (
    <View style={{ flex: 1, padding: 20 }}>
      <SensitiveInput
        ref={passwordRef}
        placeholder="Password"
        secureTextEntry
        style={{ height: 40, borderWidth: 1, marginBottom: 10 }}
      />

      <SensitiveInput
        ref={cardRef}
        placeholder="Credit Card"
        style={{ height: 40, borderWidth: 1, marginBottom: 10 }}
      />
    </View>
  );
}
```

### Occluding Modal Content

```javascript
import React, { useState, useRef, useEffect } from 'react';
import { View, TextInput, Button, Modal } from 'react-native';
import RNUxcam from 'react-native-ux-cam';

function ModalOcclusionScreen() {
  const [modalVisible, setModalVisible] = useState(false);
  const modalContentRef = useRef(null);

  useEffect(() => {
    // Apply occlusion when modal becomes visible
    if (modalVisible && modalContentRef.current) {
      setTimeout(() => {
        RNUxcam.occludeSensitiveView(modalContentRef.current);
      }, 100); // Small delay to ensure modal is rendered
    }
  }, [modalVisible]);

  return (
    <View style={{ flex: 1, padding: 20 }}>
      <Button
        title="Show Sensitive Modal"
        onPress={() => setModalVisible(true)}
      />

      <Modal
        animationType="slide"
        transparent={true}
        visible={modalVisible}
        onRequestClose={() => setModalVisible(false)}
      >
        <View
          style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}
        >
          <View
            ref={modalContentRef}
            style={{ backgroundColor: 'white', padding: 20, borderRadius: 10 }}
          >
            <TextInput
              placeholder="Sensitive Data"
              style={{ height: 40, borderWidth: 1, marginBottom: 10 }}
            />

            <Button title="Close" onPress={() => setModalVisible(false)} />
          </View>
        </View>
      </Modal>
    </View>
  );
}
```

## Error Handling and Validation

### Robust Occlusion with Error Handling

```javascript
import React, { useRef, useEffect } from 'react';
import { View, TextInput, Alert } from 'react-native';
import RNUxcam from 'react-native-ux-cam';

function RobustOcclusionScreen() {
  const sensitiveRef = useRef(null);

  const applyOcclusion = () => {
    try {
      if (sensitiveRef.current) {
        RNUxcam.occludeSensitiveView(sensitiveRef.current);
        console.log('Occlusion applied successfully');
      } else {
        console.warn('Sensitive view reference not available');
      }
    } catch (error) {
      console.error('Error applying occlusion:', error);
      Alert.alert('Error', 'Failed to apply occlusion to sensitive view');
    }
  };

  useEffect(() => {
    applyOcclusion();
  }, []);

  return (
    <View style={{ flex: 1, padding: 20 }}>
      <TextInput
        ref={sensitiveRef}
        placeholder="Sensitive Information"
        style={{ height: 40, borderWidth: 1, marginBottom: 10 }}
      />
    </View>
  );
}
```

## TypeScript Support

### Typed View Occlusion

```typescript
import React, { useRef, useEffect } from 'react';
import { View, TextInput, ViewStyle } from 'react-native';
import RNUxcam from 'react-native-ux-cam';

interface SensitiveInputProps {
  placeholder: string;
  style?: ViewStyle;
  isSensitive?: boolean;
}

const SensitiveInput: React.FC<SensitiveInputProps> = ({
  placeholder,
  style,
  isSensitive = true,
}) => {
  const inputRef = useRef<TextInput>(null);

  useEffect(() => {
    if (isSensitive && inputRef.current) {
      RNUxcam.occludeSensitiveView(inputRef.current);
    }
  }, [isSensitive]);

  return <TextInput ref={inputRef} placeholder={placeholder} style={style} />;
};

function TypedOcclusionScreen() {
  return (
    <View style={{ flex: 1, padding: 20 }}>
      <SensitiveInput
        placeholder="Password"
        isSensitive={true}
        style={{ height: 40, borderWidth: 1, marginBottom: 10 }}
      />

      <SensitiveInput
        placeholder="Credit Card"
        isSensitive={true}
        style={{ height: 40, borderWidth: 1, marginBottom: 10 }}
      />
    </View>
  );
}
```

## Best Practices

### Performance Considerations

```javascript
import React, { useCallback, useRef, useEffect } from 'react';
import { View, TextInput } from 'react-native';
import RNUxcam from 'react-native-ux-cam';

function OptimizedOcclusionScreen() {
  const sensitiveRefs = useRef({});

  const applyOcclusion = useCallback((id) => {
    if (sensitiveRefs.current[id]) {
      RNUxcam.occludeSensitiveView(sensitiveRefs.current[id]);
    }
  }, []);

  const registerSensitiveView = useCallback(
    (id, ref) => {
      sensitiveRefs.current[id] = ref;
      applyOcclusion(id);
    },
    [applyOcclusion]
  );

  return (
    <View style={{ flex: 1, padding: 20 }}>
      <TextInput
        ref={(ref) => registerSensitiveView('password', ref)}
        placeholder="Password"
        secureTextEntry
        style={{ height: 40, borderWidth: 1, marginBottom: 10 }}
      />

      <TextInput
        ref={(ref) => registerSensitiveView('card', ref)}
        placeholder="Credit Card"
        style={{ height: 40, borderWidth: 1, marginBottom: 10 }}
      />
    </View>
  );
}
```

### Memory Management

```javascript
import React, { useRef, useEffect, useCallback } from 'react';
import { View, TextInput } from 'react-native';
import RNUxcam from 'react-native-ux-cam';

function MemoryOptimizedOcclusion() {
  const occlusionRefs = useRef(new Set());

  const applyOcclusion = useCallback((ref) => {
    if (ref && !occlusionRefs.current.has(ref)) {
      RNUxcam.occludeSensitiveView(ref);
      occlusionRefs.current.add(ref);
    }
  }, []);

  useEffect(() => {
    // Cleanup function
    return () => {
      occlusionRefs.current.clear();
    };
  }, []);

  return (
    <View style={{ flex: 1, padding: 20 }}>
      <TextInput
        ref={applyOcclusion}
        placeholder="Sensitive Data"
        style={{ height: 40, borderWidth: 1, marginBottom: 10 }}
      />
    </View>
  );
}
```

## Summary

View-level occlusion in React Native provides granular control over which components are hidden during recording. Use refs to apply occlusion to specific components, handle dynamic content appropriately, and always include error handling for robust implementations.

Key points:

* Use `RNUxcam.occludeSensitiveView(view)` for individual components
* Apply occlusion in `useEffect` or `componentDidMount`
* Handle dynamic components with proper ref management
* Include error handling for robust implementations
* Consider performance when occluding many components