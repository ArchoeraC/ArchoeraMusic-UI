# Components

Components are quiet by default. Surface changes and one accent are enough; do
not add a second border, shadow or gradient to make a control feel important.

## 1. Buttons

| Size | Height | Label | Icon | Horizontal pad | Use |
|---|---|---|---|---|---|
| Small | 28 | 12.5 / 500 | 15 | 10–12 | row actions, toolbar |
| Medium | 36 | 14 / 600 | 18 | 14 | default, page actions |
| Large | 42 | 15 / 600 | 20 | 16 | primary CTA, empty state |

- Radius `10`; `round` or `circle` may use the pill radius. Circle buttons are
  `36 × 36` (no label).
- Variants:
  - **Primary**: `primary` fill, `onPrimary` label, hover `primary @ 85%`.
  - **Secondary**: `surfaceContainerHighest` fill, `onSurface` label, hover adds
    `onSurface @ 8%`; used for the second action, not every action.
  - **Ghost**: transparent, `onSurfaceVariant` label; hover `onSurface @ 6%`.
    Default for icon-only chrome and tertiary actions.
  - **Danger**: `error @ 12%` fill + error label, or solid error for destructive
    confirmation. Never red for ordinary cancel.
- Loading: replace the leading icon with an 16–18 px spinner, keep width stable,
  disable press. Do not show a spinner and a label that both animate.
- Disabled: 38–45% foreground, no hover, no press. Keep the label readable.
- Focus: 2 px primary ring at 2 px offset. Buttons are keyboard reachable.
- Hover/press changes surface only. No scale, bounce or lift on buttons.

## 2. Inputs and search

- Fill `field`, radius `10`, 1 px transparent border; focus gets a 1 px
  `primary @ 60%` border, not a glow.
- Height: 36 (compact), 40 (page form), 44 (large settings/search). Library
  search is 36; header search is 36 and expands horizontally.
- Text 13.5–14; placeholder 13 at `onSurfaceVariant @ 70%`.
- Horizontal padding 12; leading icon 18 with 8 gap; trailing clear 16.
- Password/reveal, clear and search icons are 18 and have tooltips.
- Header search: `280 → 420` width over 180 ms; suggestions open below after
  300 ms debounce; the panel shares the field width and has square top corners.
- Never use a native underline-only input. Never use placeholder as the label
  in settings; use a persistent label or tile title.
- Error: 1 px `error`, helper text 11.5 with an icon if the cause is unclear.

## 3. Segmented control

- Track: radius `100`, padding `3`, fill `onSurface @ 6%`.
- Item: horizontal 16, vertical 6, radius `100`; label 12.5–13 / 500.
- Selected: `primary @ 12%` fill, primary label; unselected
  `onSurfaceVariant`. Transition 150 ms easeOut.
- Keep 2–4 equal options. For more, use a dropdown.
- No shadow, no individual borders, no check mark inside items.

## 4. Dropdown and menus

- Panel: `surfaceBright` (dark) / panel (light), radius 12, 1 px outline 30%,
  shadow `0 10 28 rgba(0,0,0,.28)`.
- Item height 34–36, padding 12, radius 8, label 13.
- Hover `onSurface @ 6%`; selected has primary text + 16 check.
- Menu opens at the anchor with a 300 ms fade + slight elastic scale; closes
  faster. It must not move the trigger.
- Use a menu for quality, sort, overflow, account and theme cycle. Do not build
  a custom popover for a single toggle.

## 5. Icon buttons

- Icon 18–22; hit area at least 36 × 36, ideally 40 × 40.
- Ghost by default; hover `onSurface @ 6%` circle/rounded square radius 10.
- Active state uses `primary` icon plus a 10% primary fill, not a second glyph.
- Tooltip after 500 ms; tooltip surface `#2A2A2A` dark / `#1F1F1F` light with
  white 11 px text, radius 6, 6/8 padding. Keep tooltips short.
- Heart: 17 px, outline idle; filled `like` when liked; 180 ms scale swap.

## 6. Settings

### Section

- Section label: 12.5 / 600 / +0.5, `onSurfaceVariant @ 80%`, 8 px below.
- Card: fill `surfaceContainerHighest @ 35%`, radius 12, 1 px
  `outline @ 30%`; optional 1 px `outline @ 20%` rules between rows.
- Optional note: 12 px at 60%, 8 px below the card, max two lines.
- Section spacing 18–20; do not wrap each section in another card.

### Tile

```
[34 icon tile] [title 13.5/600        ] [trailing]
               [subtitle 11.5, 2 lines]
```

- Padding `14 × 10`; icon tile 34 radius 9, `primary @ 10%`, icon 18.
- Title `onSurface`; subtitle `onSurfaceVariant @ 75%`, max two lines.
- Trailing: switch, 140 px slider, segmented, dropdown or 36 button. Align
  trailing on the right, vertically centered.
- Disabled: icon 35%, title 38%, subtitle 35%; trailing disabled. Add a note
  explaining why if the reason is not obvious.
- Row click toggles only when the whole row is the control; if trailing is a
  switch, clicking the row may toggle it, but do not double-toggle.

### Dialog layout

- Radius 16, panel surface, outline 50%, scrim 50%; max height 84% of window.
- Header: title 20 / 600, description 12.5 variant, 20/24 padding.
- Body scrolls; footer actions right aligned with 10 gap.
- Primary action on the right; destructive action separated and uses error.
- Do not nest a dialog inside a dialog. Use a sheet/panel for secondary detail.

## 7. Song row

The primary list unit. Height **68** including the 4 px vertical breathing room;
inner surface 68, radius 12.

| Part | Spec |
|---|---|
| Outer padding | 12 horizontal, 4 vertical |
| Index cell | 32 wide; 16 / 700 tabular; hover swaps to 18 play |
| Cover | 48 × 48, radius 8 (user cover radius does not apply here) |
| Cover → text | 12 |
| Title | 16 / 500, one line; playing uses `primary` |
| Subtitle | 12 / 400, one line at `onSurfaceVariant` |
| Title ↔ subtitle | 3 |
| Album | 14, flexible; 16 gap before |
| Duration | 64 wide, centered, 12, tabular |
| Like | 17 icon, 4 left pad, 36 hit |
| End padding | 28 before duration |

States:

- Hover: `onSurface @ 5%` fill, `primary @ 20%` border, 150 ms.
- Playing: `primary @ 14%` fill + `primary @ 40%` border; title primary;
  subtitle/duration primary at 70%; index shows a waveform (playing) or music
  note (paused).
- Batch selected: `primary @ 8%` fill + `primary @ 30%` border; index becomes an
  18 px circular check.
- Search hit: primary text + `primary @ 16%` text background; no size change.
- Disabled/unavailable: 38% opacity, no hover, tooltip with reason.

Source badge: 16 square, radius 4, source color fill, white 10/700 label.
Quality/VIP badges: 10 / 600, 5 × 1.5 padding, radius 4, 1 px matching border.
Lossless amber `#FFB300`. VIP `#E55B5B` outline. Do not put more than two
badges on one subtitle line; collapse the rest into a “+N”.

A lyric snippet may occupy a third line in search results at 11 px italic,
`variant @ 85%`, but the row must stay 68 high; hide the album first.

## 8. Cover card, rails and grid

- Cover square; radius `10` default (user 0/8/12), not 24. Player cover is the
  only large rounded cover.
- Card: cover + 8 gap + title 14/500 (one line) + 2 gap + subtitle 12 variant.
- Hover: 4% surface over the whole card; show a 34 px play circle at the
  bottom-right, 8 margin, fill `primary @ 85%`, icon 20 `onPrimary`; fade 200 ms.
  Do not zoom the cover.
- Source tag: top-left 6, radius 4, 9 px white, 5 × 2 padding.
- Artist variant: circular clip, centered title/subtitle; hover overlay shows a
  user icon and no play button.
- Grid: max extent 180, aspect 0.78, spacing 16, padding 20. With 1400 content
  width this yields 7 columns; do not force a fixed count.
- Rail: default card 140 × 178; home may use 150 × 198; gap 14, horizontal
  padding 20. Height is fixed; vertical overflow is a bug.
- Cover decode: request about 320 px for cards, 96–160 for rows. Never decode a
  2000 px cover for a 48 px row.

## 9. Home blocks

### Spotlight

- Min height 172; radius 16; padding `24 × 20`.
- Gradient: `primary @ 26% → primaryContainer @ 42% → high surface` at stops
  `0 / 0.55 / 1`; 1 px primary 20% border. This is the one strong gradient on
  the page.
- Cover 96, radius 14, shadow soft; 18 gap to copy.
- Overline 11 / 600 uppercase (Latin only); title 20 / 800; subtitle 12.5;
  meta 11.5 at 75%.
- Actions: primary “Play” 28 + secondary “Shuffle” 28; radius `10`, 10 gap.
- Preview column 320 wide at ≥ 760: 3 rows of 34–38, ordinal (22 min) + title
  + artist (right); no thumbnail or duration. Vertical rule at 24 from the copy.
- Without content: same height, centered 15/600 title + 12 hint + one text
  button. Do not collapse the hero to zero height.

### Quick action

- 4 columns, gap 12, aspect 2.3, radius 12, fill `surfaceContainer`,
  hover `onSurface @ 4%`.
- Padding 14; icon tile 42, radius 11, `primary @ 12%`, icon 21 primary; 12 gap.
- Title 14 / 600, subtitle 11.5 at 75%, one line each.
- At ≤ 760: 2 columns; keep the four cards compact at ≤ 480 rather than
  stacking them into a tall list.

### Section header

- Title 17 / 600; optional subtitle 12 at 50%; right “More” 13 + chevron 18.
- Gap 12–14 to the rail; 20–24 between sections.

## 10. Progress and sliders

- Player bar: simplified track 2 px, full-width hit strip 22; hover expands to
  a 4 px track; buffered segment at 30%, played at primary, idle at
  `outline @ 70%`. No thumb until hover/drag.
- Full player: 4 px track, 10 px thumb, times 12 tabular on both sides.
- Hover/drag shows a time bubble above the track; if lyrics exist, include the
  line at that position on a second line, 11 px.
- Volume: 72 wide in the bar, 100–130 in the player; icon toggles mute; no
  separate popover unless space is tight.
- Slider track radius 100; focus ring 2 px primary.

## 11. Dialogs, toast and queue

- **Dialog**: width 480 default (settings 640–960), radius 16, panel, 1 px
  outline 50%, scrim 50%; header 20/600; footer 10 gap. Animate 200 ms fade +
  0.98 scale.
- **Toast**: top center 24; max width 420; margin bottom 8; radius 10; fill
  `surfaceBright`; 1.2 px type-colored border; padding `16 × 10`; icon 16 +
  8 gap; text 12; shadow. Enter/leave 250 ms fade + 16 px vertical slide.
  Max 5, auto-dismiss 3 s, no close button, never blocks pointer.
- **Queue panel**: width 400 (max 560 height); radius 16; glass blur 24;
  header 18/600 + clear/mode actions; rows 48–56 with index, 32 cover, title
  13/500, artist 11.5, drag handle, remove on hover.
  - Player: right-side slide-in, full height minus margins, scrim 35%.
  - Bar: anchored popup from the queue button, 300 ms fade + elastic scale;
    transparent barrier, click outside closes.
- **Empty state**: icon tile 72 radius 20 at `primaryContainer @ 35%`, icon 34
  primary; title 15/600; hint 12.5 at 70%; one action. Center in the available
  area with 80 vertical padding.

## 12. Tabs, chips and filters

- Tabs: text 13.5, 10/12 padding, 2 px primary underline inset 8; inactive
  variant; no pill tabs inside a page that already uses segmented controls.
- Chips: height 30, radius 100, 12 padding, 12.5 label; selected primary 12%
  fill + primary text. Use only for filters, never for primary actions.
- Filter row: 8 gap, horizontal scroll at narrow widths, no wrapping into three
  lines.

## 13. Component state matrix

Every interactive component needs these states designed:

| State | Required change |
|---|---|
| Default | base surface + readable label |
| Hover | +5% neutral fill or +8% accent, 150 ms |
| Focus | 2 px primary ring, never removed |
| Pressed | +8% neutral fill; no scale |
| Selected | 10% primary fill + primary text |
| Playing/current | 14% primary fill + 40% border |
| Disabled | 38–45% foreground, no pointer |
| Loading | stable size + spinner; no layout shift |
| Error | error icon/label + retry; not color only |
| Empty | message + one action; not a blank panel |

If a component has more than two of these visual signals at once, remove one.
