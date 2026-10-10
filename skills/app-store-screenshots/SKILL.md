---
name: app-store-screenshots
description: Make App Store screenshots for the app in this project with Twixit — capture the app's key screens, write a headline and supporting text for each, match the app's colours and fonts, preview, and export every size and language (for example into fastlane/screenshots); and the App Store banners (product page header, search results, universal). Use when the user asks for App Store screenshots, store images, marketing screenshots, screenshot copy, or App Store header or banner images.
allowed-tools: mcp__plugin_twixit_twixit__get_info, mcp__plugin_twixit_twixit__request_folder_access, mcp__plugin_twixit_twixit__manage_project, mcp__plugin_twixit_twixit__edit_set, mcp__plugin_twixit_twixit__preview_set, mcp__plugin_twixit_twixit__export_set
---

# App Store screenshots with Twixit

Twixit (a Mac app) turns raw captures into App Store screenshots: a 3D device showing the capture, a headline, supporting text and a background. You drive it through the `twixit` MCP tools. The step-by-step workflow comes from Twixit itself, so it always matches the installed version.

## 1. Get the workflow from Twixit

Call `get_info` with `include: ["guide"]`.

- **The twixit tools aren't available, or the call fails with `app_unavailable`:** Twixit isn't installed or couldn't start. Tell the user to install Twixit from the Mac App Store. Don't try to install anything yourself.
- **`automation_off`:** ask the user to open Twixit › Settings › Automation and turn on **Allow automation**, then try again.
- **`invalid_argument` about `guide`:** this Twixit is older than the workflow it serves. Ask the user to update Twixit from the Mac App Store. Until then, work from the tools' own descriptions and the server's instructions, show the user a preview before exporting, and ask before overwriting files.
- **Otherwise:** the result has `guides.workflow`. Note `granted_folders` and any open `projects` in the same result.

## 2. Follow it

Work through `guides.workflow` step by step. When a step names another guide (capturing, copywriting, or design from the app), fetch it then with `get_info` and `include: ["guide:<name>"]`, for example `["guide:copywriting"]`. Fetch each guide once; they don't change during a session.
