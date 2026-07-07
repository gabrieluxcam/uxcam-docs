---
title: Mask PII & Sensitive Content
excerpt: Hide sensitive data and PII from session recordings in your React Native app
deprecated: false
hidden: false
metadata:
  title: 'Sensitive Data Occlusion - UXCam React Native SDK'
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
  description: 'Hide sensitive data and PII from session recordings in your React Native app'
---
Good privacy practices are **non-negotiable** — especially under GDPR, CCPA, and app store policies. UXCam lets you decide **what never gets recorded** at three levels: text fields, views, or entire screens.

<div style={{ display: 'flex', flexWrap: 'wrap', justifyContent: 'center', gap: '32px', margin: '32px 0' }}>
  <div>
    <PhoneSimulator width="240px" height="480px" glow={false}>
      <div style={{ width: '100%', height: '100%', backgroundColor: '#fff', borderRadius: '20px', overflow: 'hidden', fontFamily: 'system-ui, sans-serif', display: 'flex', flexDirection: 'column' }}>
        <div style={{ backgroundColor: '#4B2AFA', color: '#fff', padding: '18px 16px 10px', fontSize: '15px', fontWeight: 600 }}>Checkout</div>
        <div style={{ padding: '16px', display: 'flex', flexDirection: 'column', gap: '12px' }}>
          <div style={{ fontSize: '11px', color: '#666' }}>CARDHOLDER NAME</div>
          <div style={{ border: '1px solid #ddd', borderRadius: '6px', padding: '8px 10px', fontSize: '13px', color: '#161E35' }}>Alex Rivera</div>
          <div style={{ fontSize: '11px', color: '#666' }}>CARD NUMBER</div>
          <div style={{ border: '1px solid #ddd', borderRadius: '6px', padding: '8px 10px', fontSize: '13px', color: '#161E35' }}>4242 4242 4242 4242</div>
          <div style={{ fontSize: '11px', color: '#666' }}>CVV</div>
          <div style={{ border: '1px solid #ddd', borderRadius: '6px', padding: '8px 10px', fontSize: '13px', color: '#161E35', width: '60px' }}>123</div>
          <div style={{ backgroundColor: '#4B2AFA', color: '#fff', borderRadius: '6px', padding: '10px', fontSize: '13px', fontWeight: 600, textAlign: 'center', marginTop: '8px' }}>Pay $49.00</div>
        </div>
      </div>
    </PhoneSimulator>
    <p style={{ textAlign: 'center', fontSize: '13px', marginTop: '8px' }}><b>What your user sees</b></p>
  </div>

  <div>
    <PhoneSimulator width="240px" height="480px" glow={false}>
      <div style={{ width: '100%', height: '100%', backgroundColor: '#fff', borderRadius: '20px', overflow: 'hidden', fontFamily: 'system-ui, sans-serif', display: 'flex', flexDirection: 'column' }}>
        <div style={{ backgroundColor: '#4B2AFA', color: '#fff', padding: '18px 16px 10px', fontSize: '15px', fontWeight: 600 }}>Checkout</div>
        <div style={{ padding: '16px', display: 'flex', flexDirection: 'column', gap: '12px' }}>
          <div style={{ fontSize: '11px', color: '#666' }}>CARDHOLDER NAME</div>
          <div style={{ backgroundColor: '#FA2A16', borderRadius: '6px', padding: '8px 10px', fontSize: '13px', color: '#FA2A16' }}>occluded</div>
          <div style={{ fontSize: '11px', color: '#666' }}>CARD NUMBER</div>
          <div style={{ backgroundColor: '#FA2A16', borderRadius: '6px', padding: '8px 10px', fontSize: '13px', color: '#FA2A16' }}>occluded</div>
          <div style={{ fontSize: '11px', color: '#666' }}>CVV</div>
          <div style={{ backgroundColor: '#FA2A16', borderRadius: '6px', padding: '8px 10px', fontSize: '13px', color: '#FA2A16', width: '60px' }}>occ</div>
          <div style={{ backgroundColor: '#4B2AFA', color: '#fff', borderRadius: '6px', padding: '10px', fontSize: '13px', fontWeight: 600, textAlign: 'center', marginTop: '8px' }}>Pay $49.00</div>
        </div>
      </div>
    </PhoneSimulator>
    <p style={{ textAlign: 'center', fontSize: '13px', marginTop: '8px' }}><b>What UXCam records</b></p>
  </div>
</div>

***

## Dashboard-First Rules (Zero Code)

The most efficient approach is configuring privacy rules directly in your UXCam dashboard:

1. Navigate to **Settings → Video Recording Privacy**
2. Choose **Record**, **Occlude**, or **Blur** globally *or* per screen
3. (Optional) Adjust **blur radius** and **Record gestures** toggle
4. Save → record a test session to verify

> 🛠️ **Priority ladder**
> Screen-specific Dashboard rule → Global Dashboard rule → SDK overlay/blur → SDK text-field occlusion.

***

## One-Liner Occlusion APIs

### Overlay an entire screen

```javascript
import { UXCamOcclusionType } from 'react-native-ux-cam/UXCamOcclusion';

const overlay = {
  type: UXCamOcclusionType.Overlay,
  color: 0x000000,
  hideGestures: true,
  screens: ['Login Form', 'Payment Details'],
};
```

### Blur with custom radius

```javascript
const blur = {
  type: UXCamOcclusionType.Blur,
  blurRadius: 15,
  hideGestures: false,
  screens: ['User Profile Settings', 'Account Overview'],
};
```

### Hide all text inputs

```javascript
import RNUxcam from 'react-native-ux-cam';

RNUxcam.occludeAllTextFields();

// Stop occluding when leaving the screen
RNUxcam.stopOccludingAllTextFields();
```

### Hide a single sensitive view

```javascript
import RNUxcam from 'react-native-ux-cam';

// Using ref callback
<TextInput
  placeholder="Password"
  secureTextEntry
  ref={(view) => RNUxcam.occludeSensitiveView(view)}
/>
```

***

## Apply at Config Time (Recommended)

Set all privacy rules during initialization to keep them in one place and avoid race conditions:

```javascript config/uxcam.js
import RNUxcam from 'react-native-ux-cam';
import { UXCamOcclusionType } from 'react-native-ux-cam/UXCamOcclusion';

const configuration = {
  userAppKey: 'YOUR_API_KEY',
  enableAutomaticScreenNameTagging: false,
  enableImprovedScreenCapture: true,
  occlusions: [
    {
      type: UXCamOcclusionType.Overlay,
      color: 0x000000,
      hideGestures: true,
      screens: ['Login Form', 'Payment Details', 'Password Change'],
    },
    {
      type: UXCamOcclusionType.Blur,
      blurRadius: 15,
      hideGestures: false,
      screens: ['User Profile Settings', 'Account Overview'],
    },
    {
      type: UXCamOcclusionType.OccludeAllTextFields,
      screens: ['Search Results', 'Customer Support Chat'],
    },
  ],
};

RNUxcam.optIntoVideoRecordings();
RNUxcam.startWithConfiguration(configuration);
```

***

## Protection Strategy Guide

| Sensitivity Level | Protection Method | Implementation | Maintenance |
|-------------------|-------------------|----------------|-------------|
| **Entire sensitive screens** | Screen overlay/blur | `screens: ['Login', 'Payment']` | **Low** — uses screen names |
| **Form sections** | Screen-based blur | `screens: ['ProfileEdit']` | **Low** — covers entire form areas |
| **Individual fields** | View-level occlusion | `occludeSensitiveView(ref)` | **High** — per-component setup |
| **All text inputs** | Text field occlusion | `occludeAllTextFields()` | **Medium** — affects all text inputs |

> 💡 **Best Practice**: Start with screen-based protection using your tagged screen names, then add granular view-level occlusion only where needed.

***

## Verification Checklist

1. Record a debug session through all sensitive flows
2. Play it back in the dashboard and confirm:
   - Passwords and card numbers are **overlaid or blurred**
   - Text fields on protected screens are hidden
   - WebView sensitive content is not visible
3. Use the **Record gestures** toggle if taps should *not* be shown on sensitive screens
4. Test on both iOS and Android — occlusion behaviour should be identical

***

## Troubleshooting

| Issue | Cause | Fix |
|-------|-------|-----|
| Overlay shows but gestures still visible | `hideGestures` defaulted to `false` | Set `hideGestures: true` |
| Text inputs still visible | Custom component wrapping `TextInput` | Use `occludeSensitiveView(ref)` manually |
| Occlusion not applying | Screen names don't match dashboard rules | Verify screen names match exactly |
| WebView leaks card numbers | WebView content not protected | See [WebView Occlusion guide](occlude-pii-data-within-webviews-2-react-native) |
| Protection only on one platform | Platform-specific rendering differences | Test on both iOS and Android simulators |

***

## Next Steps

<Cards columns={3}>
  <Card title="View-Level Occlusion" href="view-level-occlusion-react-native" icon="fa-eye-slash">
    Hide specific components — forms, inputs, and dynamically created views.
  </Card>

  <Card title="WebView Occlusion" href="occlude-pii-data-within-webviews-2-react-native" icon="fa-globe">
    Protect sensitive content inside WebViews using HTML classes, CSS injection, or full occlusion.
  </Card>

  <Card title="Send Events" href="../events" icon="fa-bolt">
    Capture business-critical user actions while maintaining privacy compliance.
  </Card>
</Cards>
