---
title: Fragment Based Tagging
deprecated: false
hidden: true
metadata:
  robots: index
---
> 🚧 Important
>
> Please keep in mind that this feature is only available in versions 3.6.2+ of the UXCam Android SDK

> 👍 Android Recommendation
>
> If you're using this feature, we highly recommend using **only automatic screen name tagging** to avoid having manually tagged screens that don't properly represent your actual app's screens.

## Fragment Based Tagging

Fragment-based tagging is a feature that enables users to auto-tag fragments in their apps.

### How to implement this?

Enabling this feature is at easy as it is. In your dashboard, navigate to **App Settings** -> **Data capture & recording** -> And under **Additional settings** you will see and option called "**Auto tag screen fragments on Android"**

![](https://files.readme.io/d5659a9-FragmentTagging.png)

That is correct, is that simple! As soon as this option is enabled, all of your fragments will be automatically tagged.

### Example

Here you can check an example where the fragments are automatically tagged.

<Image align="center" width="40% " src="https://files.readme.io/4e18d12-FragmetExample.png" />

* In here, Transaction Activity had 2 fragments (**d45ef3** and **cceda6**) which represent a fragment being initiated.

> ⚠️ Keep in mind
>
> Please keep in mind that at the moment this is only supported for AndroidX

In case you have any questions, please feel free to favourite support team at [team@uxcam.com]()