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
When working with a Jetpack Compose Android app, UXCam's Automatic Screen Name Tagging is not supported because directly tracking composition routes is not as straightforward as in traditional Android architectures like Fragments or Activities due to Composables being functional components that don't have lifecycle-based callbacks like onStart(), onStop(), etc where the automatic tagging takes place. However, there are ways to achieve this by leveraging state management libraries.

> 📘 `navigation-compose` library is the recommended approach for navigation on Android official docs.
>
> Because of this, the below examples take into consideration the usage of said library.

Among all the possible ways the simplest way to tag the route as a screen from the highest level Composable. Generally, this is where `NavHostController` is created. There can be multiple `navController` within a single app and this should be added for all where screen tagging is expected.

The approach is to listen to `LaunchedEffect` on the current route from `navController`.

## Compose Screen Tagging Example:

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

* Loss of per-item detail – all product/id pages look identical in analytics; if you need product-level metrics you’ll have to add a separate event.
* Multiple NavHosts – if your app has several controllers (e.g., bottom-nav, modal graph) you must duplicate or abstract this collector for each one.
* Manual mapping – you maintain the when block; missing a route means missed analytics until it’s added.

**Bottom line:** placing UXCam.tagScreenName() inside the currentBackStackEntryFlow collector gives you precise, de-duplicated screen events with clean names, at the cost of losing per-argument granularity and a bit of mapping upkeep.