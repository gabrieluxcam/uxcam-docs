---
title: Custom Events and Properties
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    Events are essential for tracking user interactions in your app, providing
    insights for data-driven decisions to enhance user experience, and can be
    logged with properties for additional context, but should be named
    consistently and tested thoroughly.
  robots: index
next:
  description: ''
---
> 📘 **Automatic events already captured**
>
> The iOS SDK logs **Rage Taps** and **UI Freezes** out‑of‑the‑box—no code required. Use custom events only for product‑specific interactions.

---

#Event anatomy

| Part           | Example         | Notes                                                  |
| -------------- | --------------- | ------------------------------------------------------ |
| **Name**       | `Product Added` | Case‑sensitive, ≤ 256 UTF‑8 bytes.                     |
| **Properties** | `{ size: 42 }`  | Up to **20** per event, keys ≤ 32 chars, values ≤ 512. |

---

#Send a simple event

### UIKit / Objective‑C / Swift

```swift
UXCam.logEvent("Product Added")
```

### SwiftUI (no extra import)

```swift
UXCamCore.logEvent("Product Added")
```

> 🏷️  **Naming rule of thumb** – start with a *verb* (`Opened`, `Tapped`, `Completed`) so funnels read like sentences.

---

#Send an event with properties

```swift
let props: [String: Any] = [
    "sku": "SKU‑9876",
    "price": 29.9,
    "currency": "USD",
    "campaign": "spring‑sale"
]
UXCam.logEvent("Checkout Completed", withProperties: props)
```

*Properties support* **String**, **NSNumber**, **Bool**, **NSNull** and collections thereof. Complex objects should be flattened.

> 🚧 **20‑property cap** – extra keys are silently dropped; Xcode console prints a warning when `enableIntegrationLogging` is on.

---

#Best‑practice checklist

| ✅ Do                                           | 🚫 Don’t                               |
| ---------------------------------------------- | -------------------------------------- |
| Use *PascalCase* or *snake\_case*.             | Mix `Camel` and `kebab-case`.          |
| Reuse the same event name across app versions. | Append version numbers (`Clicked_v2`). |
| Store numeric values *as numbers*.             | Pass numbers as strings (`"42"`).      |
| Limit enums to under 10 distinct values.           | Log raw user input (`comment`).        |
| Add units in the key (`price_usd`).            | Create one key per currency.           |

---

#Batch helper (optional)

Fire several events in sequence and wait for the SDK to flush automatically:

```swift
func trackPaywallDismiss() {
    UXCam.logEvent("Paywall Dismissed")
    UXCam.logEvent("Trial Started", withProperties: [
        "plan": "Pro Monthly",
        "discount": true
    ])
}
```

No explicit flush call is required—the SDK uploads when the app resigns active or every 15 seconds on Wi‑Fi.

---

#Debug & verify

1. Enable **Console → Filter → UXCam** in Xcode.
2. Trigger the interaction in a Debug build.
3. Look for `📤 Event queued: Checkout Completed {…}`.
4. Background the app; open **Dashboard → Events**.
5. Check properties appear under **Event Details → Parameters**.

If nothing arrives after 30 seconds, confirm the device has internet and your project hasn’t **Disabled UXCam** in Dashboard settings.

---

#Troubleshooting table

| Symptom                       | Cause                           | Fix                                               |
| ----------------------------- | ------------------------------- | ------------------------------------------------- |
| **Event name truncated**      | > 256 bytes                     | Shorten name; move detail into a property         |
| **Key not stored**            | > 32 chars or reserved key      | Shorten or rename                                 |
| **Value missing**             | > 512 chars or unsupported type | Compress / summarise value                        |
| **Case‑split events**         | `signup` vs `SignUp` mismatch   | Adopt exact naming convention & audit codebase    |
| **Flood of identical events** | Called in a loop / animation    | Debounce or throttle calls (e.g. once per screen) |

---
