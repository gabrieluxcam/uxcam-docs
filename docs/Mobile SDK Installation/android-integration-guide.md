---
title: Android Integration Guide
deprecated: false
hidden: false
metadata:
  robots: index
---
Welcome! If you haven’t signed up yet, grab your **free trial** → [Sign Up](https://app.uxcam.com/signup).

Once your account is ready, follow the five‑step path below.\
Most teams finish the core setup in **under 2 hours** (often faster with UXCam AI helpers).

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
        AI time
      </th>

      <th style={{ textAlign: "left" }}>
        Manual time
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td style={{ textAlign: "left" }}>
        <strong>1</strong>
      </td>

      <td style={{ textAlign: "left" }}>
        <strong>Initialise SDK & Start Recording</strong> <a href="#initialise-sdk-and-start-recording">Jump →</a>
      </td>

      <td style={{ textAlign: "left" }}>
        Capture your first live session
      </td>

      <td style={{ textAlign: "left" }}>
        <strong>10 min</strong>
      </td>

      <td style={{ textAlign: "left" }}>
        <strong>10 min</strong>
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        <strong>2</strong>
      </td>

      <td style={{ textAlign: "left" }}>
        <strong>Mask PII & Sensitive Content</strong> <a href="#masking-pii-and-sensitive-content">Jump →</a>
      </td>

      <td style={{ textAlign: "left" }}>
        Hide passwords, PII & GDPR/CCPA data
      </td>

      <td style={{ textAlign: "left" }}>
        <strong>10 min</strong>
      </td>

      <td style={{ textAlign: "left" }}>
        <strong>≈ 1 h</strong>
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        <strong>3</strong>
      </td>

      <td style={{ textAlign: "left" }}>
        <strong>Screen Tagging</strong> <a href="#screen-tagging">Jump →</a>
      </td>

      <td style={{ textAlign: "left" }}>
        Enable heat‑maps & screen analytics
      </td>

      <td style={{ textAlign: "left" }}>
        <strong>20 min</strong>
      </td>

      <td style={{ textAlign: "left" }}>
        <strong>≈ 2 h</strong>
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        <strong>4 ★</strong>
      </td>

      <td style={{ textAlign: "left" }}>
        <strong>User Identity & Properties</strong> <a href="#user-identity-and-properties">Jump →</a>
      </td>

      <td style={{ textAlign: "left" }}>
        Unify sessions, power funnels & cohorts
      </td>

      <td style={{ textAlign: "left" }}>
        <strong>10 min</strong>
      </td>

      <td style={{ textAlign: "left" }}>
        <strong>≈ 30 min</strong>
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        <strong>5</strong>
      </td>

      <td style={{ textAlign: "left" }}>
        <strong>Event Tagging</strong> <a href="#event-tagging">Jump →</a>
      </td>

      <td style={{ textAlign: "left" }}>
        Measure actions & final QA before release
      </td>

      <td style={{ textAlign: "left" }}>
        <strong>20 min</strong>
      </td>

      <td style={{ textAlign: "left" }}>
        <strong>≈ 40 min</strong>
      </td>
    </tr>
  </tbody>
</Table>

> ★ **Optional but highly recommended.**\
> Ship steps 1‑3 to start getting replays + heat‑maps, then add steps 4‑5 for deeper analytics.

***

## Key Benefits After Setup

* **Session Replay + Heat‑maps** – watch every UX moment in context.
* **Advanced Product Analytics** – funnels, retention, feature adoption.
* **AI‑generated Alerts** – journey summaries, rage‑tap & anomaly detection.
* **Engineering Analytics** – client‑side performance tied to real sessions.

***

## 💡 Tips Before You Begin

* Create **separate keys** for *debug* & *production* to keep data clean.
* Use **feature flags** to toggle UXCam in staging builds.
* Lean on **AI helpers** – they auto‑detect sensitive views and propose screen/event names.
* In Logcat look for **“Verification successful”** and **“Session/Video uploaded”** messages to confirm everything is wired up.

Happy shipping! 🎉