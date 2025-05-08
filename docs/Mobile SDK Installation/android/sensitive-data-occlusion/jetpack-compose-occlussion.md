---
title: Jetpack Compose Occlussion
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
> If you are not using Jetpack Compose or you are interested in occluding views and data **outside of Jetpack Compose elements** please refer to the main occlusion guides [here](https://uxcam-documentation.readme.io/docs/screen-blurring)

With the introduction of Jetpack Compose recording support on Android SDK v3.4.1 onward, we've made available a new module, **UXCamKtx**, which is required for occlusion for Jetpack Compose composables. It still depends on the com.uxcam:uxcam core maven artifact, so integration process is not altered and you still need to add the Android SDK (guide [here](https://uxcam-documentation.readme.io/docs/android))

***

### Using The UXCamKtx Artifact

The process for including UXCamKtx in a project is almost exactly the same as the process to include the core UXCam module. The only difference is that you will have to use the uxcam-ktx artifact instead of the uxcam artifact:

```java
// core module
implementation "com.uxcam:uxcam:3.6.33"
// ktx module
implementation "com.uxcam:uxcam-ktx:1.2.33"
```

Then simply, import the UXCam artifact:

```java
import com.uxcam.UXCamKt
```

Now you're ready to start setting the occlusion methods for your composables!

> 📘 **Note**
>
> **Note**
>
> If you do not need the Kotlin specific APIs provided by the uxcam-ktx artifact, you can simply just use the uxcam artifact from the regular integration. However, if you need the APIs provided by the uxcam-ktx artifact, then you will have to use that instead.

### Occluding Composables

The occlusion API is defined as follows:

```java
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

```java
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