---
title: Widget Occlusion Patterns
excerpt: Advanced privacy protection techniques for Flutter widgets and components
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---

# Widget Occlusion Patterns

Master advanced privacy protection techniques specifically designed for Flutter's widget architecture, enabling precise data masking for complex UI components while maintaining user experience quality.

## Overview

Flutter's widget-based architecture requires specialized occlusion approaches that account for:
- **Widget Composition**: Complex screens built from multiple nested widgets
- **State Management**: Dynamic content that changes based on user state
- **Custom Components**: Reusable widgets with sensitive data
- **Layout Constraints**: Occlusion that respects Flutter's layout system
- **Performance**: Efficient occlusion that doesn't impact app performance

## Basic Widget Occlusion

### Single Widget Occlusion
```dart
import 'package:flutter_uxcam/flutter_uxcam.dart';

class SensitiveDataWidget extends StatefulWidget {
  final String sensitiveData;
  
  const SensitiveDataWidget({Key? key, required this.sensitiveData}) : super(key: key);
  
  @override
  _SensitiveDataWidgetState createState() => _SensitiveDataWidgetState();
}

class _SensitiveDataWidgetState extends State<SensitiveDataWidget> {
  @override
  Widget build(BuildContext context) {
    return UXCamOccludedWidget(
      child: Text(
        widget.sensitiveData,
        style: TextStyle(fontSize: 16),
      ),
    );
  }
}

// UXCam occlusion wrapper
class UXCamOccludedWidget extends StatelessWidget {
  final Widget child;
  final bool shouldOcclude;
  
  const UXCamOccludedWidget({
    Key? key,
    required this.child,
    this.shouldOcclude = true,
  }) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    if (shouldOcclude) {
      // Apply UXCam occlusion to this widget
      WidgetsBinding.instance.addPostFrameCallback((_) {
        _occludeWidget();
      });
    }
    
    return child;
  }
  
  void _occludeWidget() {
    // Get the render object of this widget
    final renderBox = context.findRenderObject() as RenderBox?;
    if (renderBox != null) {
      final position = renderBox.localToGlobal(Offset.zero);
      final size = renderBox.size;
      
      // Create occlusion rectangle
      FlutterUxcam.occludeRect(
        position.dx,
        position.dy,
        size.width,
        size.height,
      );
    }
  }
}
```

### Conditional Occlusion Widget
```dart
class ConditionalOcclusionWidget extends StatelessWidget {
  final Widget child;
  final bool Function() shouldOcclude;
  final String? occlusionReason;
  
  const ConditionalOcclusionWidget({
    Key? key,
    required this.child,
    required this.shouldOcclude,
    this.occlusionReason,
  }) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    return OcclusionBuilder(
      shouldOcclude: shouldOcclude(),
      occlusionReason: occlusionReason,
      child: child,
    );
  }
}

class OcclusionBuilder extends StatefulWidget {
  final Widget child;
  final bool shouldOcclude;
  final String? occlusionReason;
  
  const OcclusionBuilder({
    Key? key,
    required this.child,
    required this.shouldOcclude,
    this.occlusionReason,
  }) : super(key: key);
  
  @override
  _OcclusionBuilderState createState() => _OcclusionBuilderState();
}

class _OcclusionBuilderState extends State<OcclusionBuilder> {
  final GlobalKey _childKey = GlobalKey();
  
  @override
  void initState() {
    super.initState();
    _updateOcclusion();
  }
  
  @override
  void didUpdateWidget(OcclusionBuilder oldWidget) {
    super.didUpdateWidget(oldWidget);
    if (oldWidget.shouldOcclude != widget.shouldOcclude) {
      _updateOcclusion();
    }
  }
  
  void _updateOcclusion() {
    WidgetsBinding.instance.addPostFrameCallback((_) {
      if (widget.shouldOcclude) {
        _applyOcclusion();
      } else {
        _removeOcclusion();
      }
    });
  }
  
  void _applyOcclusion() {
    final renderBox = _childKey.currentContext?.findRenderObject() as RenderBox?;
    if (renderBox != null) {
      final position = renderBox.localToGlobal(Offset.zero);
      final size = renderBox.size;
      
      FlutterUxcam.occludeRect(
        position.dx,
        position.dy,
        size.width,
        size.height,
      );
      
      // Log occlusion event
      FlutterUxcam.logEvent('widget_occluded', {
        'reason': widget.occlusionReason ?? 'conditional_occlusion',
        'widget_size': '${size.width}x${size.height}',
        'timestamp': DateTime.now().toIso8601String(),
      });
    }
  }
  
  void _removeOcclusion() {
    // Note: UXCam doesn't provide a direct way to remove specific occlusions
    // This would require maintaining occlusion state and reapplying all others
    FlutterUxcam.logEvent('widget_occlusion_removed', {
      'reason': widget.occlusionReason ?? 'conditional_occlusion',
      'timestamp': DateTime.now().toIso8601String(),
    });
  }
  
  @override
  Widget build(BuildContext context) {
    return Container(
      key: _childKey,
      child: widget.child,
    );
  }
}
```

## Advanced Occlusion Patterns

### List Item Occlusion
```dart
class SensitiveListView extends StatelessWidget {
  final List<SensitiveItem> items;
  final bool Function(SensitiveItem) shouldOccludeItem;
  
  const SensitiveListView({
    Key? key,
    required this.items,
    required this.shouldOccludeItem,
  }) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      itemCount: items.length,
      itemBuilder: (context, index) {
        final item = items[index];
        
        return ConditionalOcclusionWidget(
          shouldOcclude: () => shouldOccludeItem(item),
          occlusionReason: 'sensitive_list_item',
          child: ListTile(
            leading: CircleAvatar(
              child: Text(item.initials),
            ),
            title: Text(item.name),
            subtitle: Text(item.sensitiveInfo),
            trailing: Icon(Icons.arrow_forward),
          ),
        );
      },
    );
  }
}

class SensitiveItem {
  final String name;
  final String sensitiveInfo;
  final String initials;
  final bool isConfidential;
  
  SensitiveItem({
    required this.name,
    required this.sensitiveInfo,
    required this.initials,
    this.isConfidential = false,
  });
}

// Usage
class ContactListScreen extends StatelessWidget {
  final List<SensitiveItem> contacts = [
    SensitiveItem(
      name: 'John Doe',
      sensitiveInfo: '+1-555-123-4567',
      initials: 'JD',
      isConfidential: true,
    ),
    // ... more contacts
  ];
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Contacts')),
      body: SensitiveListView(
        items: contacts,
        shouldOccludeItem: (item) => item.isConfidential,
      ),
    );
  }
}
```

### Card Widget Occlusion
```dart
class SensitiveCard extends StatelessWidget {
  final String title;
  final String sensitiveContent;
  final IconData icon;
  final bool isHighSecurity;
  
  const SensitiveCard({
    Key? key,
    required this.title,
    required this.sensitiveContent,
    required this.icon,
    this.isHighSecurity = false,
  }) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    return Card(
      elevation: 4,
      child: Padding(
        padding: EdgeInsets.all(16),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Row(
              children: [
                Icon(icon),
                SizedBox(width: 8),
                Text(
                  title,
                  style: Theme.of(context).textTheme.headline6,
                ),
              ],
            ),
            SizedBox(height: 12),
            // Conditionally occlude the sensitive content
            ConditionalOcclusionWidget(
              shouldOcclude: () => _shouldOccludeContent(),
              occlusionReason: 'sensitive_card_content',
              child: Container(
                width: double.infinity,
                padding: EdgeInsets.all(12),
                decoration: BoxDecoration(
                  color: isHighSecurity ? Colors.red.shade50 : Colors.grey.shade100,
                  borderRadius: BorderRadius.circular(8),
                  border: isHighSecurity 
                      ? Border.all(color: Colors.red.shade300)
                      : null,
                ),
                child: Text(
                  sensitiveContent,
                  style: TextStyle(
                    fontFamily: 'monospace',
                    fontSize: 14,
                  ),
                ),
              ),
            ),
          ],
        ),
      ),
    );
  }
  
  bool _shouldOccludeContent() {
    // Check user preferences, security level, etc.
    return isHighSecurity || _getUserOcclusionPreference();
  }
  
  bool _getUserOcclusionPreference() {
    // Get user's privacy preferences
    // This could come from SharedPreferences, Provider, etc.
    return true; // Default to occluding sensitive content
  }
}
```

### Tab Content Occlusion
```dart
class SensitiveTabView extends StatefulWidget {
  final List<SensitiveTab> tabs;
  
  const SensitiveTabView({Key? key, required this.tabs}) : super(key: key);
  
  @override
  _SensitiveTabViewState createState() => _SensitiveTabViewState();
}

class _SensitiveTabViewState extends State<SensitiveTabView> 
    with SingleTickerProviderStateMixin {
  late TabController _tabController;
  
  @override
  void initState() {
    super.initState();
    _tabController = TabController(length: widget.tabs.length, vsync: this);
    _tabController.addListener(_handleTabChange);
  }
  
  void _handleTabChange() {
    if (_tabController.indexIsChanging) {
      final currentTab = widget.tabs[_tabController.index];
      
      // Update occlusion based on current tab
      WidgetsBinding.instance.addPostFrameCallback((_) {
        _updateTabOcclusion(currentTab);
      });
    }
  }
  
  void _updateTabOcclusion(SensitiveTab tab) {
    if (tab.requiresOcclusion) {
      // Apply occlusion to the entire tab content
      _occludeCurrentTabContent();
      
      FlutterUxcam.logEvent('sensitive_tab_viewed', {
        'tab_name': tab.title,
        'occlusion_applied': true,
        'sensitivity_level': tab.sensitivityLevel,
      });
    }
  }
  
  void _occludeCurrentTabContent() {
    // Find the current tab view content and occlude it
    final currentContext = _tabController.index < widget.tabs.length 
        ? context 
        : null;
    
    if (currentContext != null) {
      // This is a simplified example - in practice, you'd need to
      // find the actual tab content render box
      final renderBox = currentContext.findRenderObject() as RenderBox?;
      if (renderBox != null) {
        final position = renderBox.localToGlobal(Offset.zero);
        final size = renderBox.size;
        
        FlutterUxcam.occludeRect(
          position.dx,
          position.dy + 48, // Account for tab bar height
          size.width,
          size.height - 48,
        );
      }
    }
  }
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        bottom: TabBar(
          controller: _tabController,
          tabs: widget.tabs.map((tab) => Tab(text: tab.title)).toList(),
        ),
      ),
      body: TabBarView(
        controller: _tabController,
        children: widget.tabs.map((tab) => _buildTabContent(tab)).toList(),
      ),
    );
  }
  
  Widget _buildTabContent(SensitiveTab tab) {
    return ConditionalOcclusionWidget(
      shouldOcclude: () => tab.requiresOcclusion,
      occlusionReason: 'sensitive_tab_${tab.title.toLowerCase()}',
      child: Padding(
        padding: EdgeInsets.all(16),
        child: tab.content,
      ),
    );
  }
  
  @override
  void dispose() {
    _tabController.removeListener(_handleTabChange);
    _tabController.dispose();
    super.dispose();
  }
}

class SensitiveTab {
  final String title;
  final Widget content;
  final bool requiresOcclusion;
  final String sensitivityLevel;
  
  SensitiveTab({
    required this.title,
    required this.content,
    this.requiresOcclusion = false,
    this.sensitivityLevel = 'normal',
  });
}
```

## Custom Component Occlusion

### Reusable Sensitive Widget
```dart
class SensitiveDataDisplay extends StatefulWidget {
  final String label;
  final String sensitiveValue;
  final IconData? icon;
  final bool allowReveal;
  final VoidCallback? onReveal;
  
  const SensitiveDataDisplay({
    Key? key,
    required this.label,
    required this.sensitiveValue,
    this.icon,
    this.allowReveal = false,
    this.onReveal,
  }) : super(key: key);
  
  @override
  _SensitiveDataDisplayState createState() => _SensitiveDataDisplayState();
}

class _SensitiveDataDisplayState extends State<SensitiveDataDisplay> {
  bool _isRevealed = false;
  final GlobalKey _valueKey = GlobalKey();
  
  @override
  void initState() {
    super.initState();
    _applyInitialOcclusion();
  }
  
  void _applyInitialOcclusion() {
    WidgetsBinding.instance.addPostFrameCallback((_) {
      if (!_isRevealed) {
        _occludeValue();
      }
    });
  }
  
  void _occludeValue() {
    final renderBox = _valueKey.currentContext?.findRenderObject() as RenderBox?;
    if (renderBox != null) {
      final position = renderBox.localToGlobal(Offset.zero);
      final size = renderBox.size;
      
      FlutterUxcam.occludeRect(
        position.dx,
        position.dy,
        size.width,
        size.height,
      );
    }
  }
  
  void _toggleReveal() {
    setState(() {
      _isRevealed = !_isRevealed;
    });
    
    // Log reveal/hide action
    FlutterUxcam.logEvent('sensitive_data_toggled', {
      'label': widget.label,
      'action': _isRevealed ? 'revealed' : 'hidden',
      'timestamp': DateTime.now().toIso8601String(),
    });
    
    if (_isRevealed) {
      widget.onReveal?.call();
    }
    
    // Update occlusion
    WidgetsBinding.instance.addPostFrameCallback((_) {
      if (!_isRevealed) {
        _occludeValue();
      }
    });
  }
  
  @override
  Widget build(BuildContext context) {
    return Container(
      padding: EdgeInsets.all(12),
      decoration: BoxDecoration(
        border: Border.all(color: Colors.grey.shade300),
        borderRadius: BorderRadius.circular(8),
      ),
      child: Row(
        children: [
          if (widget.icon != null) ...[
            Icon(widget.icon, size: 20),
            SizedBox(width: 8),
          ],
          Expanded(
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Text(
                  widget.label,
                  style: TextStyle(
                    fontSize: 12,
                    color: Colors.grey.shade600,
                  ),
                ),
                SizedBox(height: 4),
                Container(
                  key: _valueKey,
                  child: Text(
                    _isRevealed ? widget.sensitiveValue : '••••••••',
                    style: TextStyle(
                      fontSize: 16,
                      fontWeight: FontWeight.w500,
                      fontFamily: _isRevealed ? null : 'monospace',
                    ),
                  ),
                ),
              ],
            ),
          ),
          if (widget.allowReveal)
            IconButton(
              icon: Icon(
                _isRevealed ? Icons.visibility_off : Icons.visibility,
                size: 20,
              ),
              onPressed: _toggleReveal,
            ),
        ],
      ),
    );
  }
}

// Usage example
class AccountDetailsScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Account Details')),
      body: Padding(
        padding: EdgeInsets.all(16),
        child: Column(
          children: [
            SensitiveDataDisplay(
              label: 'Account Number',
              sensitiveValue: '1234-5678-9012-3456',
              icon: Icons.account_balance,
              allowReveal: true,
              onReveal: () {
                // Log that user revealed sensitive data
                FlutterUxcam.logEvent('account_number_revealed', {
                  'user_action': 'manual_reveal',
                });
              },
            ),
            SizedBox(height: 16),
            SensitiveDataDisplay(
              label: 'Social Security Number',
              sensitiveValue: '***-**-1234',
              icon: Icons.security,
              allowReveal: false, // Never allow reveal for SSN
            ),
            SizedBox(height: 16),
            SensitiveDataDisplay(
              label: 'Phone Number',
              sensitiveValue: '+1 (555) 123-4567',
              icon: Icons.phone,
              allowReveal: true,
            ),
          ],
        ),
      ),
    );
  }
}
```

## State Management Integration

### Provider-Based Occlusion
```dart
class OcclusionProvider extends ChangeNotifier {
  final Map<String, bool> _occlusionStates = {};
  
  bool isOccluded(String key) => _occlusionStates[key] ?? true;
  
  void setOcclusion(String key, bool occluded) {
    if (_occlusionStates[key] != occluded) {
      _occlusionStates[key] = occluded;
      notifyListeners();
      
      // Log occlusion state change
      FlutterUxcam.logEvent('occlusion_state_changed', {
        'key': key,
        'occluded': occluded,
        'timestamp': DateTime.now().toIso8601String(),
      });
    }
  }
  
  void toggleOcclusion(String key) {
    setOcclusion(key, !isOccluded(key));
  }
  
  void setGlobalOcclusion(bool occluded) {
    final changed = <String>[];
    
    for (final key in _occlusionStates.keys) {
      if (_occlusionStates[key] != occluded) {
        _occlusionStates[key] = occluded;
        changed.add(key);
      }
    }
    
    if (changed.isNotEmpty) {
      notifyListeners();
      
      FlutterUxcam.logEvent('global_occlusion_changed', {
        'occluded': occluded,
        'affected_keys': changed,
        'count': changed.length,
      });
    }
  }
}

// Consumer widget for occlusion
class OcclusionConsumer extends StatelessWidget {
  final String occlusionKey;
  final Widget child;
  final String? occlusionReason;
  
  const OcclusionConsumer({
    Key? key,
    required this.occlusionKey,
    required this.child,
    this.occlusionReason,
  }) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    return Consumer<OcclusionProvider>(
      builder: (context, occlusionProvider, _) {
        return ConditionalOcclusionWidget(
          shouldOcclude: () => occlusionProvider.isOccluded(occlusionKey),
          occlusionReason: occlusionReason ?? 'provider_controlled',
          child: child,
        );
      },
    );
  }
}

// Usage
class UserProfileScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Profile'),
        actions: [
          Consumer<OcclusionProvider>(
            builder: (context, occlusionProvider, _) {
              return IconButton(
                icon: Icon(
                  occlusionProvider.isOccluded('sensitive_info') 
                      ? Icons.visibility_off 
                      : Icons.visibility,
                ),
                onPressed: () {
                  occlusionProvider.toggleOcclusion('sensitive_info');
                },
              );
            },
          ),
        ],
      ),
      body: Column(
        children: [
          // Regular info - not occluded
          ListTile(
            title: Text('Name'),
            subtitle: Text('John Doe'),
          ),
          
          // Sensitive info - controlled by provider
          OcclusionConsumer(
            occlusionKey: 'sensitive_info',
            occlusionReason: 'user_profile_pii',
            child: ListTile(
              title: Text('Email'),
              subtitle: Text('john.doe@example.com'),
            ),
          ),
          
          OcclusionConsumer(
            occlusionKey: 'sensitive_info',
            occlusionReason: 'user_profile_pii',
            child: ListTile(
              title: Text('Phone'),
              subtitle: Text('+1 (555) 123-4567'),
            ),
          ),
        ],
      ),
    );
  }
}
```

## Performance Optimizations

### Efficient Occlusion Management
```dart
class OcclusionManager {
  static final Map<String, Rect> _activeOcclusions = {};
  static Timer? _batchUpdateTimer;
  static final Set<String> _pendingUpdates = {};
  
  static void scheduleOcclusion(String key, Rect rect) {
    _activeOcclusions[key] = rect;
    _pendingUpdates.add(key);
    
    // Batch updates to avoid excessive UXCam calls
    _scheduleBatchUpdate();
  }
  
  static void removeOcclusion(String key) {
    _activeOcclusions.remove(key);
    _pendingUpdates.add(key);
    _scheduleBatchUpdate();
  }
  
  static void _scheduleBatchUpdate() {
    _batchUpdateTimer?.cancel();
    _batchUpdateTimer = Timer(Duration(milliseconds: 100), () {
      _applyBatchedOcclusions();
    });
  }
  
  static void _applyBatchedOcclusions() {
    if (_pendingUpdates.isEmpty) return;
    
    // Apply all active occlusions
    for (final entry in _activeOcclusions.entries) {
      final rect = entry.value;
      FlutterUxcam.occludeRect(
        rect.left,
        rect.top,
        rect.width,
        rect.height,
      );
    }
    
    // Log batch update
    FlutterUxcam.logEvent('occlusion_batch_applied', {
      'updated_count': _pendingUpdates.length,
      'total_occlusions': _activeOcclusions.length,
      'timestamp': DateTime.now().toIso8601String(),
    });
    
    _pendingUpdates.clear();
  }
  
  static void clearAllOcclusions() {
    _activeOcclusions.clear();
    _pendingUpdates.clear();
    _batchUpdateTimer?.cancel();
    
    FlutterUxcam.logEvent('all_occlusions_cleared', {
      'timestamp': DateTime.now().toIso8601String(),
    });
  }
}
```

## Best Practices

### Do's
- ✅ Use conditional occlusion based on user preferences
- ✅ Occlude entire widgets rather than partial content
- ✅ Batch occlusion updates for performance
- ✅ Log occlusion events for compliance auditing
- ✅ Respect user choice about data visibility
- ✅ Test occlusion with different screen sizes

### Don'ts
- ❌ Don't occlude non-sensitive UI elements
- ❌ Don't apply occlusion synchronously in build methods
- ❌ Don't forget to handle dynamic content changes
- ❌ Don't over-occlude to the point of poor UX
- ❌ Don't leak sensitive data in logs or events
- ❌ Don't ignore user accessibility needs

## Testing Widget Occlusion

### Occlusion Testing
```dart
testWidgets('Sensitive widget is properly occluded', (WidgetTester tester) async {
  bool occlusionApplied = false;
  
  // Mock UXCam occlusion
  FlutterUxcam.occludeRect = (x, y, width, height) {
    occlusionApplied = true;
  };
  
  await tester.pumpWidget(
    MaterialApp(
      home: Scaffold(
        body: SensitiveDataDisplay(
          label: 'Test Data',
          sensitiveValue: 'Sensitive Value',
        ),
      ),
    ),
  );
  
  await tester.pumpAndSettle();
  
  expect(occlusionApplied, isTrue);
});
```

## Next Steps

- **[Form Field Masking](form-field-masking.md)** - Specialized form input occlusion
- **[Widget-Level Tagging](../screen-tagging/widget-level-tagging.md)** - Combine with analytics
- **[Advanced Configuration](../advanced-configuration-and-apis/)** - Fine-tune privacy settings

---

*Master widget occlusion patterns to build privacy-compliant Flutter apps that protect user data without sacrificing usability.*