---
title: Navigator Route Tracking
excerpt: Comprehensive screen analytics using Flutter's navigation system
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---

# Navigator Route Tracking

Implement comprehensive screen analytics by leveraging Flutter's navigation system, enabling automatic screen tagging, route-based analytics, and navigation flow tracking.

## Overview

Flutter's Navigator system provides powerful hooks for tracking user navigation patterns:

- **Automatic Screen Detection**: Tag screens based on route names and arguments
- **Navigation Flow Analysis**: Track user journeys through your app
- **Route-Based Analytics**: Analyze specific navigation paths and patterns
- **Deep Link Tracking**: Monitor how users enter your app from external sources

## Navigator Observer Implementation

### Basic Navigator Observer

```dart
class UXCamNavigatorObserver extends NavigatorObserver {
  @override
  void didPush(Route<dynamic> route, Route<dynamic>? previousRoute) {
    super.didPush(route, previousRoute);
    _handleRouteChange(route, 'push', previousRoute);
  }

  @override
  void didPop(Route<dynamic> route, Route<dynamic>? previousRoute) {
    super.didPop(route, previousRoute);
    if (previousRoute != null) {
      _handleRouteChange(previousRoute, 'pop', route);
    }
  }

  @override
  void didReplace({Route<dynamic>? newRoute, Route<dynamic>? oldRoute}) {
    super.didReplace(newRoute: newRoute, oldRoute: oldRoute);
    if (newRoute != null) {
      _handleRouteChange(newRoute, 'replace', oldRoute);
    }
  }

  void _handleRouteChange(
    Route<dynamic> route,
    String action,
    Route<dynamic>? previousRoute,
  ) {
    final screenName = _extractScreenName(route);
    final routeSettings = route.settings;

    // Tag screen in UXCam
    FlutterUxcam.tagScreenName(screenName);

    // Log navigation event
    FlutterUxcam.logEvent('navigation_event', {
      'action': action,
      'screen_name': screenName,
      'route_name': routeSettings.name ?? 'unnamed',
      'previous_screen': previousRoute != null
          ? _extractScreenName(previousRoute)
          : null,
      'arguments': _serializeArguments(routeSettings.arguments),
      'timestamp': DateTime.now().toIso8601String(),
    });
  }

  String _extractScreenName(Route<dynamic> route) {
    // Extract meaningful screen name from route
    final settings = route.settings;

    if (settings.name != null && settings.name!.isNotEmpty) {
      // Use route name if available
      return _formatRouteName(settings.name!);
    } else if (route is PageRoute) {
      // Fallback to route type
      return route.runtimeType.toString().replaceAll('Route', '');
    } else {
      // Generic fallback
      return 'Unknown Screen';
    }
  }

  String _formatRouteName(String routeName) {
    // Convert route names to readable screen names
    // Example: '/product/details' -> 'Product Details'
    return routeName
        .split('/')
        .where((part) => part.isNotEmpty)
        .map((part) => part.split('_').map(_capitalize).join(' '))
        .join(' - ');
  }

  String _capitalize(String text) {
    if (text.isEmpty) return text;
    return text[0].toUpperCase() + text.substring(1);
  }

  Map<String, dynamic>? _serializeArguments(Object? arguments) {
    if (arguments == null) return null;

    try {
      // Serialize arguments safely
      if (arguments is Map<String, dynamic>) {
        return arguments;
      } else if (arguments is String ||
                 arguments is num ||
                 arguments is bool) {
        return {'value': arguments};
      } else {
        return {'type': arguments.runtimeType.toString()};
      }
    } catch (e) {
      return {'error': 'serialization_failed'};
    }
  }
}
```

### Advanced Navigator Observer with Screen Duration

```dart
class AdvancedUXCamNavigatorObserver extends NavigatorObserver {
  final Map<Route, DateTime> _routeStartTimes = {};
  final Map<Route, String> _routeScreenNames = {};

  @override
  void didPush(Route<dynamic> route, Route<dynamic>? previousRoute) {
    super.didPush(route, previousRoute);

    // Record screen duration for previous route
    if (previousRoute != null) {
      _recordScreenDuration(previousRoute);
    }

    // Start timing new route
    _startRouteTracking(route, 'push');
  }

  @override
  void didPop(Route<dynamic> route, Route<dynamic>? previousRoute) {
    super.didPop(route, previousRoute);

    // Record duration for popped route
    _recordScreenDuration(route);

    // Resume tracking previous route
    if (previousRoute != null) {
      _resumeRouteTracking(previousRoute, 'pop');
    }
  }

  @override
  void didReplace({Route<dynamic>? newRoute, Route<dynamic>? oldRoute}) {
    super.didReplace(newRoute: newRoute, oldRoute: oldRoute);

    if (oldRoute != null) {
      _recordScreenDuration(oldRoute);
    }

    if (newRoute != null) {
      _startRouteTracking(newRoute, 'replace');
    }
  }

  void _startRouteTracking(Route route, String action) {
    final screenName = _extractScreenName(route);

    _routeStartTimes[route] = DateTime.now();
    _routeScreenNames[route] = screenName;

    // Tag screen
    FlutterUxcam.tagScreenName(screenName);

    // Log navigation
    _logNavigationEvent(route, action, screenName);
  }

  void _resumeRouteTracking(Route route, String action) {
    final screenName = _routeScreenNames[route] ?? _extractScreenName(route);

    // Don't restart timing for resumed routes
    if (!_routeStartTimes.containsKey(route)) {
      _routeStartTimes[route] = DateTime.now();
    }

    // Re-tag the resumed screen
    FlutterUxcam.tagScreenName(screenName);

    // Log resume event
    FlutterUxcam.logEvent('screen_resumed', {
      'screen_name': screenName,
      'action': action,
      'timestamp': DateTime.now().toIso8601String(),
    });
  }

  void _recordScreenDuration(Route route) {
    final startTime = _routeStartTimes[route];
    final screenName = _routeScreenNames[route];

    if (startTime != null && screenName != null) {
      final duration = DateTime.now().difference(startTime);

      FlutterUxcam.logEvent('screen_duration', {
        'screen_name': screenName,
        'duration_ms': duration.inMilliseconds,
        'duration_seconds': duration.inSeconds,
        'timestamp': DateTime.now().toIso8601String(),
      });

      // Clean up
      _routeStartTimes.remove(route);
      _routeScreenNames.remove(route);
    }
  }

  void _logNavigationEvent(Route route, String action, String screenName) {
    FlutterUxcam.logEvent('navigation_event', {
      'action': action,
      'screen_name': screenName,
      'route_name': route.settings.name ?? 'unnamed',
      'route_type': route.runtimeType.toString(),
      'arguments': _serializeArguments(route.settings.arguments),
      'timestamp': DateTime.now().toIso8601String(),
    });
  }

  // ... (include other helper methods from basic observer)
}
```

## Route-Based Configuration

### Named Routes Setup

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'My App',
      navigatorObservers: [
        AdvancedUXCamNavigatorObserver(),
      ],
      routes: {
        '/': (context) => HomeScreen(),
        '/login': (context) => LoginScreen(),
        '/profile': (context) => ProfileScreen(),
        '/product/list': (context) => ProductListScreen(),
        '/product/details': (context) => ProductDetailsScreen(),
        '/cart': (context) => ShoppingCartScreen(),
        '/checkout': (context) => CheckoutScreen(),
        '/settings': (context) => SettingsScreen(),
      },
      onGenerateRoute: (settings) {
        // Handle dynamic routes
        return _generateRoute(settings);
      },
      onUnknownRoute: (settings) {
        // Handle unknown routes
        return MaterialPageRoute(
          builder: (context) => NotFoundScreen(route: settings.name),
          settings: settings,
        );
      },
    );
  }

  Route<dynamic>? _generateRoute(RouteSettings settings) {
    // Handle parameterized routes
    final uri = Uri.parse(settings.name ?? '');

    switch (uri.path) {
      case '/product':
        final productId = uri.queryParameters['id'];
        if (productId != null) {
          return MaterialPageRoute(
            builder: (context) => ProductDetailsScreen(productId: productId),
            settings: RouteSettings(
              name: '/product/details',
              arguments: {'productId': productId},
            ),
          );
        }
        break;

      case '/user':
        final userId = uri.queryParameters['id'];
        if (userId != null) {
          return MaterialPageRoute(
            builder: (context) => UserProfileScreen(userId: userId),
            settings: RouteSettings(
              name: '/user/profile',
              arguments: {'userId': userId},
            ),
          );
        }
        break;
    }

    return null;
  }
}
```

### GoRouter Integration (go_router package)

```dart
class GoRouterUXCamObserver extends NavigatorObserver {
  @override
  void didPush(Route<dynamic> route, Route<dynamic>? previousRoute) {
    super.didPush(route, previousRoute);
    _handleGoRouterNavigation(route, 'push');
  }

  @override
  void didPop(Route<dynamic> route, Route<dynamic>? previousRoute) {
    super.didPop(route, previousRoute);
    if (previousRoute != null) {
      _handleGoRouterNavigation(previousRoute, 'pop');
    }
  }

  void _handleGoRouterNavigation(Route route, String action) {
    // Extract GoRouter-specific information
    final location = GoRouter.of(context).location;
    final screenName = _extractGoRouterScreenName(location, route);

    FlutterUxcam.tagScreenName(screenName);

    FlutterUxcam.logEvent('go_router_navigation', {
      'action': action,
      'screen_name': screenName,
      'location': location,
      'route_name': route.settings.name,
      'timestamp': DateTime.now().toIso8601String(),
    });
  }

  String _extractGoRouterScreenName(String location, Route route) {
    // Convert GoRouter location to screen name
    // Example: '/product/123' -> 'Product Details'
    final parts = location.split('/').where((p) => p.isNotEmpty).toList();

    if (parts.isEmpty) return 'Home';

    return parts
        .map((part) => part.replaceAll(RegExp(r'\d+'), ''))
        .where((part) => part.isNotEmpty)
        .map((part) => part[0].toUpperCase() + part.substring(1))
        .join(' ');
  }
}

// GoRouter setup with UXCam integration
final GoRouter _router = GoRouter(
  observers: [GoRouterUXCamObserver()],
  routes: [
    GoRoute(
      path: '/',
      name: 'home',
      builder: (context, state) => HomeScreen(),
    ),
    GoRoute(
      path: '/product/:id',
      name: 'product_details',
      builder: (context, state) {
        final productId = state.params['id']!;
        return ProductDetailsScreen(productId: productId);
      },
    ),
    // ... other routes
  ],
);
```

## Deep Link Tracking

### Deep Link Navigator Observer

```dart
class DeepLinkNavigatorObserver extends NavigatorObserver {
  static bool _isInitialNavigation = true;

  @override
  void didPush(Route<dynamic> route, Route<dynamic>? previousRoute) {
    super.didPush(route, previousRoute);

    if (_isInitialNavigation) {
      _handleInitialNavigation(route);
      _isInitialNavigation = false;
    } else {
      _handleRegularNavigation(route, 'push');
    }
  }

  void _handleInitialNavigation(Route route) {
    final screenName = _extractScreenName(route);
    final routeName = route.settings.name ?? 'unnamed';
    final arguments = route.settings.arguments;

    // Tag the initial screen
    FlutterUxcam.tagScreenName(screenName);

    // Determine if this was from a deep link
    bool isDeepLink = routeName != '/' && routeName != '/home';

    if (isDeepLink) {
      // Log deep link entry
      FlutterUxcam.logEvent('deep_link_entry', {
        'screen_name': screenName,
        'route_name': routeName,
        'entry_point': screenName,
        'arguments': _serializeArguments(arguments),
        'timestamp': DateTime.now().toIso8601String(),
      });

      // Set user property for deep link usage
      FlutterUxcam.setUserProperty('used_deep_link', 'true');
      FlutterUxcam.setUserProperty('last_deep_link_route', routeName);
    } else {
      // Log normal app launch
      FlutterUxcam.logEvent('app_launch', {
        'screen_name': screenName,
        'entry_type': 'normal',
        'timestamp': DateTime.now().toIso8601String(),
      });
    }
  }

  void _handleRegularNavigation(Route route, String action) {
    // Handle regular navigation as before
    final screenName = _extractScreenName(route);
    FlutterUxcam.tagScreenName(screenName);

    FlutterUxcam.logEvent('navigation_event', {
      'action': action,
      'screen_name': screenName,
      'route_name': route.settings.name ?? 'unnamed',
      'timestamp': DateTime.now().toIso8601String(),
    });
  }
}
```

## Navigation Analytics

### Navigation Flow Tracker

```dart
class NavigationFlowTracker {
  static final List<NavigationStep> _navigationHistory = [];
  static const int MAX_HISTORY_LENGTH = 50;

  static void trackNavigation({
    required String screenName,
    required String action,
    String? previousScreen,
    Map<String, dynamic>? context,
  }) {
    final step = NavigationStep(
      screenName: screenName,
      action: action,
      previousScreen: previousScreen,
      timestamp: DateTime.now(),
      context: context,
    );

    _navigationHistory.add(step);

    // Keep history manageable
    if (_navigationHistory.length > MAX_HISTORY_LENGTH) {
      _navigationHistory.removeAt(0);
    }

    // Log to UXCam
    FlutterUxcam.logEvent('navigation_step', {
      'screen_name': screenName,
      'action': action,
      'previous_screen': previousScreen,
      'step_number': _navigationHistory.length,
      'context': context ?? {},
      'timestamp': step.timestamp.toIso8601String(),
    });

    // Analyze navigation patterns
    _analyzeNavigationPatterns();
  }

  static void _analyzeNavigationPatterns() {
    if (_navigationHistory.length < 3) return;

    // Detect navigation patterns
    _detectBackAndForthPattern();
    _detectCircularNavigation();
    _detectDeepDiving();
  }

  static void _detectBackAndForthPattern() {
    if (_navigationHistory.length < 4) return;

    final recent = _navigationHistory.takeLast(4).toList();

    // Check for A -> B -> A -> B pattern
    if (recent.length == 4 &&
        recent[0].screenName == recent[2].screenName &&
        recent[1].screenName == recent[3].screenName &&
        recent[0].screenName != recent[1].screenName) {

      FlutterUxcam.logEvent('navigation_pattern_detected', {
        'pattern_type': 'back_and_forth',
        'screens': [recent[0].screenName, recent[1].screenName],
        'occurrences': 2,
        'timestamp': DateTime.now().toIso8601String(),
      });
    }
  }

  static void _detectCircularNavigation() {
    if (_navigationHistory.length < 5) return;

    final recent = _navigationHistory.takeLast(5).toList();
    final startScreen = recent.first.screenName;
    final endScreen = recent.last.screenName;

    if (startScreen == endScreen) {
      final uniqueScreens = recent.map((step) => step.screenName).toSet();

      FlutterUxcam.logEvent('navigation_pattern_detected', {
        'pattern_type': 'circular',
        'start_screen': startScreen,
        'unique_screens_visited': uniqueScreens.length,
        'total_steps': recent.length,
        'timestamp': DateTime.now().toIso8601String(),
      });
    }
  }

  static void _detectDeepDiving() {
    if (_navigationHistory.length < 5) return;

    final recent = _navigationHistory.takeLast(5).toList();
    final pushCount = recent.where((step) => step.action == 'push').length;

    if (pushCount >= 4) {
      FlutterUxcam.logEvent('navigation_pattern_detected', {
        'pattern_type': 'deep_diving',
        'consecutive_pushes': pushCount,
        'screens_visited': recent.map((s) => s.screenName).toList(),
        'timestamp': DateTime.now().toIso8601String(),
      });
    }
  }

  static List<NavigationStep> getNavigationHistory() {
    return List.unmodifiable(_navigationHistory);
  }

  static void clearHistory() {
    _navigationHistory.clear();
    FlutterUxcam.logEvent('navigation_history_cleared', {
      'timestamp': DateTime.now().toIso8601String(),
    });
  }
}

class NavigationStep {
  final String screenName;
  final String action;
  final String? previousScreen;
  final DateTime timestamp;
  final Map<String, dynamic>? context;

  NavigationStep({
    required this.screenName,
    required this.action,
    this.previousScreen,
    required this.timestamp,
    this.context,
  });
}
```

## Custom Route Matching

### Route Pattern Matcher

```dart
class RoutePatternMatcher {
  static final Map<RegExp, String> _routePatterns = {
    RegExp(r'^/product/\d+$'): 'Product Details',
    RegExp(r'^/user/\d+/profile$'): 'User Profile',
    RegExp(r'^/category/[\w-]+$'): 'Category View',
    RegExp(r'^/search\?.*'): 'Search Results',
    RegExp(r'^/order/\d+/tracking$'): 'Order Tracking',
  };

  static String matchRouteToScreenName(String routeName) {
    // Try to match against known patterns
    for (final entry in _routePatterns.entries) {
      if (entry.key.hasMatch(routeName)) {
        return entry.value;
      }
    }

    // Fallback to generic conversion
    return _convertRouteToScreenName(routeName);
  }

  static String _convertRouteToScreenName(String routeName) {
    if (routeName == '/') return 'Home';

    return routeName
        .split('/')
        .where((part) => part.isNotEmpty)
        .map((part) => part.split('_').map(_capitalize).join(' '))
        .join(' - ');
  }

  static String _capitalize(String text) {
    if (text.isEmpty) return text;
    return text[0].toUpperCase() + text.substring(1);
  }
}
```

## Handling Bottom Navigation and Tab Navigation Limitations

> ⚠️ **Important Limitation**: The automatic tagging feature currently does not support Bottom Navigation or Tab Navigation for tagging when using Navigator 1.0 or 2.0. This section provides advanced solutions for these scenarios.

### Why Advanced Solutions Are Needed

When using `BottomNavigationBar` or `TabBar`, the automatic tagging may only capture the parent screen name (e.g., "MainScreen") rather than the specific tab content (e.g., "Home Tab", "Profile Tab"). This can lead to:

- **Incomplete user journey tracking**: All tab interactions appear under one screen name
- **Missing heatmap data**: User interactions are aggregated incorrectly
- **Poor funnel analysis**: Cannot distinguish between different tab content
- **Inaccurate navigation patterns**: Pattern detection fails to identify tab-specific behaviors

### Advanced Bottom Navigation Tracking

For apps with bottom navigation, combine the basic hybrid approach with advanced analytics:

```dart
class AdvancedBottomNavObserver extends NavigatorObserver {
  int _currentTabIndex = 0;
  final List<String> _tabNames = ['Home', 'Profile', 'Settings'];
  final Map<int, DateTime> _tabStartTimes = {};

  void onTabChanged(int index) {
    // Record duration for previous tab
    _recordTabDuration(_currentTabIndex);

    _currentTabIndex = index;
    final screenName = _tabNames[index];

    // Start timing new tab
    _tabStartTimes[index] = DateTime.now();

    // Tag the tab change
    FlutterUxcam.tagScreenName(screenName);

    // Log advanced analytics
    FlutterUxcam.logEvent('tab_navigation', {
      'tab_index': index,
      'tab_name': screenName,
      'previous_tab': _currentTabIndex != index ? _tabNames[_currentTabIndex] : null,
      'timestamp': DateTime.now().toIso8601String(),
    });

    // Track tab usage patterns
    NavigationFlowTracker.trackNavigation(
      screenName: screenName,
      action: 'tab_change',
      context: {'tab_index': index},
    );
  }

  void _recordTabDuration(int tabIndex) {
    final startTime = _tabStartTimes[tabIndex];
    if (startTime != null) {
      final duration = DateTime.now().difference(startTime);

      FlutterUxcam.logEvent('tab_duration', {
        'tab_name': _tabNames[tabIndex],
        'tab_index': tabIndex,
        'duration_ms': duration.inMilliseconds,
        'duration_seconds': duration.inSeconds,
        'timestamp': DateTime.now().toIso8601String(),
      });

      _tabStartTimes.remove(tabIndex);
    }
  }

  @override
  void didPush(Route<dynamic> route, Route<dynamic>? previousRoute) {
    super.didPush(route, previousRoute);
    // Handle regular navigation alongside tab navigation
    _handleRegularNavigation(route, 'push');
  }

  void _handleRegularNavigation(Route route, String action) {
    final screenName = _extractScreenName(route);
    FlutterUxcam.tagScreenName(screenName);

    FlutterUxcam.logEvent('navigation_event', {
      'action': action,
      'screen_name': screenName,
      'current_tab': _tabNames[_currentTabIndex],
      'timestamp': DateTime.now().toIso8601String(),
    });
  }
}
```

### Advanced TabBar with Analytics

```dart
class AdvancedTabController extends StatefulWidget {
  @override
  _AdvancedTabControllerState createState() => _AdvancedTabControllerState();
}

class _AdvancedTabControllerState extends State<AdvancedTabController>
    with SingleTickerProviderStateMixin {
  late TabController _tabController;
  final List<String> _tabNames = ['Products', 'Orders', 'Analytics'];
  final Map<int, DateTime> _tabStartTimes = {};

  @override
  void initState() {
    super.initState();
    _tabController = TabController(length: 3, vsync: this);

    _tabController.addListener(() {
      if (!_tabController.indexIsChanging) {
        _handleTabChange();
      }
    });

    // Start timing initial tab
    _tabStartTimes[0] = DateTime.now();
  }

  void _handleTabChange() {
    final previousIndex = _tabController.previousIndex;
    final currentIndex = _tabController.index;
    final tabName = _tabNames[currentIndex];

    // Record duration for previous tab
    _recordTabDuration(previousIndex);

    // Start timing new tab
    _tabStartTimes[currentIndex] = DateTime.now();

    // Basic tagging
    FlutterUxcam.tagScreenName(tabName);

    // Advanced analytics
    FlutterUxcam.logEvent('tab_analytics', {
      'tab_name': tabName,
      'tab_index': currentIndex,
      'previous_tab': _tabNames[previousIndex],
      'tab_switch_count': _calculateTabSwitchCount(),
      'timestamp': DateTime.now().toIso8601String(),
    });

    // Pattern detection
    NavigationFlowTracker.trackNavigation(
      screenName: tabName,
      action: 'tab_switch',
      context: {
        'tab_index': currentIndex,
        'tab_count': _tabNames.length,
        'previous_tab_index': previousIndex,
      },
    );
  }

  void _recordTabDuration(int tabIndex) {
    final startTime = _tabStartTimes[tabIndex];
    if (startTime != null) {
      final duration = DateTime.now().difference(startTime);

      FlutterUxcam.logEvent('tab_duration', {
        'tab_name': _tabNames[tabIndex],
        'tab_index': tabIndex,
        'duration_ms': duration.inMilliseconds,
        'duration_seconds': duration.inSeconds,
        'timestamp': DateTime.now().toIso8601String(),
      });
    }
  }

  int _calculateTabSwitchCount() {
    // Calculate how many times user has switched tabs in this session
    return _tabStartTimes.length;
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
    // Record final tab duration
    _recordTabDuration(_tabController.index);
    _tabController.dispose();
    super.dispose();
  }
}
```

### GoRouter with Advanced Bottom Navigation

```dart
class GoRouterWithBottomNav extends StatefulWidget {
  @override
  _GoRouterWithBottomNavState createState() => _GoRouterWithBottomNavState();
}

class _GoRouterWithBottomNavState extends State<GoRouterWithBottomNav> {
  int _currentIndex = 0;
  final Map<int, DateTime> _tabStartTimes = {};

  @override
  void initState() {
    super.initState();
    _tabStartTimes[0] = DateTime.now();
  }

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

          // Advanced navigation tracking
          _handleAdvancedNavigationChange(index);
        },
        items: [
          BottomNavigationBarItem(icon: Icon(Icons.home), label: 'Home'),
          BottomNavigationBarItem(icon: Icon(Icons.person), label: 'Profile'),
          BottomNavigationBarItem(icon: Icon(Icons.settings), label: 'Settings'),
        ],
      ),
    );
  }

  void _handleAdvancedNavigationChange(int index) {
    final previousIndex = _currentIndex;
    final screenName = _getScreenName(index);

    // Record duration for previous tab
    _recordTabDuration(previousIndex);

    // Start timing new tab
    _tabStartTimes[index] = DateTime.now();

    // Manual tagging for navigation changes
    FlutterUxcam.tagScreenName(screenName);

    // Advanced analytics
    FlutterUxcam.logEvent('go_router_bottom_nav', {
      'action': 'tab_change',
      'screen_name': screenName,
      'tab_index': index,
      'previous_tab_index': previousIndex,
      'location': GoRouter.of(context).location,
      'timestamp': DateTime.now().toIso8601String(),
    });

    // Pattern detection
    NavigationFlowTracker.trackNavigation(
      screenName: screenName,
      action: 'bottom_nav_change',
      context: {
        'tab_index': index,
        'previous_tab': _getScreenName(previousIndex),
        'router_location': GoRouter.of(context).location,
      },
    );
  }

  void _recordTabDuration(int tabIndex) {
    final startTime = _tabStartTimes[tabIndex];
    if (startTime != null) {
      final duration = DateTime.now().difference(startTime);

      FlutterUxcam.logEvent('bottom_nav_duration', {
        'tab_name': _getScreenName(tabIndex),
        'tab_index': tabIndex,
        'duration_ms': duration.inMilliseconds,
        'duration_seconds': duration.inSeconds,
        'timestamp': DateTime.now().toIso8601String(),
      });
    }
  }

  String _getScreenName(int index) {
    return switch (index) {
      0 => "Home Screen",
      1 => "Profile Screen",
      2 => "Settings Screen",
      _ => "Unknown Screen"
    };
  }
}
```

### Advanced Pattern Detection for Navigation Bars

```dart
class NavigationBarPatternDetector {
  static final Map<String, int> _tabUsageCount = {};
  static final List<TabSwitch> _recentSwitches = [];
  static const int MAX_RECENT_SWITCHES = 10;

  static void recordTabSwitch({
    required String fromTab,
    required String toTab,
    required int fromIndex,
    required int toIndex,
  }) {
    // Update usage counts
    _tabUsageCount[toTab] = (_tabUsageCount[toTab] ?? 0) + 1;

    // Record switch
    final switch = TabSwitch(
      fromTab: fromTab,
      toTab: toTab,
      fromIndex: fromIndex,
      toIndex: toIndex,
      timestamp: DateTime.now(),
    );

    _recentSwitches.add(switch);

    // Keep recent switches manageable
    if (_recentSwitches.length > MAX_RECENT_SWITCHES) {
      _recentSwitches.removeAt(0);
    }

    // Analyze patterns
    _detectTabPatterns();
  }

  static void _detectTabPatterns() {
    if (_recentSwitches.length < 3) return;

    // Detect rapid switching
    _detectRapidSwitching();

    // Detect favorite tabs
    _detectFavoriteTabs();

    // Detect navigation patterns
    _detectNavigationPatterns();
  }

  static void _detectRapidSwitching() {
    if (_recentSwitches.length < 3) return;

    final recent = _recentSwitches.takeLast(3).toList();
    final timeSpan = recent.last.timestamp.difference(recent.first.timestamp);

    if (timeSpan.inSeconds < 5) {
      FlutterUxcam.logEvent('navigation_pattern_detected', {
        'pattern_type': 'rapid_tab_switching',
        'switches_count': recent.length,
        'time_span_seconds': timeSpan.inSeconds,
        'tabs_involved': recent.map((s) => s.toTab).toSet().toList(),
        'timestamp': DateTime.now().toIso8601String(),
      });
    }
  }

  static void _detectFavoriteTabs() {
    final mostUsedTab = _tabUsageCount.entries
        .reduce((a, b) => a.value > b.value ? a : b);

    if (mostUsedTab.value >= 5) {
      FlutterUxcam.logEvent('navigation_pattern_detected', {
        'pattern_type': 'favorite_tab',
        'tab_name': mostUsedTab.key,
        'usage_count': mostUsedTab.value,
        'timestamp': DateTime.now().toIso8601String(),
      });
    }
  }

  static void _detectNavigationPatterns() {
    if (_recentSwitches.length < 4) return;

    final recent = _recentSwitches.takeLast(4).toList();

    // Detect A -> B -> A -> B pattern
    if (recent.length == 4 &&
        recent[0].toTab == recent[2].toTab &&
        recent[1].toTab == recent[3].toTab &&
        recent[0].toTab != recent[1].toTab) {

      FlutterUxcam.logEvent('navigation_pattern_detected', {
        'pattern_type': 'tab_back_and_forth',
        'tabs': [recent[0].toTab, recent[1].toTab],
        'occurrences': 2,
        'timestamp': DateTime.now().toIso8601String(),
      });
    }
  }
}

class TabSwitch {
  final String fromTab;
  final String toTab;
  final int fromIndex;
  final int toIndex;
  final DateTime timestamp;

  TabSwitch({
    required this.fromTab,
    required this.toTab,
    required this.fromIndex,
    required this.toIndex,
    required this.timestamp,
  });
}
```

### Best Practices for Advanced Navigation Tracking

1. **Combine Automatic and Manual Tracking**: Use automatic tagging for main navigation and manual tagging for navigation bars
2. **Track Tab Duration**: Record how long users spend on each tab
3. **Detect Usage Patterns**: Identify favorite tabs and navigation behaviors
4. **Handle Edge Cases**: Account for rapid switching and unusual navigation patterns
5. **Maintain Performance**: Avoid heavy analytics code that could impact navigation responsiveness
6. **Test Thoroughly**: Verify that both automatic and manual tracking work correctly together

### Verification Checklist for Advanced Navigation

After implementing advanced navigation tracking, verify:

- [ ] Tab changes are properly tagged with meaningful names
- [ ] Tab duration is being recorded accurately
- [ ] Navigation patterns are being detected
- [ ] No conflicts between automatic and manual tagging
- [ ] Performance is not impacted by analytics code
- [ ] Deep links work correctly with navigation bar tracking
- [ ] Pattern detection provides actionable insights

<GitHubCallout type="tip">Advanced navigation tracking combines the simplicity of automatic tagging with the precision of manual tagging, providing comprehensive analytics for complex navigation patterns.</GitHubCallout>

## Best Practices

### Do's

- ✅ Use Navigator observers for automatic screen tagging
- ✅ Include meaningful route names in your navigation setup
- ✅ Track navigation patterns and user flows
- ✅ Handle deep links and initial navigation separately
- ✅ Log navigation duration and screen time
- ✅ Maintain navigation history for pattern analysis

### Don'ts

- ❌ Don't over-complicate screen name extraction
- ❌ Don't ignore unknown routes
- ❌ Don't forget to handle route parameters
- ❌ Don't log sensitive data in navigation events
- ❌ Don't create memory leaks with unbounded history
- ❌ Don't block navigation with heavy analytics code

## Testing Navigation Tracking

### Navigator Observer Testing

```dart
testWidgets('Navigator observer tracks navigation', (WidgetTester tester) async {
  final observer = UXCamNavigatorObserver();
  bool screenTagged = false;
  String lastScreenName = '';

  // Mock UXCam
  FlutterUxcam.tagScreenName = (name) {
    screenTagged = true;
    lastScreenName = name;
  };

  await tester.pumpWidget(
    MaterialApp(
      navigatorObservers: [observer],
      routes: {
        '/': (context) => HomeScreen(),
        '/details': (context) => DetailsScreen(),
      },
    ),
  );

  // Navigate to details
  await tester.tap(find.text('Go to Details'));
  await tester.pumpAndSettle();

  expect(screenTagged, isTrue);
  expect(lastScreenName, equals('Details'));
});
```

## Next Steps

- **[Privacy Protection](../sensitive-data-occlusion-flutter/)** - Implement data masking
- **[User Analytics](../users-and-properties-flutter)** - Connect navigation to user insights

---

_Master navigation tracking to understand user journeys and optimize your app's flow._
