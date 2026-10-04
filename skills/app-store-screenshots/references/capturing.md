# Capturing the app's screens

Good captures make good screenshots. Aim for a clean status bar, realistic content, and one clear idea per screen.

## iPhone and iPad (Simulator)

1. **Pick the device:** the largest current iPhone ("Pro Max", 6.9", 1320 × 2868) and the 13" iPad Pro. Twixit fits other sizes too, but these match the store's aspect exactly.

2. **Build and run** the app on that Simulator. Use the editor's build and run tools when you have them, otherwise `xcodebuild` and `xcrun simctl`.

3. **Clean status bar:**

   ```sh
   xcrun simctl status_bar booted override --time 9:41 --dataNetwork wifi --wifiBars 3 --cellularMode active --cellularBars 4 --batteryState charged --batteryLevel 100
   ```

   Clear it afterwards with `xcrun simctl status_bar booted clear`.

4. **Realistic content.** Empty lists sell nothing. In order of preference:
   - the app's own demo or sample data: a launch argument, a debug menu, or preview data used by the SwiftUI previews;
   - adding a few items through the UI;
   - as a last resort, a temporary launch argument that loads sample data. Ask the user before changing code, and remove it afterwards.

5. **Open each screen.** Tap and scroll with the editor's device interaction tools if you have them. If the app supports deep links or launch arguments, use those (`xcrun simctl openurl booted <url>`). For many screens or repeated runs, a UI test that navigates and saves screenshots is the most reliable way. Only add one with the user's agreement.

6. **Capture:**

   ```sh
   xcrun simctl io booted screenshot --type=png "<project>/fastlane/captures/iphone/01-<screen>.png"
   ```

7. **Appearance:** use the app's best-looking appearance (usually light, unless the app is dark-first). Use the same one for the whole set.

8. **Other languages:** if the app is localized, recapture in each language, so the screens inside the device are localized too:

   ```sh
   xcrun simctl launch booted <bundle id> -AppleLanguages "(ja)" -AppleLocale ja_JP
   ```

   If that's too much, using the primary language's captures for every language is acceptable. Mention it to the user.

## Mac apps

1. Run the app, size the window to a pleasant 16:10-ish size, and fill it with realistic content.
2. Capture just the window:
   - `screencapture -o -l <window id> <file>.png` (the window id comes from `osascript` or the editor's tools);
   - or `screencapture -o -w <file>.png`, which needs the user to click the window.

   Twixit trims the transparent shadow around window captures automatically.
3. For a Mac set, use `create_set` with `device: "mac"`. Twixit puts the window on a MacBook screen.

## Order and count

- Use 3 to 6 screenshots. Put the core action first, then depth (features), then delight (polish, personalization, widgets).
- Name captures in order (`01-…`, `02-…`) and pass them to `add_screenshots` in that order.
