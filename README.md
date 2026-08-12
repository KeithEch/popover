# Figma to Claude pipeline test

## Overview
Testing the accuracy of importing a Figma design into Claude. This test was of a single modal popover, that appears over an app canvas and lets a user browse and manage library items. It has two versions sharing one shell:

- **Asset version** — a scrollable list of image assets (thumbnail, filename, pixel dimensions, drag handle),
  with a filter/search field, row selection, and an upload state.
- **Color version** — a scrollable list of colors (swatch, name, hex, delete), split across a **Presets** tab
  and a **Custom** tab, plus a full colour-picker screen for creating/editing a custom colour.

## About the Design Files
The files in this bundle are **design references created in HTML** — prototypes that show intended look and
behaviour. They are not production code to copy directly. The task is to **recreate these designs in the
target codebase's existing environment** (React, Vue, SwiftUI, native, whatever is in use), following its
established component patterns, styling approach, and state conventions. If no environment exists yet,
pick the framework most appropriate for the project and implement the designs there.

`Popover.dc.html` is authored in a component format specific to the design tool it was made in; treat its
markup and inline styles as a spec, not as source to port.

## Fidelity
**High-fidelity.** Every colour, size, padding, radius, font size and line-height below is transcribed from
the source Figma file (`Popover.fig`) and should be reproduced exactly. Do not round values to a 4px/8px
grid — odd numbers (e.g. the 14.19px picker-handle radius, the 1.774px handle ring) are intentional.

Two deliberate deviations from the Figma file, both noted with the client:
1. **Roobert** (the display typeface, SemiBold 24/32) is not web-licensed here and is substituted with
   **General Sans** SemiBold. If the target codebase licenses Roobert, use Roobert.
2. The icon vectors did not decode from the .fig binary, so Cross, Plus, Trash, chevron and the upload
   glyph are hand-drawn SVGs matching the source visually. Replace them with the real icon set if the
   codebase already has one.

---

## Screens / Views

The popover shell is identical across all six states:

**Shell**
- Width `692px`, `border-radius: 8px`, background `rgb(24,24,26)` (`#18181A`)
- Vertical flex column, three bands: Header → Body → Footer
- Drop shadow in the prototype: `0 24px 64px rgba(0,0,0,0.55)` (prototype affordance; the Figma frame has none)

**Header** — `padding: 16px 32px 24px 32px`, `gap: 8px`
- Nav row, height `32px`, `justify-content: space-between`:
  - **Back button** — `70×32`, `padding: 8px 8px 8px 4px`, `gap: 8px`, chevron-left 16×16 + label "Back"
    (Inter SemiBold 14 / 16px line-height / `0.01em` letter-spacing / `rgb(255,255,255)`).
    Chevron stroke is `rgb(102,200,255)` in the asset version, `rgb(255,255,255)` in the colour version.
    **Hidden (opacity 0, non-interactive) in all states except Asset → Add asset and Color → Custom picker.**
  - **Close (X)** — 24×24, stroke `rgb(255,255,255)`, 1.6px
- Title/tabs container — flex column, `gap: 16px`, `padding-bottom: 8px`
  - **Title** — General Sans (Roobert) SemiBold, `24px / 32px`, `rgb(250,248,248)`
  - **Tabs row** — height `28px`; gap `8px` in the asset version, `4px` in the colour version.
    Each tab: `padding: 8px 8px 4px 8px`, **no border radius**, transparent background,
    Inter Medium `14px / 16px`. Inactive: colour `rgb(187,190,199)`, transparent top border.
    Active: colour `rgb(191,107,255)` with a **2px top border only** in `rgb(191,107,255)`.
  - **Subline** (colour version and upload state only) — Inter Regular `14px / 16px`, `rgb(187,190,199)`
  - **Search field** (asset list states only) — `382×32`, radius `4px`, background `rgb(0,0,0)`,
    `box-shadow: inset 0 0 0 1px rgb(64,66,71)`, `padding: 8px`, `gap: 8px`.
    Placeholder "Filter and search" — Inter Regular **italic 12px / 16px**, `rgb(118,118,128)`.

**Body** — `padding: 0 32px`, content column `636px` wide, per-state fixed height (below), scrolls vertically.
Scrollbar is thin, thumb `rgb(255,255,255)`, track transparent, occupying a 24px rail on the right.

**Footer** — `padding: 48px 32px 32px 32px`, `justify-content: space-between`, left button + right button group (`gap: 16px`).
The Add-asset state uses `padding: 32px` and no left button.

---

### 1. Asset version — Default
- **Purpose**: browse the asset library, filter, pick an asset.
- Title: "Creative asset library" · Tabs: "Main library" (active), "In-game items" · Search field shown
- Body height `304px`; rows `gap: 16px`
- Footer: **Delete** (secondary, disabled) left; **Cancel** (secondary), **Add asset +** (secondary + plus icon),
  **Continue** (primary, disabled) right.

**Asset row** — `636×48`, `padding-right: 8px`, `gap: 10px`, `align-items: center`, cursor pointer
- Thumbnail `48×48`, no radius, `background: url(...) center / cover no-repeat` over white
- Name — flex 1, Inter Medium `16px / 16px`, `rgb(187,190,199)`, ellipsis on overflow
- Dimensions — fixed `140px`, same type treatment
- Drag handle — `9×16` container, `display:flex; flex-wrap:wrap; gap:2px; justify-content:center; align-items:center`,
  six `3×3` dots, `border-radius: 8px`, `rgba(255,255,255,0.5)`

### 2. Asset version — Row selected
Same as Default, with:
- The clicked row's background becomes `rgba(144,77,255,0.3)` (token `--transparents-secondary-30`),
  spanning the full 636px row including the handle.
- **Delete** switches to the danger treatment: `rgba(0,0,0,0.5)` fill, `inset 0 0 0 1px rgb(229,0,0)`, white label.
- **Continue** becomes the enabled primary: `rgb(106,244,183)` fill, label `rgb(61,20,122)`.

### 3. Asset version — Add asset (upload)
- Back button visible. Title "Image library".
- Subline: "Lorem ipsum dolor sit amet, consectetur a adipiscing eoot. Suspendisse blandit sapien egaet
  suscipit pretium. Nullam interdum, lectus sot amet pretium interdum, purus leo tempor libero."
  (placeholder copy from the source file — replace with real copy)
- No search field.
- Body height `344px`. Dropzone fills the width: `border-radius: 8px`, background `rgb(48,48,51)`,
  `outline: 2px dashed rgb(129,132,140)` with `outline-offset: -2px`, `padding: 24px 20px`, `gap: 16px`,
  centred column.
  - Icon wrapper `94×94` with `16px` padding, containing a `62×62` image-upload glyph, stroke `rgb(255,255,255)`
  - Copy: "Click to upload PNG, JPEG, or drag and drop here" — Inter Regular `14px / 20px`, `0.01em`,
    centred, `rgb(228,232,242)`
- Footer: no left button; **Cancel**, **Add asset +** (disabled), **Continue** (disabled), right-aligned.

### 4. Color version — Presets
- Title "Color library" · Tabs "Presets" (active), "Custom" · Subline "Click to edit each preset below"
- Body height `240px`
- Footer: **Cancel** (secondary) left. No right buttons.

**Color row** — same 636×48 geometry as the asset row, but:
- Swatch `48×48`, `border-radius: 8px`, solid fill
- Name — flex 1, Inter Medium `16px / 16px`, `rgb(187,190,199)`
- Hex — fixed `140px`, same treatment
- Trash icon `16×16`, stroke `rgb(255,255,255)`, revealed on row hover / selection
- Clicking a row opens the Custom picker for that colour

### 5. Color version — Custom (list)
- Tabs: "Custom" active · Subline "Name your color so you can reference it later across our suite of
  applications. Learn More" ("Learn More" is a link in the source; style it with the app's link colour —
  the file's link blue is `rgb(102,200,255)`)
- Body height `176px`
- Footer, right-aligned: **Cancel**, **Add color +** (secondary + plus), **Save** (primary).
  **Add color +** navigates to the Custom picker.

### 6. Color version — Custom picker
- Back button visible. Same title/tabs/subline as Custom list.
- Body height `270px`. Centred `454px` column, `gap: 24px`:
  - Picker group `360×214`, `gap: 16px`
    - Saturation/brightness field `320×214`, `border-radius: 4px`, `box-shadow: inset 0 0 0 1px rgb(0,0,0)`,
      overflow hidden, filled with the gradient bitmap (`assets/121a43283fa65368.png`; in production, render
      this as a live SL gradient driven by the selected hue).
      Handle: `16×16` at `left: 278px, top: 30px`, `border-radius: 14.193548202514648px`,
      `box-shadow: inset 0 0 0 1.774px rgb(250,248,248)`.
    - Hue rail `24px` wide, full height, `border-radius: 4px`, gradient:
      `linear-gradient(180deg, rgb(255,0,0) 0%, rgb(188,0,23) 13.78%, rgb(131,0,255) 25.6%, rgb(5,0,255) 38.23%,
      rgb(0,224,255) 54.13%, rgb(0,255,10) 70.43%, rgb(250,255,0) 85.5%, rgb(255,0,0) 100%)`.
      Handle: `32×8`, `border-radius: 4px`, `rgb(255,255,255)`, offset `left: -4px`.
  - Two fields, `454×32`, `gap: 16px`, each flex 1, styled exactly like the search field.
    Placeholders: "Name your color - optional" and "#ff0000".
- Footer, right-aligned: **Cancel** (returns to Custom list), **Save** (primary).

---

## Components

### Buttons (all `min-width: 80px`, `height: 32px`, `border-radius: 4px`, `padding: 8px 16px`, centred)

| Variant | Fill | Border | Label |
|---|---|---|---|
| Primary (default) | `rgb(106,244,183)` | none | Inter Medium 16/16, `rgb(61,20,122)` |
| Primary (disabled) | `rgb(118,118,128)` | none | Inter Medium 16/16, `rgb(177,177,191)` |
| Secondary (default) | `rgba(0,0,0,0.5)` | `inset 0 0 0 1px rgb(170,170,170)` | Inter Medium 14/16, `rgb(255,255,255)` |
| Secondary (disabled) | `rgba(0,0,0,0.5)` | `inset 0 0 0 2px rgb(64,66,71)` | Inter Medium 14/16, `rgb(129,132,140)` |
| Secondary danger | `rgba(0,0,0,0.5)` | `inset 0 0 0 1px rgb(229,0,0)` | Inter Medium 14/16, `rgb(255,255,255)` |

Secondary-with-icon variants use `gap: 4px` (plain secondary uses `gap: 8px`) and append a `16px` plus glyph
whose stroke matches the label colour. Labels are `max-width: 160px`, `white-space: nowrap`, ellipsis on overflow.

### Field
`height: 32px`, `border-radius: 4px`, background `rgb(0,0,0)`, `inset 0 0 0 1px rgb(64,66,71)`,
`padding: 8px`, `gap: 8px`. Placeholder: Inter Regular italic `12px / 16px`, `rgb(118,118,128)`.
(The base component in Figma is light-on-white; every instance in this design overrides it to the dark treatment above.)

### Tab (ghost button)
See Header above. Square corners, 2px top border on the active tab only.

---

## Interactions & Behavior

- **Version toggle / state chips** at the top of the prototype canvas are a **demo harness only** — they exist
  so a reviewer can jump between states. Do not ship them.
- **Tab click** switches the list beneath. In the colour version, "Presets" and "Custom" are two distinct
  datasets; the Custom tab stays visually active while the picker screen is open.
- **Asset row click** toggles selection. Selecting enables Delete (danger) and Continue (primary);
  deselecting returns the footer to the disabled default.
- **Color row click** opens the Custom picker seeded with that colour.
- **"Add asset +"** → upload state. **"Add color +"** (Custom tab) → Custom picker.
- **Back** returns from Add asset → Asset default, and from Custom picker → Custom list.
- **Cancel** in the asset states clears selection; in the colour states it steps back one screen.
- **Trash** on a colour row appears on hover (and on the selected row); it deletes that colour.
- **Drag handle** on asset rows implies reorderable rows — not wired in the prototype.
- Lists scroll vertically within the fixed body height; the popover itself does not scroll.
- The source file defines no motion. Use the codebase's standard modal enter/exit and hover transitions.
- Not specified as responsive: the popover is fixed at 692px. Decide mobile behaviour with the designer.

## State Management

```
version:     'asset' | 'color'            // which library
assetState:  'default' | 'selected' | 'add'
colorState:  'presets' | 'custom' | 'picker'
assetTab:    0 | 1                        // Main library | In-game items
selected:    number | null                // index of selected row
hover:       number | null                // hovered row, drives trash visibility
```

Data needs: a list of assets (`{ name, width, height, url }`) and two lists of colours
(`{ name, hex }`) for presets and custom. The picker needs the working colour (hue + saturation/brightness
point) and an optional name. Saving a custom colour appends to the custom list.

## Design Tokens

Extracted from the file's Figma Variables (32 colour variables, one collection). Full CSS in
`components/fig-tokens.css`.

**Used in this design**

| Token | Value |
|---|---|
| `--neutrals-off-black` | `rgb(24,24,26)` — popover surface |
| `--neutrals-dark` | `rgb(48,48,51)` — dropzone fill |
| `--neutrals-dark-grey` | `rgb(64,66,71)` — field / disabled borders |
| `--neutrals-medium-grey` | `rgb(129,132,140)` — disabled label, dashed outline |
| `--neutrals-light-grey` | `rgb(187,190,199)` — row text, subline |
| `--neutrals-off-white` | `rgb(228,232,242)` — upload copy |
| `--neutrals-white` | `rgb(255,255,255)` — icons, primary labels |
| `--base-grey-light` | `rgb(170,170,170)` — secondary button border |
| `--basics-grey-medium-2` | `rgb(118,118,128)` — placeholder text, disabled primary fill |
| `--basics-grey-light` | `rgb(177,177,191)` — disabled primary label |
| `--legacy-purple` | `rgb(191,107,255)` — active tab |
| `--secondary-purple` | `rgb(144,77,255)` |
| `--transparents-secondary-30` | `rgba(144,77,255,0.3)` — selected row |
| `--transparents-black-50` | `rgba(0,0,0,0.5)` — secondary button fill |
| `--transparents-white-50` | `rgba(255,255,255,0.5)` — drag-handle dots |
| `--primary-green` | `rgb(106,244,183)` — primary button |
| `--tertiary-purple` | `rgb(61,20,122)` — primary button label |
| `--legacy-blue` | `rgb(102,200,255)` — back chevron (asset), links |
| `--semantic-danger-red` | `rgb(229,0,0)` — delete border |
| — | `rgb(250,248,248)` — title colour (not tokenised in the file) |
| — | `#5C6166` — prototype page background (canvas only, not part of the component) |

**Spacing used**: 2, 4, 8, 10, 16, 20, 24, 32, 48 px. Header `16/32/24/32`; body `0/32`; footer `48/32/32/32`.

**Radii**: 0 (tabs, asset thumbnails), 4 (buttons, fields, picker field, hue rail), 8 (popover, colour swatches,
dropzone, handle dots), 14.193548202514648 (picker handle).

**Typography**
| Role | Font | Size / line-height | Weight | Colour |
|---|---|---|---|---|
| Title | Roobert (sub: General Sans) | 24 / 32 | SemiBold 600 | `rgb(250,248,248)` |
| Row text, primary button | Inter | 16 / 16 | Medium 500 | `rgb(187,190,199)` |
| Secondary/tab labels | Inter | 14 / 16 | Medium 500 | varies |
| Back label | Inter | 14 / 16 | SemiBold 600, `0.01em` | `rgb(255,255,255)` |
| Subline, upload copy | Inter | 14 / 16 (upload: 14 / 20, `0.01em`) | Regular 400 | `rgb(187,190,199)` / `rgb(228,232,242)` |
| Field placeholder | Inter | 12 / 16, italic | Regular 400 | `rgb(118,118,128)` |

**Shadows / borders**: the file uses `box-shadow: inset 0 0 0 Npx <color>` rather than `border` for every
button and field outline, so borders sit inside the box and don't affect layout. Reproduce that behaviour.

## Assets
All bitmaps are extracted verbatim from `Popover.fig` and live in `assets/`:

- `d1ebce84b61c4484.png` — "CC-logo" artwork, used for the first three asset rows in the source
- `6cb04604a007a3a1.jpg` — "Rooster-5685" artwork
- `f57cee3cd95a89b5.png` — "KeyartSlice_chicken" artwork
- `121a43283fa65368.png` — the saturation/brightness gradient behind the colour picker
- `arrow-left.svg` — the back chevron as exported from the file

These are sample content. In production the asset thumbnails come from the user's uploaded library, and the
picker gradient should be generated (CSS gradients or canvas), not shipped as a PNG.

Icons (Cross, Plus, Trash, chevron-left, image-upload) are hand-drawn SVGs in the prototype because the
originals did not decode from the .fig — swap in the codebase's icon set, sized 16px (24px for the close X,
62px for the upload glyph).

## Files
- `Popover.dc.html` — the full prototype, all six states, with the demo state-switcher at the top
- `components/fig-tokens.css` — all 32 Figma colour variables as CSS custom properties
- `styles.css` — imports the token sheet
- `support.js` — runtime for the prototype's component format; not part of the design
- `assets/` — the bitmaps listed above
