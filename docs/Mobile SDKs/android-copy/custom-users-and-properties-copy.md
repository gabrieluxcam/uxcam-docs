---
title: Set User Identity & Properties
deprecated: false
hidden: false
metadata:
  robots: index
---
## Identify Users Reliably with `setUserIdentity`

UXCam gives every fresh install a random alias name based on unique **Install ID**.\
That works—until:

* The same person **re-installs** the app or shares the same device with another user.
* The same account signs-in on **multiple devices** (one Install ID per device).

**Fix:** call **`UXCam.setUserIdentity()`once per session as soon as the real user is known** (login, sign‑up, deep‑link token, silent auth).

***

### When to call `setUserIdentity`

| Trigger                                          | Why it works                                                       |
| ------------------------------------------------ | ------------------------------------------------------------------ |
| **Immediately after successful login / sign‑up** | Backend‑verified identifier—safe to map to the Install ID.         |
| **Every app launch** (recommended, idempotent)   | Catches silent/S‑SO log‑ins and reinstalls; the call is debounced. |

> One call per session is enough. After UXCam links **Install ID ↔︎ User ID**, the value persists; extra calls noop.

***

### Code snippet

```java
// After the auth flow succeeds
String uid = authResult.getUserId();      // e.g. "user_42"
UXCam.setUserIdentity(uid);
```

```kotlin
UXCam.setUserIdentity(authResult.userId)
```

***

## Enrich Users with Custom Properties

Attach up to **100** key–value pairs per user to segment funnels and heat‑maps by plan, cohort, geography, and more.

```java
UXCam.setUserProperty("plan",            "pro");
UXCam.setUserProperty("signup_source",   "google_ads");
UXCam.setUserProperty("nps_score",       "9");  // Cast numbers to strings
```

| **Best-practice**                    | **Why**                                                                  |
| ------------------------------------ | ------------------------------------------------------------------------ |
| Use stable, **non‑PII IDs / enums**  | Avoid GDPR headaches; you can always map to an email in your own DB.     |
| Prefer **snake\_case keys**          | Prevents duplicates (`planType` vs `plan_type`).                         |
| Update **only when a value changes** | The SDK overwrites the previous value—no need to resend unchanged props. |

> 🚧 Need to send PII (e.g. email)? Sign a DPA with UXCam first: [team@uxcam.com](mailto:team@uxcam.com).

***

## Verify Your Work

1. Log in with a test account on a debug build.
2. Wait for the session to upload.
3. In the Dashboard, open **Users → Search** and enter the test *User ID*.
4. You should see **one merged user** with sessions from all devices and reinstalls.

If the user is missing:

* Check Logcat for `UXCam: setUserIdentity called with ...`.
* Ensure the call runs **after** `UXCam.startWithConfiguration()`.
* Confirm the ID is **non‑null & non‑empty** (empty strings are ignored).

***

## Troubleshooting Cheat‑Sheet

| Issue                           | Likely cause                                   | Fix                                                             |
| ------------------------------- | ---------------------------------------------- | --------------------------------------------------------------- |
| Two records for the same user   | Different casing (`User42` vs `user42`)        | Convert IDs to lower‑case before sending.                       |
| User disappears after reinstall | `setUserIdentity` only called on first install | Also call it on every app launch or after silent token refresh. |
| Property not visible            | Sent as `int`; dashboard expects strings       | Cast all numeric values to strings.                             |

***

## QA Checklist

* `UXCam.setUserIdentity()` fires **once per session** after auth.
* Key properties (`plan`, `signup_source`, …) appear under **user properties**.
* No PII stored unless a DPA is in place.
* Re‑install the app → first session links correctly after login.

***