---
title: Crash Symbolication
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
> 🚧 Crash Symbolication is only available for iOS SDK 3.6.19 onwards.

# Uploading dSYM Symbols

To ensure proper crash reporting with UXCam, you need to upload dSYM files. Follow the steps below to correctly set up dSYM symbol uploading in your Xcode project.

## Step 1: Download the Upload Symbols Executable

1. Download the "uxcam-upload-symbols" executable from **[this link](http://uxcam.com/mobile/uxcam-upload-dsym.sh)**.
2. Place the executable in your app's root folder.

## Step 2: Set Up dSYM File Generation in Xcode

1. In Xcode, select your project from the **Project Navigator**.
2. Click on the application target.
3. Select the **Build Settings** tab in the Settings editor.
4. Change **DEBUG_INFORMATION_FORMAT** to **DWARF with dSYM File**.

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/482dd7ef5b062815fe8a03040b467b24d27bb79afc9fa1ca38978d34cc183714-Uploading_dSYM_Symbols_-_Google_Docs.jpg",
        null,
        ""
      ],
      "align": "center"
    }
  ]
}
[/block]


5. Set **ENABLE_USER_SCRIPT_SANDBOXING** in your Xcode project settings to **NO**.

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/7bedf23ccfb03f0d83c2bd4407f65f30793f29f1b68abbf12df82dd314607c43-dsym2.jpg",
        null,
        ""
      ],
      "align": "center"
    }
  ]
}
[/block]


## Step 3: Add a Run Script to Build Phase

1. Select the **Build Phase** tab in the Settings editor.
2. Click the **+** icon in the upper left corner of the main panel.
3. Select **New Run Script Phase** from the dropdown.

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/eef86ca61a8d6462cccc22235e161d6a960d64b8545d64a3b0b4dde78e20db71-dsym3.jpg",
        null,
        ""
      ],
      "align": "center"
    }
  ]
}
[/block]


4. In the script box, add the following lines based on your dependency management method:

### For CocoaPods

```c
SCRIPT=$(/usr/bin/find "${SRCROOT}" -name uxcam-upload-symbols.sh | head -n 1)
"${SCRIPT}" "app-key"
```

### For Swift Package Manager (SPM)

```c
SCRIPT=$(/usr/bin/find "${BUILD_DIR%Build/}SourcePackages" -name uxcam-upload-symbols.sh | head -n 1)
"${SCRIPT}" "app-key"
```

## Step 4: Add Input File Path

1. Add the input files path as follows:
   ```c
   ${DWARF_DSYM_FOLDER_PATH}/${DWARF_DSYM_FILE_NAME}/Contents/Resources/DWARF/${TARGET_NAME}
   ```

2. Ensure that the **For install builds only** option is checked.

By following these steps, your dSYM files will be automatically uploaded to UXCam, enabling detailed crash reports and better insights.