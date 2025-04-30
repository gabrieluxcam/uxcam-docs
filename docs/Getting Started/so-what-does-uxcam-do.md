---
title: So What Does UXCam Do?
excerpt: Let's Understand What UXCam Is Before Integrating It, Right?
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
# Getting Started with UXCam 🚀

Welcome to UXCam! This is where you’ll unlock powerful insights into how users interact with your mobile app and website. Integrating UXCam gives you a comprehensive toolkit that fuels product growth and optimization through **Session Replays**, **Heatmaps**, **User Analytics**, and more.

With UXCam, you'll be able to capture the real story of user experiences and make better, data-driven decisions about your product’s usability.

> **Fun Fact:** UXCam relies on session recording to give you the best insight into your users actions. Each open-and-close is counted as one "session" in UXCam terms.

***

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/118cef2ea4c88dccb1002f41e8ee68e0cae1e41fe748403769e0bbe031c73dee-02_Showcase_Tagless_autocapture_Hero_1.png",
        "",
        ""
      ],
      "align": "center"
    }
  ]
}
[/block]


## Basic Concepts 📘

Before diving in, let’s breeze through some core concepts:

### Session

A **session** is the window of time when a user interacts with your app, starting when they open it and ending when it goes to the background. Each session tells a unique story of how the user interacts, which screens they visit, and more.

### Screen

**Screens** are the individual pages or views a user navigates through in your app. Tracking these helps reveal user journeys and which parts of your app are most engaging.

### Custom Event

An **event** is a specific action a user takes, like tapping a button or completing a form. Events help you understand how users engage with different features and parts of your app.

### Custom User Property

**User properties** are details about each user, like device type or app version. These properties help segment users for deeper insights into usage patterns and preferences.

***

## Your App Key 🔑

To start tracking, each UXCam account needs an **App Key** for both test and production environments. This keeps your data clean and actionable – no need to mix test actions with real user insights!

### Finding Your App Key

1. **Log In** to the UXCam dashboard and ensure you have both a test and production environment. If not, create a new app specifically for testing.
2. **Locate the App Key**: If you’re setting up a new app, you’ll see the App Key listed in the **App Settings**. For an existing app, click the app name in the top-left corner > **App Settings** to find it.
3. **Invite Your Team**: If others need access, don’t forget to add them! Here’s how to [add team members](https://help.uxcam.com/hc/en-us/articles/360036092012-Manage-your-team).

> **Pro Tip:** Keep your test data separate from your production data to ensure clean, actionable insights!

***

<br />

### Ready for Action? 🔍

Explore our powerful analytics features and start uncovering how users experience your app. For a detailed look at each feature, check out the [full list here](https://help.uxcam.com/hc/en-us/categories/360002359512-knowledge-base).

***

<br />

## Next Steps ➡️

Now that you’re all set up with your App Key, it’s time to integrate the UXCam SDK. Just identify your platform below (or in the side panel) and follow the step-by-step guides in the **SDKs section**.

<br />

Happy analyzing with UXCam! 🎉

[block:html]
{
  "html": "<style>\n  /* Enable smooth scrolling */\n  html {\n    scroll-behavior: smooth;\n  }\n\n  /* Container for the cards */\n  .uxcam-card-container {\n    display: grid;\n    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));\n    gap: 20px;\n    margin-top: 30px;\n  }\n\n  /* Card styling */\n  .uxcam-card {\n    display: flex;\n    flex-direction: column;\n    align-items: center;\n    justify-content: center;\n    padding: 40px;\n    font-family: \"Roboto\", sans-serif;\n    font-size: 1.2rem;\n    font-weight: 500;\n    color: white;\n    background-color: rgb(20, 30, 53);\n    border-radius: 12px;\n    text-decoration: none;\n    transition: transform 0.3s ease, box-shadow 0.3s ease, background-color 0.3s ease;\n    height: 160px;\n    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);\n    cursor: pointer;\n  }\n\n  /* Hover effect */\n  .uxcam-card:hover {\n    background-color: rgb(15, 25, 43);\n    transform: translateY(-10px) scale(1.02);\n    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.3);\n  }\n\n  /* Active (click) effect */\n  .uxcam-card:active {\n    transform: translateY(-2px) scale(0.98);\n    background-color: rgb(5, 10, 28);\n  }\n\n  /* Floating arrow container */\n  .floating-arrow-container {\n    position: fixed;\n    bottom: 20px;\n    right: 20px;\n    display: flex;\n    flex-direction: column-reverse;\n    align-items: center;\n    text-decoration: none;\n    cursor: pointer;\n  }\n\n  /* Floating arrow styling */\n  .floating-arrow {\n    background-color: rgb(20, 30, 53);\n    color: white;\n    font-size: 3rem;\n    width: 90px;\n    height: 90px;\n    display: flex;\n    align-items: center;\n    justify-content: center;\n    border-radius: 50%;\n    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);\n    transition: background-color 0.3s ease, transform 0.3s ease;\n  }\n\n  /* Hover effect for the arrow */\n  .floating-arrow-container:hover {\n    transform: translateY(-5px);\n  }\n \n\n  /* Callout text styling */\n  .floating-arrow-callout {\n    font-family: \"Roboto\", sans-serif;\n    font-size: 1rem;\n    font-weight: bold;\n    color: white;\n    background-color: rgb(94 151 255); /* Light blue color */\n    padding: 8px 12px;\n    border-radius: 8px;\n    position: relative;\n    margin-bottom: 15px;\n    opacity: 0;\n    transform: translateY(20px);\n    animation: fadeInUp 1s ease-in-out 0.8s forwards;\n  }\n\n  /* Comic-style arrow for callout */\n  .floating-arrow-callout::after {\n    content: '';\n    position: absolute;\n    top: 100%;\n    left: 50%;\n    transform: translateX(-50%);\n    border-width: 10px;\n    border-style: solid;\n    border-color: rgb(94 151 255) transparent transparent transparent;\n  }\n\n  /* Animation for callout text */\n  @keyframes fadeInUp {\n    from {\n      opacity: 0;\n      transform: translateY(20px);\n    }\n    to {\n      opacity: 1;\n      transform: translateY(0);\n    }\n  }\n \n</style>\n\n<!-- Floating Arrow with Callout -->\n<a href=\"#card-section\" class=\"floating-arrow-container\">\n  <div class=\"floating-arrow\">&darr;</div>\n  <div class=\"floating-arrow-callout\">Choose Your Framework!</div>\n</a>\n\n<!-- Card Container -->\n<div id=\"card-section\" class=\"uxcam-card-container\">\n  <a href=\"https://developer.uxcam.com/v1.1/docs/android\" class=\"uxcam-card\">\n    <img src=\"https://files.readme.io/7d87e025fa0de0b38c83ecfbb1ca851a897a4d2691c82e1576aeb533f712b1cf-android.svg\" alt=\"Android Icon\">\n    Android\n  </a>\n  <a href=\"https://developer.uxcam.com/v1.1/docs/ios\" class=\"uxcam-card\">\n    <img src=\"https://files.readme.io/06201ab772578c4b561dd263620abaf3866f6c58de501ffa7ec685b9fc50ea2b-ios.svg\" alt=\"iOS Icon\">\n    iOS\n  </a>\n  <a href=\"https://developer.uxcam.com/v1.1/docs/react-native\" class=\"uxcam-card\">\n    <img src=\"https://files.readme.io/8581b7562913537b68e6ab8550e346292bbbd81b8343578787012e92265c5e1c-react.svg\" alt=\"React Native Icon\">\n    React Native\n  </a>\n  <a href=\"https://developer.uxcam.com/v1.1/docs/flutter\" class=\"uxcam-card\">\n    <img src=\"https://files.readme.io/f1ed6aa060e6b0110ce0629c47c66290b01967563e85d82d1eb4bc0fe4b5e4d2-flutter.svg\" alt=\"Flutter Icon\">\n    Flutter\n  </a>\n  <a href=\"https://developer.uxcam.com/v1.1/docs/cordova\" class=\"uxcam-card\">\n    <img src=\"https://files.readme.io/5b98f6652a0dc15db6a6d0ce4b0ab612e726de64a51c0ea65bdbc2c676a611eb-cordova.svg\" alt=\"Cordova Icon\">\n    Cordova\n  </a>\n  <a href=\"https://developer.uxcam.com/v1.1/docs/xamarin-maui\" class=\"uxcam-card\">\n    <img src= \"https://files.readme.io/b6042d82b9921a24bfb7bd09d570b0c81215529f53382e7ffc43d29565f440a8-xamarin.svg\" alt=\"Xamarin Icon\">\n    Xamarin\n  </a>\n  <a href=\"https://developer.uxcam.com/v1.1/docs/nativescript\" class=\"uxcam-card\">\n    <img src=\"https://files.readme.io/7b2a504a8251b70f4aac5ac1e00566964640b90700f1c47c7eb784c2def8f09a-native.svg\" alt=\"NativeScript Icon\">\n    NativeScript\n  </a>\n  <a href=\"https://developer.uxcam.com/v1.1/docs/installation\" class=\"uxcam-card\">\n    <img src=\"https://files.readme.io/3b0f74fbbf5f9aa6bedf518d0cefe586c05fffdd2a89c3b24e6cd71219ea9538-web_1.svg\" alt=\"Web Icon\">\n    Web\n  </a>\n</div>\n</div>\n"
}
[/block]