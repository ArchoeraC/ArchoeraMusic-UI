# Motion

Motion should feel like a heavy door, not a toy. The shell changes quickly; the
player settles; lyrics glide; nothing loops for decoration.

## 1. Duration scale

| Token | Value | Use |
|---|---|---|
| `instant` | 0 ms | reduced motion / performance mode |
| `fast` | 150 ms | nav/row hover, branch transition, segmented selection |
| `quick` | 180 ms | search expand, lyric line style, like swap |
| `base` | 200 ms | card hover, cover hover overlay, menu/dialog enter |
| `toast` | 250 ms | toast enter/leave |
| `lyric` | 280 ms | lyric list scroll to the active line |
| `panel` | 300 ms | immersive chrome, queue, bar info, spectrum settle |
| `coverOut` | 350 ms | slide out |
| `coverFade` | 360 ms | full cover crossfade |
| `coverIn` | 400 ms | slide in |
| `color` | 420 ms | cover-tone background |
| `player` | 500 ms | full-screen player open/close, cover play/pause |
| `settle` | 600 ms | no-lyrics cover centering, lyric scroll |
| `ambient` | 500–700 ms | blurred cover crossfade, ripple track change |

Rules:

- Interactions under the pointer: ≤ 200 ms.
- State changes that move a large surface: 300–500 ms.
- Ambient loops: slower than 1 s and never attention-grabbing.
- Nothing UI-critical exceeds 600 ms except the player route (500) and the
  deliberate cover settle.
- Stagger only lists of 3–6 items, 30–40 ms apart. Never stagger a long list.

## 2. Curves

| Curve | Value | Use |
|---|---|---|
| `easeOut` | `cubic-bezier(0.0, 0.0, 0.2, 1)` | default enter, fades |
| `easeIn` | `cubic-bezier(0.4, 0.0, 1, 1)` | exits |
| `easeOutCubic` | `cubic-bezier(0.33, 1, 0.68, 1)` | side nav, centering, scroll |
| `easeInOutCubic` | `cubic-bezier(0.65, 0, 0.35, 1)` | cover fade |
| `easeOutExpo` | `cubic-bezier(0.16, 1, 0.3, 1)` | cover entering after out-in |
| `easeOutBack` | `cubic-bezier(0.34, 1.56, 0.64, 1)` | cover play/pause only |
| `player` | `cubic-bezier(0.7, 0, 0.3, 1)` | full-screen player + shell |
| `standard` | `cubic-bezier(0.2, 0, 0, 1)` | menus, dialogs |

Use one curve per interaction. Do not combine a spring with an ease on the same
property. Only the cover play/pause may overshoot (`easeOutBack`), and only
slightly.

## 3. App shell and routes

### Branch switch

- Duration **150 ms**.
- `fade`: opacity 0 → 1, easeOut; reverse easeIn.
- `slide`: opacity 0 → 1 plus x `+6 px` (0.8% of width) → 0, easeOut.
- `zoom`: opacity 0 → 1 plus scale `0.97 → 1`.
- Only the entering branch animates; the old branch is not crossfaded.
- Do not animate the sidebar, header or player bar during a branch switch.

### Player open / close

- Duration **500 ms**, curve `cubic-bezier(0.7, 0, 0.3, 1)`.
- Player translates `y: 100% → 0`.
- Shell scales `1 → 0.95`, opacity `1 → 0`, same curve, driven by the player
  route's animation so both sides are frame-synchronized.
- At completion the shell is replaced by a solid placeholder (lists/images
  unload); on reverse it is restored immediately.
- The player is opaque during the whole transition. No white flash, no blur on
  the route.
- Reverse uses the same 500 ms and curve; do not snap back.

### Dialogs, menus, queue

- Dialog: opacity 0 → 1 + scale 0.98 → 1, 200 ms easeOut; reverse 150 ms.
- Menu/popover: opacity 0 → 1 + scale 0.96 → 1, 200–300 ms easeOutBack (very
  slight); close 150 ms easeIn.
- Queue popup: 300 ms fade + elastic scale from the anchor; queue slide: 300 ms
  from the right with 35% scrim fade.
- Toasts: 250 ms opacity + translate 16 px (enter from below, leave upward);
  auto-dismiss 3 s. Multiple toasts stack without re-animating existing ones.

## 4. Navigation and hover

- Nav item hover: fill 0 → `onSurface @ 5%`, 150 ms easeOut.
- Selected plate (animated style): move/resize 200 ms easeOutCubic; the 3 px
  edge fades in 150 ms.
- Sidebar collapse: width 240 ↔ 64, 200 ms easeOutCubic; labels fade 120 ms and
  are removed after the width settles. Do not animate layout beyond width.
- Search expansion: 180 ms, width 280 → 420, easeOut. Suggestions 300 ms debounce.
- Button hover/press: background alpha 150 ms; no transform.
- Card hover: 200 ms cover overlay fade; play button fades + 0.98 → 1 scale.
- Row hover: 150 ms fill + border. No horizontal slide.
- Tooltip: 500 ms delay, 120 ms fade; avoid flicker when moving between icons.

## 5. Cover motion

- Play/pause: scale 0.9 ↔ 1.0, **500 ms easeOutBack**. Paused is smaller, not
  desaturated.
- Beat pulse: optional, max 2.4%, attack/decay 250 ms each, disabled unless the
  user enables it. Never pulse on every frame of a drum roll.
- Cover transition on track change:
  - `fade`: 360 ms opacity, easeInOutCubic.
  - `scale`: 200 ms leave (1 → 1.06, fade out, 16 px shift) then 350 ms enter
    (0.94 → 1, fade in), easeOutExpo.
  - `slide`: 350 ms leave then 400 ms enter, full card width; next enters from
    the right, previous from the left.
  - Out completes before in starts; never cross the two covers in the same
    frame unless the style is explicitly a crossfade.
- No cover: 180 ms placeholder fade; do not animate a broken image.
- Fullscreen cover mask is static per cover; the noise seed can change with the
  track but must not animate.

## 6. Lyrics

- Line style change (active/inactive): 180 ms easeOut for size/weight/color;
  scale 0.97 → 1 for the active line in the AMLL engine.
- Scroll to the active line: **280 ms** easeOutCubic. One animation per line change.
- Clicking a line seeks; do not animate the entire list before seeking.
- Karaoke sweep follows the audio clock; no independent easing that drifts.
- Translation/romanization fade in with the active line, 180 ms, 4 px upward.
- Edge fade is static. Never animate the mask opacity per line.
- Pause: freeze the lyric clock; resume from the current position, no jump.
- Reduced motion: instant line switch and scroll; keep the active color.

## 7. Immersive chrome

- Idle delay **5 s** after the last pointer/keyboard event.
- Chrome opacity 1 → 0 and cursor hide over **300 ms easeOut**.
- Pointer move/click/key restores over 300 ms; controls become interactive
  immediately (do not wait for the fade).
- Spectrum opacity: 1 when chrome hidden, 0.4 when controls visible, 300 ms.
- If reduced motion is on, chrome still fades (it is spatial, not decorative)
  but uses 150 ms; keep a keyboard escape to pin controls.
- On touch, do not hide chrome while interacting; tap the empty area to toggle.

## 8. Backgrounds

- Style switch: 200 ms crossfade.
- Blur cover track change: 500 ms crossfade, easeInOut.
- Cover-tone color: 420 ms easeInOut.
- Ripple: 700 ms crossfade; pause freezes or slows the simulation.
- Fluid: 30 fps cap by default; crossfade per track; optional bass pulse, never
  a strobe; pause can freeze.
- Ambient effects start only after the player route has settled (500 ms) to
  avoid competing with the open transition.
- Performance mode: no ambient animation, no crossfade > 0; show a baked frame.
- Style changes never animate layout or chrome.

## 9. Progress, queues, spectrum

- Progress moves continuously with the audio clock; do not animate the value.
- Seek: thumb follows pointer immediately; on release, settle to the audio
  position within one frame.
- Buffering: a 1.2 s linear indeterminate sweep at 35% track opacity; disable
  when buffering ends.
- Queue row removal: 180 ms fade + height collapse; current-row highlight moves
  150 ms.
- Drag reorder: lift 1.0 → 1.02 only while dragging; no shadow, no rotation.
- Spectrum bars: 300 ms attack/release smoothing; 60 fps max; freeze on pause
  after 300 ms. Do not animate when reduced motion is on.
- Volume slider: value follows pointer; no easing.

## 10. Reduced motion

Trigger by the OS `prefers-reduced-motion` or the app's performance switch.

- All durations become 0 except continuous progress.
- No player slide: instant route swap, but keep the shell placeholder logic.
- No cover scale, pulse, slide, zoom or blur crossfade.
- No lyric scroll animation; jump to the active line.
- No background animation; use a static gradient/solid.
- No chrome auto-hide (keep controls visible), or fade instantly.
- Keep color/opacity state changes instant so the user can still read state.
- Never remove focus rings or keyboard access in this mode.

## 11. Performance rules

- Animate `transform` and `opacity`; avoid width/height/top/left except for
  deliberate layout transitions (sidebar, queue height).
- Bake static backdrops when cover/size changes; do not blur every frame.
- Reuse one shader program per effect and one texture per cover. Never create a
  shader, image or `BackdropFilter` inside `build` on a ticking widget.
- Isolate high-frequency regions (progress, lyrics, spectrum) in their own
  repaint layers so 50 ms position updates do not repaint the page.
- Cap background FPS (30) and render scale (0.5–1.0); stop tickers when paused,
  hidden, minimized or off-screen.
- Crossfade at most two full-screen background layers. Do not stack blur +
  ripple + fluid simultaneously.
- Decode images at display size; release the previous cover when the transition
  completes.
- Measure: if opening the player drops below 50 fps on the target machine,
  disable blur/ripple first, then cover pulse, then the spectrum.

## 12. Motion QA

- [ ] Every duration comes from the scale; no 173/271 ms magic numbers.
- [ ] Player open and shell shrink share one curve and end together.
- [ ] Branch transitions are ≤ 150 ms and do not animate chrome.
- [ ] No animation runs forever except the audio-driven spectrum/background.
- [ ] Hover never scales rows, cards or buttons.
- [ ] Cover pause is visibly smaller but not dimmed.
- [ ] Lyrics scroll once per line; clicking seeks immediately.
- [ ] Auto-immersive restores chrome on the first pointer move.
- [ ] Reduced motion removes all decorative movement and keeps state legible.
- [ ] Performance mode stops shaders, blur animation and cover pulse.
- [ ] Window resize, track change and theme change do not replay page entrances.
