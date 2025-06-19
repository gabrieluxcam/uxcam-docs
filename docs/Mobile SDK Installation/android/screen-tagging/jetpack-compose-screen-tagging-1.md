---
title: Jetpack Compose Screen Tagging
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
UXCam's Automatic Screen Name Tagging is not supported out-of-the-box in Jetpack Compose. Unlike traditional Android views, Composables are functional components that lack lifecycle callbacks (like `onStart()`), which the automatic tagging relies on. However, you can manually tag screens by integrating with Compose's navigation and state management.

> 📘 Since `navigation-compose` is the officially recommended library for handling navigation in Compose, the following examples are based on its implementation.

The simplest approach is to tag the screen from your highest-level Composable where the `NavHostController` is created. This logic should be applied to every`navController` in your app where you want screen tagging to occur. The recommended method is to create a listener that observes the navigation controller's back stack for changes.

The approach is to listen to `LaunchedEffect` on the current route from `navController`.

## Example: Screen Tagging with a Navigation Listener

Example Jetpack Compose Navigation listener:

```coffeescript Android
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

#### **Pros**

* Accurate timing – emits exactly when the destination changes; no duplicate tags from recomposition.
* Clean analytics – parameterised routes collapse into one logical screen name, keeping UXCam dashboards uncluttered.
* Low overhead – one long-lived coroutine instead of restarting a LaunchedEffect for every navigation.
* Centralised logic – keeps navigation-side effects beside the NavHost, easier to maintain and test.

#### **Potential Cons**

* Loss of per-item detail – All `product/{id}` pages look identical in analytics. If you need product-level metrics, you will need to track them with a separate custom event.
* Multiple NavHosts – if your app has several controllers (e.g., bottom-nav, modal graph) you must duplicate or abstract this collector for each one.
* Manual mapping – you maintain the when block; missing a route means missed analytics until it’s added.

**The takeaway:** Placing `UXCam.tagScreenName()`inside the`currentBackStackEntryFlow` collector gives you precise, de-duplicated screen events with clean names, but it comes at the cost of losing per-argument granularity and requires some manual mapping.