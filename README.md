# Twixit plug-in for coding agents

Makes App Store screenshots for the app you're building, with [Twixit](https://twixit.webzler.com) for Mac. The agent:

1. captures the app's key screens;
2. writes a headline and supporting text for each screen, in every language the app supports;
3. matches the app's colours and fonts;
4. previews the set with you;
5. exports every size into `fastlane/screenshots`.

## What's inside

- **MCP server `twixit`:** connects the agent to Twixit. The server is part of the Twixit app; this plug-in only tells the agent how to start it (it finds Twixit in `/Applications`, or wherever it's installed).
- **Skill `app-store-screenshots`:** tells the agent when to use Twixit and to get the workflow from it. The workflow and its guides (capturing screens, writing store copy, matching the app's look) come from the Twixit app, so they update with it; [`guides/`](guides) has a copy to read here.

## Requirements

- Twixit installed from the Mac App Store.
- **Twixit › Settings › Automation › Allow automation** turned on.
- The first time the agent works on a project, Twixit asks you to allow the project folder: choose **Allow…**, then **Allow Access**.

## Install

- **From Twixit (easiest):** open Twixit › Settings › Automation and click **Set Up Xcode Agent…**. Twixit turns on automation and opens Xcode's plug-in installer; click **Install**.
- **One-click link:** open
  `xcode://agent-plugin-clone?repo=https%3A%2F%2Fgithub.com%2Fwebzler%2FTwixit-Xcode-Plugin`
  with Xcode 27 or later running.
- **Manually in Xcode:** Settings › Intelligence › Plug-ins › **Add Plug-in…** › **Add from URL**, and enter
  `https://github.com/webzler/Twixit-Xcode-Plugin`.
- **Other agents** that support plug-ins: add this repository as a plug-in source.

Then ask the agent, for example: *"Make App Store screenshots for this app."*

This repository contains only the plug-in. Twixit itself is available on the Mac App Store.

[Website](https://twixit.webzler.com) · [Support](https://twixit.webzler.com/support/) · [Privacy policy](https://twixit.webzler.com/privacy/)
