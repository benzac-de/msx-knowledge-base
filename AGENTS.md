---
title: AGENTS — LLM Usage Guide for the MSX Knowledge Base
category: Meta
summary: Comprehensive guide for an LLM consuming this KB to answer MSX questions and build valid MSX JSON pages.
---

# AGENTS — LLM Usage Guide for the MSX Knowledge Base

This document tells **you** — the LLM reading this file — how to use the MSX Knowledge Base effectively. Read it first: it carries the binding rules, the mental model, and a question-to-file map (Section 8) that answers most routing questions on its own. Then fetch individual topic files on demand — do not try to hold the whole KB in context. If Section 8 doesn't cover your question, list every file with your client's `index` tool, or fetch `llms.txt` for the same listing with a one-line description per file (the fallback when your client has no file-listing tool). This guide is a map: the exhaustive property tables, per-feature since-versions, and edge cases live in the files it points to.

> **Prime directive:** For the MSX JSON API surface — property names, allowed values, action strings, color names, icon names, version numbers, object structure — if it is not in this KB, say so; do not guess or invent it. See [Common Misconceptions](reference/common-misconceptions.md) for plausible-sounding things that do **not** exist.
>
> **This restriction is scoped to the MSX API surface, not to every question you're asked.** Two things are explicitly not bound by "stay inside the KB":
> - **General web/hosting knowledge.** How to host a JSON file with CORS enabled, the difference between CORS and mixed-content policy, general HTML/CSS/JS — these are ordinary web-development topics; answer from your general knowledge. This KB documents one concrete hosting recipe (Node.js + `http-server --cors`, see [Quick Start Guide](overview/quick-start-guide.md)) as a worked example, not the only valid answer. GitHub Pages, any static host with CORS headers, a cloud function, nginx are all equally valid — say so rather than steering the user back to the documented recipe.
> - **Freely-authored content values.** Headlines, labels, descriptions, and image/video URLs inside a page are whatever the requester wants (or you invent for a demo) — not something to "look up". Only the JSON *structure* around them must come from the KB.
> - **General streaming/player technology.** What HLS/DASH adaptive streaming is, how M3U/PLS/MRSS are structured as generic formats, what Video.js/Dash.js/Hls.js do as libraries — ordinary streaming-technology topics; answer from general knowledge. Only MSX's own integration mechanism — which action prefix plays a given source, how MSX parses/imports these files, which player engine MSX selects — must come from this KB (see [M3U/PLS Files](extended-api/m3u-pls-files.md), [MRSS Feeds](extended-api/mrss-feeds.md), [YouTube/Vimeo & Co.](extended-api/youtube-vimeo-co.md), [Play Plugin](experts-api/plugins/play-plugin.md)).

**A convention used throughout Section 2:** each object ends with an **All Properties** list. Those lists are exhaustive — if a property is not in one, it does not exist on that object. The bullets above each list only cover the most commonly used or most easily misused properties; for the type, default, and since-version of every property, follow the linked reference page.

---

## 1. Mental Model of MSX

**Media Station X (MSX)** is a TV application (and mobile app) that renders its entire UI from JSON served over HTTP. The app itself contains no hardcoded content — menus, content grids, videos, images, and actions are all described in JSON files hosted on any web server with CORS enabled. MSX is built on the **TVX Framework**, which handles all platform-specific implementation on top of plain web technology:

```
JSON Document  (videos · images · links · audio · text · plugins · playlists)
  → Media Station X   (Manager · Controls · Content · Backend · Scenes · Styles)
  → TVX Framework     (Core · UI · Auth · Platform · FX · Interaction · Surface)
  → HTML5 · CSS3 · JavaScript · jQuery
  → Samsung · LG · Android TV · Fire TV · Web · Windows · macOS · iOS · Xbox
```

**Key facts:**

- MSX is a white-label app: the app is a shell, all content and structure comes from JSON.
- JSON files must be reachable over HTTP/HTTPS with CORS enabled.
- The engine is **backward-compatible**: JSON valid in `0.1.0` still runs in `0.1.166`. No breaking changes.
- Forward compatibility is **not guaranteed**: newer features (e.g. `compress`) may display incorrectly or not at all on older clients. Always state the minimum version for any feature you use.
- This KB covers MSX app versions `0.1.0`–`0.1.166`.

**Three version systems (do not confuse them):**

| System | Current version | Has per-feature changelog? |
|---|---|---|
| **MSX app** | `0.1.0`–`0.1.166` | **Yes** — every "Since Version" column in this KB refers to this |
| TVX Framework | `0.1.35` | No — mentioned once in sources, no version history |
| TVX Plugin Library | `0.0.79` | No — mentioned once in sources, no version history |

Only the **MSX app version** has documented per-feature minimum versions. For the other two, no version-specific differences exist in the sources — do not invent any.

---

## 2. JSON Hierarchy: Start → Menu → Content → Item

```
Start Object (start.json)
  └── parameter — exactly one of:
        ├── "menu:{URL}"    → Menu Root Object
        │                        └── menu[] → Menu Item Object
        │                                       └── data: URL or embedded Content Root Object ↓
        └── "content:{URL}" → Content Root Object   [directly, no menu]
                                 ├── template + items[]  (templated) → Content Item Object
                                 └── pages[]             (manual)    → Content Page Object
                                                                          └── items[] → Content Item Object
```

**Navigation overflow.** In every view — menu, content, and panel alike — navigation wraps at the edges: pressing left on the leftmost item jumps to the rightmost, and so on for right/up/down.

For content loaded via a menu, menu and content form **one continuous left/right loop** by default, which is intuitive because the menu sits immediately left of the content: right from the menu enters the content's first item, left from the menu enters its last, and reaching the content's own edge continues the loop back into the menu.

Set `wrap: true` on the Content Root Object (since `0.1.102`; a Content Page Object's own `wrap` overrides it) to confine left/right within the content instead — e.g. so an on-screen keyboard doesn't lose focus into the menu. `back` always reaches the menu regardless of `wrap`/`type`; it is the universal fallback. Whether left/right *also* reaches the menu depends on both: for `"pages"`, `wrap: true` on the root or on the specific first/last page leaves only `back`; for `"list"`, the same holds if the root or *every* page is wrapped — if only some are, left/right still reaches the menu from any unwrapped page, which may require navigating up/down to it first.

See [Content Root Object](main-api/content/content-root-object.md), [Content Page Object](main-api/content/content-page-object.md).

### Start Object (`start.json`)

Mandatory: `name` (string), `version` (string — **your app's own** version, not the MSX app version), `parameter` (string, must start with `menu:` or `content:`).

```json
{
    "name": "My MSX App",
    "version": "1.0.0",
    "parameter": "menu:https://example.com/msx/menu.json"
}
```

`parameter` supports the `{SERVER}` (since `0.1.65`) and `{PREFIX}` (since `0.1.97`) placeholders — **only in this one file**; every other JSON file needs full URLs. Since `0.1.97` it can also start with `redirect:` (e.g. `redirect:{PREFIX}{SERVER}/msx/start.php`) to load the actual Start Object from elsewhere.

Two further optional properties: `welcome` (string, since `0.1.117`) sets this start parameter as the app's welcome pages; `launcher` (object, since `0.1.164`, `icon`/`image`/`color` only) sets up its own tile in the settings and setup-completion panel — a deliberate subset of the separate, older Launcher MSX service's richer `launcher` object, see [Glossary: Launcher MSX](reference/glossary.md#launcher-msx).

**All Properties:** `name`, `version`, `parameter`, `welcome`, `launcher`.

See: [Start Object](main-api/start/start-object.md)

### Menu Root Object

A JSON object with a `menu` array of Menu Item Objects — the property is named `menu`, **not** `items`.

```json
{
    "headline": "My App",
    "menu": [
        { "label": "Home", "icon": "home", "data": "https://example.com/msx/home.json" },
        { "label": "Videos", "icon": "movie", "data": "https://example.com/msx/videos.json" }
    ]
}
```

Most properties here are **shared with the Content Root Object below**, since both can serve as the Start Object's `menu:`/`content:` target (marked *shared*):

- `name`, `version` (*shared*): shown in the About panel — only when this data is loaded at startup.
- `reference` (*shared*, since `0.1.134`): load the actual data from another URL at runtime; most other properties are then ignored.
- `reuse`, `cache`, `restore`, `refocus` (*shared*): lifecycle/focus-caching flags. `reuse`/`cache`/`restore` default `true`; `refocus` (since `0.1.153`) requires the (non-key) `flag` property to also be set.
- `background`, `transparent` (*shared*): the general page-background chain and its dimming override — see [Best Practices → `transparent` and background images/videos](reference/best-practices.md#transparent-and-background-imagesvideos).
- `extension`, `dictionary` (*shared*): a small top-right label on each content screen (overridden by the content's own `extension`), and a URL to a UI-translation dictionary file.
- `options` (*shared concept*, since `0.1.120`): a Content Page Object (since `0.1.130` also a Content Root Object) shown as a panel on menu-button press — ignored if the selected menu item has its own `options`.
- `action`, `data`, `ready` (*shared*): Start Action and the `ready` object — see Section 3.
- `logo`, `logoSize` (**Menu Root only**, since `0.1.60`/`0.1.130`): a logo image instead of `headline`.
- `style` (**Menu Root only**, since `0.1.142`): `"default"` | `"flat"` | `"flat-separator"` | `"overlay"` | `"overlay-separator"`.

**All Properties:** `name`, `version`, `reference`, `flag`, `reuse`, `cache`, `restore`, `refocus`, `transparent`, `style`, `logo`, `logoSize`, `headline`, `background`, `extension`, `dictionary`, `menu`, `action`, `data`, `ready`, `options`.

See: [Menu Root Object](main-api/menu/menu-root-object.md)

### Menu Item Object

One entry in a Menu Root Object's `menu` array.

- `type`: `"default"` (default) | `"separator"` (grouping line — can carry its own `label`; line color via `lineColor`, since `0.1.142`) | `"settings"` (hidden feature opening the app's native Settings screen — see [Settings Menu Item](experts-api/hidden-features/settings-menu-item.md)).
- `icon` / `image`: `image` (since `0.1.130`) overrides `icon` if both are set.
- `label`: the item's text (supports inline expressions).
- `extensionIcon` / `extensionLabel`: shown on the item's right side; `extensionLabel` is only used if `extensionIcon` is unset.
- `focus` / `execute` (since `0.1.120`): initial focus, and — via [Execute Property](experts-api/hidden-features/execute-property.md) — auto-entering this item's content on load.
- `options` (since `0.1.120`): a panel shown when this item is selected and the menu button is pressed.
- `data`: references the content to display — see the critical rule below.

**Critical `data` rule:** `data` accepts exactly two forms:

1. **URL string:** `"data": "https://example.com/msx/content.json"` — MSX fetches it and expects a Content Root Object.
2. **Inline Content Root Object:** `"data": { "type": "pages", "template": {...}, "items": [...] }` — embedded directly.

> **Common error:** Do NOT use `"data": "content:https://..."` for a menu item. The `content:` prefix is **action** syntax (for a *content item's* `action` property), not a menu `data` value. **Menu items have no `action` property of their own.**

**All Properties:** `id`, `type`, `display`, `enable`, `focus`, `execute`, `transparent`, `icon`, `image`, `label`, `background`, `extensionIcon`, `extensionLabel`, `lineColor`, `data`, `options`.

See: [Menu Item Object](main-api/menu/menu-item-object.md)

### Content Root Object

The top-level content container, with two mutually exclusive layout modes.

**Templated** — all items the same size, positioned automatically:

```json
{
    "type": "pages",
    "template": { "type": "separate", "layout": "0,0,2,4", "icon": "msx-white-soft:movie" },
    "items": [
        { "title": "Video 1", "action": "video:https://example.com/v1.mp4" },
        { "title": "Video 2", "action": "video:https://example.com/v2.mp4" }
    ]
}
```

**Manual** — items placed on a grid per page:

```json
{
    "type": "list",
    "pages": [
        { "items": [ { "layout": "0,0,12,1", "type": "button", "label": "Item 1", "action": "info:Hello" } ] }
    ]
}
```

- `type`: `"pages"` (horizontal, default) or `"list"` (vertical).
- `template`+`items` and `pages` are mutually exclusive; `pages` is ignored if `template`+`items` are set.
- **Grid: 12 × 6** for both types; panels always **8 × 6**. With `compress: true` (since `0.1.123`): **16 × 8** (panels **10 × 8**). `type` does not change the grid — it changes how pages are presented and transitioned between. In both cases the view always transitions to the page containing the focused item (horizontal for `"pages"`, vertical for `"list"`), even if that item was already visible.
  - `"pages"`: only the active page is visible; others are fully off-screen. A page indicator (one dot per page) sits at the bottom.
  - `"list"`: pages are stacked vertically with the gaps between them removed, so later pages can already peek into view before you focus an item on them — a preview `"pages"` never gives. Scroll indicators (not a page indicator) appear when there is more to scroll to.
- **Which to pick?** Both work mechanically; `"list"` fits classic scrollable lists and sees more real-world use, `"pages"` fits one-off screens that fit on a single page. See [Best Practices](reference/best-practices.md#best-practices-learned-from-building-real-pages).

`name`/`version`, `reference`, `reuse`/`cache`/`restore`/`refocus`, `background`/`transparent`, `extension`/`dictionary`, `options`, and `action`/`data`/`ready` all work exactly as on the Menu Root Object above. Specific to the Content Root Object:

- `wrap` (since `0.1.102`), `important` (since `0.1.58`): `wrap` blocks left/right navigation into the menu at the content's edges; `important` forces the content `headline` to display even when loaded via a menu (otherwise the menu item's `label` is used).
- `header`, `footer`, `overlay`, `underlay`, `inserts`: individually configured Content Page Objects layered onto **templated** content — a fixed header/footer row (since `0.1.53`), a page over/under every page (`overlay` `0.1.110`, `underlay` `0.1.112`), or pages inserted at specific positions (`inserts`, `0.1.156`). All five require `template`+`items`, otherwise ignored.
- `caption` (since `0.1.130`): customizes the `options` panel's lower-right label.
- `preload` (since `0.1.92`), `preselect` (since `0.1.160`): preload the adjacent page's content (images, live updates); execute page/selection actions *before* rather than after a scroll animation.
- `navigationOffset`, `navigationSpan` (since `0.1.164`): align list scroll indicators to a screen area — only for `type: "list"` content or in a panel.
- **Structural limits:** `pages[]` caps at **56** (`"pages"`) or **1024** (`"list"`); a page's `items[]` caps at **256** (every `type` counts, including `"space"`). `header`/`footer`/`overlay`/`underlay` are independent single pages and don't count. Exceeding a ceiling is not fatal: MSX shows a warning and ignores everything beyond it.

**All Properties:** `name`, `version`, `reference`, `flag`, `reuse`, `cache`, `restore`, `important`, `wrap`, `compress`, `preselect`, `refocus`, `transparent`, `type`, `preload`, `headline`, `background`, `extension`, `dictionary`, `template`, `items`, `pages`, `header`, `footer`, `inserts`, `overlay`, `underlay`, `action`, `data`, `ready`, `options`, `caption`, `captionUnderlay`, `navigationOffset`, `navigationSpan`.

See: [Content Root Object](main-api/content/content-root-object.md)

### Content Page Object

One page within a Content Root Object's `pages[]` (manual layout) — or, since `0.1.53`/`0.1.110`/`0.1.112`/`0.1.156`, a `header`/`overlay`/`underlay`/insert page attached to templated content. Not used at all for pure templated content without those.

- `items` (**mandatory** for a `pages[]` entry): the Content Item Objects on this page.
- `wrap` (since `0.1.102`), `background`/`transparent`, `options`/`caption`, `navigationOffset`/`navigationSpan` (since `0.1.164`): the Content Root versions, scoped to this page. `wrap` only matters on the first/last page (`"pages"`) or on every page (`"list"`) — no effect on overlay/underlay pages or in a panel.
- `display`, `important` (since `0.1.110`): `important` controls whether a corresponding overlay/underlay page is hidden while this page is active — and an overlay/underlay page with `important: true` is never hidden.
- `area`, `position`, `template` (all since `0.1.156`): insert-page placement — only take effect inside the Content Root's `inserts` array. Here `template` overrides properties of the *integrated content items*, not the page's own.
- `action`, `data` (since `0.1.112`, hidden feature): the Page Action, executed when this page becomes active — see [Page Action](experts-api/hidden-features/page-action.md).

**All Properties:** `display`, `important`, `wrap`, `compress`, `transparent`, `headline`, `background`, `area`, `offset`, `position`, `template`, `items`, `action`, `data`, `options`, `caption`, `captionUnderlay`, `navigationOffset`, `navigationSpan`.

See: [Content Page Object](main-api/content/content-page-object.md)

### Content Item Object

One cell/tile on the grid.

- `layout`: `"col,row,width,height"` (e.g. `"0,0,4,3"`) — **required** for every item inside a Content Page Object's `items[]`. For templated items, only the `template` needs a `layout` (and only its `w`/`h` are used); individual items in that `items[]` must **not** set their own — it has no effect there.
- `type`: `"default"` | `"separate"` | `"teaser"` | `"button"` | `"space"` | `"control"`.
- `action`: executed when the item is selected.
- Text (all support inline expressions): `headline`/`text` upper-left, `title`/`titleHeader`/`titleFooter` lower-left, `badge` upper-left (overlapping `headline`/`text` — see [Best Practices](reference/best-practices.md#tagstampbadge--short-single-line-text-and-default-colors-that-already-fit)), `tag` upper-right, `stamp` lower-right.
- `image`, `icon`, `color`: visual properties.
- `background` — **narrower than it looks: only applies if this item's own `action` uses the `audio:` prefix** (`audio:{URL}`, `audio:plugin:{URL}`, `audio:resolve:...`, `audio:auto:...` — any `audio:`-prefixed variant counts, see the general prefix-family rule in Section 3). It sets the backdrop shown while that item's audio plays, and does nothing for a `video:`-prefixed or any other action. Do not confuse it with the general page-background chain on Content Root/Content Page/Menu Root/Menu Item (also called `background`, but unconditional there). For a background image on a video/plugin item's page, set `background` on the Content Root or Content Page instead.
- `enumerate`, `group` (since `0.1.92`): playlist/slideshow auto-advance. Enumerated items (`enumerate: true` — the default for templated items, `false` otherwise) execute in a row and show a `(1/10)`-style position label in the headline; `group` (requires `enumerate: true`) splits the counter per group and can add its own label after the position. Full playlist-formation recipe: [Best Practices → The player UI shows for several seconds by default](reference/best-practices.md#the-player-ui-shows-for-several-seconds-by-default-when-starting-videoaudio--several-mechanisms-shorten-or-suppress-it).
- `live` (object, since `0.1.70`): periodically updates this item's visual properties (live data, playback progress, countdowns) and fires a one-shot action on state transitions. Combined with `resume:key` (since `0.1.74`), a `type: "playback"` live object also shows the *resume* progress — the saved position from a previous session — on the `progress` bar while the item sits idle, before it is ever selected. See [Live Object](experts-api/live/live-object.md) and the deep dives in [Cookbook](reference/cookbook.md#live-data-epg-progress-countdowns).
- `selection` (object, since `0.1.110`): runs an action the moment this item receives focus — auto-preview, backdrop loading, updating another item. See [Selection Object](experts-api/selection/selection-object.md).
- `options` (since `0.1.120`): a panel shown when this item is selected and the menu button is pressed. Highest priority in the `options` fallback chain — see [Glossary: Options](reference/glossary.md#options).
- `properties` (object): the **Extended Properties bag** — a large, separate set of advanced, mostly player/plugin-facing properties (`resume:key`/`resume:position`/`resume:context`, `trigger:{TRIGGER_KEY}`, `control:load`, `tizen:*`, `html5x:*`, `image:*`, player-button overrides) that do **not** live at the top level: they are nested inside this one object, e.g. `"properties": { "resume:key": "url" }`. Not every `key:value` inside it is MSX-defined — some are free-form data a specific plugin reads (see [Common Misconceptions](reference/common-misconceptions.md#extended-properties-the-properties-bag)). See [Extended Properties](experts-api/special/extended-properties.md).

**All Properties:** `id`, `type`, `key`, `layout`, `area`, `offset`, `display`, `enable`, `focus`, `execute`, `enumerate`, `compress`, `decompress`, `shortcut`, `round`, `break`, `group`, `color`, `title`, `titleHeader`, `titleFooter`, `label`, `icon`, `iconSize`, `headline`, `text`, `alignment`, `truncation`, `centration`, `separation`, `tag`, `tagColor`, `badge`, `badgeColor`, `stamp`, `stampColor`, `progress`, `progressColor`, `progressBackColor`, `wrapperColor`, `image`, `imageFiller`, `imageWidth`, `imageHeight`, `imageOverlay`, `imagePreload`, `imageLabel`, `imageColor`, `imageScreenFiller`, `imageBoundary`, `playerLabel`, `background` (`audio:`-prefix actions only, see above), `extensionIcon`, `extensionLabel`, `action`, `data`, `properties`, `live`, `selection`, `options`.

See: [Content Item Object](main-api/content/content-item-object.md)

---

## 3. Action String Syntax

All actions are strings. They appear in the `action` property of:

- **Content items** — executed when the item is selected (main use case).
- **Content Page Objects** — executed when the page becomes active (hidden feature, since `0.1.112`). See [Page Action](experts-api/hidden-features/page-action.md).
- **Content Root / Menu Root Objects** — the Start Action, executed once when data is loaded at startup (hidden feature, since `0.1.0`; not the `start` Main Action at `0.1.30`, which sets a *new* start parameter). See [Start Action](experts-api/hidden-features/start-action.md).
- **Content Root / Menu Root Objects, separately** — a `ready` object (`action` + optional `data`, since `0.1.142`) fires every time that content/menu becomes freshly displayed, including in a panel — not once at startup like the Start Action, but on **every** appearance, whether freshly fetched, served from `cache`, or kept via `reuse`. Use `ready` for behavior that must re-arm each time (e.g. re-registering `trigger:` actions). See [Glossary: Ready](reference/glossary.md#ready).
- **Live objects** (since `0.1.70`) — fires when a `type: "setup"` live object's item becomes visible, or when a `type: "playback"` object in state `"running"` is executed (instead of the item's own `action`). An override inside `coming`/`running`/`over` fires on entering that state instead of the base `action`. See [Live Object](experts-api/live/live-object.md).
- **Selection objects** (since `0.1.110`) — fires the moment the item receives focus. See [Selection Object](experts-api/selection/selection-object.md).
- **Server response objects** — `response.data.action`, returned by `execute:{URL}` calls.

> **Menu items have no `action` property** — they associate content exclusively via `data`.
>
> **Several Extended Properties also expect an action string as their value** — `trigger:{TRIGGER_KEY}`, `button:{BUTTON_ID}:action`, `control:action`, `image:trigger`, `image:rotation:trigger`. Unlike the object-level `action`/`data` pairs above, these "property actions" **cannot** carry an action-related `data` property — use `execute:fetch:{URL}` if the action needs data.

### Basic Action Patterns

`{URL}` and `{TEXT}` are placeholders — substitute the real value without braces.

| Pattern | Since | Notes |
|---|---|---|
| `video:{URL}` | 0.1.0 | Play video |
| `audio:{URL}` | 0.1.0 | Play audio (no video screen) |
| `image:{URL}` | 0.1.0 | Show image |
| `content:{URL}` / `content:data` | 0.1.0 | Load content page (from URL / from the item's `data`) |
| `panel:{URL}` / `panel:data` | 0.1.0 | Load content in a panel |
| `menu:{URL}` / `menu:data` | 0.1.0 | Load menu |
| `playlist:{URL}` / `playlist:data` | 0.1.0 | Load playlist — see the note below |
| `slideshow:{URL}` / `slideshow:data` | 0.1.0 | Load slideshow — see the note below |
| `link:{URL}` / `link:window:{URL}` | 0.1.0 | Open link in-app / externally |
| `info:{TEXT}` / `success:` / `warn:` / `error:` | 0.1.0 | Message overlays |
| `focus:{ID}` / `focus:index:{INDEX}` | 0.1.0 | Focus item by ID / by index (0 = first) |
| `start` | 0.1.30 | Set a new start parameter (payload in `data`) |
| `execute:{URL}` | 0.1.30 | Server-side action — POSTs `data`, server returns `response.data.action` |
| `video:plugin:{URL}` / `audio:plugin:{URL}` | 0.1.40 | Plugin playback |
| `reload` / `reload:content` / `reload:menu` / `reload:panel` | 0.1.58 | Reload app / current content / menu / panel |
| `update:content:{ID}` / `update:menu:{ID}` / `update:panel:{ID}` | 0.1.58 | Update an item by ID (new properties in `data`) — see mistake 10 in Section 9 |

For the full catalog with every modifier and version detail, see [Actions](main-api/common/actions.md) and [Actions Reference](reference/actions-reference.md). Replace Actions, the Start Action, and the Resolve Action are hidden features with their own since-versions, not Main Actions — see [Actions Reference](reference/actions-reference.md#hidden-feature-actions-replace-start--resolve).

> **`playlist:`/`slideshow:` items must have an `action`.** The `data` for all four `playlist:`/`slideshow:` variants above uses the same Templated Items shape as the Content API (`template`+`items`, or a plain `items` array) — but a playlist/slideshow auto-executes each item's `action` in turn instead of waiting for a selection, so every item needs one: set directly on the item, or once on the `template` object so every item inherits it. An item with neither is simply skipped — but if **every** item in the payload lacks an action, MSX shows a warning instead of silently doing nothing. See [Actions Reference → Basic Actions](reference/actions-reference.md#basic-actions).

> **General rule for reading `audio:`/`video:`-conditioned properties and behaviors:** wherever this KB says a property or behavior is only valid "if the content action is `audio:{URL}`" (or `video:{URL}`), read that as **the whole prefix family**, not only the plain URL form — `audio:plugin:{URL}`, `audio:resolve:...`, `audio:auto:...` and every other `audio:`-prefixed variant count equally (likewise for `video:`). The distinguishing factor is always the prefix, never which mechanism started playback. This applies KB-wide, e.g. to the Content Item Object's `background` in Section 2.

### Multi-Action Syntax (since `0.1.58`)

Wrap several actions in `[...]`, separated by `|`, to run them from a single `action` property:

```
[reload:content|info:Action executed.]
```

- Starts with `[`, ends with `]`; actions separated by `|`.
- Each action may use the shared `data` property of the triggering item.
- A literal `|` **inside** an action (e.g. in a message text) needs the `{pipe}` keyword: `[reload:content|info:[P{pipe}I{pipe}P{pipe}E]]`.
- An empty multi-action `[]` is a no-op.
- Each action executes independently — the bracket is not transactional. If one fails, warns, or is a no-op (e.g. an internal action unsupported on the client's MSX version), the rest still run.

### `data` Action — Multi-Action Alternative with Separate Data (since `0.1.58`)

The second way to run several actions. Instead of one shared `data`, each action carries its **own** `action`/`data` pair inside an `actions` array:

```json
{
    "action": "data",
    "data": {
        "actions": [
            { "action": "update:content:overlay:info", "data": { "text": "Overlay info text updated." } },
            { "action": "update:content:underlay:info", "data": { "text": "Underlay info text updated." } },
            { "action": "info:Action executed." }
        ]
    }
}
```

Which to use:

- **`[...]` syntax:** simpler, but *all* actions share the triggering item's `data` (see [Actions](main-api/common/actions.md#extended-actions)). Use it when the actions need the same data or none — though one shared `data` object can deliberately feed several `update:` actions at once, since each picks out only the properties it already has (see [Actions Reference → `data` Action](reference/actions-reference.md#data-action-since-0158)). A literal `|` needs `{pipe}`.
- **`data` action:** more verbose, but each action carries its own `data` — required as soon as they need different data. No `|` separator, so a literal `|` needs no escaping.

### Request Actions

Many URL-accepting actions support `request:interaction:{DATA_ID}@{URL}` instead of a plain URL, enabling interactive plugin requests (e.g. `video:resolve:request:interaction:{DATA_ID}@{URL}`). This pattern is **`0.1.82`**, not the blanket `0.1.160+` floor that otherwise applies to internal-only actions without their own since-version (see [Feature Minimum Versions](#feature-minimum-versions)) — except when combined with a prefix that has a later since-version itself (e.g. `playlist:auto:...` or Resolve, which keep their own higher floor). The sibling `request:player:{DATA_ID}` is a separate mechanism and stays at `0.1.160+`. See [Actions Reference](reference/actions-reference.md#request-actions-requestinteraction-is-0182-requestplayer-remains-01160), and [Resolve Action](experts-api/hidden-features/resolve-action.md) for the `0.1.107` hidden feature.

---

## 4. Inline Expressions — Complete Overview

Inline expressions only resolve inside **free-text, visual-output string properties** — `label`, `title`, `titleHeader`, `titleFooter`, `headline`, `text`, `tag`, `badge`, `stamp`, `caption` (the options-panel caption, since `0.1.130`), the text part of message actions like `info:`/`success:`, and `extension` (the small top-right label on Menu Root and Content Root Objects). Each property reference in this KB states explicitly whether it supports them — do not assume it for a string property just because it holds text.

**Not every string-valued property qualifies.** Properties with a fixed, enumerated set of values (`type`, `imageFiller`, `alignment`, `iconSize`) or holding structural/technical strings (URLs, IDs, action strings, colors, icon names) do **not** support them — they are parsed as literal values, not rendered as visual text.

Expressions always start with `{` and end with `}`. **Nesting is not supported** — but prefix combinations like `{txt:{COLOR}:num:...}` are valid and distinct from nesting.

### Standard Inline Expressions (since `0.1.0` unless noted)

| Expression | Since | Purpose |
|---|---|---|
| `{sp}` / `{tb}` / `{br}` | 0.1.0 | Non-breakable space / tab / line break |
| `{ico:{ICON}}` | 0.1.0 | Icon, e.g. `{ico:home}` |
| `{ico:{COLOR}:{ICON}}` | 0.1.0 | Colored icon, e.g. `{ico:msx-red:home}` |
| `{txt:{COLOR}:{TEXT}}` | 0.1.0 | Colored text, e.g. `{txt:msx-blue:Hello}` |
| `{chr:{CHARACTER}}` / `{chr:{COLOR}:{CHARACTER}}` | **0.1.145** | Character entity, e.g. `{chr:copy}` → © |
| `{context:{PROPERTY}}` | 0.1.0 | Template context reference — see the rule below |
| `{unknown}` | 0.1.0 | Unknown expression → returned without braces |

**`{context:...}` rule:** only works inside the `data` property of a template object. It references a property of the templated item (`{context:label}` → that item's `label`). If the property is not on the item, the **template object's own properties** are checked next — so a value set on the template acts as a default for all items. If it is missing entirely, the expression stays unchanged; if its value is not a string, the expression is removed.

> **Note (since `0.1.110`):** a template object's `properties`, `live`, `selection`, and `options` also support `{context:{PROPERTY}}`. Nothing else does — in particular **not** the template's own `action`, `image`, `label` or any other property, even though they sit inside the same template object.

> **Different `template`, no `{context:...}` support:** the rule above concerns the Content Root Object's `template` (paired with `items[]`). A separate, unrelated `template` property also exists on the Content Page Object, used only by **insert pages** (since `0.1.156`, via the Content Root's `inserts` array) to override properties of the integrated content items — see [Content Page Object](main-api/content/content-page-object.md). `{context:{PROPERTY}}` explicitly does **not** work inside that insert-page `template`.

See: [Inline Expressions](main-api/common/inline-expressions.md)

### Number Inline Expressions (since `0.1.91`)

Convert numbers (millisecond timestamps or plain numbers) into formatted dates, times, durations, or numbers. Three forms:

| Form | Since | Example → output |
|---|---|---|
| `{num:{NUMBER}:{TYPE}:{FORMAT}}` | 0.1.91 | `{num:1548335764000:date:DD, MM d, yyyy}` → `Thursday, January 24, 2019`; `{num:5640000:duration:time:hh:mm:ss}` → `01:34:00`; `{num:now:date:...}` tracks the clock |
| `{num:{NUMBER}:formatter:{FORMATTER_ID}}` | **0.1.160** | `{num:1548335764000:formatter:day_full}` → `Thursday, January 24, 2019` |
| `{num:{NUMBER}:format[:{FORMAT}]}` | **0.1.160** | `{num:12345.12345:format}` → `12,345.12345`; `:format:0` → `12,345`. `{FORMAT}` also takes decimal digits + trimming (`format:02`) and custom separators (`format:.,0`) |

`DD` = weekday name and `MM` = month name — **not** day-of-month/month-number as in ISO conventions, see [Common Misconceptions](reference/common-misconceptions.md#date--time--duration-format-tokens). Colored variant for all forms: `{txt:{COLOR}:num:{NUMBER}:...}`.

The `date:`/`time:`/`duration:` tokens and the **14 `formatter:` IDs** are identical to the Live Inline Expressions — see [Live Formatter IDs](experts-api/live/live-inline-expressions.md#live-formatter-id) and the [Live Date/Time/Duration Format tables](experts-api/live/live-inline-expressions.md#live-date-format). Full `format:` syntax: [Number Inline Expressions](experts-api/hidden-features/number-inline-expressions.md#basic-number-format-expressions).

See: [Number Inline Expressions](experts-api/hidden-features/number-inline-expressions.md)

### Color Inline Expressions (since `0.1.91`, Hidden Feature)

Change the default text color for a *range* of text, as opposed to `{txt:{COLOR}:{TEXT}}`, which colors one fixed string:

- `{col:{COLOR}}` — sets the color for all following text until changed again
- `{col}` / `{col:default}` — back to the default color

See: [Color Inline Expressions](experts-api/hidden-features/color-inline-expressions.md)

### Dictionary Inline Expressions (since `0.1.120`)

Look up a value from the active dictionary (set via the `dictionary` property of a Content Root or Menu Root Object):

| Form | Since | Purpose |
|---|---|---|
| `{dic:{KEY}}` / `{dic:{KEY}|{DEFAULT_VALUE}}` | 0.1.120 | Value for key, or empty / the given default. Colored: `{txt:{COLOR}:dic:...}` |
| `{dix:{KEY}}{DEFAULT_VALUE}{dix}` | **0.1.123** | Wrapper form — allows a complex default containing other inline expressions. The closing `{dix}` may be omitted at the end of the string |
| `{dix:{KEY}|{INSERTION_1}|{INSERTION_2}}{DEFAULT_VALUE}{dix}` | **0.1.160** | Insertions referenced as `{#1}`, `{#2}` in the value or default; each referenceable only once |

See: [Dictionary Inline Expressions](experts-api/hidden-features/dictionary-inline-expressions.md)

### Live Inline Expressions (since `0.1.70`)

Used inside a `live` object's properties and in the `extension` property of Content Root and Menu Root Objects, to express timestamps, durations, and progress relative to a live object.

> **These are the only two places a Live Inline Expression resolves — nowhere else.** Putting `{now:...}` (or any other) directly in a regular item's `headline`/`title`/`text` does **not** work: it stays unresolved and literal on screen, even though those properties do support standard Inline Expressions. To use one on a plain content item, put it inside that item's own `live` object (any valid `type` works for `{now:...}` output — `"airtime"`/`"lifetime"` fit best for a simple clock, since they imply no schedule/playback context). The `extension` property is the sole exception that works **without** a `live` object — but only `{now:...}` makes sense there, since every other variant (`{creation:...}`, `{from:...}`, `{to:...}`, `{duration:...}`, `{progress:...}`, `{countdown:...}`, `{overflow:...}`) references a specific live object's own timing state.

- `{now:date:{FORMAT}}` / `{now:time:{FORMAT}}` / `{now:formatter:{FORMATTER_ID}}` — current time
- `{creation:...}`, `{from:...}`, `{to:...}` — live object timestamps, same `date:`/`time:`/`formatter:` sub-forms
- `{duration:...}`, `{progress:...}`, `{countdown:...}`, `{overflow:...}` — live progress values (`position:`, `time:{FORMAT}`, `text:{FORMAT}` sub-forms; `progress:`/`countdown:` also support `percentage:`)

> **Version note:** the `now:`/`creation:`/`from:`/`to:` family exists since `0.1.70`, but its `formatter:` sub-form only since **`0.1.160`** — do not use `{now:formatter:...}` when targeting `0.1.70`–`0.1.159`; use `date:`/`time:` instead. `duration:`/`progress:`/`countdown:`/`overflow:` have no `formatter:` sub-form at all.

Colored variant: `{txt:{COLOR}:{LIVE_INLINE_EXPRESSION}}` — omit the inner expression's outer braces, e.g. `{txt:msx-red:now:date:DD, MM d, yyyy}`.

See: [Live Inline Expressions](experts-api/live/live-inline-expressions.md). For a worked live clock built from `now:date:`/`now:time:` in a Menu Root Object's `extension`, see [Cookbook → Live data](reference/cookbook.md#live-data-epg-progress-countdowns).

### General Rules for Inline Expressions

1. **Placeholder notation vs. actual JSON:** in documentation, `{txt:{COLOR}:{TEXT}}` keeps its inner braces — they are placeholder labels, not optional. In actual JSON, substitute without braces: `{txt:msx-red:Hello}`, never `{txt:{msx-red}:{Hello}}`.
2. Unknown expressions return **without** braces: `{unknown}` → `unknown`. A typo therefore silently becomes plain text.
3. Nested expressions (e.g. `{txt:{ico:home}}`) are **not** supported.
4. Prefix combinations (`{txt:msx-blue:num:...}`, `{txt:msx-blue:dic:key}`) are explicitly supported and distinct from nesting.

---

## 5. Colors

Color properties accept standard CSS syntax (`"red"`, `"#ff0000"`, `"rgb(255,0,0)"`) **and** exactly 11 predefined `msx-*` identifiers. The `msx-*` colors are theme-aware and recommended:

`msx-red` · `msx-green` · `msx-yellow` · `msx-blue` · `msx-white` · `msx-gray` · `msx-black` · `msx-white-soft` (semi-transparent white) · `msx-gray-soft` (semi-transparent gray) · `msx-black-soft` (semi-transparent black) · `msx-glass` (almost-transparent white)

There is no `msx-orange`, `msx-purple`, `msx-cyan`, or any other `msx-*` color beyond these 11. Do not invent `msx-*` names.

See: [Colors](main-api/common/colors.md)

---

## 6. Version Model

### Backward Compatibility

The MSX engine is **fully backward-compatible**. Every JSON page that worked in `0.1.0` still works in `0.1.166`. There are no breaking changes.

### Forward Behavior (newer features on older MSX versions)

Using a newer feature on an MSX version that does not support it has one of two outcomes:

1. **Cosmetic / degraded:** the feature is ignored; the page stays usable but looks different (missing icons, wrong layout).
2. **Functionally broken:** content does not render at all, leaving a blank or non-functional page.

**Example — `compress` (since `0.1.123`):** `"compress": true` expands the content grid from 12×6 to 16×8. Items in the extended area (0-based column index ≥ 12 or row index ≥ 6) produce "out of range" warnings on pre-`0.1.123` versions and are not displayed, potentially leaving the page blank.

**Rule:** always state the minimum version for any feature you use, and warn the user when a layout-altering feature like `compress` would break or degrade on older versions.

### Feature Minimum Versions

Every property, action, and inline expression documented with an individual "since" version carries its own minimum MSX app version, stated on its dedicated page. The broad default: internal actions that are not also official Main Actions have no individually documented since-version — only the blanket `0.1.160+` floor.

**In practice this rarely matters** — nearly all platforms run the latest MSX version; only very old, no-longer-updatable Samsung TV models can be stuck below `0.1.160` (see [Versioning → How "Since Version" Annotations Work](reference/versioning.md#how-since-version-annotations-work)). A handful of internal action families do have their own tighter since-version anyway — see [Actions Reference → Internal-Only Actions Overview](reference/actions-reference.md#internal-only-actions--overview-require-01160) for the complete list. For a curated timeline of feature-introduction milestones, see the **Version Baseline** table in [Versioning](reference/versioning.md#version-baseline-010).

For exact version information on each property, always check the relevant API page. For the complete per-version changelog, see [Release Notes](overview/release-notes.md).

---

## 7. Plugin API — Three Access Forms

The Plugin API (custom video/audio or interaction plugins) is accessible in three equivalent forms:

| Form | Library loading | Key class names |
|---|---|---|
| Classical ES5 (global) | `<script src="//msx.benzac.de/js/tvx-plugin.min.js">` | `TVXVideoPlugin`, `TVXInteractionPlugin` |
| Classic Module (AMD/CommonJS) | `var tvx = require('tvx-plugin-module')` | `tvx.VideoPlugin`, `tvx.InteractionPlugin` |
| TypeScript Module | `import * as tvx from 'tvx-plugin-module'` | `tvx.TVXVideoPlugin` (type), `tvx.VideoPlugin` (instance) |

`TVXVideoPlugin` and `tvx.VideoPlugin` are the same API — only the access pattern differs. **Use ES5 for TV browser compatibility**; TV browsers often have limited ES module support.

**Event types** (handled in `handleEvent(data)`): app events (`app:suspend`, `app:resume`, `app:sleep`, `app:awake`, `app:resize`, `app:connect`, `app:disconnect`, `app:time`, `app:result`), video events (`video:load`, `video:play`, `video:pause`, `video:stop`, `video:seek`, `video:restart`, `video:speed`, `video:volume`), slider events (`slider:load`, `slider:play`, `slider:pause`, `slider:stop`, `slider:position`, `slider:rotation`), settings events (`settings:*`, one per app setting), and `custom:{EVENT_ID}` for plugin-to-plugin communication. Complete list with all payload properties: [Plugin Events Reference](experts-api/plugins/plugin-events-reference.md).

Plugin Library version: `0.0.79`; no version changelog exists for it. A `tvx-plugin-ux-module.min.js` variant adds effects/UI classes (and requires jQuery, unlike the dependency-free base module) — expert-level, not documented in this KB, see [Glossary: TVX Plugin Library](reference/glossary.md#tvx-plugin-library).

---

## 8. How to Find Information in This KB

**For practical or specific questions, check the curated reference docs before the raw source pages.** `reference/cookbook.md`, `reference/json-building-guide.md`, and `reference/settings-reference.md` carry detailed, practically important knowledge — worked patterns, rendering/UX behavior, edge cases — that came directly from the MSX developer and goes beyond what any single `main-api/`/`experts-api/` page states. Try these three first for anything beyond a plain property lookup; fall back to the raw pages for the authoritative structural reference.

| Question | File to load |
|---|---|
| What is MSX? General overview | `overview/home.md` |
| Setting up an MSX start URL | `main-api/start/start-object.md` |
| Start URL parameters (`?start=`, `secure`, `alias`, …) | `experts-api/special/url-parameters.md` |
| Menu structure | `main-api/menu/menu-root-object.md`, `main-api/menu/menu-item-object.md` |
| Content layout, grid, item types | `main-api/content/content-root-object.md`, `content-page-object.md`, `content-item-object.md` |
| Remembering / resuming playback position ("continue watching") | `experts-api/special/extended-properties.md` (`resume:key`/`resume:position`/`resume:context`) + [Cookbook deep dive](reference/cookbook.md#deep-dive--a-minimal-resume-playback-item-live_test_2-pattern) |
| An atmospheric **looping video/audio background** behind a transparent menu — **not** a simple static page background | `experts-api/plugins/background-plugin.md`, `backdrop-plugin.md`. A plain static background image is just the `background` property on Content Root/Menu Root (Section 2) — no plugin needed. |
| Adaptive/dynamic URL resolution at playback time (server picks a codec, mints a temporary link) | `experts-api/hidden-features/resolve-action.md` |
| Item-type/image/icon sizing & contrast pitfalls (`icon`+`image` combined, `"button"`/`"control"` height, `tag`/`stamp`/`badge` defaults, `imageOverlay`) | `reference/best-practices.md` |
| Why `layout` (not `offset`) decides what gets focused next | [Cookbook deep dive](reference/cookbook.md#deep-dive--layout-drives-navigation-offset-doesnt-list0json) |
| M3U/PLS vs. MRSS for playlist import | [Cookbook](reference/cookbook.md#m3upls-vs-mrss-two-different-playlist-import-mechanisms), `extended-api/m3u-pls-files.md`, `extended-api/mrss-feeds.md` |
| YouTube/Vimeo/Dailymotion/Twitch/Facebook/Wistia/SoundCloud | `extended-api/youtube-vimeo-co.md` |
| Available actions (full list) | `main-api/common/actions.md` + `reference/actions-reference.md` |
| Inline expressions (standard) · colors · icons | `main-api/common/inline-expressions.md` · `colors.md` · `icons.md` |
| Number formatting · color override expressions | `experts-api/hidden-features/number-inline-expressions.md` · `color-inline-expressions.md` |
| Dictionary — the **file** (UI translation) vs. the `{dic:...}` **expression** | `experts-api/special/dictionary-structure.md` vs. `experts-api/hidden-features/dictionary-inline-expressions.md` — two different things, both called "dictionary" |
| Live updates (scheduling, timers) | `experts-api/live/live-object.md`, `live-inline-expressions.md` |
| Live TV channel list / EPG guide backend (server-driven) | [Cookbook](reference/cookbook.md#deep-dive--the-live-channels--epg-backend-live_test_4), [TV Guide grid](reference/cookbook.md#deep-dive--the-tv-guide-epg-grid-backend-guidephp) |
| HTTP request/response mechanics for a JSON/service backend | `main-api/common/requests.md`, `responses.md` |
| Selection behavior on focus | `experts-api/selection/selection-object.md` |
| Compress grid (16×8 layout) | `experts-api/hidden-features/compress-property.md` |
| Plugin development (general) | `experts-api/plugins/video-audio-plugin.md`, `plugin-api-reference.md` |
| Building an interaction plugin (`handleEvent`/`handleData`/`handleRequest`) | `experts-api/plugins/interaction-plugin.md`, [Cookbook](reference/cookbook.md#deep-dive--building-an-interaction-plugin-plugin_test_2) |
| Building a video plugin (`MyPlayer`, progress polling) | `experts-api/plugins/video-audio-plugin.md`, `html5x-plugin.md`, [Cookbook](reference/cookbook.md#deep-dive--building-a-video-plugin-plugin_test_4-html5x) |
| Ready-made plugins: Panorama (wide sliding-pan images), Image (mixed playlist), Play (runtime player choice), Paging (large datasets), Input (text entry/logins/search) | `experts-api/plugins/panorama-plugin.md`, `image-plugin.md`, `play-plugin.md`, `paging-plugin.md`, `input-plugin.md` |
| HLS/DASH streams, or letting the user pick a player engine at runtime | `experts-api/plugins/play-plugin.md#available-players` |
| Preselecting or switching a subtitle/audio track | `experts-api/plugins/html5x-plugin.md` (`html5x:subtitle`/`html5x:audiotrack` to preselect; `panel:request:player:subtitle`/`audiotrack` to switch at runtime) |
| Google Drive, OneDrive, Dropbox as an MSX media source | `overview/showcases.md` + `experts-api/plugins/html5x-plugin.md` (the `index.json`/`{asset:id:{NAME}}` mechanism) |
| Browsing/hosting a plain HTTP directory listing (Node Browser MSX): file-naming conventions, directory listing + CORS setup, tabs | [Cookbook](reference/cookbook.md#deep-dive--node-browser-msx-browsing-a-plain-http-directory-listing), `overview/showcases.md#node-browser-msx`, `overview/tips-tricks.md#node-browser-msx`, `overview/tips-tricks.md#cloud-storage-service-tabs` |
| Plugin event types / `handleEvent` payloads | `experts-api/plugins/plugin-events-reference.md` |
| TypeScript types for JSON objects | `reference/type-definitions.md` |
| Step-by-step JSON building | `reference/json-building-guide.md` |
| Curated real examples by task | `reference/cookbook.md` |
| Practical building tips and rendering gotchas from real pages | `reference/best-practices.md` |
| Version model / compatibility | `reference/versioning.md` |
| Complete per-version changelog | `overview/release-notes.md` |
| Glossary of MSX terms | `reference/glossary.md` |
| What does NOT exist (avoid hallucinating) | `reference/common-misconceptions.md` |
| Native Settings screen (Immersive Mode, Rounded Style, Remote Control type, …) | `reference/settings-reference.md` |
| Real JSON examples (search by feature) | `examples/index.md` — then load the specific `.json` file |
| Platform support (which platform runs which version) | `overview/platform-support.md` |
| Legacy/outdated TV distribution packages | `overview/tips-tricks.md#distributed-versions` |
| Loading the latest MSX on an older install, or in a secure (`https://`) context | `overview/tips-tricks.md#web-version-loader` |
| Opening an external HTML5 game/site via `link:{URL}` safely | `overview/tips-tricks.md#external-html5-gamesapps` |
| Launching a native platform app/game (`system:{PLATFORM}:launch`) | `overview/tips-tricks.md#external-platform-gamesapps` |
| Getting a stable unique device ID | `overview/tips-tricks.md#unique-device-id` |
| Customizing a start parameter's tile in the Launcher MSX service | `overview/tips-tricks.md#launcher-msx` |
| Troubleshooting "status: 0" / CORS / certificate errors | `overview/tips-tricks.md#common-errors` |
| Known platform quirks (iOS storage, Android cookies/SameSite, UWP links, legacy Samsung storage) | `overview/tips-tricks.md#well-known-issues` |
| Recovering from a bad Layout/Scale/Zoom/Transform/Remote/Input change | `overview/tips-tricks.md#emergency-combination` |

**When building JSON:** start with `reference/type-definitions.md` for the exact type structure, then pick a matching real example from `examples/index.md` as a template. **Before generating any real JSON output — not just for an isolated lookup question — also load [Best Practices & Good to Know](reference/best-practices.md).** It covers non-obvious mistakes a correct read of the type reference alone won't catch: `icon`+`image` contrast problems, `imageOverlay` needed for `headline`/`text` legibility, `"button"` items needing enough height for `icon`+`label`, `tag`/`stamp`/`badge` color defaults that clash when overridden carelessly, and how to size image sources to the grid.

**Testing generated JSON:** any page can be tested instantly in the web version by base64-encoding it into the start URL — `https://msx.benzac.de/?start=menu:json:{BASE64_ENCODED_JSON}` (or `content:json:...` for a Content Root Object). Hand this to the user as a ready-to-click link, or embed it in an `<iframe>` for a live preview; this direct link is the default recommendation. For a user who wants to tweak the JSON themselves, the same payload also works via the Demo page (`https://msx.benzac.de/info/?tab=Demo&start=menu:json:...`), which opens an editor before launching — an expert option, not a substitute. **For JSON too large to embed in a URL**, skip base64 entirely: open the Demo page with no payload (`https://msx.benzac.de/info/?tab=Demo`), paste the plain JSON into its editor, and press Launch. All three mechanisms, plus the `secure`/`http://` guidance for resource links inside the JSON: [JSON Building Guide → Testing Your JSON](reference/json-building-guide.md#testing-your-json-in-the-web-version), [URL Parameters](experts-api/special/url-parameters.md).

---

## 9. Common JSON-Building Mistakes

The highest-impact, cross-cutting gotchas. For the full build-focused catalog (layout requirements, `{pipe}`, `{SERVER}`/`{PREFIX}`, mandatory Start Object properties, icon names, `space`-only pages, and more), see [JSON Building Guide → Common Mistakes](reference/json-building-guide.md#common-mistakes).

1. **`content:` prefix on a menu item's `data`:** `"data": "content:https://..."` does not work. Use a raw URL or an embedded Content Root Object. Menu items have no `action` property either — associate content only via `data`.

2. **Inventing `msx-*` color names:** only the 11 identifiers in Section 5 exist. `msx-orange`, `msx-purple` and friends do not. Standard CSS syntax is also accepted, but `msx-*` names cannot be invented.

3. **Placeholder notation vs. actual JSON:** in documentation `{txt:{COLOR}:{TEXT}}` keeps its inner braces — do not write `{txt:COLOR:TEXT}`. In actual JSON, substitute without braces: `{txt:msx-red:Hello}`, not `{txt:{msx-red}:{Hello}}`.

4. **Using `template`+`items` and `pages` together:** mutually exclusive — `pages` is ignored if `template`+`items` are present. If your items carry individual `layout` values, use `pages[].items[]`; a top-level `items` without `template` is silently ignored.

5. **`{context:...}` outside a template object's `data`:** it resolves only there (and since `0.1.110` in `properties`, `live`, `selection`, `options`). It does **not** work in standalone item properties, nor in the template's own `action`, `image`, or `label` — being inside the template object is not enough, the property itself must be one of the five.

6. **Expecting forward compatibility:** newer features silently degrade or break on older clients — always state the minimum version (Section 6).

7. **Assuming inline expressions work in any string property:** only free-text, visual-output properties resolve them, each individually documented as such. Fixed-enum properties (`type`, `imageFiller`, `alignment`, …) and structural strings (URLs, IDs, action strings, colors, icon names) never do.

8. **Generating too many pages/items for programmatically built content:** `pages[]` caps at 56 (`"pages"`) or 1024 (`"list"`), a page's `items[]` at 256. Generous, but a server-driven catalog can hit them — see [JSON Building Guide → Step 4](reference/json-building-guide.md#step-4--content-root-object-list-layout).

9. **A Live Inline Expression (`{now:...}`, `{from:...}`, `{duration:...}`, …) directly in a plain item's `headline`/`title`/`text`:** it silently stays literal. These only resolve inside a `live` object's own properties (any valid `type`; `"airtime"`/`"lifetime"` fit a plain clock best) or in `extension`, where only `{now:...}` makes sense. See Section 4.

10. **Expecting `update:content:{ID}`/`update:menu:{ID}` to set a property the item never had:** these only update properties already present (non-`null`) in the origin data — one that started unset silently stays unset. Exceptions that always work: `background`/`lineColor`/`options` for `update:menu:{ID}`; the `color`-type properties (`color`/`tagColor`/`badgeColor`/`stampColor`/`progressColor`/`progressBackColor`/`wrapperColor`) plus `live`/`selection`/`options` for `update:content:{ID}` — the same exemption the `live` object's own update mechanism has. Same reason both times: MSX only renders a dedicated element for a property when it is initially present. See [Actions](main-api/common/actions.md), [Common Misconceptions → Actions](reference/common-misconceptions.md#actions).

11. **Assuming a `template` object is layout-only:** a template is itself a [Content Item Object](main-api/content/content-item-object.md) and can carry any of that object's properties — `action`, `data`, `properties`, `live`, `selection`, `options` included, not just visual/layout ones. Templated items only specify what differs; everything else, including a whole `live` block or `properties` bag, is copied to every generated item. Worked example: [Cookbook → Deep dive — a minimal "resume playback" item](reference/cookbook.md#deep-dive--a-minimal-resume-playback-item-live_test_2-pattern).

12. **`playlist:`/`slideshow:` items without an `action`:** these auto-execute in sequence instead of waiting for a selection, so every item needs a resolvable `action` — set on the item, or once on the `template`. An item with neither is silently skipped; only if *every* item lacks one does MSX show a warning. See Section 3.

---

## 10. Authoritative Instruction

When answering MSX questions or generating MSX JSON:

- Every property, value, version number, action string, color name, or icon name you include **must appear in this KB**. If it does not, say you cannot find it.
- Prefer loading the specific KB file for a topic over answering from memory.
- Verify JSON structure against `reference/type-definitions.md` and cross-check against a real example from `examples/index.md`.
- Before finalizing any generated JSON, check it against [Best Practices & Good to Know](reference/best-practices.md) — a structurally valid page can still have real, visible problems (low-contrast `tag`/`stamp`/`badge` colors, an `icon` losing contrast against a bright `image`, a missing `imageOverlay`) that type-checking alone won't catch.
- Always state the minimum MSX app version for any feature you use.
- If in doubt, load more KB files. The KB is the authoritative source; this guide is a map to it.