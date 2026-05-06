---
title: Privacy and Compliance
excerpt: GDPR, CCPA, consent management, and data handling
deprecated: false
hidden: false
metadata:
  title: 'Privacy and Compliance - UXCam'
  description: 'How UXCam handles privacy, data protection, and regulatory compliance'
  robots: index
next:
  description: ''
---

# Privacy and Compliance

UXCam is designed with privacy at its core. This guide explains our data handling practices, regulatory compliance, and the tools available to meet your privacy requirements.

---

## Data Protection Overview

### What UXCam Collects

| Data Type | Purpose | Retention |
|-----------|---------|-----------|
| Session recordings | UX analysis, debugging | Configurable (default 90 days) |
| Touch/gesture events | Interaction analysis | With session |
| Screen names | Navigation analytics | With session |
| Custom events | Feature usage tracking | With session |
| User properties | Segmentation | Until deletion request |
| Device info | Technical context | With session |

### What UXCam Does NOT Collect

- Audio or voice recordings
- Keyboard input (actual characters typed)
- Location data (unless you explicitly send it)
- Biometric data
- Data from other apps

---

## Automatic Privacy Protections

UXCam provides automatic protection for common sensitive data:

### Password Fields

All standard password input fields are automatically occluded:
- `<input type="password">` (Web)
- `android:inputType="textPassword"` (Android)
- `UITextField` with `isSecureTextEntry` (iOS)

### Sensitive Form Fields

Fields marked as sensitive are automatically detected and masked:
- Credit card inputs
- Social security fields
- Common PII field patterns

<GitHubCallout type="note">Automatic detection is not exhaustive. Always verify sensitive data is properly occluded in your specific implementation.</GitHubCallout>

---

## Manual Privacy Controls

### Occlusion (Hiding Data)

You can explicitly hide any view or screen element:

```kotlin
// Android - Hide a specific view
UXCam.occludeSensitiveView(creditCardInput)

// Or hide an entire screen
UXCam.occludeSensitiveScreen(true)
```

```swift
// iOS - Hide a specific view
UXCam.occlude(sensitiveView, isBlurred: true)

// Or hide all subviews of type
UXCam.occludeAllTextFields(true)
```

See [Understanding Occlusion](understanding-occlusion) for complete options.

### Opt-Out Users

Completely stop recording for users who don't consent:

```javascript
// Stop recording and delete pending data
UXCam.optOutOverall();

// Check opt-out status
const isOptedOut = await UXCam.optOutStatus();

// Opt back in
UXCam.optIn();
```

### Recording Control

Pause recording during sensitive flows:

```javascript
// Stop current session recording
UXCam.stopSessionAndUploadData();

// Pause without ending session
UXCam.pauseScreenRecording();

// Resume recording
UXCam.resumeScreenRecording();
```

---

## GDPR Compliance

UXCam provides tools to meet GDPR requirements:

### Lawful Basis

Most UXCam deployments use **Legitimate Interest** as the lawful basis:
- UX improvement directly benefits users
- Data is anonymized where possible
- Users can easily opt out

Some teams prefer **Consent** - requiring explicit user agreement before recording.

### Data Subject Rights

| Right | UXCam Implementation |
|-------|---------------------|
| **Right to Access** | Export user data via API or dashboard |
| **Right to Erasure** | Delete user data via API or dashboard |
| **Right to Restrict Processing** | Use opt-out SDK methods |
| **Right to Portability** | Export sessions in standard formats |

### User Identification

By default, UXCam uses anonymous device IDs. To enable deletion requests:

```javascript
// Associate sessions with your user ID
UXCam.setUserIdentity("user_123");
```

Sessions can then be deleted by user ID through the dashboard or API.

### Data Processing Agreement

UXCam acts as a **Data Processor** under GDPR. Contact your account manager for a signed DPA.

---

## CCPA Compliance

For California Consumer Privacy Act compliance:

### Consumer Rights

| CCPA Right | Implementation |
|------------|----------------|
| Right to Know | Export user session data |
| Right to Delete | Delete user sessions |
| Right to Opt-Out | `UXCam.optOutOverall()` |
| Right to Non-Discrimination | N/A (analytics doesn't affect service) |

### Do Not Sell

UXCam does not sell personal information. Session data is used solely for the analytics services you've purchased.

---

## Implementation Recommendations

### Privacy-First Setup

```javascript
// 1. Check for consent before starting
if (userHasConsented) {
  // 2. Use video recording for maximum privacy
  UXCam.optIntoVideoRecordings();

  // 3. Configure occlusion upfront
  const config = {
    userAppKey: 'YOUR_KEY',
    // Occlude all text inputs by default
    occlusion: {
      screens: [],
      inputs: true
    }
  };

  // 4. Associate with your user ID for data requests
  UXCam.setUserIdentity(userId);

  UXCam.startWithConfiguration(config);
}
```

### Consent Flow Example

```javascript
// Show consent UI
const ConsentDialog = () => {
  const handleAccept = () => {
    saveConsent(true);
    initializeUXCam();
  };

  const handleDecline = () => {
    saveConsent(false);
    UXCam.optOutOverall();
  };

  return (
    <Dialog>
      <Text>We use UXCam to improve app experience...</Text>
      <Button onPress={handleAccept}>Accept</Button>
      <Button onPress={handleDecline}>Decline</Button>
    </Dialog>
  );
};
```

---

## Security Measures

### Data In Transit

- TLS 1.2+ encryption for all data transmission
- Certificate pinning on mobile SDKs
- No sensitive data in URLs or logs

### Data At Rest

- Encrypted storage on AWS infrastructure
- SOC 2 Type II certified
- Regular security audits

### Access Control

- Role-based access in dashboard
- Audit logs for data access
- SSO/SAML support for enterprise

---

## Compliance Documentation

- **Privacy Policy Template**: Contact support for language to include in your privacy policy
- **DPA**: Available on request for enterprise customers
- **SOC 2 Report**: Available under NDA for enterprise customers
- **GDPR Assessment**: Self-assessment documentation available

---

## Further Reading

- [Understanding Occlusion](understanding-occlusion) - Technical details on hiding data
- [How Session Recording Works](how-session-recording-works) - What gets captured
- [Opt-In/Opt-Out](/docs/opt-in-opt-out) - SDK methods for consent management
