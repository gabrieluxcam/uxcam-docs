---
title: User Identity & Properties
deprecated: false
hidden: true
metadata:
  robots: index
---
UXCam automatically records an **Install ID** for every fresh install.  
That’s handy, but:

* A user who *re‑installs* gets a **new** Install ID → journey is split.  
* A user who signs‑in on **multiple devices** generates one Install ID per device.  
* Support can’t search the dashboard for “user‑42” if you never tell UXCam who that is.

Fixing this is a one‑liner: **set the User ID as soon as you know it** (login, sign‑up, or deep‑link token).

---

## 1 When to call `setUserIdentity`

| Timing | Why it works |
|--------|--------------|
| **Immediately after successful login / sign‑up** | You have a backend‑verified ID and can safely map it to the Install ID. |
| **On every app launch** (optional but recommended) | Makes sure the ID is set even if the user logs in via push‑token or is already authenticated. The call is cheap and debounced. |

> **One call per session is fine.** Once UXCam links Install ID ↔︎ User ID, it persists on the device; extra calls simply noop.

---

### Code snippet

```java
// After your auth flow returns success
String uid = authResult.getUserId();      // e.g. "user_42"
UXCam.setUserIdentity(uid);
```
```kotlin
UXCam.setUserIdentity(authResult.userId)
```

---

## 2 Attach custom user properties

User properties let you segment funnels and heat‑maps by meaningful traits—plan, cohort, geography, etc.  
You can store **up to 100** key‑value pairs per user.

```java
UXCam.setUserProperty("plan", "pro");
UXCam.setUserProperty("signup_source", "google_ads");
UXCam.setUserProperty("nps_score", "9");
```

| Best‑practice | Reason |
|---------------|--------|
| **Use stable, non‑PII values** (IDs, enums) | Avoids GDPR headaches. You can always look up an email in your own DB. |
| **Snake_case keys** | Consistent naming prevents duplicates (`planType` vs `plan_type`). |
| **Update only when value changes** | The SDK overwrites the previous value; no need to resend unchanged props. |

---

## 3 Verify your work

1. Log in with a test account on a debug build.  
2. Wait for the session to upload.  
3. In the Dashboard, go to **Users → Search** and type your test *User ID*.  
4. Confirm you see **one merged user** with sessions from all devices and reinstalls.

If the user does **not** appear:

* Check Logcat—`UXCam: setUserIdentity called with …` should be logged once.  
* Ensure the call runs **after** `UXCam.startWithConfiguration()`.  
* Verify the ID is **not null / empty** (empty strings are ignored).

---

## 4 Troubleshooting cheat‑sheet

| Issue | Possible cause | Fix |
|-------|----------------|-----|
| Two records for the same user | Different casing (`User42` vs `user42`) | Normalise to lower‑case before calling. |
| User disappears after reinstall | `setUserIdentity` only called on first install | Call it every app launch or after silent token refresh. |
| Support can’t find user | You passed PII instead of UID and later obfuscated it | Store UID in ticket system and share with Support. |
| Property not visible | Sent as `int` but dashboard shows strings | Cast all numeric props to strings. |

---

## 5 QA checklist

- [ ] `UXCam.setUserIdentity()` fires **once per session** after login.  
- [ ] Dashboard search returns the user across reinstalls / devices.  
- [ ] Important properties (`plan`, `signup_source`, etc.) show under **User Profile**.  
- [ ] No emails or phone numbers stored unless DPA is signed.  
- [ ] Re‑install app → first session still linked after login.

---

### Next steps ➡️

* **Events** – tag key actions (e.g. upgrade_clicked).  
* **Push UXCam URL into your support tool** – deep‑link to the user’s session list from Zendesk or Intercom.

Happy identifying! 🔍
