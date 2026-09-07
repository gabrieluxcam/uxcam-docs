---
title: SDK Logs
deprecated: false
hidden: false
metadata:
  robots: index
---
# SDK Logs

Control what the UXCam Web SDK prints to the browser console.

| Mode | How to enable | What you see |
|---|---|---|
| **Default** | No option needed | A few informational lines and the warnings you must act on. |
| **Silent** | `silent: true` | Nothing. |

---

## Default

Out of the box the SDK prints only what an integrator needs:

- `[UXCam] connected successfully` once recording starts
- Warnings that need action, such as a missing App Key or a cross-origin iframe without `recordCrossOriginIframes`

All lines are prefixed with `[UXCam]`.

---

## Silent mode

Use this when you want a clean console for your visitors or your own developers. Set `silent` to `true` in the options object at the end of the SDK snippet:

```javascript
})('Your_App_Key', {
    silent: true
});
```

Silent mode only affects the browser console. Session recording continues as normal.

> **Note:** With `silent: true` the `[UXCam] connected successfully` line is not printed. Verify your installation from the UXCam dashboard instead.

---

## Configuration Reference

| Option | Type | Default | Description |
|---|---|---|---|
| `silent` | `boolean` | `false` | Suppress all SDK console output |

---

## FAQ

**Will silent mode hide errors that break recording?**
The console stays empty, but the SDK still reports problems to UXCam internally. If sessions are missing, turn `silent` off and check the console again.

**Does this option affect what is recorded?**
No. It only changes console output.

## Support

For questions or support, reach out to us at [team@uxcam.com](mailto:team@uxcam.com).
