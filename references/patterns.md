# Patterns

Use these page recipes before inventing a layout. They keep the shell, density
and states consistent across the product.

## 1. Choose a page archetype

| Archetype | Primary job | Hero | Density |
|---|---|---|---|
| Home | Re-enter listening | Spotlight card | Medium |
| Library | Find something owned | Header + list | High |
| Search | Type → recognize | Field + filters | High |
| Collection / detail | Understand a set | Cover header | Medium → high |
| Player | Stay with one track | Cover + lyrics | Low |
| Queue | Edit what plays next | Header | High |
| Settings | Change a preference | Category nav | Medium |

One page = one scroll owner, one hero, one primary action. If two blocks compete
for the hero, demote one to a rail.

## 2. Home

```
Good evening, <name>                          13, variant
<title 26/700>  <optional one-line summary 13>
┌ spotlight 172 ─────────────────────────────────────────────┐
│ [96 cover]  overline / title / subtitle / meta   preview 320│
│             [Play] [Shuffle]                                │
└─────────────────────────────────────────────────────────────┘
[Daily] [Rankings] [Playlists] [Artists]        four 2.3 cards
Recently played                                         See all
[68 row] ...
Made for you
[150×198] [150×198] [150×198] ...               horizontal rail
```

Rules:

- Greeting is optional and time-aware; never fake it with a stock quote.
- Spotlight is the only strong gradient. It must have a real fallback/empty
  state at the same height.
- Quick actions: four core destinations, not a dumping ground. Each has a
  42 px icon tile, title and one-line subtitle.
- “Recently played” is a list of 4–8 rows; do not turn it into a huge table.
- Rails are horizontal, 5–8 visible cards, with a clear “More” affordance.
- A new user with no history sees: spotlight empty/login prompt, one primary
  action, and one “Browse” rail — not six empty sections.
- Do not autoplay a preview on page load; previews are click-to-play.
- Loading keeps the hero skeleton at 172; do not collapse the page.

## 3. Library

```
<title 26/700>  <count/status 13>                    [search 190]
[Play all] [Scan] [Scrape] [More]        (36 high, one line)
─────────────────────────────────────────────────────────────
#   TITLE / ARTIST                         ALBUM        TIME  ♥
[68 row] ...
```

Rules:

- Header is fixed; the list scrolls. The list is virtualized.
- Primary action is `Play all`; scan/scrape are secondary and must not look
  equally important.
- Search filters in place; it does not navigate away until the user submits.
- Row density: 68 px; album and duration can be hidden before the title is
  truncated.
- Empty/no-folder: 72 icon tile, “Add a music folder”, one primary button.
- Empty/not-scanned: “Ready to scan”, primary “Scan now”; show progress inline,
  not a blocking dialog.
- Empty/search-no-result: query is echoed; clear-search action.
- Sorting/filtering goes in a menu, not eight visible chips.
- Errors keep the list intact and show a banner with retry; do not blank the
  page for a failed refresh.

## 4. Search

- Global search lives in the header; a full search page can add filters and
  results. Search is not a sidebar destination.
- Focus opens a dropdown with recent searches and suggestions; 300 ms debounce.
- Results are grouped: top result, songs, albums, artists, playlists, lyrics.
  Do not mix types into one undifferentiated list.
- Search hit highlight: primary text + 16% primary text background; do not bold
  or resize.
- Query chips are removable; recent searches have a clear-all action.
- No results: echo the query, offer “Search all sources” or “Clear filter”; do
  not show a blank pane.
- Loading: keep the previous results visible with a 2 px top progress bar; no
  full-page spinner after the first search.
- Keyboard: up/down moves through suggestions, Enter submits, Esc closes.

## 5. Collection / detail

```
[cover 160–200]  ALBUM / PLAYLIST                    12 overline
                 Title 26–30 / 700
                 artist · year · N tracks · duration 13
                 [Play] [Shuffle] [Like] [More]
─────────────────────────────────────────────────────────────
description or notes (max 3 lines, expandable)
[song rows] or [cover grid]
```

Rules:

- Cover 160–200, radius 12–16 (use the user cover radius for grid covers; the
  header cover may be 16). No parallax.
- Title max two lines; metadata one line; long descriptions collapse.
- Header background may use a low-opacity cover wash (12–18%), but text must sit
  on the normal surface with a scrim.
- `Play` is primary; `Shuffle` secondary; like/more are icon buttons.
- Track list is the same 68 px row as library. For albums, hide the album column.
- Empty playlist: “This playlist is empty”, one “Find music” action.
- Missing/unavailable tracks stay in the list at 38% opacity with a reason
  tooltip; do not silently remove them.

## 6. Settings

Use the dialog/section pattern from `components.md` and `layout.md`.

Order categories by user intent, not by code module:

1. Appearance (theme, accent, cover radius, bar mode, background, motion)
2. Playback (crossfade, audio quality, output)
3. Lyrics (engine, size, colors, alignment, translation)
4. Library / scanning
5. Downloads / storage
6. Integrations / sources
7. Shortcuts
8. About / licenses

Rules:

- Each row is one decision. Do not put two unrelated toggles on one line.
- Show the current value in the trailing control; no “Edit” detours.
- Destructive actions are at the bottom of their section and require
  confirmation; do not use a red switch.
- Long help goes in a note or an info tooltip, not a paragraph above every card.
- Search in settings filters rows by title and subtitle; it does not reorder
  categories.
- Closing the dialog keeps unsaved text input only if the user confirms; toggles
  apply immediately.
- License and font attribution live in About; never bury required notices.

## 7. Player and queue

See `player.md` for layout and transport. Pattern rules:

- Opening the player is a route transition, not a modal; closing returns to the
  same scroll position and selected tab.
- The player bar remains visible on all non-player pages, even during loading.
- Queue is an overlay: player → right slide; bar → anchored popup.
- Clicking an item in the queue switches immediately and keeps the panel open.
- Do not show a “now playing” full page inside a page; it is always the route.

## 8. State design

Every data surface needs all applicable states:

| State | Visual | Action |
|---|---|---|
| First load | Skeleton matching final geometry, or a centered spinner after 300 ms | none |
| Refreshing | Keep content; 2 px top progress line | cancel if long |
| Empty (first use) | Icon 72 tile, title, one sentence, one primary action | create/add |
| Empty (filter) | Query/filter summary + clear | clear filter |
| No results | Search icon, echo query, suggestion | search all / clear |
| Error | 44–64 icon, plain reason, retry | retry / details |
| Offline | Banner + cached content, disabled network actions | retry |
| Partial | Content + inline warning; never hide good rows | retry failed part |
| Disabled | 38–45% foreground, tooltip reason | none |
| Loading row | Fixed 68 height, shimmer or 20 spinner; no jump | none |
| End of list | 20 spinner, then “All tracks loaded” or nothing | load more |

Skeletons:

- Use the same block geometry as the final content (68 rows, 180 grid tiles,
  172 hero).
- Shimmer is a slow 1.5 s gradient, 4–8% contrast; no bright white sweep.
- Stop shimmer when reduced motion is on; show static blocks.
- Never skeleton the chrome, sidebar or player bar.

## 9. Density and truncation

- Page title: one line, 26; long titles ellipsize, never wrap into the actions.
- Section title: one line, 17.
- Row title: one line, 16; subtitle: one line, 12; album: one line, 14.
- Card title: one line, 14; subtitle: one line, 12.
- Dialog description: max 2–3 lines; settings subtitle: max 2.
- Lyrics: wrap, but cap the text column at 72 characters (Latin) or ~30 CJK
  characters; long lines still wrap rather than scroll horizontally.
- Numeric columns are tabular and right/center aligned; time never jitters.
- Empty space is allowed. Do not fill a wide window with oversized type.

## 10. Accessibility

- Contrast: body 4.5:1, large text 3:1, icons 3:1; verify on both themes and on
  image backgrounds.
- Focus: visible 2 px primary ring; never `outline: none` without a replacement.
- Keyboard: Tab order follows visual order; Escape closes menus/dialogs/player;
  Space/Enter activate; arrow keys move within menus and sliders.
- Hit targets: 36 minimum, 40 preferred for icon-only chrome; rows are 68.
- Labels: every icon button has a tooltip and accessible name; sliders have
  min/max/current values; progress has elapsed/total.
- Status: never color-only; pause/playing, error, lossless and VIP all include a
  glyph or text.
- Motion: respect reduced motion; no autoplay video; no flashing > 3 Hz.
- Text scaling: layout must survive 150–200% without clipping labels or hiding
  transport. Prefer wrapping/expanding rows over truncating controls.
- Images: cover alt text uses “<title> cover”; decorative background is hidden
  from assistive tech.
- Language: CJK and Latin share one family; do not rely on uppercase for CJK.
  Leave 20–30% extra width for German/Russian labels.

## 11. Copy

- Use sentence case for buttons and titles; no all-caps except 10–11 px badges.
- Verbs first: “Play all”, “Scan folder”, “Clear queue”, “Add music”.
- Empty states say what will happen: “Add a folder to start your library.”
- Errors say what failed and what to do: “Couldn't load lyrics. Retry.”
- Do not blame the user, do not use “Oops”, do not expose stack traces.
- Confirm destructive actions with the object name: “Remove 12 tracks from the
  queue?” not “Are you sure?”.
- Keep one term per concept: track/song, playlist/collection, liked/favorite.
- Numbers: thousands separators; durations `m:ss` (or `h:mm:ss`); dates local.
- Lyrics are content: preserve line breaks, do not auto-capitalize, do not
  truncate a line in the lyric view.

## 12. Responsive patterns

- Sidebar: 240 → 64 at ≤ 1100; labels removed, icons and tooltips remain.
- Header: search 280 → 420 expanded; at ≤ 900 it shrinks to 200–240; account
  remains visible.
- Home: preview hides at ≤ 760; quick actions 2 × 2; rails scroll horizontally.
- Library: action row wraps search below at ≤ 680; album column hides; duration
  stays.
- Player: Split may become single column at ≤ 820 (cover top, lyrics below with
  internal scroll); Poster/Classic fall back to Split. Docked bar at ≤ 560;
  hide mini info, then volume.
- Settings: dialog becomes near-full-window at ≤ 760; nav becomes a horizontal
  category strip or a select; content scrolls.
- Never shrink the player cover below 150; hide secondary columns instead.

## 13. Review rubric

Score each item 0–2. Ship at 90%+ with no zero in accessibility or states.

| Area | What good looks like |
|---|---|
| Hierarchy | One hero; page title → section → row is obvious at a glance |
| Chrome | 240/64 sidebar, 64 header, 82 bar; transport always reachable |
| Color | Near-black dark theme; neutral accent; cover color optional and subtle |
| Type | Consistent scale; CJK readable; no 10 px body text |
| Density | 68 rows, 180 cards, 14–24 gaps; no cramped or bloated areas |
| Player | Split/Poster/Classic specs followed; cover and lyric proportions correct |
| Lyrics | Active line clear, inactive readable, edge fade, click-to-seek |
| States | Empty/loading/error/offline/disabled all designed |
| Motion | 150/300/500/600 scale; no gratuitous loops; reduced motion works |
| Accessibility | Contrast, focus, labels, keyboard, 200% text |
| Performance | Static backdrops, bounded effects, no per-frame blur/shader |
| Originality | No copied third-party layout, icon font, cover or brand identity |

## 14. Boundaries

- This is a visual system, not a component library dump. Implement only what the
  page needs.
- Do not add behavior, API calls, scanning, storage or playback logic to satisfy
  a visual state; use static placeholder data.
- Do not bundle MiSans, the custom icon font, cover art or brand marks. Reference
  them by license, not by file.
- Do not clone another player’s exact layout, controls or branding. The three
  layouts here are described by proportion and intent, not copied markup.
