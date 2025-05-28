---
title: Screen Tagging (COPY)
deprecated: false
hidden: false
metadata:
  robots: index
next:
  pages:
    - slug: sensitive-data-occlusion
      title: Sensitive Data Occlusion
      type: basic
---
So you've integrated the SDK and have some sessions going already, good! Now it's time to dig into screens—what they are, how to tag them, and why it matters for understanding user behaviour in your app.

## Automatic Screen Tagging - The Recommended, Simple Approach

For Android, UXCam's SDK automatically tags screens (i.e., activities), and this is the recommended default behaviour. Automatic tagging means that each activity your user navigates to is recorded automatically, without requiring you to manually intervene. This gives you the advantage of capturing user activity with minimal integration effort.

### Tagging Fragments

Fragments can also be automatically tagged in Android. However, we recommend testing with the option disabled when starting out, especially if your app's structure includes many fragment-based views. This will give you more granular control over screen identification as you get familiar with UXCam.

To enable (default) or disable automatic tagging during SDK initialisation, use the following code:

```coffeescript Android
UXConfig config = new UXConfig.Builder("yourAppKey")
    .enableAutomaticScreenNameTagging(true) // True by Default. Set to false if you want to disable automatic screen tagging.
    .build();
UXCam.startWithConfiguration(config);

```

As for enabling or disabling Fragment Based Tagging, it can be done trough the options in your UXCam Dashboard:

<Image align="center" src="https://files.readme.io/07229bc9d7a26e06060c2acbd17e5204d40f73c933de16f20d85a2b24f1f8adf-image.png" />

## Manual Screen Tagging - For In Depth Customisation

While we recommend automatic tagging, you may sometimes need more precise control over your screen names or want to ensure custom screens are clearly identified in your analytics. In these cases, you can use manual screen tagging.

**Example**: **Tagging an Activity Manually**\
To tag a screen in an activity, use the following code in the `onCreate()` method of your activity:

```coffeescript Android
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
  
    UXCam.tagScreenName("Main Activity"); // Manually tag this activity with a screen name.
}
```

**Example: Tagging a Fragment Manually**\
Similarly, to tag a fragment, place the code in the `onResume()` method of the fragment:

```coffeescript Android
@Override
public void onResume() {
    super.onResume();
    UXCam.tagScreenName("Fragment Name"); // Manually tag this fragment with a screen name.
}

```

## How to Tag WebViews

WebViews can be a unique challenge when it comes to screen tagging, as they often contain dynamic content. To properly tag your WebView screens, you can follow our detailed guide that walks you through the best practices and methods for effective WebView tagging.

For more information, click the button below:

[Tagging WebViews Documentation](/docs/web-view-tagging)

> 👍 Summary:
>
> **Automatic Tagging:** By default, UXCam automatically tags screens in Android apps, which we recommend as the simplest and most efficient approach.\
> **Tagging Fragments:** Fragments can also be automatically tagged, but consider disabling this initially for more control.\
> **Manual Tagging:** When you need specific names for screens or want to add more meaningful tags for your analytics, manual tagging is straightforward and can be done in the lifecycle methods like `onCreate()` for activities or `onResume()` for fragments.

With this approach, you'll be well-equipped to make data-driven decisions based on how users move through your app's screens.

<br />

## Next Steps ➡️

<br />

You're on a roll! You've now set up some of the most important features from UXCam to get full insights already, but let's dive next into protecting sensitive information.