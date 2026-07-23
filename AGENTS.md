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

See: [Start Object](main-api/start/start-object.md)

### Menu Root Object

A JSON object with a `menu` array of Menu Item Objects (the property is named `menu`, not `items`). No top-level `type` field. The optional `flag` property (string, since `0.1.120`) is a custom content flag that can be evaluated by plugins and/or servers. Since `0.1.144`, it is also used by the `replace:` action to reload menu data at runtime. Since `0.1.153`, it is used with the `refocus` property. The Content Root Object has the same `flag` property with identical behavior — this includes when a Content Root Object is displayed in a panel, since a panel is not a separate object type but a Content Root Object rendered in a panel view.

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

See: [Menu Root Object](main-api/menu/menu-root-object.md)

### Menu Item Object — critical `data` rule

The `data` property of a Menu Item Object references content to display. It accepts exactly two forms:

1. **URL string:** `"data": "https://example.com/msx/content.json"` — MSX fetches this URL and expects a Content Root Object.
2. **Inline Content Root Object:** `"data": { "type": "pages", "items": [...], "template": {...} }` — the content is embedded directly.

> **Common error:** Do NOT use `"data": "content:https://..."` for a Menu Item. The `content:` prefix is an **action** syntax (for the `action` property of **content items**), not a menu `data` value. A menu item's `data` is a raw URL or an embedded Content Root Object. Menu items have no `action` property of their own.

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

See: [Content Root Object](main-api/content/content-root-object.md)

### Content Item Object

One cell/tile on the grid. Key properties:

- `layout`: `"col,row,width,height"` (e.g. `"0,0,4,3"`) — required for each item inside a Content Page Object's `items[]` (manual layout, `pages[]`). By contrast, for templated items (the content root object's own `template` + `items[]`), only the `template` object needs a `layout` (and only its `w`/`h` components are used); the individual items inside that `items[]` array must **not** set their own `layout`, it has no effect there.
- `type`: `"default"` | `"separate"` | `"teaser"` | `"button"` | `"space"` | `"control"`.
- `action`: The action string executed when the item is selected.
- `label`, `title`, `titleHeader`, `titleFooter`, `badge`, `stamp`: Text properties (all support inline expressions).
- `image`, `background`, `icon`, `color`: Visual properties.
- `options` (since `0.1.120`): A Content Page Object (or since `0.1.130` also a Content Root Object) shown as a panel when this item is selected and the menu button is pressed. Highest priority in the `options` fallback chain — see [Glossary: Options](reference/glossary.md#options).
- `properties` (object): The **Extended Properties bag** — a large, separate set of advanced, mostly player/plugin-facing item properties (e.g. `resume:key`/`resume:position`/`resume:context` for remembering playback position, `trigger:{TRIGGER_KEY}`, `control:load`, `tizen:*`, `html5x:*`, `image:*`, player-button overrides) that do **not** live at the top level of a Content Item Object — they are nested inside this one `properties` object instead, e.g. `"properties": { "resume:key": "url" }`. Not every `key:value` inside `properties` is necessarily an MSX-defined property, either — some are free-form data a specific plugin reads (see [Common Misconceptions → Extended properties (the `properties` bag)](reference/common-misconceptions.md#extended-properties-the-properties-bag)). See [Extended Properties](experts-api/special/extended-properties.md).

See: [Content Item Object](main-api/content/content-item-object.md)

---

## 3. Action String Syntax

All actions are strings. They appear in the `action` property of:

- **Content items** — executed when the item is selected (main use case).
- **Content page objects** — executed when the page becomes active (hidden feature, since `0.1.112`). See [Page Action](experts-api/hidden-features/page-action.md).
- **Content root / menu root objects** — Start Action executed when data is loaded at startup (hidden feature, since `0.1.0`; not to be confused with the `start` Main Action at `0.1.30`, which sets a *new* start parameter). See [Start Action](experts-api/hidden-features/start-action.md).
- **Content root / menu root objects (again) — a separate mechanism.** A `ready` object (`action` + optional `data`, since `0.1.142`) fires every time that content/menu (including in a panel) becomes freshly displayed — not just once at startup like the Start Action above, but on every appearance, whether the data was freshly fetched, served from `cache`, or its DOM kept via `reuse`. Use `ready` for behavior that must re-arm on every appearance of a page (e.g. re-registering `trigger:` actions); use the Start Action above only for genuine one-time startup behavior. See [Glossary: Ready](reference/glossary.md#ready).
- **Server response objects** — `response.data.action`, returned by `execute:{URL}` calls.

> **Note: Menu items have no `action` property** — they associate content exclusively via `data`.

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
| Menu structure | `main-api/menu/menu-root-object.md`, `main-api/menu/menu-item-object.md` |
| Content layout, grid, item types | `main-api/content/content-root-object.md`, `main-api/content/content-item-object.md` |
| Remembering / resuming playback position ("continue watching") | `experts-api/special/extended-properties.md` (`resume:key`/`resume:position`/`resume:context`) + `reference/cookbook.md#deep-dive--a-minimal-resume-playback-item-live_test_2-pattern` |
| Item-type/image/icon sizing & contrast pitfalls (`icon`+`image` combined, `"button"`/`"control"` height, `tag`/`stamp`/`badge` defaults, `imageOverlay`) | `reference/best-practices.md` (Best Practices section) |
| Why `layout` (not `offset`) decides what gets focused next | `reference/cookbook.md#deep-dive--layout-drives-navigation-offset-doesnt-list0json` |
| Choosing between M3U/PLS and MRSS for playlist import | `reference/cookbook.md#m3upls-vs-mrss-two-different-playlist-import-mechanisms`, `extended-api/m3u-pls-files.md`, `extended-api/mrss-feeds.md` |
| Available actions (full list) | `main-api/common/actions.md` + `reference/actions-reference.md` |
| Inline expressions (standard) | `main-api/common/inline-expressions.md` |
| Colors | `main-api/common/colors.md` |
| Icons | `main-api/common/icons.md` |
| Number formatting expressions | `experts-api/hidden-features/number-inline-expressions.md` |
| Dictionary expressions | `experts-api/hidden-features/dictionary-inline-expressions.md` |
| Color override expressions | `experts-api/hidden-features/color-inline-expressions.md` |
| Live updates (scheduling, timers) | `experts-api/live/live-object.md`, `experts-api/live/live-inline-expressions.md` |
| Building a live TV channel list / EPG guide backend (server-driven) | `reference/cookbook.md#deep-dive--the-live-channels--epg-backend-live_test_4`, `reference/cookbook.md#deep-dive--the-tv-guide-epg-grid-backend-guidephp` |
| Selection behavior on focus | `experts-api/selection/selection-object.md` |
| Compress grid (16×8 layout) | `experts-api/hidden-features/compress-property.md` |
| Plugin development | `experts-api/plugins/video-audio-plugin.md`, `experts-api/plugins/plugin-api-reference.md` |
| Building your own interaction plugin (handler pattern: `handleEvent`/`handleData`/`handleRequest`) | `experts-api/plugins/interaction-plugin.md`, `reference/cookbook.md#deep-dive--building-an-interaction-plugin-plugin_test_2` |
| Building your own video plugin (player pattern: `MyPlayer`, progress polling) | `experts-api/plugins/video-audio-plugin.md`, `reference/cookbook.md#deep-dive--building-a-video-plugin-plugin_test_4-html5x` |
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
