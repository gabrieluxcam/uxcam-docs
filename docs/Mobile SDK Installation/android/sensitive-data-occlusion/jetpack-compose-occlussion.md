---
title: Jetpack Compose Occlusion
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
<GitHubCallout type="warning"> 🚧 This guide covers occlusion methods specifically for Jetpack Compose. For occluding traditional Android Views, please refer to our main occlusion documentation.</GitHubCallout>

To support occlusion in Jetpack Compose, we have introduced a new helper module: **UXCamKtx**. This module is required to occlude Composables and is available for Android SDK v3.4.1 and newer. The standard UXCam SDK is still a required dependency. (guide [here](https://uxcam-documentation.readme.io/docs/android))

***

### Using The UXCamKtx Artifact

The process for including UXCamKtx in a project is almost exactly the same as the process to include the core UXCam module. The only difference is that you will have to use the uxcam-ktx artifact instead of the uxcam artifact:

```java Kotlin
// core module
implementation "com.uxcam:uxcam:3.+"
// ktx module
implementation "com.uxcam:uxcam-ktx:1.+"
```

Then simply, import the UXCam artifact:

```java Kotlin
import com.uxcam.UXCamKt
```

Now you're ready to start setting the occlusion methods for your composables!

> 📘 **Note**
>
> The `uxcam-ktx`artifact is only required if you need to use the Jetpack Compose occlusion APIs. Otherwise, you only need the core`uxcam` dependency.

### Occluding Composables

The occlusion API is defined as follows:

```java Kotlin
fun occludeSensitiveComposable(
    identifier: Any,
    view: View,
    coordinates: LayoutCoordinates,
    isInDialog: Boolean = false
) {
    UXCamKtxModule.getComposeHelper().occludeSensitiveComposable(
        identifier, view, coordinates, isInDialog
    )
}
```

Where:

<p style={{ fontSize: "16px" }}>
  <code>Identifier</code>

  <br />
</p>

This identifier needs to be unique, at least in the screen, i.e. there should be no other composable being occluded using the same identifier in the same screen. The identifier can be of any type.

<p style={{ fontSize: "16px" }}>
  <code>View</code>

  <br />
</p>

The occlusion API needs the view object that represents the current composable view. You can obtain this using <br />\
<code>val view = LocalView\.current</code>

<p style={{ fontSize: "16px" }}>
  <code>LayoutCoordinates</code>

  <br />
</p>

This parameter represents the current coordinates of the composable. This can only be obtained by using an onGloballyPositioned callback on a Modifier.

<p style={{ fontSize: "16px" }}>
  <code>isInDialog</code>

  <br />
</p>

This parameter represents if a composable is in a dialog or not. Default value is false. True should be passed if the composable is placed in the dialog.

***

#### Example:

Here is a complete example of how the occlusion would be applied:

```java Kotlin
@Composable
fun ExampleComposable(modifier: Modifier = Modifier) {
  Column(modifier = modifier) {
    Text(text = "This is a composable!")
    val view = LocalView.current
    Text(text = "Sensitive data!", modifier = Modifier.onGloballyPositioned { coordinates ->
      UXCamKt.occludeSensitiveComposable(
        identifier = "SensitiveView1",
        view = view,
        coordinates = coordinates,
        isInDialog = false
      )
    })
  }
}
```