---
title: Screen Tagging
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    The document provides guidance on manually and automatically tagging screens in a Flutter app
    for accurate analytics, emphasizing the importance of disabling automatic
    tagging and using lifecycle methods like `initState()` for consistent screen
    identification. It also introduces the `FlutterUxcamNavigatorObserver` for
    automatic route capture, with considerations for navigation events and
    consistent naming to enhance data analysis.
  robots: index
next:
  description: ''
---

Good screen names turn raw replays into **actionable heat‑maps**, **screen analytics**, **conversion funnels** and **journey charts**. This guide walks you through **reviewing the automatic tags first**, deciding when (and how) to add manual tags, and finally verifying that every screen shows up with a meaningful duration.

# Automatic Screen Tagging - The Recommended, Simple Approach

While native frameworks may offer automatic tagging, in Flutter, the architecture requires manual tagging to ensure all screens are clearly and consistently identified in your analytics. This approach gives you full control over how each screen is represented, leading to more accurate and meaningful insights.

> 🚧 Important:
>
> Flutter UXCam SDK version 2.2.2 introduces the option to add tagging from the Flutter side of your app. This feature is optional and still under active development, so any feedback during testing is highly appreciated.
>
> Note: This feature currently does not support Bottom Navigation or Tab Navigation for tagging when using Navigator 1.0 or 2.0. However, ongoing work on Navigator 2.0 aims to address these issues in the future.

## Adding `FlutterUxcamNavigatorObserver` to Your App:

From FlutterUXCam SDK version 2.2.2, you can add a `FlutterUxcamNavigatorObserver` to your app, enabling automatic capture of your routes (screens) for screen tagging without requiring manual intervention.

### Steps to Integrate FlutterUxcamNavigatorObserver:

1. **Import the SDK:**\
   In your Dart file, import the `flutter_uxcam` package:\
   `import 'package:flutter_uxcam/flutter_uxcam.dart';`
2. **Disable Automatic Screen Name Tagging**\
   Ensure the configuration parameter `enableAutomaticScreenNameTagging` is set to `false`.
3. **Add the Navigation Observer**
   - **Using Navigator 1.0:**\ <br />\
     Add the `FlutterUxcamNavigatorObserver` to the `navigatorObservers` of your \`MaterialApp
     ```coffeescript Flutter
     MaterialApp(
       ...
       navigatorObservers: [
         FlutterUxcamNavigatorObserver(),
       ],
       ...
     )
     ```
   - **Using Navigator 2.0:**\ <br />\
     Add it to the observers for your router. Below is an example using GoRouter:
     ```coffeescript Flutter
     final GoRouter router = GoRouter(
       ...
       observers: [
         FlutterUxcamNavigatorObserver(),
       ],
       ...
     );
     ```
4. **Add Names to Your Routes**\ <br />\
   It is recommended to add names to your routes to ensure clear tagging. If no names are provided, routes may show up as default values such as '/'.

### Additional Considerations for Flutter

**Navigation Events**: If you're using a navigation package like flutter_bloc or provider, ensure that screen tagging is part of the navigation logic. This guarantees that every screen transition is tracked properly.

**Consistent Naming**: Be consistent with your screen names across the app. This will make analyzing data in the UXCam dashboard more intuitive and prevent confusion.

<br />

## Handling Bottom Navigation and Tab Navigation

> ⚠️ **Important Limitation**: The automatic tagging feature currently does not support Bottom Navigation or Tab Navigation for tagging when using Navigator 1.0 or 2.0. These scenarios require manual tagging for accurate analytics.

### Why Manual Tagging is Needed for Navigation Bars

When using `BottomNavigationBar` or `TabBar`, the automatic tagging may only capture the parent screen name (e.g., "MainScreen") rather than the specific tab content (e.g., "Home Tab", "Profile Tab"). This can lead to:

- **Incomplete user journey tracking**: All tab interactions appear under one screen name
- **Missing heatmap data**: User interactions are aggregated incorrectly
- **Poor funnel analysis**: Cannot distinguish between different tab content

### Hybrid Approach: Automatic + Manual Tagging

The best practice is to use automatic tagging for your main navigation flow and supplement it with manual tagging for navigation bars. Here's how to implement this:

#### Example 1: Bottom Navigation with Manual Tab Tagging

```dart
import 'package:flutter/material.dart';
import 'package:flutter_uxcam/flutter_uxcam.dart';

class MainScreen extends StatefulWidget {
  @override
  _MainScreenState createState() => _MainScreenState();
}

class _MainScreenState extends State<MainScreen> {
  int _currentIndex = 0;

  final List<Widget> _screens = [
    HomeTab(),
    ProfileTab(),
    SettingsTab(),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: _screens[_currentIndex],
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: _currentIndex,
        onTap: (index) {
          setState(() {
            _currentIndex = index;
          });

          // Manual tagging for tab changes
          switch (index) {
            case 0:
              UXCam.tagScreenName("Home Tab");
              break;
            case 1:
              UXCam.tagScreenName("Profile Tab");
              break;
            case 2:
              UXCam.tagScreenName("Settings Tab");
              break;
          }
        },
        items: [
          BottomNavigationBarItem(icon: Icon(Icons.home), label: 'Home'),
          BottomNavigationBarItem(icon: Icon(Icons.person), label: 'Profile'),
          BottomNavigationBarItem(icon: Icon(Icons.settings), label: 'Settings'),
        ],
      ),
    );
  }
}
```

#### Example 2: TabBar with Manual Tab Tagging

```dart
import 'package:flutter/material.dart';
import 'package:flutter_uxcam/flutter_uxcam.dart';

class TabScreen extends StatefulWidget {
  @override
  _TabScreenState createState() => _TabScreenState();
}

class _TabScreenState extends State<TabScreen> with SingleTickerProviderStateMixin {
  late TabController _tabController;

  @override
  void initState() {
    super.initState();
    _tabController = TabController(length: 3, vsync: this);

    // Listen to tab changes for manual tagging
    _tabController.addListener(() {
      if (!_tabController.indexIsChanging) {
        _tagCurrentTab();
      }
    });

    // Tag initial tab
    _tagCurrentTab();
  }

  void _tagCurrentTab() {
    switch (_tabController.index) {
      case 0:
        UXCam.tagScreenName("Products Tab");
        break;
      case 1:
        UXCam.tagScreenName("Orders Tab");
        break;
      case 2:
        UXCam.tagScreenName("Analytics Tab");
        break;
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Dashboard'),
        bottom: TabBar(
          controller: _tabController,
          tabs: [
            Tab(text: 'Products'),
            Tab(text: 'Orders'),
            Tab(text: 'Analytics'),
          ],
        ),
      ),
      body: TabBarView(
        controller: _tabController,
        children: [
          ProductsTab(),
          OrdersTab(),
          AnalyticsTab(),
        ],
      ),
    );
  }

  @override
  void dispose() {
    _tabController.dispose();
    super.dispose();
  }
}
```

#### Example 3: GoRouter with Bottom Navigation

```dart
import 'package:flutter/material.dart';
import 'package:flutter_uxcam/flutter_uxcam.dart';
import 'package:go_router/go_router.dart';

class AppWithBottomNav extends StatefulWidget {
  @override
  _AppWithBottomNavState createState() => _AppWithBottomNavState();
}

class _AppWithBottomNavState extends State<AppWithBottomNav> {
  int _currentIndex = 0;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: IndexedStack(
        index: _currentIndex,
        children: [
          HomeScreen(),
          ProfileScreen(),
          SettingsScreen(),
        ],
      ),
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: _currentIndex,
        onTap: (index) {
          setState(() {
            _currentIndex = index;
          });

          // Manual tagging for navigation changes
          _tagNavigationChange(index);
        },
        items: [
          BottomNavigationBarItem(icon: Icon(Icons.home), label: 'Home'),
          BottomNavigationBarItem(icon: Icon(Icons.person), label: 'Profile'),
          BottomNavigationBarItem(icon: Icon(Icons.settings), label: 'Settings'),
        ],
      ),
    );
  }

  void _tagNavigationChange(int index) {
    final String screenName = switch (index) {
      0 => "Home Screen",
      1 => "Profile Screen",
      2 => "Settings Screen",
      _ => "Unknown Screen"
    };

    UXCam.tagScreenName(screenName);
  }
}
```

### Best Practices for Hybrid Tagging

1. **Keep Automatic Tagging Enabled**: Let the `FlutterUxcamNavigatorObserver` handle your main navigation flow
2. **Manual Tagging for Navigation Bars**: Add manual tags in navigation change callbacks
3. **Consistent Naming Convention**: Use descriptive names like "Home Tab" or "Profile Screen"
4. **Avoid Duplicate Tags**: Ensure manual tags don't conflict with automatic ones
5. **Test Thoroughly**: Verify that both automatic and manual tags appear correctly in your dashboard

### Verification Checklist

After implementing hybrid tagging, verify:

- [ ] Main navigation flows are captured automatically
- [ ] Tab/bottom navigation changes are tagged manually
- [ ] No duplicate screen names appear in the dashboard
- [ ] Each tab/screen has meaningful duration (> 0s)
- [ ] Screen names are consistent with your analytics terminology

<GitHubCallout type="tip">This hybrid approach gives you the best of both worlds: automatic tagging for simplicity and manual tagging for precision where needed.</GitHubCallout>

<br />

# Manual Screen Tagging - For In Depth Customisation

> **IMPORTANT**: Please make sure to set `enableAutomaticScreenNameTagging`: **`false`** for better results

**Tagging a Screen Manually: One Simple Line of Code:**

In Flutter, tagging a screen manually is straightforward. You can use the following line of code to tag a screen:

```coffeescript Flutter
import 'package:flutter_uxcam/flutter_uxcam.dart';

UXCam.tagScreenName("Home Screen");
```

**Example**: **The Recommended Place to Tag**

To ensure your screens are tagged consistently, we recommend placing the tagging code inside the initState() method of your widget or using a navigation listener to call the screen tagging method every time the screen appears.

```coffeescript Flutter
import 'package:flutter/material.dart';
import 'package:flutter_uxcam/flutter_uxcam.dart';

class HomeScreen extends StatefulWidget {
  @override
  _HomeScreenState createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  @override
  void initState() {
    super.initState();
    UXCam.tagScreenName("Home Screen");
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Home Screen"),
      ),
      body: Center(
        child: Text("Welcome to the Home Screen!"),
      ),
    );
  }
}
```

This approach ensures that every time a user navigates back to this screen, it's correctly tagged in your analytics.

> 👍 Summary:
>
> - **Automatic Tagging Should be Off**: Due to how Flutter manages navigation, automatic tagging may not always be reliable.
> - **Manual Tagging**: Use lifecycle methods like initState() or navigation listeners to tag screens accurately every time they are shown.

By taking this approach, you can make data-driven decisions and gain actionable insights into how users navigate through your app.

---

## Verify Your Tags

So you've decided to manually tag some or all your screens, making sure this is properly set up is a fundamental part in ensuring heat-maps, funnels and user journey analytics is giving you full insights, so it is extremely important to verify everything works well before moving on:

1. In your debug environment, run the app and visit all the manually tagged screens, then background the app.
2. Once the session uploads, check:

   - Each screen appears **exactly once**, according to the user's navigation, with duration **> 0 s**.
   - No “Unknown” or class‑name (if fully manual) screens remain.
   - Names match your analytics language.

If something is off, look for duplicate tag calls or a missing route handler.

<GitHubCallout type="important">Having a solid screen tagging setup will make masking PII Data and occluding screens extremely easier.</GitHubCallout>

---

## Troubleshooting cheat‑sheet

| Issue                         | Quick diagnosis                          | Solution                                 |
| ----------------------------- | ---------------------------------------- | ---------------------------------------- |
| **0 s screens**               | Duplicate tag same frame (auto + manual) | Disable auto tagging or remove extra tag |
| **Screen missing**            | NavController route not handled          | Add case in `when(route)`                |
| **Random class names**        | Forgot to rename in Dashboard            | Edit in **Screens** tab                  |
| **Stale name after refactor** | Hard‑coded tag string                    | Update `UXCam.tagScreenName()` constant  |

---

## How to Tag WebViews

WebViews can be a unique challenge when it comes to screen tagging, as they often contain dynamic content. To properly tag your WebView screens, you can follow our detailed guide that walks you through the best practices and methods for effective WebView tagging.

For more information, click the button below:

[Tagging WebViews Documentation](/docs/web-view-tagging)

## Next Steps:

You've properly tagged screens and are ready to move on! Let's review some next steps you should take.

<Cards columns={3}>
    <Card title="Mask PII Data" href="/docs/sensitive-data-occlusion-flutter" icon="fa-credit-card">
    Protect Your Users' Privacy and PII Data
    > Mask or Blur Screens, Views and Fields
  </Card>

  <Card title="Assign User IDs" href="/docs/users-and-properties-flutter" icon="fa-user">
    Get The Full Picture with User Analytics
    > Assign Custom User IDs and Properties
  </Card>

  <Card title="Send Events" href="/docs/sending-events-flutter" icon="fa-question">
    Deeper Insights of Your Users' interactions
    > Send Events and Add Properties
  </Card>
</Cards>
