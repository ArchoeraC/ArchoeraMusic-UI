# Tokens

A single token layer feeds both themes and every player layout. Pages never
write raw colors, font sizes, radii or durations.

## 1. Palette

### Dark (default)

| Token | Value | Role |
|---|---|---|
| `surface` | `#000000` | window canvas, page background |
| `surfaceAlt` | `#0A0A0A` | sidebar, secondary canvas |
| `surfacePanel` | `#121212` | cards, dialogs, docked bar |
| `surfaceBright` | `#1C1C1C` | secondary buttons, menus, toast, hover on dark |
| `surfaceContainer` | `#1C1C1C` | quick cards, raised blocks |
| `surfaceContainerHighest` | `#2A2A2A` | settings card fill at 35% |
| `field` | `#161616` | input fill |
| `onSurface` | `#ECECEC` | primary text and icons |
| `onSurfaceVariant` | `#9E9E9E` | secondary text, idle nav |
| `outline` | `#2E2E2E` | dividers, card borders |
| `primary` | `#D0D3DA` | neutral accent, selected, active controls |
| `onPrimary` | `#1A1A1A` | text on primary |
| `primaryContainer` | `#2A2A2A` | logo block, placeholder cover, empty-state tile |
| `onPrimaryContainer` | `#E4E4E4` | glyph on container |
| `secondary` | `#9AA0A8` | secondary accent / metadata |
| `error` | `#FF6B61` | destructive, playback failure |
| `warning` | `#FFB300` | lossless/quality, caution |

### Light

| Token | Value | Role |
|---|---|---|
| `surface` | `#F7F7F8` | window canvas |
| `surfaceAlt` | `#EFEFEF` | sidebar, secondary canvas |
| `surfacePanel` | `#FFFFFF` | cards, dialogs |
| `surfaceBright` | `#FFFFFF` | high surface |
| `surfaceContainer` | `#E4E4E6` | quick cards, raised blocks |
| `surfaceContainerHighest` | `#E4E4E6` | settings card fill at 35% |
| `field` | `#EFEFEF` | input fill |
| `onSurface` | `#1A1A1A` | primary text |
| `onSurfaceVariant` | `#5E5E5E` | secondary text |
| `outline` | `#D4D4D4` | dividers, borders |
| `primary` | `#4A4D54` | neutral accent |
| `onPrimary` | `#FFFFFF` | text on primary |
| `primaryContainer` | `#E4E4E6` | logo / placeholder |
| `onPrimaryContainer` | `#2A2A2A` | glyph |
| `secondary` | `#5E5E5E` | secondary accent |
| `error` | `#D94438` | destructive |
| `warning` | `#B7791F` | quality/caution |

Semantic extras (derive, do not invent new families):

| Token | Dark | Light |
|---|---|---|
| `like` | `#FF5252` | `#E23A4E` |
| `sourceBadge` | provider color + `#FFFFFF` label | same |
| `playerSolidBg` | `#141420` | `#141420` (player-only style) |

The default accent is **neutral gray**, not a brand color. That is deliberate:
album art is the only place saturated color belongs.

## 2. Accent modes

Three modes, in priority order:

1. **Neutral (default).** `primary` / `onPrimary` from the palette.
2. **Custom seed.** The user picks a color; generate `primary`, `primaryContainer`
   and `onPrimary` from it. Low-chroma seeds stay neutral instead of exploding
   into an unexpected hue.
3. **Cover-derived.** Take a dominant color from the current cover; use it at
   low saturation for accents and background washes. Never tint body text.

Derivation rules:

```
onPrimary        = luminance(seed) > 0.55 ? #101318 : #FFFFFF
hover surface    = seed @ 12% over the current surface
selected fill    = seed @ 10%
playing row fill = seed @ 14%
playing border   = seed @ 40%
soft glow        = seed @ 18–26%
```

If the cover is near-gray (low chroma), fall back to the neutral accent. A
white/gray cover must not create a cyan or pink UI.

Optional global tint: shift the surface family **6%** toward `primary`, never
more. Text and status colors are excluded from tinting. This is a preference,
not a default.

## 3. State overlays

Use alpha over the current surface, not extra hex values:

| State | Dark | Light |
|---|---|---|
| Hover | `onSurface @ 5%` | `onSurface @ 5%` |
| Pressed | `onSurface @ 8%` | `onSurface @ 8%` |
| Selected / active | `primary @ 10%` | `primary @ 10%` |
| Playing row | `primary @ 14%` | `primary @ 14%` |
| Playing row edge | `primary @ 40%` | `primary @ 40%` |
| Soft accent badge | `primary @ 12%` | `primary @ 12%` |
| Divider | `outline @ 60%` | `outline @ 60%` |
| Dialog/card border | `outline @ 30–50%` | `outline @ 30–50%` |
| Scrim behind dialog | `#000 @ 50%` | `#000 @ 50%` |
| Queue slide scrim | `#000 @ 35%` | `#000 @ 35%` |

Disabled is not just lower opacity: keep the label readable, drop the control's
contrast, and explain why nearby. Source badges use 4 px radius and 10 px text.

## 4. Image background mode

When the user chooses a picture background, chrome becomes translucent so art
shows through:

| Element | Treatment |
|---|---|
| Page surface | `surface @ 22%` over the art |
| Floating surface | `surfaceBright @ 55%` |
| Cards / content | translucent panel, still bordered |
| Dialogs / menus | **solid** panel with `blur(16px) saturate(1.4)` behind |
| Player bar | `surfaceBright @ 70%` + `blur(16)` |
| Player page | solid `playerBackground` — never transparent |
| Background image | cover, optional `blur 0–80`, dim `30–90%`, scale `1.0–2.0` |

Text contrast is enforced by the dim scrim, not by making the art darker in
one corner. If text fails, raise dim or place a solid surface beneath it.

## 5. Typography

Reference family: **MiSans** (Regular / Medium / Semibold), with a CJK fallback
chain: `Noto Sans CJK SC → PingFang SC → Microsoft YaHei → WenQuanYi Micro Hei
→ sans-serif`. Use one sans for CJK + Latin; do not pair a Latin display face
with a CJK system fallback.

| Token | Size / line | Weight | Use |
|---|---|---|---|
| `display` | 26 / 34 | 700 | page title, library title |
| `title` | 20 / 28 | 600 | player title, settings title |
| `heading` | 17 / 24 | 600 | section title |
| `subheading` | 15 / 22 | 600 | empty state, compact dialog title |
| `body` | 14 / 22 | 400 | labels, general body |
| `bodyStrong` | 14 / 22 | 600 | button label, selected row |
| `rowTitle` | 16 / 22 | 500 | song row title only (68 px rows) |
| `compact` | 13 / 20 | 400 | nav, metadata, search |
| `caption` | 12 / 18 | 400 | secondary metadata |
| `micro` | 11 / 16 | 500 | badges, source tags, captions |
| `badge` | 10 / 12 | 700 | VIP / quality corner tags |

Additional rules:

- `display` uses `+0.5` letter spacing; `title` uses `−0.2`. Do not track CJK
  body text.
- Hero/spotlight may use weight 800 for one line; that is the ceiling.
- Use `TextLeadingDistribution.even` (or the platform equivalent) so CJK glyphs
  sit optically centered in fixed-height rows.
- Time labels, queue ordinals and counters use tabular figures. Everything else
  uses the normal figures.
- Long titles truncate on one line; subtitles may use two lines in settings/
  dialogs, one line in rows/rails.
- Do not use all-caps for Chinese; uppercase is acceptable for 10–11 px badges.

## 6. Spacing

Base rhythm is 4 px, but the design uses 2 px micro-adjustments:

`2 / 4 / 6 / 8 / 10 / 12 / 14 / 16 / 18 / 20 / 24 / 32`

| Situation | Value |
|---|---|
| Icon ↔ label | 8–12 |
| Inside a button | 14 horizontal |
| Card padding | 14 (quick action), 20–24 (hero) |
| Page gutter | 24 (home/library), 32 (player) |
| Section gap | 20–24 |
| Settings tile | 14 horizontal, 10 vertical |
| Grid gap | 16; rail gap 14 |
| Player bar horizontal | 12 body, 24 floating margins |

Do not invent a new 15/17/19 px gap. Reuse the scale.

## 7. Radii and shapes

| Token | Value | Use |
|---|---|---|
| `radiusXs` | 4 | source/quality badges |
| `radiusNav` | 8 | sidebar/settings nav item, queue row |
| `radiusControl` | 10 | buttons, inputs, search, segmented track |
| `radiusCover` | 10 | default grid/rail/row cover (user 0/8/12) |
| `radiusTile` | 11 | 42 px quick-action icon tile |
| `radiusCard` | 12 | cards, menus, settings cards |
| `radiusCoverLg` | 14 | 96 px spotlight cover |
| `radiusDialog` | 16 | dialogs, hero/spotlight, queue panel |
| `radiusEmpty` | 20 | 72 px empty-state icon tile |
| `radiusPlayerCover` | 24 | default player cover |
| `radiusBar` | 30 | floating player bar |
| `radiusPill` | 100 | segmented controls, pills, avatars |

Cover radius is user-selectable (`0 / 8 / 12`, default `10`). Keep it consistent
across library, rails, queue and search results. The player cover is the
exception: `24` in the split layout, `12` in the classic layout, `0` in poster.

Never use a border radius to fake a bevel. Never use a pill for a primary form
button; pills belong to segmented controls and compact toggles.

## 8. Borders, elevation and blur

- Divider: `1 px`, `outline @ 60%`, space `1`.
- Card/panel border: `1 px`, `outline @ 30–50%`.
- Floating bar border: `primary @ 12%`.
- Shadows are minimal and dark:
  - player cover: `0 12 32 rgba(0,0,0,0.30)`
  - floating bar: `0 8 24 rgba(0,0,0,0.35)`
  - toast: `0 4 12 rgba(0,0,0,0.25)`
  - queue/dialog: `0 10 28 rgba(0,0,0,0.28)`
- Cards and rows do not float. Page sections do not use shadows.
- Docked player bar may use a surface step + 1 px top rule instead of shadow.
- Glass: `blur(16) saturate(1.4)` for image-mode dialogs/bar; queue uses
  `blur(24)`. In performance mode blur becomes 0 and opacity rises to 0.92.

## 9. Icons

- Outline family for idle; filled variant for selected nav and liked heart.
- 24 × 24 grid, 1.8–2 px stroke, round caps/joins.
- Display sizes: 16 (inline), 18–19 (nav/settings), 20–22 (header/player),
  26 (lyric toggle), 34 (empty tile), 40–64 (placeholder covers).
- One semantic name per meaning. Pages reference a semantic registry, never raw
  glyph constants.
- Color: `onSurface`, `onSurfaceVariant`, `primary`, `error`, or status. No
  two-tone, gradient or multi-color icons.
- Icon-only controls need a tooltip (500 ms delay) and a label.

## 10. Contrast

- Body text ≥ 4.5:1; large text (≥ 24 px / 18.66 px bold) and essential icons
  ≥ 3:1.
- `onSurfaceVariant` at 75% is for supporting copy; at 50% only for decorative
  or duplicated metadata.
- On cover art, text always gets a scrim or solid surface. Never place
  `onSurfaceVariant` directly on a bright cover.
- Focus: 2 px `primary` ring at 2 px offset, or a 2 px border; visible in both
  themes and on artwork.
- Do not signal status with color alone: pair `error`/`warning` with an icon or
  a word.

## 11. CSS variable map

```
--surface --surface-alt --panel --panel-high --surface-container
--surface-container-highest --field
--text --text-muted --outline
--primary --on-primary --primary-container --secondary
--error --warning --like
--radius-xs --radius-nav --radius-control --radius-cover --radius-tile
--radius-card --radius-cover-lg --radius-dialog --radius-empty
--radius-player --radius-bar --radius-pill
--space-1 … --space-16
--font-sans
--dur-fast --dur-quick --dur-base --dur-panel --dur-player --dur-settle
--ease-out --ease-in --ease-out-cubic --ease-in-out-cubic --ease-player
--sidebar-w --sidebar-w-collapsed --header-h --playerbar-h
```

Use the same names in other stacks (camelCase / platform constants) so specs
map one-to-one. Never duplicate the palette inside a component.
