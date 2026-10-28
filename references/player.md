# Player

The full-screen player is the emotional center of the product. It is not a
details page. It holds still, lets the cover and lyrics breathe, and keeps every
transport control within one screen.

The player opens as an opaque route above the shell; the shell behind it scales
to `0.95` and fades out. Layouts are registered strategies, so adding one must
not change the chrome, transitions or transport behavior.

## 1. Shared chrome

| Part | Spec |
|---|---|
| Horizontal padding | 32 |
| Top bar | 56 high |
| Page background | opaque player background, clipped to the page |
| Top bar left | close 32, 4 gap, lyrics toggle 26 |
| Top bar right | sleep timer, quality menu, fullscreen |
| Bottom | 90 spectrum, progress, 4 gap, control row |
| Bottom safe inset | 8 above the control row |
| Content | fills the space between top and bottom; page does not scroll |

- Close is a down chevron, not an X. Tooltip “Collapse player”.
- Lyrics toggle is primary when on, variant at 35% when no lyrics.
- The quality menu is a compact pill/text menu (e.g. `Lossless`); it does not
  become a second toolbar.
- Sleep timer shows primary when armed; tooltip includes remaining minutes.
- Top and bottom chrome use `AnimatedOpacity` over `300 ms` in immersive mode.
- Automatic immersive: pointer leave/idle fades chrome, cursor hides; pointer
  move restores. Keyboard focus keeps chrome visible.
- Do not put close at both top and bottom. Do not add a hamburger.

## 2. Layout selection

| Id | Public name | Character | Bottom transport |
|---|---|---|---|
| `default` | Split | Cover left, lyrics right; balanced and familiar | Shared overlay |
| `fullscreen` | Poster | Cover fills the left 52%, dissolves into black; editorial | Shared overlay |
| `vutron` | Classic | Half cover/controls, half lyrics; album-page feel | Inside left column |

The default should be Split. Poster is a deliberate “cinema” mode; Classic is a
nostalgic mode. All three use the same 32 px chrome and 56 px top bar. Do not
mix, for example, Poster cover with the Classic control column.

## 3. Split layout

```
┌ 32 ─────────────────────────────────────────────────────────── 32 ┐
│ top bar 56                                                        │
│ ┌──────────── 45% ───────────┐ ┌──────────── 55% ──────────────┐  │
│ │                            │ │                               │  │
│ │        [cover 180–520]     │ │          lyrics               │  │
│ │        24 radius           │ │          right pad 64         │  │
│ │        title / subtitle    │ │                               │  │
│ └────────────────────────────┘ └───────────────────────────────┘  │
│                      [90 spectrum]                                │
│                      progress + times                             │
│                      controls                                     │
└───────────────────────────────────────────────────────────────────┘
```

- Left column width = body width × `coverLyricRatio`, default **0.45**
  (allowed 0.30–0.60).
- Cover box: `min(width × ratio × 0.85, height × 0.50)`, clamped
  **180–520**. Square.
- Cover radius **24**; shadow `0 12 32 rgba(0,0,0,.30)`; placeholder fill
  `primaryContainer @ 60%`; icon 110.
- Cover sits vertically centered in the left column.
- Playing scale `1.0`, paused scale `0.9`, 500 ms `easeOutBack`. Optional beat
  pulse peaks at +2.4%; never above that.
- Cover → title 20; title is `titleLarge` 22 / 600 (or 20 for compact windows),
  one line; subtitle 14 at variant, 6 below.
- No lyrics: the cover block shifts right by `11/18` of its column width over
  600 ms so it reads as centered, not as a left-heavy orphan.
- Lyrics column: right padding **64**; text centered by default.
- Body height below ~640: reduce cover clamp minimum to 150 and hide the
  subtitle before shrinking the cover further.

The cover is a card, not a full-bleed image. If the product wants full bleed,
use Poster.

## 4. Poster layout

```
┌ 32 ─────────────────────────────────────────────────────────── 32 ┐
│ top bar 56 (over cover)                                           │
│ ┌──── cover 52% ────────────┐┌──── text starts 52% ─────────────┐ │
│ │                           ││ title 22–34 / 600                │ │
│ │  full-bleed square cover  ││ subtitle 12–17 variant           │ │
│ │  no radius, no shadow     ││ 22 gap                           │ │
│ │  right edge dissolves     ││ lyrics, right pad 24             │ │
│ │  to black over 8% more    ││                                  │ │
│ └───────────────────────────┘└──────────────────────────────────┘ │
│       [90 spectrum]  progress + times  controls                   │
└───────────────────────────────────────────────────────────────────┘
```

- Cover drawing width = `pageWidth × 0.52`; draw an extra **8%** page width for
  the dissolve so the fade extends under the lyric column.
- The square source art is **cover-cropped to fill the full height** of the
  body, left aligned. No radius. The right mask starts around **48–56%** and is
  fully transparent by **88–93%**; use a low-frequency noise wobble so the edge
  is not a ruler-straight line. Fallback is a 0 → 0.82 linear fade.
- Text column starts at 52% page width. Cover inset is accounted for in padding;
  do not let text sit on opaque artwork.
- Title: `height / 1080 × 30`, clamp **22–34**, 600, line 1.2, −0.2 tracking.
- Subtitle: `height / 1080 × 15`, clamp **12–17**, variant.
- Info padding: left 4, right 24; 8 gap title → subtitle; 22 gap → lyrics.
- Lyrics right pad **24** (not 64), min width 340. If the window would make the
  lyric column narrower than 340, shrink the cover or switch to Split.
- Top scrim **80** and bottom scrim **192**, black with multi-stop alphas
  `0.50, 0.44, 0.36, 0.27, 0.18, 0.10, 0.04, 0.0`. Never a simple 0.5 → 0 line.
- No lyrics: move the title/subtitle to the vertical center of the right column;
  do not leave an empty top.
- Cover transition: crossfade/scale, not a slide that reveals the page edge.

This layout is the best showcase for a strong cover. It is also the least
forgiving: contrast scrims are mandatory.

## 5. Classic (vutron) layout

No shared bottom bar. The left column owns progress and transport; the right
column is pure lyrics.

- 50 / 50 columns; both stretch full body height; left content vertically
  centered; left aligned within its column.
- Cover: `height × 0.54`, capped at `width × 0.38`, clamp **180–460**; square;
  radius **12**; shadow `0 12 28 rgba(0,0,0,.35)`; placeholder icon
  `size × 0.28` clamp 48–120.
- Cover → title 22; title 20 / 600 at 88%; subtitle 14 at 70%, 3 gap.
- Title → progress 18. Progress track 4, times 15 / 600 at 58%.
- Controls: repeat, previous, play, next, shuffle; icons 20/26/48/26/20 with
  12/14/14/12 gaps. The play icon is a 48 circle; buffering swaps in a 20
  spinner at 35% opacity.
- Volume below controls: 8 gap, thin track, icon 20; no separate card.
- Lyrics container: **90%** of the right column, right aligned; text left
  aligned; horizontal room comes from the column, not extra padding.
- Font: `28 × (windowHeight / 720)`, clamp **24–44**; line height `× 2.5`.
- Current line 95% foreground, 600 weight, no font-size bump; inactive 28%
  foreground, 600 weight, scale **0.95**. This is intentionally flatter than
  Split.
- Edge fade: 10% top, 25% bottom, middle fully opaque.
- No lyrics and auto-center enabled: center the entire left column; do not leave
  a blank right half.
- Poster/Classic at widths below 820: fall back to Split or a single column.
  Never clip lyrics to a 100 px strip.

## 6. Lyrics

Applies to Split/Poster unless Classic overrides.

### Typography

- Base size 18 (user 14–60), adaptive scale = `windowHeight / 1080`, clamp
  **0.85–1.6**.
- Line height = `size × 1.6 + 12` (clamped 34–160). Do not use 1.2 leading.
- Active line: size +3 (or configured size), weight 600, accent/cover color.
- Inactive: same size, weight 400, `unplayedColor @ 55%`; translation 75% of
  active color, about 0.72× size, min 9.
- Current line centered in the viewport; the list has symmetric top/bottom
  padding of `viewport/2 − lineHeight/2`.
- Click a line to seek; cursor becomes pointer only when seeking is possible.
- Long lines wrap; row height is measured, never clipped by a fixed extent.
- Scroll animation: one smooth settle per line change (~280 ms easeOutCubic).
  Do not continuously animate the whole list.
- Edge fade (Poster and Classic; Split opts in only if the lyric column has no
  surface behind it): top 0→60% by 5%, full by 10%; bottom full until 75%, 60%
  at 85%, 0 at 100%. This keeps the middle opaque.
- No lyrics: 64 music-note icon at 30%, centered; no fake lines.
- Karaoke/word sweep is optional. If enabled, highlight only the current line and
  keep inactive lines static. Under reduced motion, switch instantly.

### Lyric color

- Default played `#D0D3DA`, unplayed `#9AA1B5`.
- “Follow accent” uses `primary`; “follow cover” uses the cover’s dominant
  color. Both stay at full opacity for the active line; inactive stays dim.
- Never put a text shadow on every line. If contrast over artwork fails, add a
  local scrim or use the Poster bottom gradient.

## 7. Cover behavior

- Default card: radius 24, object-fit cover, background
  `primaryContainer @ 60%`.
- Paused scale 0.9; playing 1.0; 500 ms easeOutBack. Beat pulse is opt-in, peaks
  at 2.4%, and is disabled in performance/reduced-motion mode.
- Cover switch:
  - fade: 360 ms easeInOutCubic (crossfade);
  - scale: 200 ms out (1 → 1.06 + fade) then 350 ms in (0.94 → 1 + fade),
    easeOutExpo, 16 px shift;
  - slide: 350 ms out then 400 ms in, full width, direction follows next/previous;
  - never overlap out and in; use the out-in state machine.
- Fullscreen/Poster cover has no rounded corners; the right mask is the shape.
- Decode at display size: player cover ~1024, rows 160, cards 320, poster
  viewport width capped 2048. Do not decode a 4K cover for a 48 px thumbnail.
- On missing/error: `primaryContainer` tile + music note, never a broken-image
  icon.

## 8. Backgrounds

Six registered styles, all behind the content and independent of it:

| Style | Visual | Notes |
|---|---|---|
| `gradient` | linear top-left `primary @ 28%` → player background, 45° | default + fallback |
| `blur` | cover blurred 45, saturated 1.2, zoomed 1.5, 50% black | bake once per cover/size |
| `solid` | `#141420` | cheapest, no cover |
| `ripple` | blurred cover + water ripples | 700 ms crossfade; can freeze |
| `fluid` | fluid mesh derived from cover | 32×32 cover texture, 30 fps cap |
| `coverTone` | cover dominant color, darken 10% bottom-right, lighten 20% + hue −30° top-left | 420 ms color transition |

- No cover: every style falls back to `gradient` (or `solid`).
- Style switch: 200 ms crossfade, never a hard cut.
- Dim overlay 30–90%; default 50% for blurred art.
- Performance mode: stop animation, keep one static frame; disable blur/shaders.
- Never let the background animate faster than the music or flash on beat. Beat
  response is a slow scale/pulse, not a strobe.
- Background is a separate repaint layer, baked when cover/size changes. Do not
  rebuild a shader or run a full-screen blur every frame because the progress
  bar ticks.
- The player page always has a solid base color underneath. If the background
  fails, the page is still readable.

## 9. Transport

### Shared bottom row

- Spectrum: 90 high, full width, behind progress; opacity 100% when chrome is
  hidden, 40% when controls are visible. It is ambience, not a chart; no axis,
  labels or legend.
- Progress: full width, 4 px track, 10 px thumb; times 12 tabular at both ends;
  hover shows a time bubble (and lyric line if enabled).
- Controls are transparent icon buttons, no filled pills:
  - left: like 24 + comment 24;
  - center: shuffle 20, previous 26, play 48, next 26, repeat 20;
  - right: volume 104 + queue 24.
- Gaps 12–14. Play center is the only filled/primary element.
- Disabled controls at 35%, but remain legible. Loading shows a spinner over the
  play icon, never a layout jump.
- Repeat: off = variant, list/one = primary. Shuffle on = primary.
- Like: filled `like` color; scale swap 180 ms.
- Keyboard: Space play/pause, arrows seek ±5 s, Shift+arrows track, M mute.
  Visible focus ring on every control.

### Classic variant

Progress and controls live under the cover; the shared overlay is not rendered.
Keep the same icon order but use the specified 20/26/48 sizes and 15 px times.

## 10. Player bar transport

The bar is a compact echo of the player, not a second full control surface.

- Cover 40, radius 8; hover overlay + chevron opens the player.
- Title 14 / 500 + source badge; subtitle 12 variant; one line each.
- Center: previous 26, play 40, next 26. Do not add shuffle/repeat to the
  bar; those live in the player.
- Progress: 2 px simplified line across the top 22 strip; hover expands to a
  4 px track; buffered segment 30%.
- Right: time 150 total, mini lyrics/spectrum 120 × 15, volume 72, queue 24,
  like 22.
- At < 900 drop mini info, then volume; never drop play/next/previous.
- Empty: placeholder cover, “Choose something to play”, disabled transport.

## 11. Queue

- Width 400; max height clamp 240–560; radius 16; glass blur 24 + 50% panel.
- Header 18 / 600 + count 12 variant; actions: shuffle, repeat, clear.
- Rows 48–56: index/playing bars 24, cover 32 radius 6, title 13 / 500, artist
  11.5 variant, duration 11.5 tabular, drag handle 16 and remove 16 on hover.
- Current row: `primary @ 12%` + primary title; no border.
- Drag reorder; reorder proxy is transparent, no lift shadow.
- Empty: 40 queue icon at 30%, “Queue is empty”, one link back to library.
- Player: slide in from the right, full height minus margins, scrim 35%.
- Bar: anchored popup above the button, fade + elastic scale 300 ms, transparent
  barrier.

## 12. Player states

| State | UI |
|---|---|
| No track | Cover placeholder, “Nothing playing”, hint to choose a song; transport disabled |
| Loading track | Cover placeholder shimmer (optional) + 24 spinner near title; do not flash the old cover |
| Buffering | Spinner over play; progress at 35%; keep time labels stable |
| Paused | Cover scale 0.9; play glyph; background may freeze |
| Playing | Cover 1.0; pause glyph; optional beat pulse |
| Ended | Keep last frame; play becomes replay; no toast |
| Error | Inline message under title + retry; keep transport usable for other tracks |
| No lyrics | Music-note placeholder; lyrics toggle at 35% |
| No queue | Queue panel empty state; next/previous disabled |

## 13. Player review checklist

- [ ] The player is one screen at 1440 × 900 and 1280 × 720; no page scroll.
- [ ] Chrome is 32/56; no control sits inside the unsafe area.
- [ ] Cover never exceeds 520 (Split) or 460 (Classic).
- [ ] Poster text starts at 52% and the cover fade is visible under it.
- [ ] Both scrims exist on Poster; top icons are readable on a white cover.
- [ ] Lyrics active line is unmistakable without relying on size alone.
- [ ] Inactive lyrics remain readable at 28–55%, not invisible.
- [ ] Cover pause/play scale is 0.9/1.0 in 500 ms, not a bounce.
- [ ] Background switch is a 200 ms crossfade; performance mode freezes it.
- [ ] Progress and time do not shift when the duration label changes width.
- [ ] Every icon-only control has a tooltip and keyboard focus.
- [ ] Reduced motion removes the slide, pulse, blur animation and lyric spring.
- [ ] The bar and player show the same track, artist and like state.
