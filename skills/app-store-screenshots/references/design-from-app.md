# Matching the app's look

The screenshots should feel like the app: its colours, its type, its mood. Gather the evidence first, then map it to Twixit's options.

## 1. Find the app's colours

Look, in this order:

1. **The accent colour:** `Assets.xcassets/AccentColor.colorset/Contents.json` (the `components` are sRGB or Display P3, from 0–1 or 0–255; convert them to `#rrggbb`).
2. **Other named colours:** `*.colorset` folders in the asset catalogs ("Brand", "Primary", "Background"…).
3. **The app icon:** `AppIcon.appiconset` or an `.icon` file. Look at the icon image and note its two or three dominant colours.
4. **Code:** `.tint(…)`, `.accentColor(…)`, `Color("…")`, `Color(red:green:blue:)`, `UIColor(…)`, and theme or style files.
5. **The captures themselves:** the main background and highlight colours of the screens.

From this, choose:
- one **brand colour** (usually the accent colour);
- whether the app is **light** or **dark** at heart;
- a **mood**: calm, energetic, playful, premium, technical or natural.

## 2. Map the colours to the background

`set_design` takes `color_one` and `color_two` (a gradient), plus `angle`.

- **Brand-led (the default):**
  - `color_one`: the brand colour.
  - `color_two`: the same hue, 15–25% darker for a deep look, or lighter for an airy one.
  - `angle`: 135–170.
- **Soft and light** (calm, productivity, health): a very light tint of the brand colour (about 8–15% saturation), falling to a slightly deeper tint. Use near-black text.
- **Dark and premium** (pro, media, finance, dark-first apps): a near-black navy or charcoal, falling to a deep shade of the brand colour. Use white text.
- **Playful** (kids, games, social): two analogous bright hues, such as the brand colour and its neighbour about 30° away on the colour wheel.

**Contrast:** text must stay readable over the whole background. White on dark or saturated backgrounds; `#111418`-ish on light ones. Aim for a contrast ratio of at least 4.5:1, and check the preview.

**Separation:** the device must stand out. If the app's screens are mostly white, avoid a white background; use a tint.

### A background picture

If the app has strong imagery (a hero photo, an illustration or texture in its asset catalog or marketing folder), or the user asks for one, put it behind every screenshot with `set_design`:

- `background_picture`: `{ "path": "<absolute path inside the project>" }` (or `{ "data": "<base64>", "name": "…" }`). It fills each screenshot (a spread's whole width) and replaces the gradient.
- `background_dim` (0–80) and `background_blur` (0–40): busy or bright pictures need some of both so the headline stays readable; start around dim 25, blur 8, and check `preview_set`.
- Use `"pattern": "none"` with a picture unless the user wants both.
- `remove_background_picture: true` goes back to the gradient.

Only use pictures from the app's own project or ones the user gave you.

## 3. Pattern

`pattern` and `pattern_strength` (0–100). Keep it subtle (15–30) so it adds texture without competing with the screen. See `list_patterns` for the ids. Some ideas:
- **Calm or productivity:** `dots`, `grid`, `contours`, `waves`.
- **Energetic or social:** `confetti`, `bubbles`, `sunrise`.
- **Technical or dev:** `grid`, `isometric`, `circuit`, `hexagons`.
- **Natural or outdoors:** `mountains`, `dunes`, `petals`, `terrazzo`.
- **Premium:** `none`, or a very faint geometric pattern.

## 4. Fonts

First find the app's type:
- `Font.custom("…")` or `UIFont(name:)` in the code;
- `UIAppFonts` in `Info.plist`;
- the font files in the project.

Then choose, in this order:

1. **Twixit's own copy of the font**, if `list_fonts` has it.
2. **The app's font installed on this Mac:** `list_fonts` with `source: "mac"` lists every installed family by name; use the name as the font id. If the app bundles its font but it isn't installed, tell the user they can install it (double-click the font file) to use it.
3. **The nearest Twixit font** from the table below. These look the same on every Mac, so prefer them when the project will be shared.

A Mac font is saved by name: on a Mac without it, the text shows in the system font.

| The app feels… | Headline | Supporting |
|---|---|---|
| System and neutral (San Francisco) | Inter 800 | Inter 500 |
| Friendly and rounded | Nunito 800 or Quicksand 700 | Nunito 600 |
| Geometric and modern | Outfit 800, Sora 800 or Poppins 700 | the same family, 500 |
| Technical or developer | Space Grotesk 700 | Inter 500 |
| Editorial or premium | Fraunces 700 or Playfair Display 700 | Work Sans 500 or Lora 400 |
| Bold and loud (games, sports) | Archivo Black 400 or Bebas Neue 400 | Inter 600 |

Set them with `set_copy`: `headline_style: {font, weight, size}` and `supporting_style`. Sizes are on a 1000-wide screenshot. A headline of 70–84 suits 2 lines of about 15 characters each; supporting text of 32–40.

## 5. Device finish

`finish` (see `list_finishes`): choose the device colour that sits best on the background. Silver or light finishes suit dark backgrounds; black or dark finishes suit light ones. A finish that echoes the brand colour also works.

## 6. Starting from a starter

If a starter in `list_starters` already matches the mood, pass it to `create_set` and then override just the colours with `set_design`. You get its tuned layout, shadow and fonts, with the app's colours on top.
