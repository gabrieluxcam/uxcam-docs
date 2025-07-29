---
title: Custom Events and Properties
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    The document explains how to track user interactions in an application using
    events and properties, providing methods for logging these events in code,
    and emphasizes the importance of consistent naming and relevant properties
    for effective analytics.
  robots: index
next:
  description: ''
---

# Event Tracking with UXCam

Screens tell you **where** users go; **events show what they do**.\
With a handful of well‑chosen events (5‑15 is ideal) plus descriptive properties, you can build funnels, spot drop‑offs, and debug support tickets in minutes.

---

## Choose the Right Moments to Track

| Event type           | Why tag it?              | Typical name examples                |
| -------------------- | ------------------------ | ------------------------------------ |
| **Flow milestones**  | Build conversion funnels | `Signup_Started`, `Signup_Completed` |
| **Key feature use**  | Measure adoption         | `Video_Export`, `AR_Scan`            |
| **Errors / cancels** | Quantify friction        | `Payment_Failed`, `Upload_Cancelled` |
| **A/B exposure**     | Compare cohorts          | `Variant_Shown_A`                    |

> **Tip:** Too many events dilute insight and bloat dashboards—focus on what drives decisions.

---

## Send a Basic Event

```dart
FlutterUxcam.logEvent("Signup_Started");
```

**Best‑practice**

- Use **PascalCase** or **snake_case**.
- Store names as **constants** to prevent typos.
- Remember that names are **case‑sensitive**: `signup_started` ≠ `Signup_Started`.

**Example with Constants:**

```dart
class EventNames {
  static const String signupStarted = "Signup_Started";
  static const String signupCompleted = "Signup_Completed";
  static const String paymentSucceeded = "Payment_Succeeded";
  static const String videoExport = "Video_Export";
}

// Usage
FlutterUxcam.logEvent(EventNames.signupStarted);
```

---

## Add Context with Properties

Attach up to **20** key‑value pairs to any event for richer analysis.

```dart
Map<String, dynamic> properties = {
  'plan': 'pro',
  'source': 'google_ads',
  'price_cents': 1499,
  'user_type': 'premium',
};

FlutterUxcam.logEvent("Payment_Succeeded", properties);
```

| Rule                                          | Reason                                        |
| --------------------------------------------- | --------------------------------------------- |
| Keys are **case‑sensitive**                   | `Plan` and `plan` create separate properties. |
| Values must be **String, Number, or Boolean** | Serialize complex objects to JSON if needed.  |
| Avoid **PII**                                 | Use hashed values or IDs to stay GDPR‑safe.   |
| Stop at **20 properties**                     | Extras are discarded and a warning is logged. |

**Complex Object Example:**

```dart
class Product {
  final String id;
  final String name;
  final double price;

  Product({required this.id, required this.name, required this.price});

  Map<String, dynamic> toEventProperties() {
    return {
      'product_id': id,
      'product_name': name,
      'price': price,
    };
  }
}

// Usage
final product = Product(id: '123', name: 'Premium Plan', price: 29.99);
FlutterUxcam.logEvent("Product_Viewed", product.toEventProperties());
```

---

## Automatic Events (No Code Needed)

| Auto event  | Fires when …                                              |
| ----------- | --------------------------------------------------------- |
| `Rage Tap`  | User taps ≥ 3 times within 300 ms at the same coordinates |
| `UI Freeze` | Main thread blocked for ≥ 2 s                             |

Combine these with your custom events for a complete picture of the user experience. You can find more details about _Rage Tap_ and _UI Freeze_ in the UXCam Help Center.

---

## Flutter-Specific Examples

### Navigation Events

```dart
// Track screen navigation
void onScreenChanged(String screenName) {
  FlutterUxcam.logEvent("Screen_Viewed", {
    'screen_name': screenName,
    'timestamp': DateTime.now().toIso8601String(),
  });
}
```

### User Interaction Events

```dart
// Track button taps
void onButtonTapped(String buttonName) {
  FlutterUxcam.logEvent("Button_Tapped", {
    'button_name': buttonName,
    'screen': ModalRoute.of(context)?.settings.name ?? 'unknown',
  });
}
```

### Form Events

```dart
// Track form interactions
void onFormSubmitted(String formName, Map<String, dynamic> formData) {
  FlutterUxcam.logEvent("Form_Submitted", {
    'form_name': formName,
    'field_count': formData.length,
    'has_errors': formData.values.any((value) => value == null),
  });
}
```

---

## Verify Your Events

1. Trigger the event in a **debug build** and wait for upload.
2. Open **Dashboard → Events**.
3. Confirm the new event and its properties appear.
4. Play a session replay—the event pin should align with the exact moment.

**Testing Example:**

```dart
// Add this to your debug builds for testing
void testEventLogging() {
  FlutterUxcam.logEvent("Test_Event", {
    'test_property': 'test_value',
    'timestamp': DateTime.now().toIso8601String(),
  });
}
```

---

## Troubleshooting Cheat‑Sheet

| Issue                 | Likely cause                                                     | Fix                                                                                                           |
| --------------------- | ---------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| Event missing         | Name typo or the event was logged before the SDK was initialized | Use constants; ensure the event is logged only after `FlutterUxcam.startWithConfiguration()` has been called. |
| Property not shown    | Sent > 20 props                                                  | Trim to 20; bundle extras in one JSON string                                                                  |
| Duplicate events      | Called inside loops / retries                                    | Add guards (e.g., send once per session)                                                                      |
| Mixed‑case duplicates | `Signup_started` vs `Signup_Started`                             | Standardise naming casing                                                                                     |

**Example Guard Implementation:**

```dart
class EventGuard {
  static final Set<String> _sentEvents = {};

  static void logEventOnce(String eventName, [Map<String, dynamic>? properties]) {
    if (!_sentEvents.contains(eventName)) {
      FlutterUxcam.logEvent(eventName, properties);
      _sentEvents.add(eventName);
    }
  }

  static void reset() {
    _sentEvents.clear();
  }
}
```

---

## QA Checklist

- [ ] All custom events appear in the **Events** dashboard and on session replays.
- [ ] Properties display correct values, types, and casing.
- [ ] No unwanted duplicates (case or spelling).
- [ ] Event pins align with the correct second in replay.
- [ ] No PII present in names or properties.
- [ ] Events are logged only after SDK initialization.
- [ ] Property count stays under 20 per event.

---

By leveraging events and properties effectively, you can unlock valuable insights into how users interact with your app, leading to better product decisions and enhanced user experiences.
