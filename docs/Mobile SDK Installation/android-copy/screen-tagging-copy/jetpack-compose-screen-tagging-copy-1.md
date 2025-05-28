---
title: Screen Tagging on Jetpack Compose
deprecated: false
hidden: true
metadata:
  robots: index
---
When working with a Jetpack Compose Android app, UXCam's Automatic Screen Name Tagging is not supported because directly tracking composition routes is not as straightforward as in traditional Android architectures like Fragments or Activities due to Composeables being functional components that don't have lifecycle-based callbacks like onStart(), onStop(), etc where the automatic tagging takes place. However, there are ways to achieve this by leveraging state management libraries.

> 📘 `navigation-compose` library is the recommended approach for navigation on Android official docs.
>
> Because of this, the below examples take into consideration the usage of said library.

Among all the possible ways the simplest way to tag the route as a screen from the highest level Composable. Generally, this is where `NavHostController` is created. There can be multiple `navController` within a single app and this should be added for all where screen tagging is expected.

The approach is to listen to `LaunchedEffect` on the current route from `navController`.

## Compose Screen Tagging Example:

Below is an example of how to achieve "automatic" tagging of your compose screens by finding a `fun` where `navController` is created with the method `rememberNavController()`.

```coffeescript Android
val navController = rememberNavController()

// Snippet for tagging screen on route
val navBackStackEntry by navController.currentBackStackEntryAsState()
val currentRoute = navBackStackEntry?.destination?.route
LaunchedEffect(currentRoute) {
  currentRoute?.let { 
    UXCam.tagScreenName(currentRoute)
  }
} 
```

This approach, while requiring you to set it up, should allow you to tag screens in your app according to your routes.