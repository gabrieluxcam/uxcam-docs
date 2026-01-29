---
title: Concepts
excerpt: Understand how UXCam works under the hood
deprecated: false
hidden: false
metadata:
  title: 'UXCam Concepts'
  description: 'Learn the fundamentals of session recording, privacy, and data architecture'
  robots: index
next:
  description: ''
---

# Concepts

This section explains how UXCam works at a fundamental level. Understanding these concepts helps you make better integration decisions and get more value from your analytics.

---

## Core Concepts

<Cards columns={2}>
  <Card title="How Session Recording Works" href="how-session-recording-works" icon="fa-solid fa-video">
    Understand the technical approach behind screen capture, data transmission, and session reconstruction
  </Card>

  <Card title="Privacy and Compliance" href="privacy-and-compliance" icon="fa-solid fa-shield-halved">
    GDPR, CCPA, consent management, and data handling practices
  </Card>

  <Card title="Understanding Occlusion" href="understanding-occlusion" icon="fa-solid fa-eye-slash">
    How UXCam protects sensitive data in recordings and the different occlusion strategies
  </Card>
</Cards>

---

## Quick Reference

| Concept | Description |
|---------|-------------|
| **Session** | A period of user activity from app open to background |
| **Screen** | A distinct view or page in your app |
| **Event** | A tracked user action (tap button, complete purchase) |
| **User Property** | An attribute assigned to a user (subscription tier, account type) |
| **Occlusion** | Hiding sensitive data in recordings |
| **Schematic Recording** | Wireframe-style recording that captures layout without actual content |

---

## Integration Concepts

Understanding these helps you configure UXCam effectively:

### Automatic vs Manual Screen Tagging

- **Automatic**: UXCam detects screens based on Activity/ViewController names
- **Manual**: You explicitly tag screens with meaningful names

Most teams use a hybrid approach - automatic detection with manual overrides for key screens.

### Recording Modes

- **Full Recording**: Captures actual screen content (requires occlusion for sensitive data)
- **Schematic Recording**: Captures screen structure as wireframes - more privacy-friendly

### Session Lifecycle

```
App Opens → Session Starts → User Interacts → App Backgrounds → Session Uploads → Dashboard Shows Data
```

Sessions upload when the app goes to background. If force-closed before upload, data may be lost.

---

## Further Reading

- [SDK Reference](/docs/all-uxcam-apis) - API method documentation
- [Getting Started](/docs/getting-started) - Platform integration guides
- [Troubleshooting](/docs/troubleshooting-android) - Common issues and solutions
