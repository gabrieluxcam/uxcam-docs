---
title: UXCam Integration – 5‑Step Fast‑Track
deprecated: false
hidden: false
metadata:
  robots: index
---
Welcome! If you haven’t signed up yet, start your **free trial** → [Sign Up](https://app.uxcam.com/signup).

Once your account is ready, walk through the five steps below.\
Most teams complete the core install in **≈ 2 hours** with a single developer.

***

## Integration Journey at a Glance

<Table align={["left","left","left"]}>
  <thead>
    <tr>
      <th>
        #
      </th>

      <th>
        Step
      </th>

      <th>
        Goal
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        **1**
      </td>

      <td>
        **Initialise SDK & Start Recording**
      </td>

      <td>
        Capture your first live session
      </td>
    </tr>

    <tr>
      <td>
        **3**
      </td>

      <td>
        **Screen Tagging**
      </td>

      <td>
        Enable heat‑maps & screen analytics
      </td>
    </tr>

    <tr>
      <td>
        **2**
      </td>

      <td>
        **Mask PII & Sensitive Content**
      </td>

      <td>
        Hide passwords and other GDPR/CCPA data
      </td>
    </tr>

    <tr>
      <td>
        **4 ★**
      </td>

      <td>
        **User Identity & Properties**
      </td>

      <td>
        Unify sessions, power funnels & cohorts
      </td>
    </tr>

    <tr>
      <td>
        **5 ★**
      </td>

      <td>
        **Event Tagging**
      </td>

      <td>
        Measure key actions & final QA
      </td>
    </tr>
  </tbody>
</Table>

<GitHubCallout type="note"> ★ Optional But Highly Recommended:                                                Ship steps 1‑3 to start getting replays and heat‑maps, then add steps 4‑5 for deeper analytics.</GitHubCallout>

<br />

## Supported Platforms and Technical Integration Guides

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

***

<br />

## 💡 Tips Before You Begin

Create **separate keys** for *debug* & *production* to keep data clean.

Use **feature flags** to toggle UXCam in staging builds.

Let the built‑in helpers auto‑detect sensitive views and suggest screen/event names.

In Logcat look for **“Verification successful”** and **“Session/Video uploaded”** messages to confirm everything is wired up.

***

<br />

## Key Benefits After Setup

* **Session Replay + Heat‑maps** – watch every UX moment in context.
* **Advanced Product Analytics** – funnels, retention, feature adoption.
* **Insight Alerts** – journey summaries, rage‑tap & anomaly detection.
* **Engineering Analytics** – client‑side performance tied to real sessions.

***

<br />

Happy shipping! 🎉