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

<Image align="center" src="https://files.readme.io/118cef2ea4c88dccb1002f41e8ee68e0cae1e41fe748403769e0bbe031c73dee-02_Showcase_Tagless_autocapture_Hero_1.png" />

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

<HTMLBlock>{`
<style>
  /* Enable smooth scrolling */
  html {
    scroll-behavior: smooth;
  }

  /* Container for the cards */
  .uxcam-card-container {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 20px;
    margin-top: 30px;
  }

  /* Card styling */
  .uxcam-card {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 40px;
    font-family: "Roboto", sans-serif;
    font-size: 1.2rem;
    font-weight: 500;
    color: white;
    background-color: rgb(20, 30, 53);
    border-radius: 12px;
    text-decoration: none;
    transition: transform 0.3s ease, box-shadow 0.3s ease, background-color 0.3s ease;
    height: 160px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
    cursor: pointer;
  }

  /* Hover effect */
  .uxcam-card:hover {
    background-color: rgb(15, 25, 43);
    transform: translateY(-10px) scale(1.02);
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.3);
  }

  /* Active (click) effect */
  .uxcam-card:active {
    transform: translateY(-2px) scale(0.98);
    background-color: rgb(5, 10, 28);
  }

  /* Floating arrow container */
  .floating-arrow-container {
    position: fixed;
    bottom: 20px;
    right: 20px;
    display: flex;
    flex-direction: column-reverse;
    align-items: center;
    text-decoration: none;
    cursor: pointer;
  }

  /* Floating arrow styling */
  .floating-arrow {
    background-color: rgb(20, 30, 53);
    color: white;
    font-size: 3rem;
    width: 90px;
    height: 90px;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 50%;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
    transition: background-color 0.3s ease, transform 0.3s ease;
  }

  /* Hover effect for the arrow */
  .floating-arrow-container:hover {
    transform: translateY(-5px);
  }
 

  /* Callout text styling */
  .floating-arrow-callout {
    font-family: "Roboto", sans-serif;
    font-size: 1rem;
    font-weight: bold;
    color: white;
    background-color: rgb(94 151 255); /* Light blue color */
    padding: 8px 12px;
    border-radius: 8px;
    position: relative;
    margin-bottom: 15px;
    opacity: 0;
    transform: translateY(20px);
    animation: fadeInUp 1s ease-in-out 0.8s forwards;
  }

  /* Comic-style arrow for callout */
  .floating-arrow-callout::after {
    content: '';
    position: absolute;
    top: 100%;
    left: 50%;
    transform: translateX(-50%);
    border-width: 10px;
    border-style: solid;
    border-color: rgb(94 151 255) transparent transparent transparent;
  }

  /* Animation for callout text */
  @keyframes fadeInUp {
    from {
      opacity: 0;
      transform: translateY(20px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }
 
</style>

<!-- Floating Arrow with Callout -->
<a href="#card-section" class="floating-arrow-container">
  <div class="floating-arrow">&darr;</div>
  <div class="floating-arrow-callout">Choose Your Framework!</div>
</a>

<!-- Card Container -->
<div id="card-section" class="uxcam-card-container">
  <a href="https://developer.uxcam.com/v1.1/docs/android" class="uxcam-card">
    <img src="https://files.readme.io/7d87e025fa0de0b38c83ecfbb1ca851a897a4d2691c82e1576aeb533f712b1cf-android.svg" alt="Android Icon">
    Android
  </a>
  <a href="https://developer.uxcam.com/v1.1/docs/ios" class="uxcam-card">
    <img src="https://files.readme.io/06201ab772578c4b561dd263620abaf3866f6c58de501ffa7ec685b9fc50ea2b-ios.svg" alt="iOS Icon">
    iOS
  </a>
  <a href="https://developer.uxcam.com/v1.1/docs/react-native" class="uxcam-card">
    <img src="https://files.readme.io/8581b7562913537b68e6ab8550e346292bbbd81b8343578787012e92265c5e1c-react.svg" alt="React Native Icon">
    React Native
  </a>
  <a href="https://developer.uxcam.com/v1.1/docs/flutter" class="uxcam-card">
    <img src="https://files.readme.io/f1ed6aa060e6b0110ce0629c47c66290b01967563e85d82d1eb4bc0fe4b5e4d2-flutter.svg" alt="Flutter Icon">
    Flutter
  </a>
  <a href="https://developer.uxcam.com/v1.1/docs/cordova" class="uxcam-card">
    <img src="https://files.readme.io/5b98f6652a0dc15db6a6d0ce4b0ab612e726de64a51c0ea65bdbc2c676a611eb-cordova.svg" alt="Cordova Icon">
    Cordova
  </a>
  <a href="https://developer.uxcam.com/v1.1/docs/xamarin-maui" class="uxcam-card">
    <img src= "https://files.readme.io/b6042d82b9921a24bfb7bd09d570b0c81215529f53382e7ffc43d29565f440a8-xamarin.svg" alt="Xamarin Icon">
    Xamarin
  </a>
  <a href="https://developer.uxcam.com/v1.1/docs/nativescript" class="uxcam-card">
    <img src="https://files.readme.io/7b2a504a8251b70f4aac5ac1e00566964640b90700f1c47c7eb784c2def8f09a-native.svg" alt="NativeScript Icon">
    NativeScript
  </a>
  <a href="https://developer.uxcam.com/v1.1/docs/installation" class="uxcam-card">
    <img src="https://files.readme.io/3b0f74fbbf5f9aa6bedf518d0cefe586c05fffdd2a89c3b24e6cd71219ea9538-web_1.svg" alt="Web Icon">
    Web
  </a>
</div>
</div>
`}</HTMLBlock>
