---
title: Widget-Level Screen Tagging
excerpt: Advanced screen tagging techniques for Flutter widgets and custom components
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---

# Widget-Level Screen Tagging

Master advanced screen tagging techniques specifically designed for Flutter's widget-based architecture, enabling precise analytics for complex UI compositions and custom components.

## Overview

Flutter's widget-based architecture requires specialized approaches to screen tagging that account for:
- **Widget Composition**: Complex screens built from multiple widgets
- **State Management**: StatefulWidget lifecycle and state changes
- **Custom Components**: Reusable widgets that need individual tracking
- **Modal Overlays**: Dialogs, bottom sheets, and overlay widgets
- **Tab Navigation**: TabBar and TabBarView tracking

## Basic Widget Tagging

### StatefulWidget Screen Tagging
```dart
class ProductDetailsScreen extends StatefulWidget {
  final String productId;
  
  const ProductDetailsScreen({Key? key, required this.productId}) : super(key: key);
  
  @override
  _ProductDetailsScreenState createState() => _ProductDetailsScreenState();
}

class _ProductDetailsScreenState extends State<ProductDetailsScreen> {
  @override
  void initState() {
    super.initState();
    
    // Tag screen with context
    FlutterUxcam.tagScreenName('Product Details - ${widget.productId}');
  }
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Product Details')),
      body: _buildContent(),
    );
  }
}
```

### StatelessWidget Screen Tagging
```dart
class WelcomeScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // Tag screen in build method for StatelessWidget
    WidgetsBinding.instance.addPostFrameCallback((_) {
      FlutterUxcam.tagScreenName('Welcome Screen');
    });
    
    return Scaffold(
      body: Center(
        child: Text('Welcome to our app!'),
      ),
    );
  }
}
```

## Advanced Widget Tagging Patterns

### Mixin-Based Tagging
```dart
mixin UXCamScreenTagging<T extends StatefulWidget> on State<T> {
  String get screenName;
  Map<String, dynamic>? get screenProperties => null;
  
  @override
  void initState() {
    super.initState();
    _tagScreen();
  }
  
  void _tagScreen() {
    FlutterUxcam.tagScreenName(screenName);
    
    // Add screen properties if provided
    final properties = screenProperties;
    if (properties != null) {
      FlutterUxcam.logEvent('screen_viewed', {
        'screen_name': screenName,
        ...properties,
      });
    }
  }
  
  // Method to retag screen if needed (for dynamic content)
  void retagScreen({String? newName, Map<String, dynamic>? newProperties}) {
    final name = newName ?? screenName;
    FlutterUxcam.tagScreenName(name);
    
    if (newProperties != null) {
      FlutterUxcam.logEvent('screen_retagged', {
        'screen_name': name,
        ...newProperties,
      });
    }
  }
}

// Usage
class ShoppingCartScreen extends StatefulWidget {
  @override
  _ShoppingCartScreenState createState() => _ShoppingCartScreenState();
}

class _ShoppingCartScreenState extends State<ShoppingCartScreen> 
    with UXCamScreenTagging {
  @override
  String get screenName => 'Shopping Cart';
  
  @override
  Map<String, dynamic>? get screenProperties => {
    'item_count': _cartItems.length,
    'total_value': _calculateTotal(),
  };
  
  List<CartItem> _cartItems = [];
  
  void _addItem(CartItem item) {
    setState(() {
      _cartItems.add(item);
    });
    
    // Retag with updated properties
    retagScreen(newProperties: screenProperties);
  }
}
```

### Custom Widget Screen Tagging
```dart
class CustomModalWidget extends StatefulWidget {
  final String modalType;
  final Map<String, dynamic>? context;
  
  const CustomModalWidget({
    Key? key,
    required this.modalType,
    this.context,
  }) : super(key: key);
  
  @override
  _CustomModalWidgetState createState() => _CustomModalWidgetState();
}

class _CustomModalWidgetState extends State<CustomModalWidget> {
  @override
  void initState() {
    super.initState();
    
    // Tag modal as separate screen
    final screenName = 'Modal - ${widget.modalType}';
    FlutterUxcam.tagScreenName(screenName);
    
    // Log modal appearance event
    FlutterUxcam.logEvent('modal_displayed', {
      'modal_type': widget.modalType,
      'context': widget.context ?? {},
      'timestamp': DateTime.now().toIso8601String(),
    });
  }
  
  @override
  void dispose() {
    // Log modal dismissal
    FlutterUxcam.logEvent('modal_dismissed', {
      'modal_type': widget.modalType,
      'duration_ms': _getModalDuration(),
    });
    super.dispose();
  }
  
  int _getModalDuration() {
    // Calculate how long modal was displayed
    return DateTime.now().millisecondsSinceEpoch - _displayStartTime;
  }
}
```

## Tab Navigation Tagging

### TabBar Screen Tagging
```dart
class MainTabScreen extends StatefulWidget {
  @override
  _MainTabScreenState createState() => _MainTabScreenState();
}

class _MainTabScreenState extends State<MainTabScreen> 
    with SingleTickerProviderStateMixin {
  late TabController _tabController;
  
  final List<String> _tabNames = [
    'Home',
    'Search',
    'Favorites',
    'Profile',
  ];
  
  @override
  void initState() {
    super.initState();
    
    _tabController = TabController(length: _tabNames.length, vsync: this);
    
    // Tag initial tab
    FlutterUxcam.tagScreenName('Tab - ${_tabNames[0]}');
    
    // Listen for tab changes
    _tabController.addListener(_onTabChanged);
  }
  
  void _onTabChanged() {
    if (_tabController.indexIsChanging) {
      final currentTab = _tabNames[_tabController.index];
      
      // Tag new tab screen
      FlutterUxcam.tagScreenName('Tab - $currentTab');
      
      // Log tab switch event
      FlutterUxcam.logEvent('tab_switched', {
        'tab_name': currentTab,
        'tab_index': _tabController.index,
        'previous_tab': _tabController.previousIndex,
      });
    }
  }
  
  @override
  void dispose() {
    _tabController.removeListener(_onTabChanged);
    _tabController.dispose();
    super.dispose();
  }
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        bottom: TabBar(
          controller: _tabController,
          tabs: _tabNames.map((name) => Tab(text: name)).toList(),
        ),
      ),
      body: TabBarView(
        controller: _tabController,
        children: [
          HomeTabWidget(),
          SearchTabWidget(),
          FavoritesTabWidget(),
          ProfileTabWidget(),
        ],
      ),
    );
  }
}
```

### PageView Screen Tagging
```dart
class OnboardingScreen extends StatefulWidget {
  @override
  _OnboardingScreenState createState() => _OnboardingScreenState();
}

class _OnboardingScreenState extends State<OnboardingScreen> {
  final PageController _pageController = PageController();
  int _currentPage = 0;
  
  final List<String> _pageNames = [
    'Onboarding - Welcome',
    'Onboarding - Features', 
    'Onboarding - Permissions',
    'Onboarding - Complete',
  ];
  
  @override
  void initState() {
    super.initState();
    
    // Tag initial page
    FlutterUxcam.tagScreenName(_pageNames[0]);
  }
  
  void _onPageChanged(int page) {
    setState(() {
      _currentPage = page;
    });
    
    // Tag new page
    FlutterUxcam.tagScreenName(_pageNames[page]);
    
    // Log page transition
    FlutterUxcam.logEvent('onboarding_page_viewed', {
      'page_index': page,
      'page_name': _pageNames[page],
      'progress': (page + 1) / _pageNames.length,
    });
  }
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: PageView.builder(
        controller: _pageController,
        onPageChanged: _onPageChanged,
        itemCount: _pageNames.length,
        itemBuilder: (context, index) => OnboardingPage(index: index),
      ),
    );
  }
}
```

## Dialog and Modal Tagging

### Dialog Screen Tagging
```dart
class DialogTaggingHelper {
  static Future<T?> showTaggedDialog<T>({
    required BuildContext context,
    required WidgetBuilder builder,
    required String dialogName,
    Map<String, dynamic>? dialogContext,
    bool barrierDismissible = true,
  }) async {
    // Tag dialog as screen
    FlutterUxcam.tagScreenName('Dialog - $dialogName');
    
    // Log dialog display
    FlutterUxcam.logEvent('dialog_displayed', {
      'dialog_name': dialogName,
      'context': dialogContext ?? {},
      'timestamp': DateTime.now().toIso8601String(),
    });
    
    final startTime = DateTime.now();
    
    final result = await showDialog<T>(
      context: context,
      barrierDismissible: barrierDismissible,
      builder: builder,
    );
    
    // Log dialog dismissal
    final duration = DateTime.now().difference(startTime);
    FlutterUxcam.logEvent('dialog_dismissed', {
      'dialog_name': dialogName,
      'duration_ms': duration.inMilliseconds,
      'result': result?.toString() ?? 'null',
    });
    
    return result;
  }
}

// Usage
class MyWidget extends StatelessWidget {
  void _showConfirmDialog(BuildContext context) {
    DialogTaggingHelper.showTaggedDialog(
      context: context,
      dialogName: 'Delete Confirmation',
      dialogContext: {'item_type': 'photo', 'item_id': '123'},
      builder: (context) => AlertDialog(
        title: Text('Confirm Delete'),
        content: Text('Are you sure you want to delete this photo?'),
        actions: [
          TextButton(
            onPressed: () => Navigator.of(context).pop(false),
            child: Text('Cancel'),
          ),
          TextButton(
            onPressed: () => Navigator.of(context).pop(true),
            child: Text('Delete'),
          ),
        ],
      ),
    );
  }
}
```

### Bottom Sheet Tagging
```dart
class BottomSheetTagging {
  static Future<T?> showTaggedModalBottomSheet<T>({
    required BuildContext context,
    required WidgetBuilder builder,
    required String sheetName,
    Map<String, dynamic>? sheetContext,
  }) async {
    // Tag bottom sheet as screen
    FlutterUxcam.tagScreenName('Bottom Sheet - $sheetName');
    
    // Log sheet display
    FlutterUxcam.logEvent('bottom_sheet_displayed', {
      'sheet_name': sheetName,
      'context': sheetContext ?? {},
    });
    
    final startTime = DateTime.now();
    
    final result = await showModalBottomSheet<T>(
      context: context,
      builder: builder,
    );
    
    // Log sheet dismissal
    final duration = DateTime.now().difference(startTime);
    FlutterUxcam.logEvent('bottom_sheet_dismissed', {
      'sheet_name': sheetName,
      'duration_ms': duration.inMilliseconds,
    });
    
    return result;
  }
}
```

## State Management Integration

### Provider Integration
```dart
class ScreenTaggingProvider extends ChangeNotifier {
  String _currentScreen = 'Unknown';
  final Map<String, DateTime> _screenStartTimes = {};
  
  String get currentScreen => _currentScreen;
  
  void tagScreen(String screenName, {Map<String, dynamic>? properties}) {
    // Log previous screen duration if exists
    if (_screenStartTimes.containsKey(_currentScreen)) {
      final duration = DateTime.now().difference(_screenStartTimes[_currentScreen]!);
      FlutterUxcam.logEvent('screen_duration', {
        'screen_name': _currentScreen,
        'duration_ms': duration.inMilliseconds,
      });
    }
    
    // Tag new screen
    FlutterUxcam.tagScreenName(screenName);
    _currentScreen = screenName;
    _screenStartTimes[screenName] = DateTime.now();
    
    // Log screen view event
    FlutterUxcam.logEvent('screen_viewed', {
      'screen_name': screenName,
      'timestamp': DateTime.now().toIso8601String(),
      ...?properties,
    });
    
    notifyListeners();
  }
}

// Usage in screens
class ProductScreen extends StatefulWidget {
  @override
  _ProductScreenState createState() => _ProductScreenState();
}

class _ProductScreenState extends State<ProductScreen> {
  @override
  void initState() {
    super.initState();
    
    // Use provider for screen tagging
    WidgetsBinding.instance.addPostFrameCallback((_) {
      context.read<ScreenTaggingProvider>().tagScreen(
        'Product Screen',
        properties: {'product_id': widget.productId},
      );
    });
  }
}
```

### BLoC Integration
```dart
abstract class ScreenTaggingEvent {}

class TagScreenEvent extends ScreenTaggingEvent {
  final String screenName;
  final Map<String, dynamic>? properties;
  
  TagScreenEvent(this.screenName, {this.properties});
}

class ScreenTaggingBloc extends Bloc<ScreenTaggingEvent, String> {
  ScreenTaggingBloc() : super('Unknown') {
    on<TagScreenEvent>(_onTagScreen);
  }
  
  void _onTagScreen(TagScreenEvent event, Emitter<String> emit) {
    // Tag screen in UXCam
    FlutterUxcam.tagScreenName(event.screenName);
    
    // Log screen view
    FlutterUxcam.logEvent('screen_viewed', {
      'screen_name': event.screenName,
      ...?event.properties,
    });
    
    emit(event.screenName);
  }
}
```

## Best Practices

### Do's
- ✅ Tag screens in `initState()` for StatefulWidgets
- ✅ Use `addPostFrameCallback()` for StatelessWidgets
- ✅ Include relevant context in screen names
- ✅ Track modal dialogs and bottom sheets as separate screens
- ✅ Log screen transitions and durations
- ✅ Use consistent naming conventions

### Don'ts
- ❌ Don't tag the same screen multiple times unnecessarily
- ❌ Don't include sensitive data in screen names
- ❌ Don't forget to tag modal overlays
- ❌ Don't use overly generic screen names
- ❌ Don't skip tab and page view tracking

## Testing Widget Tagging

### Widget Testing
```dart
testWidgets('Screen tagging test', (WidgetTester tester) async {
  // Mock UXCam for testing
  bool screenTagged = false;
  String taggedScreenName = '';
  
  // Build widget
  await tester.pumpWidget(
    MaterialApp(home: ProductDetailsScreen(productId: 'test123')),
  );
  
  // Wait for screen tagging
  await tester.pump();
  
  // Verify screen was tagged
  expect(screenTagged, isTrue);
  expect(taggedScreenName, contains('Product Details'));
});
```

## Next Steps

- **[Navigator Route Tracking](navigator-route-tracking.md)** - Advanced navigation-based tagging
- **[Privacy Protection](../sensitive-data-occlusion/)** - Implement data masking for widgets
- **[Advanced Configuration](../advanced-configuration-and-apis/)** - Recording control and optimization

---

*Master widget-level tagging to get precise analytics for your Flutter app's user interface.*