---
name: archoera-music-ui
description: Build or restyle music-app interfaces in the ArchoeraMusic visual language — dark-first near-black chrome, a neutral monochrome accent that can yield to cover art, 10/12/16 rounded surfaces, a persistent 82 px transport bar, and a cover-driven full-screen player with split, poster and classic layouts. Use when designing playback screens, music libraries, queues, lyrics, now-playing views, or a music-app theme; not for non-media dashboards.
---

# Archoera Music UI

A UI language for music software: a dark listening room, monochrome chrome, and
artwork + lyrics as the only real color. The transport is always present; the
full-screen player is a place, not a modal.

> Scope: **interface only.** This spec covers layout, type, color, components,
> motion and visual states. It deliberately excludes playback, scanning,
> networking, storage, accounts and every other behavior.

> Related but distinct: the `archoera-ui` skill is a flat, beveled desktop
> system. **This one is soft and rounded, with a permanent player bar and
> cover-led color.** Do not mix their shape languages.

## When to use

- A desktop or large-screen music player, library, queue, lyric view or
  now-playing screen.
- A media app that needs a dark, calm shell with persistent transport.
- Reviewing a music UI for hierarchy, contrast, density or motion.
- Theming an existing media app with cover-derived or custom accent color.

If the product has no playback context, use a general UI system instead. If it
has playback but should look flat/angular, use `archoera-ui`, not this one.

## Design stance

1. **The room is dark; the record is bright.** Near-black chrome, restrained
   neutral text, artwork supplies the color. Never tint the whole app from a
   cover; tint accents, glows and background washes only.
2. **Transport never disappears.** A compact bar sits under every page. Opening
   the player is a full-screen vertical transition; closing it returns to the
   exact same place.
3. **Rounded, not bubbly.** Controls 10, cards 12, dialogs 16, player cover 24,
   floating bar 30, pills only for segmented controls. No bevels, no hard edges
   on surfaces.
4. **One hero per page.** Home has the spotlight; the player has the cover;
   library has the list. Do not give every block a gradient, glow or shadow.
5. **Lyrics are content.** Treat them as typography, not decoration: readable
   active line, quiet history, generous line height and an edge fade.
6. **Layered but cheap.** Blur, shaders and animated backgrounds are optional
   garnish. Bake static backdrops, decode images at display size, and always
   keep a solid fallback.

## Non-negotiables

- Dark theme is the default and must stand on its own; light theme is a complete
  peer, not an afterthought.
- Default accent is **neutral gray** (`#D0D3DA` dark / `#4A4D54` light). Custom
  or cover-derived accents are optional. Never bake in a brand blue.
- Radii: `10` controls, `12` cards, `16` dialogs/hero, `24` player cover,
  `30` floating bar, `100` pills, `8` nav items. No cut corners.
- Shell: sidebar `240` expanded / `64` collapsed; header `64`; player bar
  `22 + 60 = 82`; docked content reserves `82` at the bottom.
- The player bar has two modes: **docked** full width or **floating** (max
  `960`, margins `24 / 14`, radius `30`, shadow). Pick one per product.
- Full-screen player uses one of three layouts: `split` (default), `poster`,
  `classic`. Keep the chrome padding `32` and top bar `56` in all three.
- Player chrome over artwork needs a scrim: top `80` and bottom `192` with the
  multi-stop black gradient. Text on raw bright artwork is a bug.
- Lyrics: min width `340`; inactive alpha around `55%`; active line gets the
  accent (or cover color) and one weight step, not a glow.
- Motion: `150` branch transition, `300` immersive fade, `500` player open,
  `600` cover centering, about `280` lyric scroll. Everything collapses to zero
  in reduced motion.
- Do not create shaders, blur layers or images per frame. Reuse one program,
  bake static backdrops, and downsample cover decodes.

## Token snapshot

| Role | Dark | Light |
|---|---|---|
| background | `#000000` | `#F7F7F8` |
| sidebar / canvas alt | `#0A0A0A` | `#EFEFEF` |
| panel / card | `#121212` | `#FFFFFF` |
| high surface | `#1C1C1C` | `#FFFFFF` |
| input field | `#161616` | `#EFEFEF` |
| text | `#ECECEC` | `#1A1A1A` |
| secondary text | `#9E9E9E` | `#5E5E5E` |
| outline | `#2E2E2E` | `#D4D4D4` |
| primary (neutral) | `#D0D3DA` | `#4A4D54` |
| on primary | `#1A1A1A` | `#FFFFFF` |
| container | `#2A2A2A` | `#E4E4E6` |
| secondary tint | `#9AA0A8` | `#5E5E5E` |
| error | `#FF6B61` | `#D94438` |
| warning / lossless | `#FFB300` | `#B7791F` |
| liked | `#FF5252` | `#E23A4E` |

Type: display `26/34 · 700 · +0.5`; title `20/28 · 600 · −0.2`;
heading `17/24 · 600`; subheading `15/22 · 600`; body `14/22`; compact
`13/20`; caption `12/18`; micro `11/16 · 500`; badge `10/10 · 700`.
Default body weight 400; labels 500; titles 600. MiSans is the reference
face — if you cannot license it, use a neutral CJK sans, not a display face.

Space: `2 / 4 / 6 / 8 / 10 / 12 / 14 / 16 / 18 / 20 / 24 / 32`.
Icons: outline + filled pairs, 24 grid, 1.8–2 px stroke, 19 px in nav, 18–22 in
chrome, 34–40 in placeholders. Selected nav uses the filled variant.

## Shell at a glance

```
┌──────────┬──────────────────────────────────────────────┐
│ logo 64  │ header 64: back · search 280→420 · actions   │
│          ├──────────────────────────────────────────────┤
│ sidebar  │ page canvas                                  │
│ 240 / 64 │ home gutter 24 / max 1400                    │
│          │                                              │
│          │                 (82 px reserved when docked) │
├──────────┴──────────────────────────────────────────────┤
│ player bar 82: progress 22 + cover/title · controls · time│
└─────────────────────────────────────────────────────────┘
```

- Home: padding `24 / 18 / 24 / 24`, max width `1400`.
- Library header: padding `24 / 18 / 24 / 10`; rows `68`; grid extent `180`.
- Player: horizontal padding `32`; top bar `56`; body fills; bottom overlay
  `90` spectrum + progress + controls.
- Settings: centered dialog, `80% × 84%` clamped `640–960 × 480–660`, left nav
  `210`, content padding `24 / 20 / 24 / 16`.

## Apply workflow

1. **Choose the chrome mode.** Docked bar for a desktop app; floating bar only
   when the product wants a lighter, cover-forward canvas.
2. **Lay the shell.** Sidebar + 64 header + scrollable page + transport. Decide
   the sidebar collapse rule and keep it consistent across pages.
3. **Set tokens and typography.** Dark palette first, neutral primary, then the
   optional cover/custom accent rule. Verify both themes before building pages.
4. **Build the page archetype.** Home (spotlight + shortcuts + rails), library
   (header + list), detail/collection, search, settings. Use the patterns file.
5. **Add the player.** Dock bar first, then the full-screen split layout; add
   poster/classic only if the product supports layout choice.
6. **Define states.** No track, loading, buffering, paused, playing, empty
   library, no search results, error, offline, unavailable source.
7. **Verify.** Both themes, 1440 / 1024 / 760 / 430 widths, 150–200% zoom,
   reduced motion, keyboard, long CJK titles and 1–3 line lyrics.

## Verify checklist

- [ ] Dark theme uses true near-black chrome; text is not pure white.
- [ ] Accent is neutral by default; custom/cover accent affects accents only.
- [ ] No hard-coded color, radius, font size or duration in a page.
- [ ] Transport is reachable on every page and never covers content when docked.
- [ ] Expanded sidebar is `240`, collapsed `64`; labels vanish, icons remain.
- [ ] Search grows `280 → 420` without pushing account actions off-screen.
- [ ] Player cover uses `24` radius; poster cover uses `0` and a right fade.
- [ ] Text over artwork passes contrast with the scrim or a surface behind it.
- [ ] Lyrics active/inactive hierarchy is clear at 3 m; edge fade does not gray
      out the middle.
- [ ] Every icon-only control has a tooltip and accessible name.
- [ ] Hover/press change color or surface only; no scale on rows/buttons.
- [ ] Player open/close is `500 ms`; shell settles to `0.95 / 0` behind it.
- [ ] Background effects degrade to gradient/solid; no per-frame shader or blur.
- [ ] Empty, error and no-playback states are designed, not blank.
- [ ] No third-party layout, icon font, cover art or branding is copied.

## Reference map

| File | Contents |
|---|---|
| `references/tokens.md` | Palettes, neutral vs cover accent, typography, spacing, radii, shadows, icon rules, contrast |
| `references/layout.md` | Shell, sidebar, header, player bar modes, page gutters, settings, breakpoints, scrolling |
| `references/components.md` | Buttons, inputs, segmented/dropdown, settings tiles, rows, cover cards, badges, dialogs, toast, queue |
| `references/player.md` | Three player layouts, cover sizes, lyrics, backgrounds, immersive chrome, transport details |
| `references/motion.md` | Route/cover/lyrics motion, immersive fade, curve table, reduced motion, performance |
| `references/patterns.md` | Home, library, search, detail, settings, states, accessibility, copy, review rubric |
| `assets/tokens.json` | Machine-readable tokens |
| `assets/tokens.css` | CSS variables + base music-ui recipes |
| `assets/preview.html` | Dependency-free shell + player preview (theme and layout controls) |
| `NOTICE.md` | Attribution, AGPL boundary, font/icon/cover licensing |

## Anti-patterns

- A bright page background with a dark player bar; the room should stay dark.
- A different accent for every card, or a rainbow spectrum as decoration.
- Square covers with pill buttons, or 12 px covers floating in 240 px cards.
- A player bar that shifts layout, grows on hover or hides on scroll.
- Full-screen player that scrolls like a page instead of holding still.
- Cover art behind unreadable text without a scrim.
- Lyrics as a ticker: tiny inactive lines, all lines bright, or constant motion.
- Blur/shaders on the page layer; rebuilding backgrounds per frame.
- Auto-playing video/cover animation with no reduced-motion escape.
- Copying another player's layout, icon set or brand color.

---

Origin: re-authored from the ArchoeraMusic interface (AGPL-3.0-or-later),
including its player-layout and style-registry design notes. This is an
independent visual specification, not a source-code extraction. No fonts, icon
fonts, cover art or other assets are bundled. MiSans is free to use with
attribution but may not be redistributed as a standalone font file.
