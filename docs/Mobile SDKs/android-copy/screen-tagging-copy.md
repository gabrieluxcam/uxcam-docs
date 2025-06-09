---
title: Tagging Screens
excerpt: >-
  Configure automatic and manual screen tagging to unlock heat‑maps, funnels and
  journey analytics.
deprecated: false
hidden: false
metadata:
  robots: index
next:
  pages:
    - slug: custom-users-and-properties-copy-1
      title: Set user IDs and properties
      type: basic
---
Good screen names turn raw replays into **actionable heat‑maps**, **screen analytics**, **conversion funnels** and **journey charts**.\
This guide walks you through **reviewing the automatic tags first**, deciding when (and how) to add manual tags, and finally verifying that every screen shows up with a meaningful duration.

## Automatic Screen Tagging - The Recommended, Simple Approach

<GitHubCallout type="note">The UXCam Android SDK auto‑tags most screens for you, but you can override or extend this behaviour where needed.</GitHubCallout>

For Android, UXCam's SDK automatically tags screens (i.e., activities), and this is the recommended default behaviour. Automatic tagging means that each activity your user navigates to is recorded automatically, without requiring you to manually intervene. This gives you the advantage of capturing user activity with minimal integration effort.

1. Record **one or two sessions** in your debug build.
2. Open any replay in the UXCam Dashboard and review the **screen list** on the right.
3. For each entry, ask:

| Check‑question                                       | Why it matters                                 |
| ---------------------------------------------------- | ---------------------------------------------- |
| Does the name describe the UI the user actually saw? | Makes funnels human‑readable.                  |
| Would a heat‑map over the frames make sense?         | Ensures events aggregate on the right surface. |
| Do any screens repeat with a **0 s** duration?       | Signals duplicate or missing tags.             |

> **If everything looks good** – simply rename screens in **Dashboard → Screens** (e.g. change `MainActivity` → **Home**) and you’re done. No code required.

### How to Enable or Disable Automatic Tagging?

It will always be enabled by default, but you can disable this from your SDK configuration options as such:

```coffeescript Kotlin
val config = UXConfig.Builder(BuildConfig.YOUR_UXCAM_KEY)
    .enableAutomaticScreenNameTagging(false) // TRUE BY DEFAULT
    .build()
UXCam.startWithConfiguration(config)
```
```coffeescript Java
UXConfig config = new UXConfig.Builder(BuildConfig.YOUR_UXCAM_KEY)
    .enableAutomaticScreenNameTagging(false) // TRUE BY DEFAULT
    .build()
UXCam.startWithConfiguration(config)
```

<GitHubCallout type="caution">Only disable automatic tagging if you experience incongruent or meaningless names in your session timeline or want to dedicate time to manually configure it.</GitHubCallout>

## Manual Tagging - When Do I Need It?

| Symptom                                                       | Typical cause                                                                      | Fix                                                                |
| ------------------------------------------------------------- | ---------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| Same visual screen logged under two names                     | Activity reused with different intent extras                                       | Call `UXCam.tagScreenName("BetterName")` when you know the context |
| You're using Jetpack Compose                                  | Traditional screen tagging requires extra setup                                    | Please refer to this section to tag screens in Jetpack Compose     |
| Compose NavGraph only shows `MainActivity`                    | Single‑Activity architecture                                                       | Tag once per navigation change (see example)                       |
| Same screen shows different interfaces/places within your app | You're using Fragments within some activities and are not automatically tagged yet | Tag the fragment or enable fragment based tagging                  |

<br />

### **Example**: **Tagging an Activity Manually**

<GitHubCallout type="warning">Tag **once per navigation event** to avoid 0 s duplicates.</GitHubCallout>

To tag a screen in an activity, use the following code in the `onCreate()` method of your activity:

```coffeescript Android
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
  
    UXCam.tagScreenName("Main Activity"); // Manually tag this activity with a screen name.
}
```

### **Example: Tagging a Fragment Manually**

Similarly, to tag a fragment, place the code in the `onResume()` method of the fragment:

```coffeescript Android
@Override
public void onResume() {
    super.onResume();
    UXCam.tagScreenName("Fragment Name"); // Manually tag this fragment with a screen name.
}

```

<br />

***

<br />

***

## 3 Verify your tags

1. Install a **debug** build, visit every tagged screen, then background the app.
2. Once the session uploads, check:

   * Each screen appears **exactly once**, with duration **> 0 s**.
   * No “Unknown” or class‑name screens remain.
   * Names match your analytics language.

If something is off, look for duplicate tag calls or a missing route handler.

***

## 4 Troubleshooting cheat‑sheet

| Issue                         | Quick diagnosis                          | Solution                                 |
| ----------------------------- | ---------------------------------------- | ---------------------------------------- |
| **0 s screens**               | Duplicate tag same frame (auto + manual) | Disable auto tagging or remove extra tag |
| **Screen missing**            | NavController route not handled          | Add case in `when(route)`                |
| **Random class names**        | Forgot to rename in Dashboard            | Edit in **Screens** tab                  |
| **Stale name after refactor** | Hard‑coded tag string                    | Update `UXCam.tagScreenName()` constant  |

***

## 5 Edge‑cases to watch for

| Potential gap                                   | What can break                         | How to guard                                          |
| ----------------------------------------------- | -------------------------------------- | ----------------------------------------------------- |
| **Orientation change** recreates activity       | Duplicate tag → 0 s screen             | Debounce tags or handle `configChanges` in manifest   |
| **Dialogs / BottomSheets** within same activity | Heat‑map merges with parent screen     | Tag inside `show()` – e.g. `UploadDialog`             |
| **Split‑screen mode** on tablets                | Sidebar never gains focus → no tag     | Tag from `onStart()` in multi‑window mode             |
| **WebView checkout flow**                       | Every step recorded as “WebViewScreen” | Trigger tags via JS bridge on URL change              |
| **Dynamic Feature Modules**                     | ProGuard strips UXCam call sites       | Keep `com.uxcam` package in base `proguard-rules.pro` |
| **Deep‑link cold launch**                       | First screen appears before SDK start  | Initialise SDK in `Application` class                 |
| **Internationalised tag strings**               | “Profile” vs “Perfil” split analytics  | Tag with a constant key, translate in Dashboard       |
| **Rapid tab switching**                         | Tags \<  300 ms apart collapse to 0 s  | Debounce tag logic in nav listener                    |

***

## 6 QA checklist

Tick each box before handing the build to QA or releasing:

* [ ] Every visually distinct screen appears **once** with duration **> 0 s**
* [ ] Screen names are clean, without class names or typos
* [ ] No duplicate names differing only by case or locale
* [ ] Heat‑maps align with tappable areas on a random sample
* [ ] Sensitive screens remain blurred / occluded after tagging changes

Happy tagging! 🎯