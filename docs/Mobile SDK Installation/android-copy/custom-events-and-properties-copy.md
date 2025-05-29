---
title: Send custom events with properties
deprecated: false
hidden: true
metadata:
  robots: index
---
# 🎯 Event Tagging

Screens tell you **where** users go; events reveal **what they do**.  
With a handful of well‑chosen events and properties you can build funnels, spot drop‑offs and debug support tickets in minutes.

---

## 1 Pick the right moments to log

| Event type | Why tag it? | Typical name |
|------------|-------------|--------------|
| **Flow milestones** | Build conversion funnels | `Signup_Started`, `Signup_Completed` |
| **Key feature use** | Measure adoption | `Video_Export`, `AR_Scan` |
| **Errors / cancels** | Quantify friction | `Payment_Failed`, `Upload_Cancelled` |
| **A/B variant exposure** | Compare cohorts | `Variant_Shown_A` |

> **Aim for 5‑15 core events.** Too many dilute insight and blow up dashboards.

---

## 2 Log a basic event
```java
UXCam.logEvent("Signup_Started");
```
```kotlin
UXCam.logEvent("Signup_Started")
```
*Best practice*: keep names **PascalCase** or **snake_case** and store them as constants to avoid typos.

---

## 3 Add context with properties

Up to **20 key‑value pairs** per event give colour to each action.
```java
HashMap<String, Object> props = new HashMap<>();
props.put("plan",        "pro");
props.put("source",      "google_ads");
props.put("price_cents", 1499);

UXCam.logEvent("Payment_Succeeded", props);
```

| Rule | Reason |
|------|--------|
| **Keys are case‑sensitive** | `Plan` ≠ `plan`. Pick one style. |
| **Values stored as String or Number** | Cast complex objects to JSON if needed. |
| **No PII** | Avoid GDPR headaches—use IDs or hashed values. |

---

## 4 Built‑in automatic events

| Auto event | Captured when… |
|------------|----------------|
| `Rage Tap` | User taps more than 3 times in less than 300 ms at same spot |
| `UI Freeze` | Main thread blocked more than 2 s |

These fire without code; add your own tags **in addition** for business logic.

---

## 5 Verify in 3 minutes

1. Trigger the event in a debug build.  
2. Open **Dashboard → Events**.  
3. Confirm your new event and its properties appear correctly.  
4. Check a replay: event pin should align with the correct moment.

---

## 6 Troubleshooting cheat‑sheet

| Issue | Likely cause | Fix |
|-------|--------------|-----|
| Event missing | Name typo, fire before SDK start | Store names in constants; ensure call happens after `UXCam.startWithConfiguration()` |
| Property not shown | Sent > 20 props | Trim to 20; aggregate extras into one JSON string |
| Duplicate events | Called in loop or retry logic | Add guard (e.g. send once per session) |
| Mixed‑case duplicates | `Signup_started` vs `Signup_Started` | Standardise naming convention |

---

## 7 QA checklist

- [ ] All events in recorded sessions appear in **Event** page and in Nav bar of session replay.
- [ ] Properties show correct values and casing.  
- [ ] No unwanted duplicate names (case or spelling).  
- [ ] Replaying a session shows event pins at the right second.  
- [ ] No PII present in names or properties.

Happy tracking! 📊