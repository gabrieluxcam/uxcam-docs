---
title: So What Does UXCam Do?
excerpt: How UXCam finds insights in your app or website from what the SDK records
deprecated: false
hidden: false
icon: fa-solid fa-lightbulb
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
UXCam is a product analytics platform with an integrated AI analyst, for mobile apps and websites. We serve companies in 50+ countries and process 100B+ data points every month. Our platform finds insights in your mobile app or website before you ask: what changed, what's costing you users, and the sessions that show each problem.&#x20;

Everything runs on what the SDK captures by itself, so you get answers without tagging events first.

## What the SDK does

UXCam has SDKs for iOS, Android, React Native, Flutter and other mobile frameworks, and a web SDK for websites and web apps.

### Records every session

A session starts when someone opens your app or website. UXCam records each screen or page they visit and every tap, click, swipe and scroll, plus device and OS details. UXCam flags rage taps, rage clicks and unresponsive gestures on its own. In mobile apps, UXCam also catches crashes and UI freezes. On the web, you send errors as events.

In a mobile app, a session ends when the app goes to the background. On a website, a session ends when the visitor closes the tab, loses the connection or is inactive for 3 minutes.

### Masks sensitive data on the device

The SDK applies masking before anything is uploaded, so masked content never leaves the user's device. Password fields are masked automatically on iOS, Android, React Native and the web. On the web, email, phone and card fields are masked too. Flutter apps have no automatic masking.

Everything else on screen, such as names, addresses and messages, shows in the replay until you mask it. You can mask a single field, an area or a whole screen or page. UXCam doesn't record audio or anything from other apps or browser tabs.

Need consent before recording? Start the SDK once the user agrees, or use the opt-out API. <Anchor target="_blank" href="https://developer.uxcam.com/docs/privacy-and-compliance">See Privacy and Compliance.</Anchor>

### Uploads when people leave

In mobile apps, the SDK uploads each session when the app goes to the background. New sessions show up in UXCam within about 10 minutes, and a failed upload is retried with the next session.

On websites, the SDK sends each session when the visitor leaves the page or closes the tab, so a tab left open delays the upload. New sessions show up about 30 seconds after the upload. Both SDKs send data over encrypted connections.

Testing your integration? In a mobile app, send the app to the background before you stop it in Xcode or Android Studio. On a website, use a regular browser with ad blockers off, because headless browsers don't record. If your site has a Content Security Policy, allow https\://_.uxcam.com and wss\://_.uxcam.com. If you limit recording to certain domains, test on one of them, because localhost can't be added.

For capture methods and memory, network and battery use on each platform, <Anchor target="_blank" href="https://developer.uxcam.com/docs/how-session-recording-works">see How Session Recording Works</Anchor>.

### What UXCam does with your sessions

You don't have to watch thousands of replays or build reports to find problems. UXCam finds them and brings you the evidence.

- Discover: every week, Tara, UXCam's AI analyst, flags what changed and why, with the sessions that explain each change. Tara also ranks issues by business impact and recommends what to fix. You can ask Tara questions in plain language, or from Claude, ChatGPT or Cursor through the <Anchor target="_blank" href="https://uxcam.com/mcp/">UXCam MCP</Anchor>.

- See: open the sessions Tara points you to and watch where users got stuck. Every tap, click and rage tap lands on the heatmap for its screen or page automatically.

- Measure: funnels, retention and dashboards show whether a fix worked. Describe what you want to track, and UXCam builds the dashboard and keeps an eye on the numbers for you.

## What you add

Recording works without extra code. Before you release, mask anything personal on screen. The other three additions make your reports and Tara's answers more precise.

- Masking keeps names, addresses, messages and other personal data out of replays. Guides: <Anchor target="_blank" href="https://developer.uxcam.com/docs/screen-blurring">mobile apps</Anchor>, <Anchor target="_blank" href="https://developer.uxcam.com/docs/occlusion-hide-sensitive-data">websites</Anchor>.
- Screen and page names give heatmaps, funnels and journeys names your team knows, such as Checkout. Guides: <Anchor target="_blank" href="https://developer.uxcam.com/docs/tag-of-screens">mobile apps</Anchor>, <Anchor target="_blank" href="https://developer.uxcam.com/docs/define-pages-properties">websites</Anchor>.
- Events count the actions that matter, such as Purchase_Completed, so you can build funnels on them. Guides: <Anchor target="_blank" href="https://developer.uxcam.com/docs/send-events">mobile apps</Anchor>, <Anchor target="_blank" href="https://developer.uxcam.com/docs/logging-custom-events">websites</Anchor>.
- A user ID and user properties let you follow one person across sessions and compare groups by plan or role. Guides: <Anchor target="_blank" href="https://developer.uxcam.com/docs/send-user-properties">mobile apps</Anchor>, <Anchor target="_blank" href="https://developer.uxcam.com/docs/logging-custom-events">websites</Anchor>.

## Your app key

New to UXCam? <Anchor target="_blank" href="https://tara.uxcam.com/signup">Create an account first.</Anchor>

The app key tells the SDK which project and environment a session belongs to. Each project starts with a Production and a Staging environment, each with its own key. Use the Staging key in test builds and on staging sites, and the Production key in the version your users get. That keeps test sessions out of your real data.

Find the key on your project's Install page: pick the environment and copy its key. Every key is also listed under Settings → Apps. On the previous version of the UXCam dashboard, the key is under App Settings.

## Start your integration

Pick your platform on <Anchor target="_blank" href="https://developer.uxcam.com/docs/getting-started-overview">Getting Started</Anchor>. Adding the SDK takes about 5 minutes.

Working with Cursor, Claude Code or VS Code with Copilot? <Anchor target="_blank" href="https://uxcam.com/mcp/#setup">Connect the UXCam MCP</Anchor> and your assistant can add the SDK, find your app key and check that sessions arrive.

<br />
