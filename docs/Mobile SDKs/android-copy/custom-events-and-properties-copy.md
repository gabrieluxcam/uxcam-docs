---
title: Send custom events
deprecated: false
hidden: false
metadata:
  robots: index
---
# 🎯 Event Tracking with UXCam

Screens tell you **where** users go; **events show what they do**.  
With a handful of well‑chosen events (5‑15 is ideal) plus descriptive properties, you can build funnels, spot drop‑offs, and debug support tickets in minutes.

---

## 1  Choose the Right Moments to Track

| Event type               | Why tag it?              | Typical name examples              |
| ------------------------ | ------------------------ | ---------------------------------- |
| **Flow milestones**      | Build conversion funnels | `Signup_Started`, `Signup_Completed` |
| **Key feature use**      | Measure adoption         | `Video_Export`, `AR_Scan`            |
| **Errors / cancels**     | Quantify friction        | `Payment_Failed`, `Upload_Cancelled` |
| **A/B exposure**         | Compare cohorts          | `Variant_Shown_A`                    |

> **Tip:** Too many events dilute insight and bloat dashboards—focus on what drives decisions.

---

## 2  Send a Basic Event

```java
UXCam.logEvent("Signup_Started");
```
```kotlin
UXCam.logEvent("Signup_Started")
```

**Best‑practice**

* Use **PascalCase** or **snake_case**.  
* Store names as **constants** to prevent typos.  
* Remember that names are **case‑sensitive**: `signup_started` ≠ `Signup_Started`.

---

## 3  Add Context with Properties

Attach up to **20** key‑value pairs to any event for richer analysis.

```java
HashMap<String, Object> props = new HashMap<>();
props.put("plan",        "pro");
props.put("source",      "google_ads");
props.put("price_cents", 1499);

UXCam.logEvent("Payment_Succeeded", props);
```

Rule | Reason
---- | ------
Keys are **case‑sensitive** | `Plan` and `plan` create separate properties.
Values must be **String or Number** | Serialize complex objects to JSON if needed.
Avoid **PII** | Use hashed values or IDs to stay GDPR‑safe.
Stop at **20 properties** | Extras are discarded and a warning is logged.

---

## 4  Automatic Events (No Code Needed)

| Auto event  | Fires when …                                                |
| ----------- | ----------------------------------------------------------- |
| `Rage Tap`  | User taps ≥ 3 times within 300 ms at the same coordinates   |
| `UI Freeze` | Main thread blocked for ≥ 2 s                               |

Use these alongside your custom events for a complete picture.  
More details in the UXCam Help Center: *Rage Tap* and *UI Freeze* articles.

---

## 5  Verify in 3 Minutes

1. Trigger the event in a **debug build** and wait for upload.  
2. Open **Dashboard → Events**.  
3. Confirm the new event and its properties appear.  
4. Play a session replay—the event pin should align with the exact moment.

---

## 6  Troubleshooting Cheat‑Sheet

| Issue                    | Likely cause                               | Fix                                                         |
| ------------------------ | ------------------------------------------ | ----------------------------------------------------------- |
| Event missing            | Name typo or called before SDK start       | Use constants; ensure call occurs after `UXCam.startWithConfiguration()` |
| Property not shown       | Sent > 20 props                             | Trim to 20; bundle extras in one JSON string                |
| Duplicate events         | Called inside loops / retries               | Add guards (e.g., send once per session)                    |
| Mixed‑case duplicates    | `Signup_started` vs `Signup_Started`       | Standardise naming casing                                   |

---

## 7  QA Checklist

* [ ] All custom events appear in **Events** and on session replays.  
* [ ] Properties display correct values, types, and casing.  
* [ ] No unwanted duplicates (case or spelling).  
* [ ] Event pins align with the correct second in replay.  
* [ ] No PII present in names or properties.

---
