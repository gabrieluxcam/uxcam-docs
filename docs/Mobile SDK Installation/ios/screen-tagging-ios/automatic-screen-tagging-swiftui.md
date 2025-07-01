---
title: SwiftUI Automatic Screen Tagging
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: >-
    The document introduces a new feature in SDK v1.0.7 that enables automatic
    screen tagging for SwiftUI apps by capturing view names using the
    `navigationTitle` or `tabItem` methods, eliminating the need for manual
    tagging.
  robots: index
next:
  description: ''
---
## Automatic Screen Tagging for SwiftUI

Meaningful screen names unlock heat‑maps, conversion funnels and journey charts. UXCam can harvest those names straight from your SwiftUI code—no manual calls required.

## How does it work?

In SwiftUI, screens and views are the same, and multiple screens can be presented from one View class. With automatic screen tagging, if the views are using the `navigationTitle` or `tabItem` method on their class, the name is used as the screen name in the UXCam dashboard. This means that even if you haven't used `[uxcamTagScreenName]`([https://developer.uxcam.com/docs/tag-of-screens#tag-screen-name](https://developer.uxcam.com/docs/tag-of-screens#tag-screen-name)), if there is a name in the navigation bar or tab bar, it will be automatically captured as a screen name in the dashboard.

```swift
// This will auto‑tag "Home" on push
struct HomeView: View {
    var body: some View {
        Text("Welcome")
            .navigationTitle("Home")
    }
}
```

To achieve this, please enable the configuration option `configuration.enableAutomaticScreenNameTagging = true` when setting up UXCam to start

```coffeescript
init(){
     UXCamCore.optIntoSchematicRecordings()
     let config = Configuration(appKey: "YOUR APP KEY")
     config.enableAutomaticScreenNameTagging = true
     UXCamSwiftUI.start(with: config)
}
```

# 4 · Manual tags for edge‑cases

Automatic and manual tags **co‑exist**—leave the tagger ON and add manual calls only when needed.

| When to add a manual tag                         | Snippet                                             |
| ------------------------------------------------ | --------------------------------------------------- |
| You need a hidden name (not shown in the UI)     | `.uxcamTagScreenName("Checkout Confirmation")`      |
| A single view hosts many logical screens         | Call inside `switch step { … }`                     |
| Split‑view / Stage Manager creates two windows   | Tag each `WindowGroup` separately in `.onAppear {}` |
| WebView inside SwiftUI scene loads dynamic pages | Use the JS bridge (see WebView Tagging doc)         |

```swift
// Manual tag helper
extension View {
    func uxcamTagScreenName(_ name: String) -> some View {
        onAppear { UXCamCore.tagScreenName(name) }
    }
}
```

<Image align="center" src="https://files.readme.io/b99640f-Screenshot_2024-03-20_at_4.19.14_PM.png" />

# Troubleshooting

| Symptom                          | Cause                                | Fix                                                         |
| -------------------------------- | ------------------------------------ | ----------------------------------------------------------- |
| **0 s** screens                  | Manual + auto tag fire in same frame | Remove extra tag or debounce manual call                    |
| **Root view only** in Navigation | Views lack `navigationTitle`         | Add titles or manual tag in `.onAppear`                     |
| Sheet inherits parent name       | `.sheet{}` has no `navigationTitle`  | Tag inside the sheet’s root view                            |
| Name missing after refactor      | Hard‑coded in modifier               | Update the string or move to constant                       |
| Preview canvas runs SDK          | Previews initialise `@main`          | Skip start when `XCODE_RUNNING_FOR_PREVIEWS` env var is set |

***

#  Edge‑cases to watch

| Edge‑case                                | Impact                        | Mitigation                                                      |
| ---------------------------------------- | ----------------------------- | --------------------------------------------------------------- |
| **TabItem without text** (icon only)     | Falls back to type name       | Use `.accessibilityLabel` or manual tag                         |
| **Dynamic title** (localised at runtime) | Creates multiple names        | Tag with a constant key and translate later in dashboard        |
| **Background push → deep‑link launch**   | First screen before SDK start | Call `UXCamSwiftUI.start()` as early as possible (`@main` init) |
| **Widget‑only flows**                    | Widget window not recorded    | Start SDK in widget process too                                 |

***