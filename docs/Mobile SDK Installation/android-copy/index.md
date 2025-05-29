---
title: Android SDK Integration Guide
excerpt: How to Get Started with UXCam for Android
deprecated: false
hidden: false
metadata:
  robots: index
next:
  pages:
    - slug: initialise-uxcam-and-start-recording
      title: Initialise UXCam SDK and start recording
      type: basic
---
Welcome! If you haven’t yet signed up for UXCam, grab your **free trial** in seconds → [Sign Up](https://app.uxcam.com/signup) .

Once your account is ready, follow the **five-step path** below to start turning raw usage into AI-powered insights for Product and Engineering teams.\
Most teams ship the core setup in **under two hours** (often much faster with UXCam’s AI helpers).

***

## Integration Journey at a Glance

<Table align={["left","left","left","left","left"]}>
  <thead>
    <tr>
      <th style={{ textAlign: "left" }}>
        #
      </th>

      <th style={{ textAlign: "left" }}>
        Step
      </th>

      <th style={{ textAlign: "left" }}>
        Goal
      </th>

      <th style={{ textAlign: "left" }}>
        Est. time with AI
      </th>

      <th style={{ textAlign: "left" }}>
        Est. time manual
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td style={{ textAlign: "left" }}>
        **1**
      </td>

      <td style={{ textAlign: "left" }}>
        **Initialise SDK and start recording**  [Link](https://developer.uxcam.com/v2.0/update/docs/initialise-uxcam-and-start-recording#/versions)
      </td>

      <td style={{ textAlign: "left" }}>
        Capture your first live session
      </td>

      <td style={{ textAlign: "left" }}>
        **10 min**
      </td>

      <td style={{ textAlign: "left" }}>
        **10 min**
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        **2**
      </td>

      <td style={{ textAlign: "left" }}>
        **Masking PII and Sensitive Content** [Link](https://app.uxcam.com/signup)
      </td>

      <td style={{ textAlign: "left" }}>
        Hide passwords, PII & GDPR/CCPA data
      </td>

      <td style={{ textAlign: "left" }}>
        **10 min**
      </td>

      <td style={{ textAlign: "left" }}>
        **\~1 h**
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        **3**
      </td>

      <td style={{ textAlign: "left" }}>
        **Screen Tagging** [Link](https://app.uxcam.com/signup)
      </td>

      <td style={{ textAlign: "left" }}>
        Enable heat maps and screen analytics
      </td>

      <td style={{ textAlign: "left" }}>
        **20 min**
      </td>

      <td style={{ textAlign: "left" }}>
        **\~2 h**
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        **4 ★**
      </td>

      <td style={{ textAlign: "left" }}>
        **Set User Identity and Properties** [Link](https://app.uxcam.com/signup)
      </td>

      <td style={{ textAlign: "left" }}>
        Power funnels & cohorts
      </td>

      <td style={{ textAlign: "left" }}>
        **20 min**
      </td>

      <td style={{ textAlign: "left" }}>
        **\~2 h**
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        **5**
      </td>

      <td style={{ textAlign: "left" }}>
        **Send Custom Events with Properties** [Link](https://app.uxcam.com/signup)
      </td>

      <td style={{ textAlign: "left" }}>
        Final smoke-test + release
      </td>

      <td style={{ textAlign: "left" }}>
        **\~20 min**
      </td>

      <td style={{ textAlign: "left" }}>
        **\~20 min**
      </td>
    </tr>
  </tbody>
</Table>

> ★ **Optional but highly recommended.** You can launch with the core setup (steps 1-3) and revisit step 4 later.

***

## Key Benefits After Setup

* **Session Replay + Heat maps** – watch each UX moment in context.
* **Advanced Product Analytics** – measure conversions, engagement and retention of your users, and be alerted about any changes.
* **AI-generated Insights** – anomaly detection, journey summaries, rage-tap alerts.
* **Engineering Analytics** – client-side performance correlated with user behaviour.
  <br />

***

## 💡 Tips Before You Begin

* **Separate keys** for *debug* and *production* keep analytics squeaky-clean.
* **Use feature flags** to toggle UXCam in staging builds without redeploying.
* **Lean on AI helpers** – they auto-detect sensitive views and propose screen/event names.
* **Check Logcat for “Verification success” and "Session/Video uploaded" messages** to confirm everything is wired up.\
  Happy shipping! 🎉