---
title: Custom Users and Properties
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    UXCam allows you to identify users with a unique User ID and send up to 100
    additional user properties for deeper insights into user behavior, while
    recommending against using Personally Identifiable Information unless a Data
    Processing Agreement is signed.
  robots: index
next:
  description: ''
---
> 🛡️ **Privacy first**
>
> By default UXCam assigns a random alias per install. Override it **only** when you have a stable internal ID. Skip email / phone unless you have a signed DPA.

***

# Set the user identity

Assign a **stable, non‑PII** identifier—e.g. the primary key from your auth service—so you can trace sessions across devices and releases.

### UIKit / Objective‑C / Swift

```swift
UXCam.setUserIdentity("your_user_id")
```

### SwiftUI (no extra import)

```swift
UXCamCore.setUserIdentity("your_user_id")
```

> ⏱️ **When to call** – as early as you know who the user is (post‑login, token refresh, deep‑link resume). Re‑calling with the *same* ID is a no‑op; with a **different** ID it starts a new session.

***

# Attach user properties (maximum 100 different properties)

Give each user richer context—plan, cohort, A/B bucket, etc.—for filtering and segmentation.

```swift
let traits: [String: Any] = [
    "plan": "Pro",
    "signup_source": "apple_search_ads",
    "projects": 7,
    "email_verified": true
]
for (key, value) in traits {
    UXCam.setUserProperty(key, value: value)
}
```

*Accepted value types* — `String`, `NSNumber` (`Int`, `Double`, `Bool`), `NSNull`. Complex objects should be flattened.

| Limit                   | Value       |
| ----------------------- | ----------- |
| **Max properties/user** | 100         |
| **Key length**          | ≤ 32 chars  |
| **Value length**        | ≤ 512 chars |

> 🚧 **Case‑sensitive keys** – `Plan` and `plan` create two columns. Stick to one convention.

***

# Naming & PII checklist

| ✅ Do                                   | 🚫 Don’t                           |
| -------------------------------------- | ---------------------------------- |
| Use stable IDs (`auth0_id`, `crm_id`). | Store plain emails / phone numbers |
| Prefer enums (`plan = free/premium`).  | Log free‑form text (`bio`).        |
| Compress ranges (`age_band = 25‑34`).  | Send exact DOB (`1994‑04‑17`).     |
| Store booleans (`has_card = true`).    | Encode as strings (`"true"`).      |
| Rotate IDs if a leak is suspected.     | Hard‑code IDs in the app.          |

***

# Verify in Dashboard

1. Run the app, log in as a test user, then background it.
2. Open **Dashboard → Users** and search for your `user_id`.
3. Confirm identity and each property appear under **User Details**.
4. Open a session replay; the header should show the same ID.

If nothing appears after 30 s, make sure:

* The device has internet.
* Dashboard project hasn’t **Disabled UXCam**.
* You didn’t exceed the 100‑property cap (console warns when `enableIntegrationLogging = true`).

***

# Troubleshooting table

<Table align={["left","left","left"]}>
  <thead>
    <tr>
      <th>
        Symptom
      </th>

      <th>
        Cause
      </th>

      <th>
        Fix
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        **ID not stored**
      </td>

      <td>
        Called *after* session started
      </td>

      <td>
        Set before `UXCam.start()` or in a new session
      </td>
    </tr>

    <tr>
      <td>
        **Property discarded**
      </td>

      <td>
        >  32‑char key / > 512‑char value
      </td>

      <td>
        Shorten / summarise
      </td>
    </tr>

    <tr>
      <td>
        **Duplicate users**
      </td>

      <td>
        Mixed `nil` and real IDs between runs
      </td>

      <td>
        Always set identity once user is known
      </td>
    </tr>

    <tr>
      <td>
        **Sensitive data flagged**
      </td>

      <td>
        Email or phone without DPA
      </td>

      <td>
        Hash or switch to surrogate ID; sign DPA if needed
      </td>
    </tr>

    <tr>
      <td>
        **Traits not searchable**
      </td>

      <td>
        Upper/lower‑case mismatch
      </td>

      <td>
        Standardise casing
      </td>
    </tr>
  </tbody>
</Table>

***