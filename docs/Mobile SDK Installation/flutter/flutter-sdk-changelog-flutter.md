---
title: Flutter SDK Changelog
excerpt: Version history and updates for UXCam Flutter SDK
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---

# Flutter SDK Changelog

Stay up-to-date with the latest UXCam Flutter SDK releases, including new features, improvements, and bug fixes.

## Version History

### v2.6.1 (Latest)
**Release Date**: December 2024

#### ✨ New Features
- Enhanced session recording stability
- Improved widget occlusion performance
- Better support for Flutter 3.16+

#### 🐛 Bug Fixes
- Fixed session upload timing issues
- Resolved memory leaks in long-running sessions
- Corrected automatic screen tagging for named routes

#### 🔧 Improvements
- Reduced SDK initialization time by 15%
- Enhanced logging and debugging capabilities
- Better error handling for network failures

---

### v2.5.8
**Release Date**: October 2024

#### ✨ New Features
- Support for Flutter 3.13+ 
- New user property APIs
- Enhanced privacy controls

#### 🐛 Bug Fixes
- Fixed crashes on iOS 17
- Resolved Android build issues with AGP 8.0+
- Fixed screen tagging in nested navigation

#### ⚠️ Breaking Changes
- Minimum Flutter version now 3.10
- Updated Android minimum SDK to 21

---

### v2.4.12
**Release Date**: August 2024

#### ✨ New Features
- Custom event tracking improvements
- Better Dart 3.0 compatibility
- Enhanced WebView occlusion

#### 🐛 Bug Fixes
- Fixed session replay artifacts
- Resolved build issues with latest Xcode
- Improved hot reload stability

---

### v2.3.6
**Release Date**: June 2024

#### ✨ New Features
- Initial support for Flutter Web (Beta)
- Improved gesture tracking
- New occlusion APIs

#### 🐛 Bug Fixes
- Fixed memory usage in debug mode
- Resolved iOS archive issues
- Better handling of app lifecycle events

---

## Migration Guides

### Migrating from v2.5.x to v2.6.x

No breaking changes. Update your `pubspec.yaml`:

```yaml
dependencies:
  flutter_uxcam: ^2.6.1
```

Run:
```bash
flutter pub get
```

### Migrating from v2.4.x to v2.5.x

**Breaking Changes:**
- Minimum Flutter version is now 3.10
- Android minimum SDK increased to 21

**Update Steps:**
1. Update Flutter to 3.10 or higher
2. Update Android `minSdkVersion` to 21 in `android/app/build.gradle`
3. Update dependency in `pubspec.yaml`

---

## Compatibility Matrix

| Flutter SDK Version | Flutter Version | iOS Version | Android API Level | Dart Version |
|---------------------|----------------|-------------|------------------|--------------|
| 2.6.1               | 3.16+          | 12.0+       | 21+              | 3.0+         |
| 2.5.8               | 3.13+          | 12.0+       | 21+              | 3.0+         |
| 2.4.12              | 3.10+          | 11.0+       | 19+              | 2.17+        |
| 2.3.6               | 3.7+           | 11.0+       | 19+              | 2.17+        |

---

## Known Issues

### Current Issues (v2.6.1)
- **Flutter Web**: Limited session recording capabilities (Beta feature)
- **Hot Reload**: May require app restart after changing UXCam configuration
- **Large Apps**: First session upload may be delayed on slow networks

### Workarounds
- For Flutter Web issues, use native web SDK for production
- Restart app after configuration changes during development
- Implement upload retry logic for slow network conditions

---

## Deprecated Features

### Scheduled for Removal
- `FlutterUxcam.enableMultiSessionRecord()` - Will be removed in v3.0
- Legacy occlusion APIs - Migrate to new widget-based occlusion

### Migration Timeline
- **v2.7**: Deprecation warnings added
- **v3.0**: Complete removal (Q2 2025)

---

## Getting Help

- **Documentation**: [Flutter Integration Guide](index.md)
- **Troubleshooting**: [Common Issues](troubleshooting-flutter.md)
- **Support**: [team@uxcam.com](mailto:team@uxcam.com)
- **GitHub Issues**: Report bugs on our [GitHub repository](https://github.com/uxcam/flutter-uxcam)

---

*Last updated: December 2024*