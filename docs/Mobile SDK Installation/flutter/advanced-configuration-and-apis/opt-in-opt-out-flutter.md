---
title: User Consent Management
excerpt: Implement GDPR/CCPA compliant opt-in/opt-out flows in Flutter
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---

# User Consent Management

Implement comprehensive user consent management for GDPR, CCPA, and other privacy regulations while maintaining the full power of UXCam's analytics capabilities.

## Overview

User consent management is essential for:
- **GDPR Compliance** (European Union)
- **CCPA Compliance** (California)
- **Privacy-First Development** (Global best practice)
- **User Trust Building** (Transparent data practices)

UXCam's Flutter SDK provides flexible consent management that allows users to control their data while giving you the insights you need.

## Basic Consent Implementation

### Simple Opt-In/Opt-Out
```dart
import 'package:flutter_uxcam/flutter_uxcam.dart';

class ConsentManager {
  static const String CONSENT_KEY = 'uxcam_user_consent';
  
  // Check current consent status
  static Future<bool> hasUserConsented() async {
    final SharedPreferences prefs = await SharedPreferences.getInstance();
    return prefs.getBool(CONSENT_KEY) ?? false;
  }
  
  // Grant consent and start recording
  static Future<void> grantConsent() async {
    final SharedPreferences prefs = await SharedPreferences.getInstance();
    await prefs.setBool(CONSENT_KEY, true);
    
    // Opt into schematic recordings
    FlutterUxcam.optIntoSchematicRecordings();
    
    // Start UXCam with configuration
    FlutterUxConfig config = FlutterUxConfig(
      userAppKey: "YOUR_APP_KEY",
      enableAutomaticScreenNameTagging: true,
    );
    await FlutterUxcam.startWithConfiguration(config);
    
    print('User has granted consent - UXCam recording started');
  }
  
  // Revoke consent and stop recording
  static Future<void> revokeConsent() async {
    final SharedPreferences prefs = await SharedPreferences.getInstance();
    await prefs.setBool(CONSENT_KEY, false);
    
    // Opt out of recordings
    FlutterUxcam.optOutOfSchematicRecordings();
    
    // Stop current session
    await FlutterUxcam.stopSessionAndUploadData();
    
    print('User has revoked consent - UXCam recording stopped');
  }
  
  // Initialize UXCam based on consent status
  static Future<void> initializeWithConsent(String appKey) async {
    final hasConsent = await hasUserConsented();
    
    if (hasConsent) {
      await grantConsent();
    } else {
      // Show consent dialog or handle no-consent state
      await _handleNoConsent();
    }
  }
  
  static Future<void> _handleNoConsent() async {
    // Could show consent dialog or just log
    print('User has not granted consent - UXCam not initialized');
  }
}
```

### Consent Dialog Implementation
```dart
class ConsentDialog extends StatefulWidget {
  final VoidCallback onConsentGiven;
  final VoidCallback onConsentDenied;
  
  const ConsentDialog({
    Key? key,
    required this.onConsentGiven,
    required this.onConsentDenied,
  }) : super(key: key);
  
  @override
  _ConsentDialogState createState() => _ConsentDialogState();
}

class _ConsentDialogState extends State<ConsentDialog> {
  bool _analyticsConsent = false;
  bool _crashReportingConsent = false;
  
  @override
  Widget build(BuildContext context) {
    return AlertDialog(
      title: Text('Privacy & Data Collection'),
      content: SingleChildScrollView(
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          mainAxisSize: MainAxisSize.min,
          children: [
            Text(
              'We use analytics to improve your experience. You can control what data we collect:',
              style: TextStyle(fontSize: 16),
            ),
            SizedBox(height: 16),
            CheckboxListTile(
              title: Text('User Analytics'),
              subtitle: Text('Help us understand how you use the app'),
              value: _analyticsConsent,
              onChanged: (value) {
                setState(() {
                  _analyticsConsent = value ?? false;
                });
              },
            ),
            CheckboxListTile(
              title: Text('Crash Reporting'),
              subtitle: Text('Help us fix bugs and improve stability'),
              value: _crashReportingConsent,
              onChanged: (value) {
                setState(() {
                  _crashReportingConsent = value ?? false;
                });
              },
            ),
            SizedBox(height: 16),
            Text(
              'You can change these preferences anytime in Settings.',
              style: TextStyle(fontSize: 12, color: Colors.grey[600]),
            ),
          ],
        ),
      ),
      actions: [
        TextButton(
          onPressed: () {
            Navigator.of(context).pop();
            widget.onConsentDenied();
          },
          child: Text('Decline All'),
        ),
        ElevatedButton(
          onPressed: (_analyticsConsent || _crashReportingConsent)
              ? () {
                  Navigator.of(context).pop();
                  _handleConsentGiven();
                }
              : null,
          child: Text('Accept Selected'),
        ),
      ],
    );
  }
  
  void _handleConsentGiven() {
    // Store granular consent preferences
    _storeConsentPreferences(_analyticsConsent, _crashReportingConsent);
    widget.onConsentGiven();
  }
  
  Future<void> _storeConsentPreferences(
    bool analytics,
    bool crashReporting,
  ) async {
    final SharedPreferences prefs = await SharedPreferences.getInstance();
    await prefs.setBool('analytics_consent', analytics);
    await prefs.setBool('crash_reporting_consent', crashReporting);
  }
}

// Usage in your app
class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  bool _consentChecked = false;
  
  @override
  void initState() {
    super.initState();
    _checkAndRequestConsent();
  }
  
  Future<void> _checkAndRequestConsent() async {
    final hasConsent = await ConsentManager.hasUserConsented();
    
    if (!hasConsent) {
      // Show consent dialog
      WidgetsBinding.instance.addPostFrameCallback((_) {
        _showConsentDialog();
      });
    } else {
      // Initialize with existing consent
      await ConsentManager.initializeWithConsent("YOUR_APP_KEY");
      setState(() {
        _consentChecked = true;
      });
    }
  }
  
  void _showConsentDialog() {
    showDialog(
      context: context,
      barrierDismissible: false,
      builder: (context) => ConsentDialog(
        onConsentGiven: () async {
          await ConsentManager.grantConsent();
          setState(() {
            _consentChecked = true;
          });
        },
        onConsentDenied: () {
          setState(() {
            _consentChecked = true;
          });
        },
      ),
    );
  }
}
```

## Advanced Consent Management

### Granular Consent Controls
```dart
class GranularConsentManager {
  static const String ANALYTICS_CONSENT_KEY = 'uxcam_analytics_consent';
  static const String CRASH_CONSENT_KEY = 'uxcam_crash_consent';
  static const String PERFORMANCE_CONSENT_KEY = 'uxcam_performance_consent';
  static const String CONSENT_VERSION_KEY = 'uxcam_consent_version';
  static const int CURRENT_CONSENT_VERSION = 2;
  
  // Consent types enum
  enum ConsentType {
    analytics,
    crashReporting,
    performanceMonitoring,
  }
  
  // Check if consent needs to be refreshed
  static Future<bool> needsConsentRefresh() async {
    final SharedPreferences prefs = await SharedPreferences.getInstance();
    final currentVersion = prefs.getInt(CONSENT_VERSION_KEY) ?? 0;
    return currentVersion < CURRENT_CONSENT_VERSION;
  }
  
  // Get consent for specific type
  static Future<bool> hasConsentFor(ConsentType type) async {
    final SharedPreferences prefs = await SharedPreferences.getInstance();
    final key = _getConsentKey(type);
    return prefs.getBool(key) ?? false;
  }
  
  // Set consent for specific type
  static Future<void> setConsentFor(ConsentType type, bool granted) async {
    final SharedPreferences prefs = await SharedPreferences.getInstance();
    final key = _getConsentKey(type);
    await prefs.setBool(key, granted);
    
    // Update consent version
    await prefs.setInt(CONSENT_VERSION_KEY, CURRENT_CONSENT_VERSION);
    
    // Update UXCam configuration based on consent
    await _updateUXCamConfiguration();
  }
  
  // Set multiple consents at once
  static Future<void> setConsents(Map<ConsentType, bool> consents) async {
    final SharedPreferences prefs = await SharedPreferences.getInstance();
    
    for (final entry in consents.entries) {
      final key = _getConsentKey(entry.key);
      await prefs.setBool(key, entry.value);
    }
    
    await prefs.setInt(CONSENT_VERSION_KEY, CURRENT_CONSENT_VERSION);
    await _updateUXCamConfiguration();
  }
  
  // Get all consents
  static Future<Map<ConsentType, bool>> getAllConsents() async {
    final result = <ConsentType, bool>{};
    
    for (final type in ConsentType.values) {
      result[type] = await hasConsentFor(type);
    }
    
    return result;
  }
  
  // Update UXCam configuration based on current consents
  static Future<void> _updateUXCamConfiguration() async {
    final analyticsConsent = await hasConsentFor(ConsentType.analytics);
    final crashConsent = await hasConsentFor(ConsentType.crashReporting);
    final performanceConsent = await hasConsentFor(ConsentType.performanceMonitoring);
    
    if (analyticsConsent || crashConsent || performanceConsent) {
      // User has granted some form of consent
      FlutterUxcam.optIntoSchematicRecordings();
      
      FlutterUxConfig config = FlutterUxConfig(
        userAppKey: "YOUR_APP_KEY",
        enableAutomaticScreenNameTagging: analyticsConsent,
        enableCrashHandling: crashConsent,
        enableImprovedScreenCapture: performanceConsent,
      );
      
      await FlutterUxcam.startWithConfiguration(config);
    } else {
      // User has denied all consent
      FlutterUxcam.optOutOfSchematicRecordings();
      await FlutterUxcam.stopSessionAndUploadData();
    }
  }
  
  static String _getConsentKey(ConsentType type) {
    switch (type) {
      case ConsentType.analytics:
        return ANALYTICS_CONSENT_KEY;
      case ConsentType.crashReporting:
        return CRASH_CONSENT_KEY;
      case ConsentType.performanceMonitoring:
        return PERFORMANCE_CONSENT_KEY;
    }
  }
  
  // Clear all consent data (for account deletion)
  static Future<void> clearAllConsents() async {
    final SharedPreferences prefs = await SharedPreferences.getInstance();
    
    for (final type in ConsentType.values) {
      await prefs.remove(_getConsentKey(type));
    }
    
    await prefs.remove(CONSENT_VERSION_KEY);
    
    // Stop UXCam and clear data
    FlutterUxcam.optOutOfSchematicRecordings();
    await FlutterUxcam.stopSessionAndUploadData();
  }
}
```

### Regional Compliance
```dart
class RegionalComplianceManager {
  // Detect user's region for compliance requirements
  static Future<String> detectUserRegion() async {
    try {
      // Use a geolocation service or device locale
      final locale = Localizations.localeOf(context);
      final countryCode = locale.countryCode ?? 'US';
      
      return countryCode;
    } catch (e) {
      // Default to most restrictive (EU) if detection fails
      return 'EU';
    }
  }
  
  // Check if region requires explicit consent
  static bool requiresExplicitConsent(String region) {
    const gdprRegions = ['AT', 'BE', 'BG', 'HR', 'CY', 'CZ', 'DK', 'EE', 
                         'FI', 'FR', 'DE', 'GR', 'HU', 'IE', 'IT', 'LV', 
                         'LT', 'LU', 'MT', 'NL', 'PL', 'PT', 'RO', 'SK', 
                         'SI', 'ES', 'SE', 'GB', 'EU'];
    const ccpaRegions = ['CA']; // California
    
    return gdprRegions.contains(region) || ccpaRegions.contains(region);
  }
  
  // Get compliance requirements for region
  static ComplianceRequirements getComplianceRequirements(String region) {
    if (region == 'CA') {
      return ComplianceRequirements.ccpa();
    } else if (['GB', 'EU'].contains(region) || 
               requiresExplicitConsent(region)) {
      return ComplianceRequirements.gdpr();
    } else {
      return ComplianceRequirements.minimal();
    }
  }
  
  // Initialize UXCam with region-appropriate consent flow
  static Future<void> initializeWithRegionalCompliance() async {
    final region = await detectUserRegion();
    final requirements = getComplianceRequirements(region);
    
    if (requirements.requiresExplicitConsent) {
      // Check if we have valid consent
      final needsConsent = await GranularConsentManager.needsConsentRefresh();
      final hasAnyConsent = await _hasAnyConsent();
      
      if (needsConsent || !hasAnyConsent) {
        // Show appropriate consent dialog
        await _showRegionalConsentDialog(requirements);
      } else {
        // Use existing consent
        await GranularConsentManager._updateUXCamConfiguration();
      }
    } else {
      // Region doesn't require explicit consent, use default settings
      await _initializeWithDefaultSettings();
    }
  }
  
  static Future<bool> _hasAnyConsent() async {
    final consents = await GranularConsentManager.getAllConsents();
    return consents.values.any((consent) => consent);
  }
  
  static Future<void> _showRegionalConsentDialog(
    ComplianceRequirements requirements,
  ) async {
    // Implementation depends on your UI framework
    // This would show a dialog appropriate for the compliance requirements
  }
  
  static Future<void> _initializeWithDefaultSettings() async {
    FlutterUxConfig config = FlutterUxConfig(
      userAppKey: "YOUR_APP_KEY",
      enableAutomaticScreenNameTagging: true,
      enableCrashHandling: true,
    );
    
    await FlutterUxcam.startWithConfiguration(config);
  }
}

class ComplianceRequirements {
  final bool requiresExplicitConsent;
  final bool allowsOptOut;
  final bool requiresDataDeletion;
  final List<String> requiredDisclosures;
  
  ComplianceRequirements({
    required this.requiresExplicitConsent,
    required this.allowsOptOut,
    required this.requiresDataDeletion,
    required this.requiredDisclosures,
  });
  
  factory ComplianceRequirements.gdpr() {
    return ComplianceRequirements(
      requiresExplicitConsent: true,
      allowsOptOut: true,
      requiresDataDeletion: true,
      requiredDisclosures: [
        'What data we collect',
        'How we use your data',
        'Your rights under GDPR',
        'How to contact us',
      ],
    );
  }
  
  factory ComplianceRequirements.ccpa() {
    return ComplianceRequirements(
      requiresExplicitConsent: false,
      allowsOptOut: true,
      requiresDataDeletion: true,
      requiredDisclosures: [
        'Categories of data collected',
        'Business purposes for collection',
        'Right to opt-out',
        'Right to delete',
      ],
    );
  }
  
  factory ComplianceRequirements.minimal() {
    return ComplianceRequirements(
      requiresExplicitConsent: false,
      allowsOptOut: true,
      requiresDataDeletion: false,
      requiredDisclosures: [
        'Data collection notice',
      ],
    );
  }
}
```

## Consent Persistence and Migration

### Consent Data Migration
```dart
class ConsentMigrationManager {
  static Future<void> migrateConsentData() async {
    final SharedPreferences prefs = await SharedPreferences.getInstance();
    final currentVersion = prefs.getInt('consent_data_version') ?? 0;
    
    if (currentVersion < 1) {
      await _migrateToV1();
    }
    if (currentVersion < 2) {
      await _migrateToV2();
    }
    
    // Update version
    await prefs.setInt('consent_data_version', 2);
  }
  
  static Future<void> _migrateToV1() async {
    // Migration from legacy boolean consent to granular consent
    final SharedPreferences prefs = await SharedPreferences.getInstance();
    final legacyConsent = prefs.getBool('uxcam_user_consent') ?? false;
    
    if (legacyConsent) {
      // Migrate to new granular system with all permissions
      await GranularConsentManager.setConsents({
        GranularConsentManager.ConsentType.analytics: true,
        GranularConsentManager.ConsentType.crashReporting: true,
        GranularConsentManager.ConsentType.performanceMonitoring: true,
      });
    }
    
    // Remove legacy key
    await prefs.remove('uxcam_user_consent');
  }
  
  static Future<void> _migrateToV2() async {
    // Add new consent types or update existing ones
    // This is where you'd handle future consent migrations
  }
}
```

## Settings Integration

### Privacy Settings Screen
```dart
class PrivacySettingsScreen extends StatefulWidget {
  @override
  _PrivacySettingsScreenState createState() => _PrivacySettingsScreenState();
}

class _PrivacySettingsScreenState extends State<PrivacySettingsScreen> {
  Map<GranularConsentManager.ConsentType, bool> _consents = {};
  bool _loading = true;
  
  @override
  void initState() {
    super.initState();
    _loadConsents();
  }
  
  Future<void> _loadConsents() async {
    final consents = await GranularConsentManager.getAllConsents();
    setState(() {
      _consents = consents;
      _loading = false;
    });
  }
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Privacy Settings'),
      ),
      body: _loading
          ? Center(child: CircularProgressIndicator())
          : ListView(
              padding: EdgeInsets.all(16),
              children: [
                Text(
                  'Data Collection Preferences',
                  style: Theme.of(context).textTheme.headline6,
                ),
                SizedBox(height: 16),
                _buildConsentTile(
                  GranularConsentManager.ConsentType.analytics,
                  'Analytics & Usage Data',
                  'Help us understand how you use the app to improve your experience',
                  Icons.analytics,
                ),
                _buildConsentTile(
                  GranularConsentManager.ConsentType.crashReporting,
                  'Crash Reports',
                  'Automatically report crashes to help us fix bugs',
                  Icons.bug_report,
                ),
                _buildConsentTile(
                  GranularConsentManager.ConsentType.performanceMonitoring,
                  'Performance Monitoring',
                  'Monitor app performance to optimize your experience',
                  Icons.speed,
                ),
                SizedBox(height: 32),
                _buildActionButtons(),
              ],
            ),
    );
  }
  
  Widget _buildConsentTile(
    GranularConsentManager.ConsentType type,
    String title,
    String description,
    IconData icon,
  ) {
    return Card(
      child: SwitchListTile(
        title: Text(title),
        subtitle: Text(description),
        secondary: Icon(icon),
        value: _consents[type] ?? false,
        onChanged: (bool value) async {
          setState(() {
            _consents[type] = value;
          });
          
          await GranularConsentManager.setConsentFor(type, value);
        },
      ),
    );
  }
  
  Widget _buildActionButtons() {
    return Column(
      children: [
        SizedBox(
          width: double.infinity,
          child: ElevatedButton(
            onPressed: _exportData,
            child: Text('Export My Data'),
          ),
        ),
        SizedBox(height: 8),
        SizedBox(
          width: double.infinity,
          child: ElevatedButton(
            onPressed: _deleteData,
            style: ElevatedButton.styleFrom(
              backgroundColor: Colors.red,
            ),
            child: Text('Delete My Data'),
          ),
        ),
      ],
    );
  }
  
  Future<void> _exportData() async {
    // Implement data export functionality
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(content: Text('Data export requested')),
    );
  }
  
  Future<void> _deleteData() async {
    final confirmed = await showDialog<bool>(
      context: context,
      builder: (context) => AlertDialog(
        title: Text('Delete All Data'),
        content: Text('This will permanently delete all your data. This cannot be undone.'),
        actions: [
          TextButton(
            onPressed: () => Navigator.of(context).pop(false),
            child: Text('Cancel'),
          ),
          ElevatedButton(
            onPressed: () => Navigator.of(context).pop(true),
            style: ElevatedButton.styleFrom(backgroundColor: Colors.red),
            child: Text('Delete'),
          ),
        ],
      ),
    );
    
    if (confirmed == true) {
      await GranularConsentManager.clearAllConsents();
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text('All data deleted')),
      );
    }
  }
}
```

## Best Practices

### Do's
- ✅ Always check consent before initializing UXCam
- ✅ Provide clear, understandable consent language
- ✅ Allow users to change their preferences anytime
- ✅ Implement granular consent controls
- ✅ Handle consent migration properly
- ✅ Respect regional compliance requirements

### Don'ts
- ❌ Don't collect data without proper consent
- ❌ Don't make consent mandatory for app functionality
- ❌ Don't use confusing or misleading consent language
- ❌ Don't ignore user consent preferences
- ❌ Don't forget to handle consent expiration
- ❌ Don't skip regional compliance considerations

## Next Steps

- **[Integration Logging](integration-logging-guide.md)** - Debug your consent setup
- **[Troubleshooting](troubleshooting-faqs.md)** - Solve consent-related issues
- **[Recording Control](control-recording-flutter.md)** - Control recording based on consent

---

*Proper consent management builds user trust while keeping you compliant with privacy regulations.*