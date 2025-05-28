---
title: "🤖\_AI‑Powered Bootstrap Setup with Codex\_CLI"
excerpt: Implement the SDK and record your first session.
deprecated: false
hidden: true
metadata:
  robots: index
---
UXCam’s **Bootstrap Setup** can be executed automatically by OpenAI Codex so you get from **zero** to **first session recorded** in minutes.

***

## 1 · Prerequisites

| Requirement      | Notes                                                                                        |
| ---------------- | -------------------------------------------------------------------------------------------- |
| OpenAI API key   | Create one in your OpenAI dashboard and export it: \<br>\`export OPENAI\_API\_KEY="sk-..."\` |
| Node ≥ 18        | Codex CLI is distributed via **npm**.                                                        |
| Git working copy | Run the commands from the root of your Android repo.                                         |

Install Codex CLI globally:

```shell bash
npm install -g @openai/codex-cli
```

***

<br />

## 2 · One‑liner (hands‑off)

<br />

```Text bash
codex -m o3 \
      --context docs/uxcam/01-bootstrap-setup.md \
      "UXCam Bootstrap Setup skip-plan apply-all"
```

* Codex reads the **Bootstrap Setup** guide as context.
  * `skip-plan apply-all` tells it to **apply** the steps without asking.
    * The CLI prints every file change so you can review before committing.
      <br />
      ***
      <br />
      <br />

## 3 · Interactive mode (recommended for first‑timers)

<br />

```
```

<br />

Codex will:

1. Show a **plan** (diff of each file it intends to touch).
   2. Ask **yes/no** before applying.
      3. Re‑run `./gradlew assembleDebug` to confirm the build passes.
         <br />
         ***
         <br />
         <br />

## 4 · What happens under the hood?

| File                                    | Patch                                       |
| --------------------------------------- | ------------------------------------------- |
| `settings.gradle(.kts)`                 | Adds UXCam Maven repo                       |
| `app/build.gradle`                      | Adds `implementation "com.uxcam:uxcam:3.+"` |
| `local.properties`                      | Creates or updates `UXCAM_KEY=`             |
| `app/build.gradle`                      | Exposes key via `buildConfigField`          |
| `Application.kt` (or launcher Activity) | Inserts SDK init snippet                    |

***

<br />

## 5 · Embedded reference

<br />

Below is the exact **01-bootstrap-setup.md** used as context.\
Feel free to edit it and re‑run Codex—the CLI will follow your customisation.

<br />

```
```

<br />

> **Goal:** record your first UXCam session in under 10 minutes.
>
> <br />
>
> ***
>
> <br />
>
> <br />
>
> ## 1 · Add the SDK dependency
>
> <br />
>
> <br />
>
> ### `settings.gradle(.kts)`
>
> <br />
>
> ```
> ```
>
> <br />

### `app/build.gradle` / `build.gradle.kts`

<br />

```
```

<br />

***

<br />

<br />

## 2 · Store your key

<br />

Add the line below to **`local.properties`** (already *.gitignored*):

<br />

```
```

<br />

***

<br />

<br />

## 3 · Expose the key to code

<br />

<details>
  <summary>Kotlin DSL</summary>

  ```kotlin
  val uxcamKey: String = project.findProperty("UXCAM_KEY") as? String ?: ""
  android {
      defaultConfig {
          buildConfigField("String", "UXCAM_KEY", ""$uxcamKey"")
      }
  }
  ```
</details>

<details>
  <summary>Groovy DSL</summary>

  ```groovy
  def uxcamKey = project.findProperty("UXCAM_KEY") ?: ""
  android {
      defaultConfig {
          buildConfigField "String", "UXCAM_KEY", ""${uxcamKey}""
      }
  }
  ```
</details>

***

<br />

## 4 · Initialise the SDK

<br />

**Preferred** – inside your `Application` class:

<br />

```
```

<br />

**Fallback** – at the very top of the launcher `Activity`’s `onCreate()` (before `setContentView()`).

<br />

***

<br />

<br />

## 5 · Verify

<br />

1. Build & run the *debug* APK.
   2. In **Logcat** search for `UXCam` – look for **“Verification successful”**.
      3. Background the app, wait 1‑2 minutes, then open the UXCam Dashboard → **Sessions** to see your first replay.
         <br />
         ***
         <br />
         <br />

### 🤖 One‑liner for Codex CLI

<br />

```
```

<br />

*(Runs the entire bootstrap phase without manual prompts.)*

<br />

***

<br />

<br />

➡️ Next step: [02‑PII‑Occlusion →](02-pii-occlusion.md)

<br />

```
```

<br />

***

<br />

<br />

### Next steps

<br />

* **Codex PII Occlusion:**```bash
  codex -m o3 --context docs/uxcam/02-pii-occlusion.md "Detect and occlude PII views"
  ```
  * **Codex Screen Tagging:**```bash
    codex -m o3 --context docs/uxcam/03-screen-tagging.md "Tag screens plan-only"
    ```

<br />

Happy shipping! 🚀