---
title: 'Occlude sensitive data '
deprecated: false
hidden: true
metadata:
  robots: index
next:
  description: >-
    Excellent! You can record some test sessions and once you're finalised with
    occlusion, we can move onto custom events and user properties!
  pages:
    - slug: users-and-properties
      title: Users and Properties
      type: basic
---
<Tabs>
  <Tab title="Native Android">
    In this section, we focus on how to handle sensitive data within your Android application using UXCam's features to ensure privacy compliance. It is essential to occlude sensitive information to protect user data like passwords, credit card numbers, or any other Personally Identifiable Information (PII).

    UXCam ensures that as a controller you can use our platform and fulfil your obligations under GDPR. However, if you collect any **PII data** in your app such as email address, phone, or credit card number you should use our API to hide it.

    You can choose to hide:

    * **Texts**: when you only collect PII data with text fields
    * **Screen Views**: when you need to hide a specific section of your screen.
    * **Screens:** when you need to hide the whole screen, e.g. payment screen.

    Sensitive information will be hidden under red boxes or blurred screens on the device before rendering the video and therefore never sent to UXCam. Make sure that all this info is hidden before releasing your app to production to make sure your users' PII is never recorded.

    Please keep in mind that when hiding sensitive information you can still record gestures for that view or screen, however, if you're hiding passwords or keywords you should enable the option to hide gestures as well.

    ***

    ## Are there any elements occluded right out of the box?

    **Android**:

    * Password fields with `android:inputType="textPassword"` or `InputType.TYPE_TEXT_VARIATION_PASSWORD` are occluded.

    **Jetpack Compose** does not support default occlusion right out of the box at the moment.

    ***

    # Add Occlusions Directly From Your Dashboard:

    You can now add occlusion rules to your app directly from your dashboard. Simply go into your app's settings in your dashboard and enable the rules you need, below is a breakdown of how to take full advantage of this feature, without having to resort to adding additional code in your app for most scenarios:

    ### Occlude All Screens from Dashboard

    From your app's settings in the UXCam dashboard, you'll see the **video recording privacy** section, from there, you'll see the first option to either record, occlude or blur all screens in your app.

    <Image align="center" src="https://files.readme.io/751b737-image.png" alt="Blur option will also enable you to select the blur radius (strength) once selected." />

    ### Screen Specific Occlusion Rules from Dashboard

    You can also customise which screens you want to apply occlusion rules to, and can create multiple rules, for example, blurring a particular screen but occluding others:

    ![](https://files.readme.io/6b8810f-small-Staging_-_UXCam_Dashboard.png)

    ### Occlude Text Input Fields from Dashboard

    You can also choose to occlude all text input fields on a specific or multiple screens by simply checking the option and selecting the screens you'd like to occlude the text inputs in.

    ![](https://files.readme.io/253cbf0-small-Staging_-_UXCam_Dashboard.png)

    > 📘 **Note**
    >
    > **Note**
    >
    > Additionally, you can opt to record gestures on all blurred/occluded screens by toggling on the option
    >
    > ![](https://files.readme.io/33bf4ad-image.png)

    ### Occlusion priority:

    * Screen specific overlay from Dashboard
    * Screen specific blur from Dashboard
    * Global blur/overlay from Dashboard that is applied to all screens
    * Screen specific Overlay from SDK
    * Screen specific Blur from SDK
    * Global blur/overlay from SDK that is applied to all screens
    * Global blur/overlay from SDK that has Record exception screens

    ### Limitations:

    * Hiding sensitive **Views** needs to be handled from code (see [here](https://developer.uxcam.com/docs/screen-blurring#hide-sensitive-view))

    # Occlusion Setup from SDK Code

    If you desire to manually handle occlusions in your app or [occlude specific views](https://developer.uxcam.com/docs/screen-blurring#hide-sensitive-view) instead of entire screens, read below for guidance on how to set it up.

    ## Occlude The Entire Screen with Overlay

    You can configure different overlay options with the following:

    ```java
    UXCamOverlay overlay = new UXCamOverlay.Builder()
                                  .withoutGesture(false)
                                  .build();

    UXCam.applyOcclusion(overlay); //To apply overlay
    UXCam.removeOcclusion(overlay); //To remove overlay
    ```

    **Available overlay options are:**

    <p style={{fontSize: "17px"}}><code>withoutGesture(boolean withoutGesture) || hideGestures(boolean hideGestures)</code><br /></p>
    Allows the user to configure wether to capture gesture in the occluded screen or not. Passing in false to this method tells the SDK to capture gestures. Default is true, so by default the gestures are not captured.

    <p style={{fontSize: "17px"}}><code>screens(List screens)</code> - Use it in the configuration object<br /></p>
    Allows you to define the screens where the overlay is to either be applied or not, depending on the value passed to <strong>excludeMentionedScreens(boolean excludeMentionedScreens)</strong>.

    By default, if no screens are passed, the overlay is applied to all the screens unless explicitly removed. This acts as a global setting and will override all other occlusion settings defined for all screens. The occlusion must be removed to revert this action.

    If screens are passed, you have the ability to either apply overlay on the mentioned screens or to exclude the mentioned screens from being overlayed.

    <p style={{fontSize: "17px"}}><code>excludeMentionedScreens(boolean excludeMentionedScreens)</code><br /></p>
    This option should be used in conjunction with <strong>screens(List screens)</strong>.

    If the passed in value is true, it tells the SDK to exclude the mentioned screens from occlusion, while applying the occlusion to the rest of the screens in the app.

    If the passed in value is false, it tells the SDK to apply occlusion only to the screens that have been passed.

    Default value is false.

    <Image align="center" src="https://files.readme.io/095be49-Overlay.png" width="80%" alt="2534" title="Overlay.png" />

    ## Blur The Entire Screen

    Blur is an occlusion API that allows you to blur screen records of screens. This lets you obtain information regarding the state of the screen and user interaction, while also maintaining privacy in sensitive screens.

    This is useful to set all the occlusion/Blur from one place of the application without having to set it individually in different screens.

    You can configure different options using the following:

    ```java
    UXCamBlur blur = new UXCamBlur.Builder().build();

    //Example
    UXCamBlur blur = new UXCamBlur.Builder()
                          .blurRadius(10) //default 20
                          .withoutGesture(false)
                          .build();

    UXCam.applyOcclusion(blur); //To apply the Blurring
    ```

    **Available blur options are:**

    <p style={{fontSize: "17px"}}><code>blurRadius(int blurRadius)</code><br /></p>
    This option allows you to define the blur radius to be used for blurring. The higher the value, the more blurred the resulting video is going to be.

    <p style={{fontSize: "17px"}}><code>withoutGesture(boolean withoutGesture) || hideGestures(boolean hideGestures)</code><br /></p>
    Same as overlay. Please refer to overlay section.

    <p style={{fontSize: "17px"}}><code>screens(List screens)</code> - Use it in the configuration object<br /></p>
    Same as overlay. Please refer to overlay section.

    <p style={{fontSize: "17px"}}><code>excludeMentionedScreens(boolean excludeMentionedScreens)</code><br /></p>
    Same as overlay. Please refer to overlay section.

    <Image align="center" src="https://files.readme.io/4b4c4ce-UXCam_Dashboard_-_24_May_2022_1_1.gif" alt="736" title="UXCam Dashboard - 24 May 2022 (1) (1).gif" />

    **Examples on blur radius property customization:**

    <Image align="center" src="https://files.readme.io/72a8f8d-Blur_Results_Comparison_-_Product_Development_-_Confluence.png" alt="Blur radius customization examples" />

    ***

    ## Occlude all Text Fields

    Similar to the new Overlay and Blur APIs:

    ```java
    //Create the object
    UXCamOccludeAllTextFields occludeFields = new UXCamOccludeAllTextFields();

    UXCam.applyOcclusion(occludeFields); //To apply occlusion
    UXCam.removeOcclusion(occludeFields); //To remove the occlusion
    ```

    <Image align="center" src="https://files.readme.io/9caa54d-TextFields.png" width="80%" alt="2534" title="TextFields.png" />

    ## Hide Sensitive View

    Use it to hide specific views with sensitive information that you don't want to record.

    The API parameters are:

    **sensitiveView**: A View object that contains sensitive information.

    ```java
    UXCam.occludeSensitiveView(View sensitiveView);
    ```

    ## Usage from configuration object

    It's also possible to pass a list of occlusions (except Sensitive View) to be applied during configuration.  For example:

    ```java
    UXCamBlur blur = new UXCamBlur.Builder()
            .blurRadius(20)
            .screens(Arrays.asList("ActivitySecret", "LoginActivity"))
            .build();

    UXCamOverlay overlay = new UXCamOverlay.Builder()
            .screens(Arrays.asList("PaymentActivity", "ProfileActivity"))
            .build();

    UXCamOccludeAllTextFields textFields = new UXCamOccludeAllTextFields.Builder()
            .screens(Arrays.asList("PaymentActivity", "ProfileActivity"))
            .build();
            
    UXConfig config = new UXConfig.Builder(appKey)
            .occlusions(Arrays.asList(blur, overlay, textFields))
            .enableImprovedScreenCapture(true)
            .build();
    UXCam.startWithConfiguration(config);
    ```

    ### Sensitive Data Occlusion Inside WebViews

    When dealing with sensitive data in WebViews, it's essential to ensure that any personal or sensitive information is properly occluded to maintain user privacy. Our guide provides step-by-step instructions on how to handle occlusion within WebViews effectively.

    For more information, click the button below:

    [Sensitive Data Occlusion in WebViews Documentation](/docs/sensitive-views-inside-webviews)
  </Tab>

  <Tab title="Jetpack Compose">
    ### Using The UXCamKtx Artifact

    The process for including UXCamKtx in a project is almost exactly the same as the process to include the core UXCam module. The only difference is that you will have to use the uxcam-ktx artifact instead of the uxcam artifact:

    ```java
    // core module
    implementation "com.uxcam:uxcam:3.+"
    // ktx module
    implementation "com.uxcam:uxcam-ktx:1.+"
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
  </Tab>

  <Tab title="Third Tab">
    Here's content that's only inside the third Tab.
  </Tab>
</Tabs>