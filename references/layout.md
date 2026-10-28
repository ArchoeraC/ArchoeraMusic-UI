# Layout

The app is a desktop listening room: a slim navigation rail, a quiet header, a
full-width canvas, and transport that never leaves.

## 1. Window anatomy

```
┌────────────┬──────────────────────────────────────────────────────────┐
│ logo       │ header 64                                                │
│ 64         │ back · search 280→420 · spacer · weather · account · ⚙   │
│            ├──────────────────────────────────────────────────────────┤
│ sidebar    │ page canvas (scroll owner)                               │
│ 240 / 64   │                                                          │
│            │  home: 24 / 18 / 24 / 24 · max 1400                      │
│            │  library: 24 / 18 / 24 / 10                              │
│            │                                                          │
│            │                                  [docked reserve 82]      │
├────────────┴──────────────────────────────────────────────────────────┤
│ player bar 82 = progress 22 + body 60                                  │
└───────────────────────────────────────────────────────────────────────┘
```

- Sidebar and header are fixed; only the page canvas scrolls.
- Player bar is an overlay (`Positioned`) so it can be docked or floating
  without changing page layout. Docked mode adds `82` bottom padding.
- Dividers are `1 px` at 60% outline; never a 2 px chrome line.
- The full-screen player is a separate route above the shell and covers it.

## 2. Sidebar

| Part | Spec |
|---|---|
| Expanded width | 240 |
| Collapsed width | 64 |
| Width animation | 200 ms easeOutCubic |
| Background | `surfaceAlt` / `surfaceContainerLow` |
| Logo row | 64 high; 30 px logo, radius `size × 0.3` (9) |
| Logo text | 15 / 700 / +0.3, `primary`; occupied width 110 |
| Logo hover | scale 1.05 over 150 ms, tooltip "Back home" |
| Nav padding | 10 horizontal, 8 vertical |
| Group label | 12 / 500 / +0.5, `onSurfaceVariant @ 60%`; 12/12/12/5 |
| Item | 40 high, radius 8, 1.5 vertical gap |
| Item content | 12 left inset, 19 icon, 12 gap, 14 label |
| Selected | `primary @ 10%` fill, primary icon/text, label 600 |
| Selected edge | 3 × 20 px primary bar, radius 2, left |
| Hover | `onSurface @ 5%`; selected has no extra hover fill |
| Collapse footer | 48 high, 20 icon, tooltip |
| Collapsed groups | 12 px spacer instead of group label |
| Collapsed items | icon centered only; labels fully removed |

The logo is a rounded square with a brand glyph, not a wordmark-only lockup.
Clicking it returns home. Only content destinations belong in the sidebar;
search and settings live in the header.

Nav items do not draw their own permanent underline. Either a 3 px left edge
(default) or one shared sliding plate (animated style) — never both.

## 3. Header

- Height `64`, horizontal padding `12`, surface follows page (`surface`).
- Order: back (22 icon) → search → spring space → weather → account → actions.
- Back always returns to a sensible place; if the stack is empty, go home.
- Search: filled field, width `280` collapsed, `420` expanded; expansion `180`
  ms. Debounce suggestions `300` ms; the dropdown is attached directly below
  the field with square top corners so field + panel read as one object.
- Search dropdown: panel surface, radius `0 0 12 12` (top edges square), max
  width follows the field; opens `400` ms, closes `360` ms.
- Account: 30 avatar + name/type; keep one menu for all platforms.
- Weather is optional and off by default. Never add decorative header icons.
- Do not draw window controls on Linux/macOS; on borderless Windows they are a
  separate platform layer.

Responsive: at narrow widths shrink search to `180–220` before hiding labels;
keep back, search and account reachable. Never let the search field push the
account off-screen.

## 4. Page canvas

| Page | Padding (T/R/B/L) | Max width | Notes |
|---|---|---|---|
| Home | 18 / 24 / 24 / 24 | 1400 | scrolls as one column |
| Library | 18 / 24 / 10 / 24 | none | header + divider + list fill |
| Search / collection | 18 / 24 / 24 / 24 | 1400 | header + filters + results |
| Settings | dialog | 960 | own scroll inside dialog |
| Player | 0 + 32 horizontal | none | fixed canvas, no page scroll |

- Page title is `display` (26/34/700/+0.5), subtitle `compact` at 75% variant.
- Home header → 20 → spotlight → 20 → quick actions → 24 → rails.
- Library title row → 14 → action row → 10 → divider → list.
- Do not center a narrow column in a wide window. Use the width for rails,
  grids or a second panel.

## 5. Player bar

Two modes. Pick one for the whole product.

### Docked (default)

- Full window width, `82` total: progress strip `22` + body `60`.
- Background `surfaceBright @ 86%` (solid `surfacePanel` in solid mode),
  1 px top rule, elevation 8. In image mode add `blur(16)`.
- Content padding `12`; safe-area bottom respected.
- Page canvas gets `82` bottom padding.

### Floating

- Starts to the right of the sidebar (`left = 240 + 1`, or `64 + 1` collapsed).
- Margins `24` left/right, `14` bottom; `max-width 960`; centered.
- Radius `30`; shadow `0 8 24 rgba(0,0,0,.35)`; 1 px `primary @ 12%` border.
- Image mode: `surfaceBright @ 70%` + `blur(16)`.
- Does not reserve page height; the canvas runs underneath.

### Bar internals (both)

```
progress line (full width, 22 high, seekable)
[cover 40] [title 14 / subtitle 12 ...]   [prev play next]   [150 time/info] [vol] [queue] [like]
```

- Cover 40, radius 8; hover shows a dark overlay + up chevron (open player).
- Title `titleSmall` (14, 500) + source badge; subtitle 12 variant, one line.
- Center controls are icon buttons; play is not a giant filled circle here.
- Right block: 150 wide time + mini lyric/spectrum (120 × 15) + 72 volume +
  playlist + like. Collapse mini info before collapsing controls.
- Empty/no track: cover placeholder + "Choose something to play"; controls
  disabled but readable.

## 6. Full-screen player route

- Opens with a **500 ms vertical slide** from the bottom; reverse the same.
- Shell behind it scales to `0.95` and fades to `0` with the same curve,
  then unloads to a solid placeholder (memory release).
- Player page is opaque at all times; never let the shell flash through.
- Layout: horizontal padding `32`, top bar `56`, body fills, bottom overlay
  `90` spectrum + progress + controls.
- Body is not a scroll view. Lyrics scroll internally; cover stays fixed.
- Top bar: close (32 down icon), lyrics toggle (26), spacer, sleep timer,
  quality menu, fullscreen.
- Auto-immersive (optional): after pointer idle/leave, chrome fades `300` ms,
  cursor hides; any pointer move restores. Never hide on keyboard activity.

## 7. Home

```
Title 26 / greeting 13
[ spotlight 172 min: cover 96 + title/sub + play/reroll | preview 320 ]
[ shortcut ] [ shortcut ] [ shortcut ] [ shortcut ]    4 cols, 2.3 aspect
Recently played                                      See all
[ song row 68 ] ...
[ rail: 150 × 198 covers ]  (playlists / albums / artists)
```

- Spotlight radius 16, gradient `primary 26% → primaryContainer 42% → high
  surface` at `0 / 0.55 / 1`, border `primary 20%`, padding `24 × 20`.
- Preview column only at width ≥ 760; otherwise hide it and keep the hero copy.
- Quick actions: 4 columns, gap 12, aspect 2.3, card radius 12, padding 14,
  42 icon tile radius 11, title 14/600, subtitle 11.5.
- Rails: card width 150, height 198, gap 14, horizontal padding 20.
- Section title 17/600; subtitle 12 at 50%; "More" text 13 + 18 chevron.
- Loading: centered 28 spinner with 80 vertical padding. Error: icon 44,
  message, retry button. Do not skeleton the whole page if data is cached.

## 8. Library

- Header: title row (26 + status 13 at 75%), 14 gap, action row.
- Actions: play-all primary 36, circular scan 36, scrape 36, more 36, spring,
  search 190. Keep actions on one line down to ~900; wrap search below after.
- Divider 1 px; list fills remaining height and owns scroll.
- Row height 68, list virtualized; header is not sticky inside the list.
- Empty library: 72 tile radius 20, 34 icon, title 15/600, hint 12.5, one
  primary button (Add folder / Scan now). No illustration.
- Grid: max tile extent 180, aspect 0.78, gap 16, padding 20; cover square,
  title 14/500, subtitle 12 variant.

## 9. Settings dialog

- Dialog centered, width `80%` clamped `640–960`, height `84%` clamped
  `480–660`; radius 16; outline 50%; scrim `#000 @ 50%`.
- Left column `210`: title 20/700, app name 12 at 70%, 18 gap, categories.
- Category item 40 high, radius 8, 3 px primary edge + 10% fill when selected;
  18 icon + 12 gap + 13.5 label.
- Right column padding `24 / 20 / 24 / 16`; search field at top; 14 gap;
  category title 18/700 + subtitle 12 at 70%; 18 gap; scrolling settings.
- Settings content: section label 12.5/600/+0.5, card radius 12 filled
  `surfaceContainerHighest @ 35%` + outline 30%; row padding `14 × 10`.
- Bottom actions in simple dialogs: right aligned, 10 gap, both buttons easy to
  hit. Content scrolls so actions never leave the window.

## 10. Breakpoints and responsive rules

Use content width, not device labels.

| Width | Behavior |
|---|---|
| 1400 | home content cap; rails use available width |
| 1100 | collapse sidebar to 64 by default; keep an explicit expand control |
| 900 | player bar right block drops mini lyric/spectrum first, then volume |
| 820 | full-screen split layout may become single column; poster/classic drop to split |
| 760 | hide spotlight preview column; quick actions 2 × 2 |
| 680 | library action row wraps search below; row drops album column |
| 560 | hide page subtitle if space is tight; player bar time block shrinks |
| 430 | icon-only nav; player centered controls only; no floating bar |

Rules:

- Collapse labels before shrinking hit targets. Icons get tooltips.
- The floating bar is a desktop affordance; at narrow widths use docked.
- In narrow player layouts, hide the lyric column before clipping the cover.
  The cover and transport take priority; offer a lyric overlay only if the
  product has a secondary control for it.
- Ultrawide: use the width for more grid columns and a wider player body; cap
  reading text at ~72 characters; do not stretch the player cover past 520
  (split) or 460 (classic).

## 11. Scrolling

- One vertical scroll owner per page. Do not nest page scroll + list scroll.
- Sidebar and header never scroll. Settings nav fixed; content scrolls.
- Library: header fixed, list scrolls, player bar overlays/reserves space.
- Lyrics scroll internally; the player itself does not.
- Restore scroll on back navigation. Queue panel is an overlay, not a route.
- Scrollbar: 6 px, rounded, `onSurface @ 22%` dark / `28%` light, always visible
  on desktop; transparent track.

## 12. Window background

- Default: solid `surface` (near-black dark / soft gray light).
- Image mode: one fixed cover layer (`cover`, optionally blurred `0–80`,
  scaled `1–2`, dim `30–90%`), with the content surfaces translucent above.
- Player page always has its own solid background; it never inherits image mode.
- Effects are optional layers; the layout must be identical with them disabled.
