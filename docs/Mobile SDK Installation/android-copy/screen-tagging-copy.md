---
title: Screen Tagging
deprecated: false
hidden: true
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

***

## 1 Start with a quick reality check

1. Record **one or two sessions** in your debug build.
2. Open any replay in the UXCam Dashboard and scan the **screen list** on the right.
3. For each entry, ask:

   | Check‑question                                       | Why it matters                                 |
   | ---------------------------------------------------- | ---------------------------------------------- |
   | Does the name describe the UI the user actually saw? | Makes funnels human‑readable.                  |
   | Would a heat‑map over the frames make sense?         | Ensures events aggregate on the right surface. |
   | Do any screens repeat with a **0 s** duration?       | Signals duplicate or missing tags.             |

> **If everything looks good** – simply rename screens in **Dashboard → Screens** (e.g. change `MainActivity` → **Home**) and you’re done. No code required.

***

## 2 When you *need* manual tags

| Symptom                                    | Typical cause                                | Fix                                                                |
| ------------------------------------------ | -------------------------------------------- | ------------------------------------------------------------------ |
| Same visual screen logged under two names  | Activity reused with different intent extras | Call `UXCam.tagScreenName("BetterName")` when you know the context |
| A flow shows **0 s** steps                 | Auto‑tag & manual tag both fire in one frame | Disable automatic tagging **or** remove duplicate manual calls     |
| Compose NavGraph only shows `MainActivity` | Single‑Activity architecture                 | Tag once per navigation change (see example)                       |

Only tag manually **where automatic tagging fails**; keep the rest automatic to minimise maintenance.

***

### 2.1 Disable automatic tagging (optional)

```java
UXConfig config = new UXConfig.Builder(BuildConfig.UXCAM_KEY)
        .enableAutomaticScreenNameTagging(false)
        .build();

UXCam.startWithConfiguration(config);
```

### 2.2 Manual tag in an Activity

```java
@Override protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_checkout);

    UXCam.tagScreenName("Checkout");   // one clear tag
}
```

### 2.3 Jetpack Compose navigation listener

```kotlin
@Composable
fun AppNavHost(navController: NavHostController) {

    LaunchedEffect(navController) {
        navController.currentBackStackEntryFlow.collect { entry ->
            when (entry.destination.route) {
                "home"         -> UXCam.tagScreenName("Home")
                "product/{id}" -> UXCam.tagScreenName("ProductDetail")
                "settings"     -> UXCam.tagScreenName("Settings")
            }
        }
    }

    NavHost(navController, startDestination = "home") { /* … */ }
}
```

*A single tag fires per navigation event, eliminating 0 s “ghost” screens.*

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