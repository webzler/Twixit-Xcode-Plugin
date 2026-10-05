---
name: app-store-screenshots
description: Make App Store screenshots for the app in this project with Twixit — capture the app's key screens, write a headline and supporting text for each, match the app's colours and fonts, preview, and export every size and language (for example into fastlane/screenshots). Use when the user asks for App Store screenshots, store images, marketing screenshots, or screenshot copy.
allowed-tools: mcp__plugin_twixit_twixit__get_info, mcp__plugin_twixit_twixit__request_folder_access, mcp__plugin_twixit_twixit__manage_project, mcp__plugin_twixit_twixit__edit_set, mcp__plugin_twixit_twixit__preview_set, mcp__plugin_twixit_twixit__export_set
---

# App Store screenshots with Twixit

Twixit (a Mac app) turns raw captures into App Store screenshots: a 3D device showing the capture, a headline, supporting text and a background. You drive it through the `twixit` MCP tools. Twixit does the rendering; your job is the content and the taste: which screens, what they say, and how they look.

Work through the steps in order. Show the user a preview before exporting, and ask before overwriting existing screenshots.

## 1. Check Twixit is ready

Call `get_info`.

- **The twixit tools aren't available, or the call fails with `app_unavailable`:** Twixit isn't installed or couldn't start. Tell the user to install Twixit from the Mac App Store. Don't try to install anything yourself.
- **`automation_off`:** ask the user to open Twixit › Settings › Automation and turn on **Allow automation**, then try again.
- **Otherwise:** note `granted_folders` and any open `projects`.

## 2. Ask for the project folder once

If the project's root folder isn't inside one of `granted_folders`, call `request_folder_access` with the **project root** (the folder that contains the `.xcodeproj` or `Package.swift`) and a short reason, such as "To read screenshots and save App Store images for <App>." Twixit shows the user a prompt and waits up to 3 minutes. If the user denies it, carry on with inline images and previews (step 4 accepts base64 data), and explain that exporting files needs the folder.

Ask for the root, not subfolders: one approval covers captures, the `.twixit` project and `fastlane/`.

## 3. Understand the app

Before capturing anything, read enough of the project to answer these questions. Note the answers; every later step uses them.

- **What it is:** the app's name, category, and the one thing it does best. Check the `Info.plist` display name, the README, any App Store text in `fastlane/metadata/`, and the main views.
- **Platforms:** iPhone, iPad, Mac (look at the targets and their supported destinations).
- **The 3–6 screens that sell it:** the core action first, then the screens that show depth or delight. Skip settings, onboarding and empty states unless they're the point.
- **Languages:** the languages in `Localizable.xcstrings` or the `*.lproj` folders. The source language is the primary one.
- **Look and voice:** colours, fonts and tone. See [design-from-app.md](references/design-from-app.md).

## 4. Capture the screens

Follow [capturing.md](references/capturing.md). In short:
- **iPhone or iPad:** build and run in a Simulator that matches the store size (6.9" iPhone, 13" iPad), set a clean status bar, open each screen with realistic sample data, and capture it.
- **Mac:** capture the app's window.

Save the captures inside the granted project folder, for example `<project>/fastlane/captures/<device>/01-<screen>.png`. They are the inputs; Twixit never changes them.

## 5. Build the set

1. `get_info` with `include: ["starters", "fonts"]` (add `"patterns"` if you want a specific one).
2. `manage_project` with `action: "create"`:
   - `device`: `iphone`, `ipad` or `mac`. Use one set per device.
   - `language`: the primary language.
   - `starter`: the starter whose mood is closest to the app.
   - `path`: `<project>/fastlane/Screenshots.twixit`, so the user can open and refine it later.
3. `edit_set` does the rest, in one call or several. Its sections are applied in this order:
   - `screenshots`: the captures in story order, as paths. Twixit fills screenshot 1, 2, 3… in that order and varies the device angles.
   - `design`: the app's colours, pattern and finish (see [design-from-app.md](references/design-from-app.md)).
   - `copy`: a list with one entry per screenshot, each with `slides: [n]`, `headline` and `supporting`, following [copywriting.md](references/copywriting.md). Set the fonts once, in any entry, with `headline_style` and `supporting_style`, plus `text_color` if needed. **Always pass `slides`**: without it, the text goes on every screenshot.
4. **Other languages:** add `copy` entries with `locale` for each one. Translate the meaning and keep the lengths within the limits; never machine-copy the English.

## 6. Preview and refine

Call `preview_set` (for example `height: 600`) and **look at the image**. Check each screenshot:

- **Text:** readable, contrast strong enough, not clipped, and not covering important parts of the capture. Shorten the copy or set `wrap_headline: true` if it crowds the device.
- **The set as a whole:** consistent, and the first three tell the story on their own. Those are what most people see.
- **Variety:** the device angles vary but aren't wild. Use `edit_set` with `pose` entries: `turn` and `tilt` (within about ±25°), or `auto_vary: true`.
- **Spreads (optional):** for a hero, an `edit_set` `spread` entry (`count: 2`) can stretch one device across two screenshots.

Iterate until it's right. Then show the user the preview and ask if they want changes before exporting.

## 7. Export

`export_set` writes Display P3 PNGs, named `01.png`, `02.png`… in screenshot order.

- **For fastlane `deliver`,** with one language, set `folder` to `<project>/fastlane/screenshots/<locale>`. With several languages, set `folder` to `<project>/fastlane/screenshots` and `locales: ["all"]`: Twixit makes one folder per language.
- **If several devices share those folders,** pass `name_prefix` (for example `"iphone-"` and `"ipad-"`) so the files don't overwrite each other.
- **Sizes:** the 6.9" iPhone and 13" iPad sizes are what App Store Connect needs. Use `all_sizes: true` only if the user asks for every size.

Finish with `manage_project` (`action: "save"`), then tell the user:
- where the files are;
- that the `.twixit` project can be opened in Twixit to fine-tune (`manage_project` with `action: "show_in_editor"` does it for them);
- that every change you made can be undone there.

## Good to know

- **Errors** come back as `isError` with an `error` code and a `message`. Read the message: it names the folder, size or id that was wrong.
- **Unknown ids:** look them up with `get_info` (`include`) rather than guessing. Font ids are names like `"Space Grotesk"`.
- **Screenshot numbers** start at 1. A set holds up to 10.
- **Don't** put prices, "#1", rankings, other companies' names or claims the app can't back up in the copy (App Review guideline 2.3).
