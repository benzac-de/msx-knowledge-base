---
title: AGENTS — LLM Usage Guide for the MSX Knowledge Base
category: Meta
summary: Comprehensive guide for an LLM consuming this KB to answer MSX questions and build valid MSX JSON pages.
---

# AGENTS — LLM Usage Guide for the MSX Knowledge Base

This document tells **you** — the LLM reading this file — how to use the MSX Knowledge Base effectively. Load `llms.txt` first to discover all available files, read this guide next for the mental model and usage patterns, then fetch individual topic files on demand.

> **Prime directive:** For the MSX JSON API surface — property names, allowed values, action strings, color names, icon names, version numbers, object structure — if it is not in this KB, say so; do not guess or invent it. See [Common Misconceptions](reference/common-misconceptions.md) for plausible-sounding things that do **not** exist.
>
> **This restriction is scoped to the MSX API surface, not to every question you're asked.** Two things are explicitly not bound by "stay inside the KB":
> - **General web/hosting knowledge.** Questions like "how do I host a JSON file with CORS enabled," the difference between CORS and mixed-content policy, or general HTML/CSS/JS questions are ordinary web-development topics — answer with your general knowledge. This KB documents one concrete hosting recipe (Node.js + `http-server --cors`, see [Quick Start Guide](overview/quick-start-guide.md)) as a worked example, not as the only valid answer — GitHub Pages, any static host with CORS headers, a cloud function, nginx, etc. are all equally valid, and you should say so rather than steering the user back to only the documented recipe.
> - **Freely-authored content values.** Headlines, labels, descriptions, and image/video URLs inside an MSX JSON page are whatever the requester wants (or you invent for a demo) — they are not something to "look up" in the KB. Only the JSON *structure* around them (which property, which type, which action syntax) must come from the KB.

---

## 1. Mental Model of MSX

**Media Station X (MSX)** is a TV application (and mobile app) that renders its entire UI from JSON served over HTTP. The app itself contains no hardcoded content. Everything — menus, content grids, videos, images, actions — is described in JSON files hosted on any web server with CORS enabled. MSX itself is built on the TVX Framework, which handles all platform-specific implementation on top of plain web technology:

```
                            ╭───────────────╮
                            │ JSON Document │
                            ╰───────────────╯
                                    │
       Videos · Images · Links · Audio · Text · Plugins · Playlists
                                    │
                     ╔══════════════════════════════╗
                     ║       Media Station X        ║
                     ╠══════════════════════════════╣
                     ║ Manager · Controls · Content ║
                     ║  Backend · Scenes · Styles   ║
                     ╚══════════════════════════════╝
                                    │
                     ╔═════════════════════════════╗
                     ║        TVX Framework        ║
                     ╠═════════════════════════════╣
                     ║ Core · UI · Auth · Platform ║
                     ║  FX · Interaction · Surface ║
                     ╚═════════════════════════════╝
                                    │
                    HTML5 · CSS3 · JavaScript · jQuery
                                    │
 Samsung · LG · Android TV · Fire TV · Web · Windows · macOS · iOS · Xbox
```

**Key facts:**

- MSX is a white-label app. The app itself is a shell; all content and structure comes from JSON.
- JSON files must be reachable over HTTP/HTTPS with CORS enabled.
- The MSX engine is **backward-compatible**: JSON valid in `0.1.0` still runs in `0.1.166`. There are no breaking changes.
- Forward compatibility is **not guaranteed**: JSON using newer features (e.g. `compress`) may display incorrectly or not at all on older MSX versions. Always note the minimum version for any feature you use.
- MSX app versions run from `0.1.0` to `0.1.166` (the scope of this KB).

**Three version systems (do not confuse them):**

| System | Current version | Has per-feature changelog? |
|---|---|---|
| **MSX app** | `0.1.0`–`0.1.166` | **Yes** — all "Since Version" columns in this KB refer to this |
| TVX Framework | `0.1.35` | No — mentioned once in sources, no version history |
| TVX Plugin Library | `0.0.79` | No — mentioned once in sources, no version history |

Only the **MSX app version** has documented per-feature minimum versions. For the TVX Framework and TVX Plugin Library, no version-specific differences exist in the sources — do not invent any.

---

## 2. JSON Hierarchy: Start → Menu → Content → Item

A typical MSX setup follows this hierarchy:

```
Start Object (start.json)
  └── parameter (exactly one of):
        ├── "menu:{URL}"    → Menu Root Object (menu.json)
        │                         └── Menu Item Object[]
        │                               └── data: "{content_URL}" or {Content Root Object}
        │                                              └── Content Root Object (content.json)
        │                                                    ├── template + items[]  (templated)
        │                                                    │     └── Content Item Object
        │                                                    └── pages[]             (manual)
        │                                                          └── Content Page Object
        │                                                                └── items[]
        │                                                                      └── Content Item Object
        └── "content:{URL}" → Content Root Object (content.json) [directly, no menu]
                                    ├── template + items[]  (templated)
                                    │     └── Content Item Object
                                    └── pages[]             (manual)
                                          └── Content Page Object
                                                └── items[]
                                                      └── Content Item Object
```

**Navigation overflow:** In every view (menu, content, and panel alike), navigation wraps around at the edges. If the focused item is at the leftmost position and the left key is pressed, focus jumps to the rightmost position (and vice versa for right/up/down). This applies uniformly across all views. For content loaded via a menu, the menu and content form one continuous left/right loop by default — intuitive, since the menu sits immediately to the left of the content on screen: pressing right at the menu enters the content's first item, pressing left at the menu enters the content's last item, and reaching the content's own left/right edge in the matching direction continues that same loop back into the menu. Set `wrap: true` on the Content Root Object (since `0.1.102`; a Content Page Object's own `wrap` overrides it) to keep left/right confined within the content instead, e.g. so a virtual keyboard or similar on-screen control doesn't lose focus into the menu. `back` always reaches the menu regardless of `wrap`/`type` — it's the universal fallback. Whether left/right *also* reaches the menu depends on `wrap` and `type`: for `"pages"`, if the Content Root or the specific first/last page has `wrap: true`, only `back` works from that boundary page; for `"list"`, the same holds if the Content Root or *every* page is wrapped — but if only some pages are wrapped, left/right still reaches the menu from any page that isn't, which may require navigating up/down to that page first. See [Content Root Object](main-api/content/content-root-object.md), [Content Page Object](main-api/content/content-page-object.md).

### Start Object (`start.json`)

Mandatory properties: `name` (string), `version` (string — your app's own version, e.g. `"1.0.0"`, not the MSX app version), `parameter` (string, must start with `menu:` or `content:`).

```json
{
    "name": "My MSX App",
    "version": "1.0.0",
    "parameter": "menu:https://example.com/msx/menu.json"
}
```

The `parameter` supports `{SERVER}` (since `0.1.65`) and `{PREFIX}` (since `0.1.97`) placeholders only for the start file. All other JSON files must use full URLs. Since `0.1.97`, `parameter` can also start with `redirect:` (e.g. `redirect:{PREFIX}{SERVER}/msx/start.php`) to load the actual Start Object from another location instead of `menu:`/`content:` directly.

Two further optional top-level properties: `welcome` (string, since `0.1.117`) sets this start parameter as the app's welcome pages (start action or content); `launcher` (object, since `0.1.164`, `icon`/`image`/`color` only) sets up this start parameter's own tile in the settings and setup-completion panel — a deliberate subset of the separate, older Launcher MSX service's own richer `launcher` object (`type` plus `name`/`version`/`parameter` overrides), see [Glossary: Launcher MSX](reference/glossary.md#launcher-msx).

**All Properties:** `name`, `version`, `parameter`, `welcome`, `launcher`. This list is exhaustive — the JSON example above only illustrates the mandatory subset; if a property isn't in this list, it doesn't exist on the Start Object. For the type/default/since-version of each, see the full property table linked below.

See: [Start Object](main-api/start/start-object.md)

### Menu Root Object

A JSON object with a `menu` array of Menu Item Objects (the property is named `menu`, not `items`).

```json
{
    "headline": "My App",
    "menu": [
        { "label": "Home", "icon": "home", "data": "https://example.com/msx/home.json" },
        { "label": "Videos", "icon": "movie", "data": "https://example.com/msx/videos.json" },
        { "label": "Music", "icon": "music-note", "data": "https://example.com/msx/music.json" }
    ]
}
```

Key properties beyond `menu`/`headline` above — many are **shared with Content Root Object below**, since both objects can serve as the Start Object's `menu:`/`content:` target (marked *shared*; see there for the properties specific to Content Root Object instead):

- `name`, `version` (*shared*): shown in the app's About panel (only used when this data is loaded at startup).
- `reference` (*shared*, since `0.1.134`): load the actual data from another URL at runtime instead — most other properties are ignored if set.
- `reuse`, `cache`, `restore`, `refocus` (*shared*): lifecycle/focus-caching flags — `reuse`/`cache`/`restore` all default `true`; `refocus` (since `0.1.153`) requires the (non-key) `flag` property to also be set.
- `background`, `transparent` (*shared*): the general page-background chain and its dimming override — see [Best Practices & Good to Know → `transparent` and background images/videos](reference/best-practices.md#transparent-and-background-imagesvideos) below.
- `extension`, `dictionary` (*shared*): a small top-right label shown on each content screen (overridden by the content's own `extension` if set), and a URL to a UI-translation dictionary file.
- `options` (*shared concept*, since `0.1.120`): a Content Page Object (or since `0.1.130` also a Content Root Object) shown as a panel when the menu button is pressed — ignored if the selected menu item has its own `options`.
- `action`, `data`, `ready` (*shared*): Start Action and the `ready` object — see Section 3 below.
- `logo`, `logoSize` (**Menu Root only**, since `0.1.60`/`0.1.130`): a logo image instead of `headline`.
- `style` (**Menu Root only**, since `0.1.142`): the menu's own visual style — `"default"` | `"flat"` | `"flat-separator"` | `"overlay"` | `"overlay-separator"`.

**All Properties:** `name`, `version`, `reference`, `flag`, `reuse`, `cache`, `restore`, `refocus`, `transparent`, `style`, `logo`, `logoSize`, `headline`, `background`, `extension`, `dictionary`, `menu`, `action`, `data`, `ready`, `options`. This list is exhaustive — the JSON example above only illustrates a minimal subset; if a property isn't in this list, it doesn't exist on the Menu Root Object. For the type/default/since-version of each, see the full property table linked below.

See: [Menu Root Object](main-api/menu/menu-root-object.md)

### Menu Item Object

One entry in a Menu Root Object's `menu` array. Key properties:

- `type`: `"default"` (default) | `"separator"` (grouping line — can carry its own `label`; line color via `lineColor`, since `0.1.142`) | `"settings"` (hidden feature that opens the app's native Settings screen — see [Settings Menu Item](experts-api/hidden-features/settings-menu-item.md)).
- `icon` / `image`: Visual properties — `image` (since `0.1.130`) overrides `icon` if both are set.
- `label`: The item's text label (supports inline expressions).
- `extensionIcon` / `extensionLabel`: An additional icon/label shown on the item's right side (`extensionLabel` only used if `extensionIcon` is unset).
- `focus` / `execute` (since `0.1.120`): Control initial focus and — via [Execute Property](experts-api/hidden-features/execute-property.md) — auto-entering this item's content on load.
- `options` (since `0.1.120`): A Content Page Object (or since `0.1.130` also a Content Root Object) shown as a panel when this item is selected and the menu button is pressed.
- `data`: references the content to display — see the critical rule right below.

**Critical `data` rule:** The `data` property of a Menu Item Object references content to display. It accepts exactly two forms:

1. **URL string:** `"data": "https://example.com/msx/content.json"` — MSX fetches this URL and expects a Content Root Object.
2. **Inline Content Root Object:** `"data": { "type": "pages", "items": [...], "template": {...} }` — the content is embedded directly.

> **Common error:** Do NOT use `"data": "content:https://..."` for a Menu Item. The `content:` prefix is an **action** syntax (for the `action` property of **content items**), not a menu `data` value. A menu item's `data` is a raw URL or an embedded Content Root Object. Menu items have no `action` property of their own.

**All Properties:** `id`, `type`, `display`, `enable`, `focus`, `execute`, `transparent`, `icon`, `image`, `label`, `background`, `extensionIcon`, `extensionLabel`, `lineColor`, `data`, `options`. This list is exhaustive; if a property isn't in this list, it doesn't exist on the Menu Item Object. For the type/default/since-version of each, see the full property table linked below.

See: [Menu Item Object](main-api/menu/menu-item-object.md)

### Content Root Object

The top-level content container. Two layout modes:

**Templated layout** (all items the same size, positioned automatically):

```json
{
    "type": "pages",
    "template": {
        "type": "separate",
        "layout": "0,0,2,4",
        "icon": "msx-white-soft:movie"
    },
    "items": [
        { "title": "Video 1", "action": "video:https://example.com/v1.mp4" },
        { "title": "Video 2", "action": "video:https://example.com/v2.mp4" }
    ]
}
```

**Manual layout** (items placed on a grid per page):

```json
{
    "type": "list",
    "pages": [
        {
            "items": [
                { "layout": "0,0,12,1", "type": "button", "label": "Item 1", "action": "info:Hello" }
            ]
        }
    ]
}
```

- `type`: `"pages"` (horizontal, default) or `"list"` (vertical).
- `template` + `items` and `pages` are mutually exclusive; `pages` is ignored if `template`+`items` are set.
- Grid: 12 columns × 6 rows for both `"pages"` and `"list"` type; panels always use 8 × 6. With `compress: true` (since `0.1.123`): 16 × 8 (panels: 10 × 8). The `type` does not change the grid size — it changes how pages are presented and transitioned between. **General rule for both types:** the view always transitions to the page that contains the focused item — a horizontal transition for `"pages"`, a vertical one for `"list"`. This happens regardless of whether the target item happens to already be visible on screen.
  - `"pages"`: only the active page is ever visible; other pages are fully off-screen. A page indicator (one dot per page) is shown at the bottom.
  - `"list"`: pages are stacked vertically with the empty gaps between them removed. Because of this, pages that come after the active one can already be fully or partially visible (peeking into view) before you focus an item on them — this "preview" of upcoming pages does not happen with `"pages"`. Focusing an item still triggers a vertical transition to that item's page even if the item was already visible. Scroll indicators (not a page indicator) are shown at the bottom when there is more content to scroll to.
- **Which to pick?** Both work mechanically either way, but see [Best Practices & Good to Know → Best Practices](reference/best-practices.md#best-practices-learned-from-building-real-pages) for practical guidance — in short, `"list"` fits classic scrollable lists and sees more real-world use overall, `"pages"` fits special one-off screens that fit on a single page.

Further key properties beyond `type`/`template`/`items`/`pages` above — `name`/`version`, `reference`, `reuse`/`cache`/`restore`/`refocus`, `background`/`transparent`, `extension`/`dictionary`, `options`, and `action`/`data`/`ready` all work identically to Menu Root Object above (see there for details); this list covers only what's specific to Content Root Object:

- `wrap`, `important`: `wrap` (since `0.1.102`) blocks left/right navigation into the menu at the content's edges; `important` (since `0.1.58`) forces the content `headline` to display even when this content is loaded via a menu (the menu item's `label` is used as headline otherwise).
- `header`, `footer`, `overlay`, `underlay`, `inserts`: individually-configured Content Page Objects layered onto **templated** (`template`+`items`) content — a fixed header/footer row (since `0.1.53`), a page shown over/under every page (`overlay` since `0.1.110`, `underlay` since `0.1.112`), or pages inserted at specific positions (`inserts`, since `0.1.156`). All five require `template`+`items` to be set, otherwise ignored.
- `caption` (since `0.1.130`): customizes the `options` panel's own lower-right label.
- `preload` (since `0.1.92`), `preselect` (since `0.1.160`): preload the next/previous visible page's content (images, live updates); execute page/selection actions *before* (instead of after) a content scroll animation.
- `navigationOffset`, `navigationSpan` (since `0.1.164`): align the list scroll indicators to a specific screen area — only take effect for `type: "list"` content or in a panel.

**All Properties:** `name`, `version`, `reference`, `flag`, `reuse`, `cache`, `restore`, `important`, `wrap`, `compress`, `preselect`, `refocus`, `transparent`, `type`, `preload`, `headline`, `background`, `extension`, `dictionary`, `template`, `items`, `pages`, `header`, `footer`, `inserts`, `overlay`, `underlay`, `action`, `data`, `ready`, `options`, `caption`, `captionUnderlay`, `navigationOffset`, `navigationSpan`. This list is exhaustive — the two JSON examples above only illustrate the `template`/`items` vs. `pages` split; if a property isn't in this list, it doesn't exist on the Content Root Object. For the type/default/since-version of each, see the full property table linked below.

See: [Content Root Object](main-api/content/content-root-object.md)

### Content Page Object

One page/screen within a Content Root Object's `pages[]` array (manual layout) — or, since `0.1.53`/`0.1.110`/`0.1.112`/`0.1.156`, an individually-configured `header`/`overlay`/`underlay`/insert page attached to a `template`+`items` (templated) Content Root Object. Not used at all for pure templated content with no `header`/`footer`/`overlay`/`underlay`/`inserts`. Key properties:

- `items` (mandatory for a `pages[]` entry): the Content Item Objects placed on this page.
- `display`, `important` (since `0.1.110`): `important` controls whether a corresponding overlay/underlay page is hidden while this page is active (and vice versa — an overlay/underlay page with `important: true` is never hidden).
- `wrap` (since `0.1.102`): blocks left/right navigation into the menu from this page — only actually matters on the content's first/last page (`type: "pages"`) or on every page (`type: "list"`); no effect on overlay/underlay pages or in a panel.
- `background`, `transparent`: this page's own link in the general page-background chain — same mechanics as Content Root Object above.
- `area`, `position`, `template` (all since `0.1.156`): insert-page placement — only take effect when this page is set inside the Content Root Object's own `inserts` array (see above); `template` here overrides properties of the content items being integrated, not the page's own properties.
- `action`, `data` (since `0.1.112`, hidden feature): the Page Action, executed when this page becomes active — see [Page Action](experts-api/hidden-features/page-action.md).
- `options`, `caption`: the same options-panel mechanism as Content Root Object, scoped to this page.
- `navigationOffset`, `navigationSpan` (since `0.1.164`): align the list scroll indicators to a specific screen area — only take effect for `type: "list"` content or in a panel.

**All Properties:** `display`, `important`, `wrap`, `compress`, `transparent`, `headline`, `background`, `area`, `offset`, `position`, `template`, `items`, `action`, `data`, `options`, `caption`, `captionUnderlay`, `navigationOffset`, `navigationSpan`. This list is exhaustive; if a property isn't in this list, it doesn't exist on the Content Page Object. For the type/default/since-version of each, see the full property table linked below.

See: [Content Page Object](main-api/content/content-page-object.md)

### Content Item Object

One cell/tile on the grid. Key properties:

- `layout`: `"col,row,width,height"` (e.g. `"0,0,4,3"`) — required for each item inside a Content Page Object's `items[]` (manual layout, `pages[]`). By contrast, for templated items (the content root object's own `template` + `items[]`), only the `template` object needs a `layout` (and only its `w`/`h` components are used); the individual items inside that `items[]` array must **not** set their own `layout`, it has no effect there.
- `type`: `"default"` | `"separate"` | `"teaser"` | `"button"` | `"space"` | `"control"`.
- `action`: The action string executed when the item is selected.
- `headline`, `text`, `label`, `title`, `titleHeader`, `titleFooter`, `badge`, `stamp`: Text properties (all support inline expressions). `headline`/`text` sit in the upper-left corner, `title`/`titleHeader`/`titleFooter` in the lower-left, `badge` upper-left (overlapping `headline`/`text` — see [Best Practices → `badge` overlapping `headline`/`text`](reference/best-practices.md#tagstampbadge--short-single-line-text-and-default-colors-that-already-fit)), `tag` upper-right, `stamp` lower-right.
- `image`, `icon`, `color`: Visual properties.
- `background` — **narrower than it looks: only applies if this item's own `action` uses the `audio:` prefix** (`audio:{URL}`, `audio:plugin:{URL}`, `audio:resolve:...`, `audio:auto:...` — any `audio:`-prefixed variant counts, not only the plain `audio:{URL}` form; see the general prefix-family rule in Section 3). It sets the backdrop shown while that item's audio is playing — it does nothing for a `video:`-prefixed action (`video:{URL}`, `video:plugin:{URL}`, etc.) or any other action type. Do not confuse it with the general page-background chain on Content Root/Content Page/Menu Root/Menu Item Object (also called `background`, but unconditional there) — see [Best Practices & Good to Know → `transparent` and background images/videos](reference/best-practices.md#transparent-and-background-imagesvideos) for that chain. For a background image on a video/plugin item's page, set `background` on the Content Root (or Content Page) Object instead, not on the item.
- `enumerate`, `group` (since `0.1.92`): playlist/slideshow auto-advance mechanics. Enumerated items (`enumerate: true` — the default for templated items, `false` otherwise) execute in a row and show a `(1/10)`-style position label in the headline; `group` (requires `enumerate: true`) splits the enumeration counter per group and optionally adds its own label after the position — see [Best Practices → The player UI shows for several seconds by default](reference/best-practices.md#the-player-ui-shows-for-several-seconds-by-default-when-starting-videoaudio--several-mechanisms-shorten-or-suppress-it) for the full playlist-formation recipe.
- `live` (object, since `0.1.70`): periodically updates this item's visual properties (live data, playback progress, countdowns) and fires a one-shot action on state transitions. Combined with `resume:key` (since `0.1.74`), a `type: "playback"` live object doesn't just show progress while playing — it also shows the *resume* progress (the saved position from a previous session) on the `progress` bar while the item sits idle/at rest, before it's ever selected. See [Live Object](experts-api/live/live-object.md) and the resume/EPG deep dives in [Cookbook](reference/cookbook.md#live-data-epg-progress-countdowns).
- `selection` (object, since `0.1.110`): runs an action immediately when this item receives focus (auto-preview, load a backdrop, update another item) — see [Selection Object](experts-api/selection/selection-object.md).
- `options` (since `0.1.120`): A Content Page Object (or since `0.1.130` also a Content Root Object) shown as a panel when this item is selected and the menu button is pressed. Highest priority in the `options` fallback chain — see [Glossary: Options](reference/glossary.md#options).
- `properties` (object): The **Extended Properties bag** — a large, separate set of advanced, mostly player/plugin-facing item properties (e.g. `resume:key`/`resume:position`/`resume:context` for remembering playback position, `trigger:{TRIGGER_KEY}`, `control:load`, `tizen:*`, `html5x:*`, `image:*`, player-button overrides) that do **not** live at the top level of a Content Item Object — they are nested inside this one `properties` object instead, e.g. `"properties": { "resume:key": "url" }`. Not every `key:value` inside `properties` is necessarily an MSX-defined property, either — some are free-form data a specific plugin reads (see [Common Misconceptions → Extended properties (the `properties` bag)](reference/common-misconceptions.md#extended-properties-the-properties-bag)). See [Extended Properties](experts-api/special/extended-properties.md).

**All Properties:** `id`, `type`, `key`, `layout`, `area`, `offset`, `display`, `enable`, `focus`, `execute`, `enumerate`, `compress`, `decompress`, `shortcut`, `round`, `break`, `group`, `color`, `title`, `titleHeader`, `titleFooter`, `label`, `icon`, `iconSize`, `headline`, `text`, `alignment`, `truncation`, `centration`, `separation`, `tag`, `tagColor`, `badge`, `badgeColor`, `stamp`, `stampColor`, `progress`, `progressColor`, `progressBackColor`, `wrapperColor`, `image`, `imageFiller`, `imageWidth`, `imageHeight`, `imageOverlay`, `imagePreload`, `imageLabel`, `imageColor`, `imageScreenFiller`, `imageBoundary`, `playerLabel`, `background` (`audio:`-prefix actions only, see above), `extensionIcon`, `extensionLabel`, `action`, `data`, `properties`, `live`, `selection`, `options`. This list is exhaustive — the bullets above only name the most commonly-used properties; if a property isn't in this list, it doesn't exist on the Content Item Object. For the type/default/since-version of each, see the full property table linked below.

See: [Content Item Object](main-api/content/content-item-object.md)

---

## 3. Action String Syntax

All actions are strings. They appear in the `action` property of:

- **Content items** — executed when the item is selected (main use case).
- **Content page objects** — executed when the page becomes active (hidden feature, since `0.1.112`). See [Page Action](experts-api/hidden-features/page-action.md).
- **Content root / menu root objects** — Start Action executed when data is loaded at startup (hidden feature, since `0.1.0`; not to be confused with the `start` Main Action at `0.1.30`, which sets a *new* start parameter). See [Start Action](experts-api/hidden-features/start-action.md).
- **Content root / menu root objects (again) — a separate mechanism.** A `ready` object (`action` + optional `data`, since `0.1.142`) fires every time that content/menu (including in a panel) becomes freshly displayed — not just once at startup like the Start Action above, but on every appearance, whether the data was freshly fetched, served from `cache`, or its DOM kept via `reuse`. Use `ready` for behavior that must re-arm on every appearance of a page (e.g. re-registering `trigger:` actions); use the Start Action above only for genuine one-time startup behavior. See [Glossary: Ready](reference/glossary.md#ready).
- **Live objects** (`action` + optional `data`, since `0.1.70`) — fires when a `type: "setup"` live object's item becomes visible, or when a `type: "playback"` live object in state `"running"` is executed (instead of the item's own `action`). A state-specific override set inside `coming`/`running`/`over` fires when the live object enters that state instead of the base `action`. See [Live Object](experts-api/live/live-object.md).
- **Selection objects** (`action` + optional `data`, since `0.1.110`) — fires immediately when the corresponding content item receives focus. See [Selection Object](experts-api/selection/selection-object.md).
- **Server response objects** — `response.data.action`, returned by `execute:{URL}` calls.

> **Note: Menu items have no `action` property** — they associate content exclusively via `data`.
>
> **Note: Several Extended Properties also expect an action string as their value** — e.g. `trigger:{TRIGGER_KEY}`, `button:{BUTTON_ID}:action`, `control:action`, `image:trigger`, `image:rotation:trigger`. Unlike the object-level `action`/`data` pairs above, these "property actions" **cannot** carry an action-related `data` property — use `execute:fetch:{URL}` instead if the action needs data (stated explicitly on [Extended Properties](experts-api/special/extended-properties.md) itself for each of these).

### Basic Action Patterns

| Pattern | Example | Since | Notes |
|---|---|---|---|
| `video:{URL}` | `video:https://example.com/v.mp4` | 0.1.0 | Play video |
| `audio:{URL}` | `audio:https://example.com/a.mp3` | 0.1.0 | Play audio (no video screen) |
| `image:{URL}` | `image:https://example.com/img.jpg` | 0.1.0 | Show image |
| `content:{URL}` | `content:https://example.com/c.json` | 0.1.0 | Load content page |
| `content:data` | `content:data` | 0.1.0 | Load content from item's `data` property |
| `panel:{URL}` | `panel:https://example.com/p.json` | 0.1.0 | Load content in panel |
| `panel:data` | `panel:data` | 0.1.0 | Load content in panel from item's `data` property |
| `menu:{URL}` | `menu:https://example.com/m.json` | 0.1.0 | Load menu |
| `menu:data` | `menu:data` | 0.1.0 | Load menu from item's `data` property |
| `playlist:{URL}` | `playlist:https://example.com/pl.json` | 0.1.0 | Load playlist |
| `playlist:data` | `playlist:data` | 0.1.0 | Load playlist from item's `data` property |
| `slideshow:{URL}` | `slideshow:https://example.com/sl.json` | 0.1.0 | Load slideshow |
| `slideshow:data` | `slideshow:data` | 0.1.0 | Load slideshow from item's `data` property |
| `link:{URL}` | `link:https://msx.benzac.de` | 0.1.0 | Open link (in-app) |
| `link:window:{URL}` | `link:window:https://msx.benzac.de` | 0.1.0 | Open link (external) |
| `info:{TEXT}` | `info:Hello World` | 0.1.0 | Show info message |
| `success:{TEXT}` | `success:Done!` | 0.1.0 | Show success message |
| `warn:{TEXT}` | `warn:Check this` | 0.1.0 | Show warning message |
| `error:{TEXT}` | `error:Failed` | 0.1.0 | Show error message |
| `focus:{ID}` | `focus:item_id` | 0.1.0 | Focus item by ID |
| `focus:index:{INDEX}` | `focus:index:0` | 0.1.0 | Focus item by index (0 = first item) |
| `start` | `start` | 0.1.30 | Set new start parameter (data in `data` property) |
| `video:plugin:{URL}` | `video:plugin:https://example.com/plugin.html` | 0.1.40 | Plugin video |
| `audio:plugin:{URL}` | `audio:plugin:https://example.com/plugin.html` | 0.1.40 | Plugin audio (no video screen) |
| `reload` | `reload` | 0.1.58 | Reload application |
| `reload:content` | `reload:content` | 0.1.58 | Reload current content |
| `reload:menu` | `reload:menu` | 0.1.58 | Reload current menu |
| `reload:panel` | `reload:panel` | 0.1.58 | Reload current panel content |
| `update:menu:{ID}` | `update:menu:my_item` | 0.1.58 | Update menu item by ID (updated properties in `data` property) |
| `update:content:{ID}` | `update:content:my_item` | 0.1.58 | Update content item by ID (updated properties in `data` property) |
| `update:panel:{ID}` | `update:panel:my_item` | 0.1.58 | Update panel content item by ID (updated properties in `data` property) |
| `execute:{URL}` | `execute:https://example.com/service.php` | 0.1.30 | Server-side action |

For a full list of all actions with all modifiers and version details, see: [Actions](main-api/common/actions.md) and [Actions Reference](reference/actions-reference.md). Replace Actions, the Start Action, and the Resolve Action are hidden features with their own since-versions, not Main Actions — see [Actions Reference](reference/actions-reference.md#hidden-feature-actions-replace-start--resolve).

> **General rule for reading `audio:`/`video:`-conditioned properties and behaviors:** Wherever this KB states a property or behavior is only valid "if the content action is `audio:{URL}`" (or `video:{URL}`), read this as **the whole prefix family**, not only the plain URL form — `audio:plugin:{URL}`, `audio:resolve:...`, `audio:auto:...`, and any other `audio:`-prefixed variant all count equally as "an `audio:` action" (and likewise for every `video:`-prefixed variant as "a `video:` action"). The distinguishing factor is always the prefix itself, never which specific mechanism (plain URL, plugin, resolve, auto-mode, …) started playback. This applies KB-wide, not just to one property — e.g. Content Item Object's `background` (Section 2 above).

### Multi-Action Syntax (since `0.1.58`)

To execute multiple actions in sequence from a single `action` property, wrap them in `[...]` and separate with `|`:

```
[reload:content|info:Action executed.]
```

Rules:
- Starts with `[`, ends with `]`, actions separated by `|`.
- Each action may use the shared `data` property of the triggering item.
- If you need a literal `|` character **inside** one of the actions (e.g. as part of a message text like `info:a|b`), use the `{pipe}` keyword:

```
[reload:content|info:[P{pipe}I{pipe}P{pipe}E]]
```

- An empty multi-action `[]` is a no-op (does nothing).
- Each action executes independently — the bracket is not transactional. If one action fails, warns, or is a no-op (e.g. an internal action unsupported on the client's current MSX version), the rest of the list still executes.

### `data` Action — Multi-Action Alternative with Separate Data (since `0.1.58`)

The `data` action is a second way to execute multiple actions in a row. Instead of a shared `data` property, each action gets its **own** `action`/`data` pair, listed in an `actions` array inside the `data` property:

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

Which variant to use depends on the use case:
- **`[...]` syntax:** simpler, but the triggering item's `data` property is shared by **all** actions, as documented in [Actions](main-api/common/actions.md#extended-actions). Only use this when the actions need the same data, or none at all — though a single shared `data` object can still feed several `update:` actions at once on purpose, since each one only picks out the properties it already has (see [Actions Reference → `data` Action](reference/actions-reference.md#data-action-since-0158)). Also, a literal `|` inside one of the actions needs the `{pipe}` keyword (see above).
- **`data` action:** more verbose, but lets each action carry its own `data`, which is required as soon as the actions need different data (like the overlay/underlay example above). Since there is no `|` separator here, a literal `|` inside an action's text needs no `{pipe}` escaping — it's just a normal JSON string.

### Request Actions

Many URL-accepting actions support `request:interaction:{DATA_ID}@{URL}` as an alternative to a plain URL, enabling interactive plugin requests (e.g. `video:resolve:request:interaction:{DATA_ID}@{URL}`). This pattern is **`0.1.82`**, not the blanket `0.1.160+` floor that otherwise applies to internal-only actions lacking their own individual since-version (see [Section 6 → Feature Minimum Versions](#feature-minimum-versions) below) — except when combined with a prefix that has a later since-version itself (e.g. `playlist:auto:...` or Resolve, both stay at their own higher floor). The sibling `request:player:{DATA_ID}` pattern is a separate mechanism and remains at that blanket `0.1.160+` floor. See [Actions Reference](reference/actions-reference.md#request-actions-requestinteraction-is-0182-requestplayer-remains-01160) for the full breakdown, and [Resolve Action](experts-api/hidden-features/resolve-action.md) for the `0.1.107` hidden feature (distinct from `request:interaction:` itself).

---

## 4. Inline Expressions — Complete Overview

Inline expressions only work inside **free-text, visual-output string properties** — `label`, `title`, `titleHeader`, `titleFooter`, `headline`, `text`, `tag`, `badge`, `stamp`, `caption` (the options-panel caption on a Content Root Object, since `0.1.130`), the text part of message actions like `info:`/`success:`, `extension` (a small label shown in the top-right corner of content screens, available on both Menu Root Object and Content Root Object), etc. Each property reference in this KB states explicitly whether it supports inline expressions — do not assume it for a string property just because it holds text.

**Not every string-valued property qualifies.** Properties with a fixed, enumerated set of values (e.g. `type`, `imageFiller`, `alignment`, `iconSize`) or that hold structural/technical strings (URLs, IDs, action strings, colors, icon names) do **not** support inline expressions — they are parsed as literal values, not rendered as visual text. Inline expressions only apply where the property's whole point is to display free text on screen.

They always start with `{` and end with `}`. **Nested expressions are not supported** — but prefix combinations like `{txt:{COLOR}:num:...}` are valid and distinct from nesting.

### Standard Inline Expressions (since `0.1.0` unless noted)

| Expression | Since | Purpose |
|---|---|---|
| `{sp}` | 0.1.0 | Non-breakable space |
| `{tb}` | 0.1.0 | Non-breakable tab |
| `{br}` | 0.1.0 | Line break |
| `{ico:{ICON}}` | 0.1.0 | Icon (e.g. `{ico:home}`) |
| `{ico:{COLOR}:{ICON}}` | 0.1.0 | Colored icon (e.g. `{ico:msx-red:home}`) |
| `{txt:{COLOR}:{TEXT}}` | 0.1.0 | Colored text (e.g. `{txt:msx-blue:Hello}`) |
| `{chr:{CHARACTER}}` | **0.1.145** | Character entity (e.g. `{chr:copy}` → ©) |
| `{chr:{COLOR}:{CHARACTER}}` | **0.1.145** | Colored character entity |
| `{context:{PROPERTY}}` | 0.1.0 | Template context reference (see note below) |
| `{unknown}` | 0.1.0 | Unknown expression → returned without braces |

**`{context:...}` rule:** Only works inside the `data` property of a template object. It references a property of the templated item (e.g. `{context:label}` → the item's `label` value). If the property is not found on the item, the **template object's own properties** are checked next — so a property set on the template acts as a default value for all items. If the property is missing entirely, the expression remains unchanged. If the property value is not a string, the expression is removed.

> **Note (since `0.1.110`):** The `properties`, `live`, `selection`, and `options` properties of a template object also support `{context:{PROPERTY}}`.

> **Different `template`, no `{context:...}` support:** The rule above is about the Content Root Object's `template` (paired with `items[]` for templated content). A separate, unrelated `template` property also exists on the Content Page Object itself, used only by **insert pages** (since `0.1.156`, via the Content Root's `inserts` array) to override properties of the content items being integrated into that page — see [Content Page Object](main-api/content/content-page-object.md). `{context:{PROPERTY}}` explicitly does **not** work inside that insert-page `template` object.

See: [Inline Expressions](main-api/common/inline-expressions.md)

### Number Inline Expressions (since `0.1.91`)

Convert numbers (millisecond timestamps or plain numbers) into formatted dates, times, durations, or plain numbers. Three syntax forms:

**Date / Time / Duration** — `{num:{NUMBER}:{TYPE}:{FORMAT}}`

- `{num:1548335764000:date:DD, MM d, yyyy}` → `Thursday, January 24, 2019` — specific date (`DD` = weekday name, `MM` = month name — not day-of-month/month-number as in ISO conventions, see [Common Misconceptions](reference/common-misconceptions.md#date--time--duration-format-tokens))
- `{num:now:date:DD, MM d, yyyy}` — current date, same format, output changes with the clock
- `{num:5640000:duration:time:hh:mm:ss}` → `01:34:00` — duration (5,640,000 ms = 1h 34m 0s)

**Formatter** (since `0.1.160`) — `{num:{NUMBER}:formatter:{FORMATTER_ID}}`

- `{num:1548335764000:formatter:day_full}` → `Thursday, January 24, 2019` — pre-defined formatter

**Basic number format** (since `0.1.160`) — `{num:{NUMBER}:format}` or `{num:{NUMBER}:format:{FORMAT}}`

- `{num:12345.12345:format}` → `12,345.12345` — format from dictionary (no explicit FORMAT; default is comma thousands separator, full decimal precision)
- `{num:12345.12345:format:0}` → `12,345` — rounded to 0 decimal places
- `{FORMAT}` also accepts decimal digits + trimming (e.g. `format:02`) and custom thousands/decimal separators (e.g. `format: .` or `format:.,0`) — these are only sketched here, see [Number Inline Expressions](experts-api/hidden-features/number-inline-expressions.md#basic-number-format-expressions) for the full syntax and examples.

Colored variant works for all forms: `{txt:{COLOR}:num:{NUMBER}:...}`

The `date:`/`time:`/`duration:` format tokens and the **14 `formatter:` IDs** are identical to the Live Inline Expressions — `day_full` above is just one; see [Live Formatter IDs](experts-api/live/live-inline-expressions.md#live-formatter-id) and the [Live Date/Time/Duration Format tables](experts-api/live/live-inline-expressions.md#live-date-format) for the complete list.

See: [Number Inline Expressions](experts-api/hidden-features/number-inline-expressions.md)

### Color Inline Expressions (since `0.1.91`, Hidden Feature)

Change the default text color for a range of text (as opposed to `{txt:{COLOR}:{TEXT}}`, which colors a single fixed string):

- `{col:{COLOR}}` — sets the color for all following text until changed again (e.g. `{col:msx-red}`)
- `{col}` or `{col:default}` — switches back to the default color

See: [Color Inline Expressions](experts-api/hidden-features/color-inline-expressions.md)

### Dictionary Inline Expressions (since `0.1.120`)

Look up a value from the active dictionary (set via `dictionary` property of Content Root Object or Menu Root Object):

- `{dic:{KEY}}` — value for key, or empty if missing
- `{dic:{KEY}|{DEFAULT_VALUE}}` — value for key, or default
- Colored: `{txt:{COLOR}:dic:{KEY}}` or `{txt:{COLOR}:dic:{KEY}|{DEFAULT_VALUE}}`

Extended wrapper form (since `0.1.123`):

- `{dix:{KEY}}{DEFAULT_VALUE}{dix}` — allows complex default values with other inline expressions inside
- The closing `{dix}` suffix can be omitted if it is at the end of the content string.

Insertion form (since `0.1.160`):

- `{dix:{KEY}|{INSERTION_1}|{INSERTION_2}}{DEFAULT_VALUE}{dix}` — insertions referenced by `{#1}`, `{#2}`, etc. in the value or default. Each insertion can only be referenced once.

See: [Dictionary Inline Expressions](experts-api/hidden-features/dictionary-inline-expressions.md)

### Live Inline Expressions (since `0.1.70`)

Used inside `live` object properties and the `extension` property of Content Root Objects and Menu Root Objects. Express timestamps, durations, and progress relative to a live object:

> **These are the only two places a Live Inline Expression resolves — nowhere else.** Placing `{now:...}` (or any other Live Inline Expression) directly in a regular item's `headline`/`title`/`text`/etc. does **not** work; it is left unresolved/literal on screen, even though those properties support standard Inline Expressions. To use one on a plain content item, put it inside that item's own `live` object instead (any valid `type` works purely for `{now:...}` output — `"airtime"`/`"lifetime"` are the most fitting choices for a simple clock, since they don't imply an actual schedule/playback context). The `extension` property is the sole exception that works **without** a `live` object at all — but only `{now:...}` makes sense there, since every other Live Inline Expression (`{creation:...}`, `{from:...}`, `{to:...}`, `{duration:...}`, `{progress:...}`, `{countdown:...}`, `{overflow:...}`) references a specific live object's own timing state, which doesn't exist without one.

- `{now:date:{FORMAT}}`, `{now:time:{FORMAT}}` — current time; `{now:formatter:{FORMATTER_ID}}` — current time via a pre-defined formatter ID
- `{creation:...}`, `{from:...}`, `{to:...}` — live object timestamps (same `date:`/`time:`/`formatter:` sub-forms as `now:` above)
- `{duration:...}`, `{progress:...}`, `{countdown:...}`, `{overflow:...}` — live progress values (`position:`, `time:{FORMAT}`, `text:{FORMAT}` sub-forms; `progress:`/`countdown:` also support `percentage:`)

> **Version note:** The `now:`/`creation:`/`from:`/`to:` family is available since `0.1.70`, but their `formatter:` sub-form specifically is available only since **`0.1.160`** — do not use `{now:formatter:...}` etc. on an app targeting `0.1.70`–`0.1.159`; use `date:`/`time:` instead. `duration:`/`progress:`/`countdown:`/`overflow:` have no `formatter:` sub-form at all.

Colored variant: `{txt:{COLOR}:{LIVE_INLINE_EXPRESSION}}` — omit the outer `{}` of the inner expression (e.g. `{txt:msx-red:now:date:DD, MM d, yyyy}`).

See: [Live Inline Expressions](experts-api/live/live-inline-expressions.md); for a real-world example (a live-updating date/time clock built from `now:date:`/`now:time:` in a Menu Root Object's `extension`), see [Cookbook → Live data](reference/cookbook.md#live-data-epg-progress-countdowns).

### General Rules for Inline Expressions

1. **Placeholder notation vs. actual JSON:** In documentation, `{txt:{COLOR}:{TEXT}}` must not be simplified to `{txt:COLOR:TEXT}` — the inner `{COLOR}` and `{TEXT}` are placeholder labels, not optional. In actual JSON, substitute values without braces: `{txt:msx-red:Hello}`, never `{txt:{msx-red}:{Hello}}`.
2. Unknown expressions are returned **without** the braces: `{unknown}` → `unknown`. This means typos in expression names will silently produce plain text.
3. Nested expressions (e.g. `{txt:{ico:home}}`) are **not** supported.
4. The prefix-combination forms (`{txt:msx-blue:num:...}`, `{txt:msx-blue:dic:key}`) are explicitly supported and distinct from nesting.

---

## 5. Colors

Color properties accept both standard CSS color syntax (e.g. `"red"`, `"#ff0000"`, `"rgb(255,0,0)"`) and the 11 predefined `msx-*` identifiers. The `msx-*` colors are theme-aware and recommended:

| Color key | Name |
|---|---|
| `msx-red` | Red |
| `msx-green` | Green |
| `msx-yellow` | Yellow |
| `msx-blue` | Blue |
| `msx-white` | White |
| `msx-gray` | Gray |
| `msx-black` | Black |
| `msx-white-soft` | Semi-transparent white |
| `msx-gray-soft` | Semi-transparent gray |
| `msx-black-soft` | Semi-transparent black |
| `msx-glass` | Almost-transparent white |

There is no `msx-orange`, `msx-purple`, `msx-cyan`, or any other `msx-*` color beyond these 11. Do not invent `msx-*` names.

See: [Colors](main-api/common/colors.md)

---

## 6. Version Model

### Backward Compatibility

The MSX engine is **fully backward-compatible**. Every JSON page that worked in `0.1.0` still works in `0.1.166`. There are no breaking changes.

### Forward Behavior (newer features on older MSX versions)

Using a newer feature on an MSX version that does not support it has one of two outcomes:

1. **Cosmetic / degraded:** The unsupported feature is ignored; the page remains usable but looks different (e.g. missing icons, wrong layout).
2. **Functionally broken:** The unsupported feature causes content not to render at all, resulting in a blank or non-functional page.

**Example — `compress` property (since `0.1.123`):** Setting `"compress": true` expands the content grid from 12×6 to 16×8. Items positioned in the extended area (0-based column index ≥ 12 or row index ≥ 6) will produce "out of range" warnings on MSX versions before `0.1.123` and will not be displayed, potentially leaving the page blank.

**Rule:** Always state the minimum version for any feature you use in a JSON page. Warn the user when a layout-altering feature (like `compress`) would break or degrade on older versions.

### Feature Minimum Versions

Every property, action, and inline expression documented with an individual "since" version in this KB carries its own minimum MSX app version, stated on its dedicated page. The broad default: internal actions that are not also official Main Actions have no individually documented since-version at all — only the blanket `0.1.160+` floor. **In practice this rarely matters** — nearly all platforms run the latest MSX version at any given time; only very old, no-longer-updatable Samsung TV models can be stuck below `0.1.160` (see [Versioning → How "Since Version" Annotations Work](reference/versioning.md#how-since-version-annotations-work)). A handful of internal action families do have their own tighter since-version anyway — see [Actions Reference → Internal-Only Actions Overview](reference/actions-reference.md#internal-only-actions--overview-require-01160) for the complete, current list. For a curated timeline of feature-introduction milestones, see the **Version Baseline** table in [Versioning](reference/versioning.md#version-baseline-010).

For exact version information on each property, always check the relevant API page. For the complete, unabridged per-version changelog, see [Release Notes](overview/release-notes.md).

---

## 7. Plugin API — Three Access Forms

The Plugin API (for custom video/audio plugins or interaction plugins) is accessible via three equivalent forms:

| Form | Library loading | Key class names |
|---|---|---|
| Classical ES5 (global) | `<script src="//msx.benzac.de/js/tvx-plugin.min.js">` | `TVXVideoPlugin`, `TVXInteractionPlugin` |
| Classic Module (AMD/CommonJS) | `var tvx = require('tvx-plugin-module')` | `tvx.VideoPlugin`, `tvx.InteractionPlugin` |
| TypeScript Module | `import * as tvx from 'tvx-plugin-module'` | `tvx.TVXVideoPlugin` (type), `tvx.VideoPlugin` (instance) |

`TVXVideoPlugin` and `tvx.VideoPlugin` are the same API — just different access patterns depending on which form you use.

**ES5 recommendation:** Use ES5 (classical form) for TV browser compatibility; TV browsers often have limited ES module support.

**Event types** (handled in `handleEvent(data)`): app events (`app:suspend`, `app:resume`, `app:sleep`, `app:awake`, `app:resize`, `app:connect`, `app:disconnect`, `app:time`, `app:result`), video events (`video:load`, `video:play`, `video:pause`, `video:stop`, `video:seek`, `video:restart`, `video:speed`, `video:volume`), slider events (`slider:load`, `slider:play`, `slider:pause`, `slider:stop`, `slider:position`, `slider:rotation`), settings events (`settings:*`, one per app setting), and `custom:{EVENT_ID}` for plugin-to-plugin communication. For the complete list with all payload properties (`data.info`, `data.data`, etc.) and JSON structures, see [Plugin Events Reference](experts-api/plugins/plugin-events-reference.md).

Plugin Library version: `0.0.79`. No version changelog exists for this library.

There is also a `tvx-plugin-ux-module.min.js` variant — the same library plus additional effects/UI classes (requires jQuery, unlike the dependency-free base module). These extra classes need expert-level knowledge and are not documented in this KB — see [Glossary: TVX Plugin Library](reference/glossary.md#tvx-plugin-library).

---

## 8. How to Find Information in This KB

**For practical or specific questions, check the curated reference docs before the raw source pages.** `reference/cookbook.md`, `reference/json-building-guide.md`, and `reference/settings-reference.md` in particular carry a lot of detailed, practically-important knowledge — worked patterns, rendering/UX behavior, and edge cases — that came directly from the MSX developer and goes beyond what any single `main-api/`/`experts-api/` page states on its own. For anything beyond a plain property/type lookup, try these three first; fall back to the raw source pages for the authoritative structural reference.

| Question | File to load |
|---|---|
| What is MSX? General overview | `overview/home.md` |
| How to set up an MSX start URL? | `main-api/start/start-object.md` |
| MSX app start URL parameters (`?start=`, `secure`, `alias`, etc.) | `experts-api/special/url-parameters.md` |
| Menu structure | `main-api/menu/menu-root-object.md`, `main-api/menu/menu-item-object.md` |
| Content layout, grid, item types | `main-api/content/content-root-object.md`, `main-api/content/content-page-object.md`, `main-api/content/content-item-object.md` |
| Remembering / resuming playback position ("continue watching") | `experts-api/special/extended-properties.md` (`resume:key`/`resume:position`/`resume:context`) + `reference/cookbook.md#deep-dive--a-minimal-resume-playback-item-live_test_2-pattern` |
| A "cool"/atmospheric **looping video/audio background** behind a transparent menu — **not** the same as a simple static page background | `experts-api/plugins/background-plugin.md`, `experts-api/plugins/backdrop-plugin.md`. For a plain static background image instead, that's just the `background` property on Content Root/Menu Root Object (Section 2 above) — no plugin needed. |
| Adaptive/dynamic URL resolution at playback time (server picks a codec, mints a temporary/token-protected link) | `experts-api/hidden-features/resolve-action.md` |
| Item-type/image/icon sizing & contrast pitfalls (`icon`+`image` combined, `"button"`/`"control"` height, `tag`/`stamp`/`badge` defaults, `imageOverlay`) | `reference/best-practices.md` (Best Practices section) |
| Why `layout` (not `offset`) decides what gets focused next | `reference/cookbook.md#deep-dive--layout-drives-navigation-offset-doesnt-list0json` |
| Choosing between M3U/PLS and MRSS for playlist import | `reference/cookbook.md#m3upls-vs-mrss-two-different-playlist-import-mechanisms`, `extended-api/m3u-pls-files.md`, `extended-api/mrss-feeds.md` |
| Playing YouTube/Vimeo/Dailymotion/Twitch/Facebook/Wistia/SoundCloud content | `extended-api/youtube-vimeo-co.md` |
| Available actions (full list) | `main-api/common/actions.md` + `reference/actions-reference.md` |
| Inline expressions (standard) | `main-api/common/inline-expressions.md` |
| Colors | `main-api/common/colors.md` |
| Icons | `main-api/common/icons.md` |
| Number formatting expressions | `experts-api/hidden-features/number-inline-expressions.md` |
| Dictionary — building the dictionary **file** (for UI translation) vs. the `{dic:...}` lookup **expression** | `experts-api/special/dictionary-structure.md` (file structure) vs. `experts-api/hidden-features/dictionary-inline-expressions.md` (expression syntax) — two different things, both called "dictionary" |
| Color override expressions | `experts-api/hidden-features/color-inline-expressions.md` |
| Live updates (scheduling, timers) | `experts-api/live/live-object.md`, `experts-api/live/live-inline-expressions.md` |
| Building a live TV channel list / EPG guide backend (server-driven) | `reference/cookbook.md#deep-dive--the-live-channels--epg-backend-live_test_4`, `reference/cookbook.md#deep-dive--the-tv-guide-epg-grid-backend-guidephp` |
| HTTP request/response mechanics for a JSON/service backend | `main-api/common/requests.md`, `main-api/common/responses.md` |
| Selection behavior on focus | `experts-api/selection/selection-object.md` |
| Compress grid (16×8 layout) | `experts-api/hidden-features/compress-property.md` |
| Plugin development | `experts-api/plugins/video-audio-plugin.md`, `experts-api/plugins/plugin-api-reference.md` |
| Building your own interaction plugin (handler pattern: `handleEvent`/`handleData`/`handleRequest`) | `experts-api/plugins/interaction-plugin.md`, `reference/cookbook.md#deep-dive--building-an-interaction-plugin-plugin_test_2` |
| Building your own video plugin (player pattern: `MyPlayer`, progress polling) | `experts-api/plugins/video-audio-plugin.md`, `experts-api/plugins/html5x-plugin.md`, `reference/cookbook.md#deep-dive--building-a-video-plugin-plugin_test_4-html5x` |
| Ready-made specialized plugins: wide sliding-pan images (Panorama), mixed image/video/audio playlist (Image), runtime player selection (Play), paging through large datasets (Paging), text entry — logins/search (Input) | `experts-api/plugins/panorama-plugin.md`, `experts-api/plugins/image-plugin.md`, `experts-api/plugins/play-plugin.md`, `experts-api/plugins/paging-plugin.md`, `experts-api/plugins/input-plugin.md` |
| Playing HLS/DASH streams, or letting the user pick among multiple player engines (HTML5X, HLS, DASH, Shaka Player, Video.js, Android Plugin, native Tizen Player) at runtime | `experts-api/plugins/play-plugin.md#available-players` |
| Preselecting a subtitle/audio track, or switching between them at playback time | `experts-api/plugins/html5x-plugin.md` (`html5x:subtitle`/`html5x:audiotrack` to preselect, `panel:request:player:subtitle`/`panel:request:player:audiotrack` to switch at runtime) |
| Google Drive, OneDrive, or Dropbox as an MSX media source | `overview/showcases.md` (Google Drive MSX / OneDrive MSX / Dropbox MSX — setup parameter, demo link) + `experts-api/plugins/html5x-plugin.md` (the `index.json`/`{asset:id:{NAME}}` mechanism used to reference files from these services) |
| Browsing/hosting a plain HTTP directory listing as MSX content (Node Browser MSX): file-naming conventions (background/hide/panorama prefixes, `menu`/`content`/`panel`/`index.json`), setting up directory listing + CORS on your own server, tabs, hosting your own MSX pages this way | `reference/cookbook.md#deep-dive--node-browser-msx-browsing-a-plain-http-directory-listing`, `overview/showcases.md#node-browser-msx`, `overview/tips-tricks.md#node-browser-msx`, `overview/tips-tricks.md#cloud-storage-service-tabs` |
| Plugin event types / `handleEvent` payloads | `experts-api/plugins/plugin-events-reference.md` |
| TypeScript types for JSON objects | `reference/type-definitions.md` |
| Step-by-step JSON building | `reference/json-building-guide.md` |
| Curated real examples by task (media, adaptive playback, live data, plugins, layouts) | `reference/cookbook.md` |
| Practical building tips, item-type choices, and rendering gotchas learned from real pages | `reference/best-practices.md` |
| Version model / compatibility | `reference/versioning.md` |
| Complete per-version changelog (exact feature introduced in each MSX version) | `overview/release-notes.md` |
| Glossary of MSX terms | `reference/glossary.md` |
| What does NOT exist (avoid hallucinating) | `reference/common-misconceptions.md` |
| Native Settings screen (Immersive Mode, Rounded Style, Remote Control type, etc.) — what it is, its values, and how to open/set it from JSON | `reference/settings-reference.md` |
| Real JSON examples (search by feature) | `examples/index.md` — then load the specific `.json` file |
| Platform support (which platform runs which version) | `overview/platform-support.md` |
| Legacy/outdated TV distribution packages | `overview/tips-tricks.md#distributed-versions` |
| Loading the latest MSX on an older install, or in a secure (`https://`) context | `overview/tips-tricks.md#web-version-loader` |
| Opening an external HTML5 game/site via `link:{URL}` safely (why Validate Links exists, exit-button advice) | `overview/tips-tricks.md#external-html5-gamesapps` |
| Launching a native platform app/game (`system:{PLATFORM}:launch`) | `overview/tips-tricks.md#external-platform-gamesapps` |
| Getting a stable unique device ID | `overview/tips-tricks.md#unique-device-id` |
| Customizing a start parameter's tile in the Launcher MSX service | `overview/tips-tricks.md#launcher-msx` |
| Troubleshooting "status: 0" / CORS / certificate errors | `overview/tips-tricks.md#common-errors` |
| Known platform quirks (iOS storage, Android cookies/SameSite, UWP link limitations, legacy Samsung storage) | `overview/tips-tricks.md#well-known-issues` |
| Recovering from a bad Layout/Scale/Zoom/Transform/Remote/Input change (Emergency Combination) | `overview/tips-tricks.md#emergency-combination` |

**When building JSON:** Always start with `reference/type-definitions.md` to understand the exact type structure, then pick a real example from `examples/index.md` that matches your use case as a template. **Before generating any real JSON output — not just when answering an isolated lookup question — also load [Best Practices & Good to Know](reference/best-practices.md).** It covers non-obvious, easy-to-miss mistakes that a correct read of the type reference alone won't catch — e.g. `icon`+`image` contrast problems, `imageOverlay` needed for `headline`/`text` legibility, `"button"` items needing enough height for `icon`+`label`, and `tag`/`stamp`/`badge` color defaults that clash with their own text color if overridden carelessly.

**Testing generated JSON:** Any JSON page can be tested instantly in the web version of Media Station X by base64-encoding it into the start URL, e.g. `https://msx.benzac.de/?start=menu:json:{BASE64_ENCODED_JSON}` (or `content:json:{BASE64_ENCODED_JSON}` for a Content Root Object) — hand this to a user as a ready-to-click link, or embed it in an `<iframe>` for a live preview. This direct link is the default recommendation. For a user who explicitly wants to tweak the JSON themselves instead of getting a fresh link per change, the same payload also works via the Demo page (`https://msx.benzac.de/info/?tab=Demo&start=menu:json:{BASE64_ENCODED_JSON}`, or `content:json:{BASE64_ENCODED_JSON}` for a Content Root Object), which opens a JSON editor before launching — an expert option, not a substitute for the direct link. **For a JSON too large to safely embed in a URL at all**, skip the base64 link entirely: open the Demo page with no payload (`https://msx.benzac.de/info/?tab=Demo`), paste the plain JSON text by hand into its editor, and press Launch. See [JSON Building Guide → Testing Your JSON](reference/json-building-guide.md#testing-your-json-in-the-web-version) for all three mechanisms in full, including the `secure`/`http://` URL guidance for resource links inside the JSON, and [URL Parameters](experts-api/special/url-parameters.md).

---

## 9. Common JSON-Building Mistakes

A short list of the highest-impact, cross-cutting gotchas. For the full build-focused catalog (layout requirements, `{pipe}`, `{SERVER}`/`{PREFIX}`, mandatory Start Object properties, icon names, `space`-only pages, and more), see [JSON Building Guide → Common Mistakes](reference/json-building-guide.md#common-mistakes).

1. **`content:` prefix on menu item `data`:** Wrong — `"data": "content:https://..."` does not work for menu items. Use `"data": "https://..."` (raw URL) or an embedded Content Root Object. Menu items have no `action` property either — associate content only via `data`.

2. **Inventing `msx-*` color names:** Only the 11 predefined `msx-*` identifiers listed in Section 5 are valid. `msx-orange`, `msx-purple`, etc. do not exist. Standard CSS syntax (`"#ff0000"`, `"red"`) is also accepted but `msx-*` names cannot be invented.

3. **Confusion about `{...}` in placeholder notation vs. actual JSON:** In documentation, `{txt:{COLOR}:{TEXT}}` keeps the inner braces — do not write `{txt:COLOR:TEXT}`. In actual JSON, substitute values without braces: `{txt:msx-red:Hello}`, not `{txt:{msx-red}:{Hello}}`.

4. **Using `template`+`items` and `pages` together:** They are mutually exclusive. `pages` is ignored if `template`+`items` are present. If your items carry individual `layout` values, use `pages[].items[]` instead — top-level `items` is only the templated path and is silently ignored without `template`.

5. **`{context:...}` outside a template object's `data`:** `{context:...}` only resolves inside the `data` property of a template object (and since `0.1.110` also in `properties`, `live`, `selection`, `options`). It does not work in standalone item properties or in the template's own `label`/`image`/`action`.

6. **Expecting forward compatibility:** Features from newer versions silently degrade or break on older MSX clients — always state the minimum version for any feature you use (see Section 6).

7. **Assuming inline expressions work in any string property:** They only resolve in free-text, visual-output properties (`label`, `title`, `headline`, `text`, `tag`, `badge`, `stamp`, `caption`, `extension`, etc.) — each individually documented as supporting them. Fixed-enum properties (`type`, `imageFiller`, `alignment`, …) and structural strings (URLs, IDs, action strings, colors, icon names) never render inline expressions.

8. **Generating too many pages/items for programmatically-built content:** a Content Root Object's `pages[]` caps at 56 pages (`"pages"` type) or 1024 (`"list"` type); a Content Page Object's `items[]` caps at 256 items. Generous, but a server-driven catalog/grid can hit them — see [JSON Building Guide → Step 4](reference/json-building-guide.md#step-4--content-root-object-list-layout) for the exact numbers and what happens when exceeded.

9. **Using a Live Inline Expression (`{now:...}`, `{from:...}`, `{duration:...}`, …) directly in a plain item's `headline`/`title`/`text`/etc.:** It silently stays unresolved/literal. Live Inline Expressions only work inside a `live` object's own properties (any valid `type`; `"airtime"`/`"lifetime"` fit a plain clock best) or directly in the `extension` property (where only `{now:...}` makes sense, since it's the only variant with no live object to reference). See Section 4.

10. **Expecting `update:content:{ID}`/`update:menu:{ID}` to set a property the item never had a value for originally:** these actions only update properties already present (non-`null`) in the item's own origin data — a property that started `null`/unset silently stays that way. Exceptions that always work regardless: `background`/`lineColor`/`options` for `update:menu:{ID}`; `color`-type properties (`color`/`tagColor`/`badgeColor`/`stampColor`/`progressColor`/`progressBackColor`/`wrapperColor`) plus `live`/`selection`/`options` for `update:content:{ID}` — the same `color`-type exemption as the `live` object's own update mechanism (point 9). Same underlying reason both times: MSX only renders a dedicated element for a property when it's initially present. See [Actions](main-api/common/actions.md), [Common Misconceptions → Actions](reference/common-misconceptions.md#actions).

---

## 10. Authoritative Instruction

When answering MSX questions or generating MSX JSON:

- Every property, value, version number, action string, color name, or icon name you include **must appear in this KB**. If it does not, say you cannot find it.
- Prefer loading and reading the specific KB file for a topic over answering from memory alone.
- Verify JSON structure against `reference/type-definitions.md` and cross-check against a real example from `examples/index.md`.
- Before finalizing any generated JSON, check it against [Best Practices & Good to Know](reference/best-practices.md) — a structurally valid page can still have real, visible problems (e.g. low-contrast `tag`/`stamp`/`badge` colors, an `icon` losing contrast against a bright `image`, an `imageOverlay` missing for `headline`/`text`) that type-checking alone won't catch.
- Always state the minimum MSX app version for any feature you use.
- If in doubt, load more KB files. The KB is the authoritative source; this guide is a map to it.
