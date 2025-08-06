---
title: MDX Components Test Page
description: A testing ground for custom MDX components
---

# MDX Components Test Page

This page is designed for testing custom MDX components. You can use this page to experiment with different configurations and see how components render.

## Configuration Builder Component

Test the interactive UXCam configuration builder:

<ConfigurationBuilder />

---

## Other Components Available for Testing

Feel free to test other custom components here:

### Steps Component
<SimpleStepper>
  <SimpleStep header="Test Step 1">
    This is a test step for the stepper component.
  </SimpleStep>
  <SimpleStep header="Test Step 2">
    Another test step with different content.
  </SimpleStep>
</SimpleStepper>

### Terminal Component
<Terminal>{`
$ npm install @uxcam/react-native-sdk
Installing UXCam React Native SDK...
✓ Installation complete
`}</Terminal>

### Jump Callout Component
<JumpCallout to="#configuration-builder-component">Jump to Configuration Builder</JumpCallout>

### Phone Simulator Component
<PhoneSimulator frameColor="#333" width="280px" height="500px" notch={true}>
  <div style={{
    display: 'flex',
    alignItems: 'center',
    justifyContent: 'center',
    height: '100%',
    backgroundColor: '#f0f0f0',
    color: '#333',
    fontSize: '16px',
    fontWeight: 'bold'
  }}>
    UXCam Demo App
  </div>
</PhoneSimulator>

---

*This page is for testing purposes and can be used to experiment with MDX components before implementing them in production documentation.*