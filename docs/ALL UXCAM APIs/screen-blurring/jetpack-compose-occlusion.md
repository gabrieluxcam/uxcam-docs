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
> 🚧 **Warning**
>
> **Warning**
>
> This page refers only to specific occlusion methods when using Jetpack Compose.
>
> If you are not using Jetpack Compose or you are interested in occluding views and data **outside of Jetpack Compose elements** please refer to the main occlusion guides `[here]`([https://developer.uxcam.com/docs/screen-blurring](https://developer.uxcam.com/docs/screen-blurring))

### Using The UXCamKtx Artifact

The process for including UXCamKtx in a project is almost exactly the same as the process to include the core UXCam module. The only difference is that you will have to use the uxcam-ktx artifact instead of the uxcam artifact:

```kotlin
implementation “com.uxcam:uxcam-ktx:1.2.30”
```

Then simply, import the UXCam artifact:

```kotlin
import com.uxcam.UXCamKt
```

Now you're ready to start setting the occlusion methods for your composables!

> 📘 **Note**
>
> **Note**
>
> If you do not need the Kotlin specific APIs provided by the uxcam-ktx artifact, you can simply just use the uxcam artifact from the regular integration. However, if you need the APIs provided by the uxcam-ktx artifact, then you will have the use that instead.

### Occluding Composables

The occlusion API is defined as follows:

```kotlin
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

* **Identifier**: A unique identifier for the composable.
* **View**: The view object that represents the current composable.
* **LayoutCoordinates**: The current coordinates of the composable. This can only be obtained by using a onGloballyPositioned callback on a Modifier.
* **IsInDialog**: If a composable is in a dialog or not. Default value is false. True should be passed if the composable is placed in the dialog.

***

#### Example:

Here is a complete example of how the occlusion would be applied:

```kotlin
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