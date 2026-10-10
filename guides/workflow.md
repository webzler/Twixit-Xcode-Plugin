# Making App Store screenshots with Twixit

Twixit turns raw captures into App Store screenshots: a 3D device showing the capture, a headline, supporting text and a background. You drive it through the Twixit tools. Twixit does the rendering; your job is the content and the taste: which screens, what they say, and how they look.

Work through the steps in order. Show the user a preview before exporting, and ask before overwriting existing screenshots. The guides named below come from `get_info` too; fetch each one when its step needs it.

## 1. Ask for the project folder once

If the project's root folder isn't inside one of `granted_folders`, call `request_folder_access` with the **project root** (the folder that contains the `.xcodeproj` or `Package.swift`) and a short reason, such as "To read screenshots and save App Store images for <App>." Twixit shows the user a prompt and waits up to 3 minutes. If the user denies it, carry on with inline images and previews (`edit_set` `screenshots` accept base64 data), and explain that exporting files needs the folder.

Ask for the root, not subfolders: one approval covers captures, the `.twixit` project and `fastlane/`.

## 2. Understand the app

Before capturing anything, read enough of the project to answer these questions. Note the answers; every later step uses them.

- **What it is:** the app's name, category, and the one thing it does best. Check the `Info.plist` display name, the README, any App Store text in `fastlane/metadata/`, and the main views.
- **Platforms:** iPhone, iPad, Mac (look at the targets and their supported destinations). An iPhone app also runs on iPhone Duo, the foldable; make a Duo set only if the user asks for one or the app adapts its layout to the Duo's displays.
- **The 3–6 screens that sell it:** the core action first, then the screens that show depth or delight. Skip settings, onboarding and empty states unless they're the point.
- **Languages:** the languages in `Localizable.xcstrings` or the `*.lproj` folders. The source language is the primary one.
- **Look and voice:** colours, fonts and tone. See the design guide (`get_info` with `include: ["guide:design-from-app"]`).

## 3. Capture the screens

Follow the capturing guide (`get_info` with `include: ["guide:capturing"]`). In short:
- **iPhone or iPad:** build and run in a Simulator that matches the store size (6.9" iPhone, 13" iPad), set a clean status bar, open each screen with realistic sample data, and capture it.
- **iPhone Duo:** capture on an iPhone Duo, or its Simulator if the installed Xcode has one: the outer display (1398 × 2034) for the folded set, the inner display (2853 × 2007) for the open set.
- **Mac:** capture the app's window. On the MacBook, Twixit adds the Mac's menu bar above a window capture by itself (`edit_set` `menu_bar`: `mode`, `app_name`, `menus`; set `menus` to the app's real menu titles).

Save the captures inside the granted project folder, for example `<project>/fastlane/captures/<device>/01-<screen>.png`. They are the inputs; Twixit never changes them.

## 4. Build the set

1. `get_info` with `include: ["starters", "fonts"]` (add `"patterns"` if you want a specific one).
2. `manage_project` with `action: "create"`:
   - `device`: the first device: `iphone`, `duo` (iPhone Duo folded), `duo_open` (iPhone Duo open), `ipad` or `mac`. Make **one project for the app**: it holds a set for each device, all sharing the design, languages and banners (on Twixit Free, one project is all there is).
   - `language`: the primary language.
   - `starter`: the starter whose mood is closest to the app.
   - `path`: `<project>/fastlane/Screenshots.twixit`, so the user can open and refine it later.
3. `edit_set` does the rest, in one call or several. Its sections are applied in this order:
   - `screenshots`: the captures in story order, as paths. Twixit fills screenshot 1, 2, 3… in that order and varies the device angles.
   - `design`: the app's colours, pattern and finish (see the design guide (`get_info` with `include: ["guide:design-from-app"]`)).
   - `copy`: a list with one entry per screenshot, each with `slides: [n]`, `headline` and `supporting`, following the copywriting guide (`get_info` with `include: ["guide:copywriting"]`). Set the fonts once, in any entry, with `headline_style` and `supporting_style`, plus `text_color` if needed. **Always pass `slides`**: without it, the text goes on every screenshot.
4. **Other languages:** add `copy` entries with `locale` for each one. Translate the meaning and keep the lengths within the limits; never machine-copy the English.
5. **Other devices:** in the same project, `edit_set` with `device` (for example `"mac"`) and that device's `screenshots`. The first time, the set starts from the current one: the same number of screenshots, copy and layout, with no captures. If the device has fewer captures, drop the rest in the same call with `remove_screenshots` (numbers), then adjust its copy and poses. Give the same `device` to `preview_set` and `export_set`; `get_info` lists each project's sets.

## 5. Preview and refine

Call `preview_set` (for example `height: 600`) and **look at the image**. Check each screenshot:

- **Text:** readable, contrast strong enough, not clipped, and not covering important parts of the capture. Shorten the copy or set `wrap_headline: true` if it crowds the device.
- **The set as a whole:** consistent, and the first three tell the story on their own. Those are what most people see.
- **Variety:** the device angles vary but aren't wild. Use `edit_set` with `pose` entries: `turn` and `tilt` (within about ±25°), or `auto_vary: true`. For the folded iPhone Duo set, `fold` (0 folded, up to 150) opens it into a V standing on a desk.
- **Spreads (optional):** for a hero, an `edit_set` `spread` entry (`count: 2`) can stretch one device across two screenshots.

Iterate until it's right. Then show the user the preview and ask if they want changes before exporting.

## 6. Export

`export_set` writes Display P3 PNGs, named `01.png`, `02.png`… in screenshot order.

- **For fastlane `deliver`,** with one language, set `folder` to `<project>/fastlane/screenshots/<locale>`. With several languages, set `folder` to `<project>/fastlane/screenshots` and `locales: ["all"]`: Twixit makes one folder per language.
- **One call per device** (`device`). If several devices share those folders, pass `name_prefix` (for example `"iphone-"` and `"ipad-"`) so the files don't overwrite each other.
- **Sizes:** the 6.9" iPhone and 13" iPad sizes are what App Store Connect needs. iPhone Duo has two sets in the same project, `duo` (folded, outer sizes) and `duo_open` (open, inner sizes); export each, and Twixit writes them to `Outer/` and `Inner/`. App Store Connect lists those sizes, but accepts uploads only from later in 2026. Use `all_sizes: true` only if the user asks for every size.

Finish with `manage_project` (`action: "save"`), then tell the user:
- where the files are;
- that the `.twixit` project can be opened in Twixit to fine-tune (`manage_project` with `action: "show_in_editor"` does it for them);
- that every change you made can be undone there.

## 7. App Store banners (optional, Twixit Pro)

App Store Connect also takes a **product page header** (21:9), a **search results** image (3:2), and a **universal** image that works for both (16:9). They show on iOS 27 and later. Make them when the user asks for banners, a header or search results images. They use the same project, so its background and fonts.

1. **Logo:** the app icon. Use the largest PNG in the project's `Assets.xcassets/AppIcon.appiconset`. If the icon is an Icon Composer `.icon` file, ask the user to export a 1024 × 1024 PNG from Icon Composer and tell you where it is. Give it as `logo` (path or base64).
2. `edit_set` with a `banner` section:
   - `text`: `app_name` and a short `tagline` per `locale` (the tagline is the headline in Showcase: about 3–6 words, see the copywriting guide (`get_info` with `include: ["guide:copywriting"]`)).
   - `devices`: a list, front to back (up to 4, any mix of `iphone`, `duo`, `ipad`, `mac`), each with an optional `side`, `size`, `turn`, `tilt`, `finish`, or its own `screenshot`. By default each shows the first screenshot of its device's set. A Mac reads best with `tilt` about 12.
   - `banners`: per banner (`header`, `search_results`, `universal`), its `layout`: `lockup` (logo and name as a centred mark; the default for the header and universal) or `showcase` (the tagline beside the devices; the default for search results, which already show the app's icon and name above it). `show_logo`, `show_name`, `show_tagline` and `show_devices` turn parts on or off; `logo_offset`, `name_offset`, `tagline_offset` and `device_offsets` move them (percent of the banner).
   - `logo_size`, `name_size` and `tagline_size` (percent) make them larger or smaller.
3. `preview_set` with `banners: true` and **look at it**. Everything that matters must sit inside the dashed safe area: some screens crop the rest (on iPhone the header shows only its middle, under the status bar and buttons). The result lists `outside_safe_area` banners; fix those. Devices beside a Lockup header show on iPad and Mac only.
4. `export_set` with `banners: ["all"]` (the ticked ones) or a list, and `locales`, into `<project>/fastlane/banners` for example. Files go to `Banners/[<locale>/]header.png`, `search-results.png`, `universal.png`. On Free this fails with `pro_required`: tell the user banners are part of Twixit Pro; designing and previewing them is free.

Banners are uploaded in App Store Connect's Asset Library (fastlane doesn't upload them yet).

## Good to know

- **Errors** come back as `isError` with an `error` code and a `message`. Read the message: it names the folder, size or id that was wrong.
- **Unknown ids:** look them up with `get_info` (`include`) rather than guessing. Font ids are names like `"Space Grotesk"`.
- **Screenshot numbers** start at 1. A set holds up to 10.
- **Don't** put prices, "#1", rankings, other companies' names or claims the app can't back up in the copy (App Review guideline 2.3).
