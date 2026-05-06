---
title: Xamarin/MAUI Troubleshooting Guide
excerpt: Common issues and solutions for UXCam Xamarin and .NET MAUI integration
deprecated: false
hidden: false
metadata:
  title: 'Xamarin/MAUI Troubleshooting - UXCam'
  description: 'Troubleshooting guide for UXCam Xamarin and MAUI SDK integration'
  robots: index
next:
  description: ''
---

# Xamarin/MAUI Troubleshooting Guide

Common issues and solutions when integrating UXCam with Xamarin and .NET MAUI applications.

## Quick Diagnostics Checklist

Before diving into specific issues:

- [ ] Visual Studio is updated to latest version
- [ ] UXCam NuGet package is latest version
- [ ] Valid UXCam app key is configured
- [ ] iOS deployment target is 12.0+ / Android minSdkVersion is 21+
- [ ] Platform-specific initialization is correct

---

## Installation Issues

### NuGet Package Installation Fails

**Symptoms:**
```
Unable to resolve 'UXCam.Xamarin' for '.NETStandard,Version=v2.0'
```

**Solutions:**

1. **Update NuGet sources:**
   - Visual Studio > Tools > Options > NuGet Package Manager > Package Sources
   - Ensure nuget.org is listed and enabled

2. **Clear NuGet cache:**
```bash
dotnet nuget locals all --clear
```

3. **Restore packages:**
```bash
dotnet restore
```

### Platform-Specific Package Issues

**iOS missing native reference:**
```
Could not find UXCam framework
```

**Solution:**
1. Right-click iOS project > Manage NuGet Packages
2. Install `UXCam.Xamarin.iOS`

**Android missing binding:**
```
Java.Lang.NoClassDefFoundError: com/uxcam/UXCam
```

**Solution:**
1. Right-click Android project > Manage NuGet Packages
2. Install `UXCam.Xamarin.Android`

---

## Runtime Issues

### Sessions Not Appearing in Dashboard

**Symptoms:**
- App runs normally
- No sessions in UXCam dashboard

**Debugging Steps:**

1. **Verify initialization in platform projects:**

**Xamarin.iOS - AppDelegate.cs:**
```csharp
using UXCam.Xamarin.iOS;

public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    System.Diagnostics.Debug.WriteLine("Initializing UXCam...");

    UXCam.OptIntoVideoRecordings();

    var config = new UXCamConfiguration("YOUR_APP_KEY");
    UXCam.StartWithConfiguration(config);

    System.Diagnostics.Debug.WriteLine("UXCam initialized");

    // ... rest of initialization
    return base.FinishedLaunching(app, options);
}
```

**Xamarin.Android - MainActivity.cs:**
```csharp
using Com.UXCam;
using Com.UXCam.Datamodel;

protected override void OnCreate(Bundle savedInstanceState)
{
    base.OnCreate(savedInstanceState);

    System.Diagnostics.Debug.WriteLine("Initializing UXCam...");

    var config = new UXConfig.Builder("YOUR_APP_KEY").Build();
    UXCam.StartWithConfiguration(config);

    System.Diagnostics.Debug.WriteLine("UXCam initialized");

    // ... rest of initialization
}
```

2. **Check output window** in Visual Studio for initialization messages

**Common Solutions:**

**Missing OptIntoVideoRecordings (iOS):**
```csharp
// Required for iOS screen capture
UXCam.OptIntoVideoRecordings();  // Add this before StartWithConfiguration
```

**Wrong namespace:**
```csharp
// iOS
using UXCam.Xamarin.iOS;  // Not just UXCam

// Android
using Com.UXCam;
using Com.UXCam.Datamodel;
```

### .NET MAUI Initialization

**MauiProgram.cs:**
```csharp
public static class MauiProgram
{
    public static MauiApp CreateMauiApp()
    {
        var builder = MauiApp.CreateBuilder();
        builder.UseMauiApp<App>();

        // Platform-specific initialization happens in platform folders
        return builder.Build();
    }
}
```

**Platforms/iOS/AppDelegate.cs:**
```csharp
using UXCam.Xamarin.iOS;

[Register("AppDelegate")]
public class AppDelegate : MauiUIApplicationDelegate
{
    protected override MauiApp CreateMauiApp() => MauiProgram.CreateMauiApp();

    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        UXCam.OptIntoVideoRecordings();
        var config = new UXCamConfiguration("YOUR_APP_KEY");
        UXCam.StartWithConfiguration(config);

        return base.FinishedLaunching(app, options);
    }
}
```

**Platforms/Android/MainActivity.cs:**
```csharp
using Com.UXCam;
using Com.UXCam.Datamodel;

[Activity(MainLauncher = true)]
public class MainActivity : MauiAppCompatActivity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        var config = new UXConfig.Builder("YOUR_APP_KEY").Build();
        UXCam.StartWithConfiguration(config);
    }
}
```

---

## Screen Tagging Issues

### Screens Not Tagged Correctly

**Solution** - Manual screen tagging:

**Xamarin.Forms / MAUI:**
```csharp
public partial class HomePage : ContentPage
{
    protected override void OnAppearing()
    {
        base.OnAppearing();

#if __IOS__
        UXCam.Xamarin.iOS.UXCam.TagScreenName("Home");
#elif __ANDROID__
        Com.UXCam.UXCam.TagScreenName("Home");
#endif
    }
}
```

**Using DependencyService:**
```csharp
// Interface
public interface IUXCamService
{
    void TagScreen(string name);
}

// iOS Implementation
[assembly: Dependency(typeof(UXCamService))]
public class UXCamService : IUXCamService
{
    public void TagScreen(string name) => UXCam.TagScreenName(name);
}

// Android Implementation
[assembly: Dependency(typeof(UXCamService))]
public class UXCamService : IUXCamService
{
    public void TagScreen(string name) => Com.UXCam.UXCam.TagScreenName(name);
}

// Usage
DependencyService.Get<IUXCamService>().TagScreen("Home");
```

---

## Occlusion Issues

### Sensitive Data Visible

**iOS:**
```csharp
var sensitiveView = FindViewByName("CreditCardEntry");
if (sensitiveView != null)
{
    UXCam.OccludeSensitiveView(sensitiveView.GetNativeView());
}
```

**Android:**
```csharp
var sensitiveView = FindViewById<EditText>(Resource.Id.CreditCardEntry);
if (sensitiveView != null)
{
    Com.UXCam.UXCam.OccludeSensitiveView(sensitiveView);
}
```

**Occlude entire screen:**
```csharp
// iOS
UXCam.OccludeSensitiveScreen(true);

// Android
Com.UXCam.UXCam.OccludeSensitiveScreen(true);
```

---

## Build Issues

### Linker Errors

**Symptoms:**
```
MT2002: Failed to resolve UXCam.Xamarin.iOS
```

**Solution** - Update iOS linker behavior in project options:
- Right-click iOS project > Properties > iOS Build
- Set Linker behavior to "Link SDK assemblies only" or "Don't link"

### Android ProGuard

Add to `proguard.cfg`:
```proguard
-keep class com.uxcam.** { *; }
-dontwarn com.uxcam.**
```

### iOS Minimum Version

Update `Info.plist`:
```xml
<key>MinimumOSVersion</key>
<string>12.0</string>
```

Or in project settings:
- iOS Build > Deployment Target: 12.0

---

## Binding Issues

### Method Not Found

**Symptoms:**
```
System.MissingMethodException: Method 'UXCam.StartWithKey' not found
```

**Solution:**
The API may have changed. Check the current method signature:
```csharp
// Old API
UXCam.StartWithKey("YOUR_KEY");

// New API
var config = new UXCamConfiguration("YOUR_KEY");
UXCam.StartWithConfiguration(config);
```

---

## Debug Information Collection

When contacting support, include:

1. **Environment:**
   - Visual Studio version
   - Xamarin/.NET MAUI version
   - Target platforms and versions

2. **NuGet packages:**
```bash
dotnet list package
```

3. **Device logs:**
   - iOS: Xcode console
   - Android: `adb logcat | grep UXCam`

4. **Configuration:**
   - Initialization code
   - Project settings

---

## Contact Support

- **Email**: [team@uxcam.com](mailto:team@uxcam.com)
- **Documentation**: [Xamarin/MAUI Integration Guide](index.md)
- **Changelog**: [Version History](xamarin-sdk-changelog.md)
