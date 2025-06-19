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

<br />

* **`identifier: Any`**: A unique identifier for the Composable, which can be of any type. It must not be used for any other occluded Composable on the same screen.

* **`view: View`**: The `View` object that hosts the Composable. You can get this with `LocalView.current`.

* **`coordinates: LayoutCoordinates`**: The Composable's current layout coordinates, which must be obtained from an `onGloballyPositioned` Modifier callback.

* **`isInDialog: Boolean`**: Set this to `true` if the Composable is inside a dialog. The default value is `false`.

This parameter represents if a composable is in a dialog or not. Default value is false. True should be passed if the composable is placed in the dialog.

***

#### Example Usage:

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