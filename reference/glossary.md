---
title: Glossary
category: Reference
summary: Definitions of all key MSX and TVX terms, sourced exclusively from the MSX knowledge base.
---

# Glossary

All terms are sourced from the MSX API documentation. Each entry links to its primary source document.

---

## Action

A string value (an "action string") assigned to the `action` property of a content item, content page object, content root object, or menu root object — e.g. `"video:http://example.com/video.mp4"`, always a plain string in JSON, never an object. Menu items have no `action` property — they associate content exclusively via `data`. When a content item's action is executed (item selected), MSX interprets and executes the action string. Examples: `video:{URL}`, `content:{URL}`, `info:{TEXT}`, `execute:{URL}`.

Multi-actions wrap multiple action strings in `[…|…]` notation, sharing one `data` property across all of them; the [`data` Action](#data-action) is the alternative for when each action string in the sequence needs its own `data` — there, each entry in the `actions` array is an `{ "action": …, "data": … }` object, but the `action` value inside it is still a plain action string.

See [Actions](../main-api/common/actions.md), [Actions Reference](actions-reference.md).

---

## Backward Compatibility

The MSX engine is fully backward-compatible: a JSON page that worked in an older MSX version will continue to work in all newer versions without modification. See [Versioning](versioning.md).

---

## Benchmark Plugin

A plugin that tests the rendering performance of a TV device. Three benchmark plugins exist: Renderer, Drawing, and Particles. See [experts-api/benchmark/](../experts-api/benchmark/).

---

## Cache

A `boolean` property (default `true`, since `0.1.58`) on the Content Root Object and Menu Root Object. Loading a menu/content URL goes through three checks, in this order — `cache` governs only the middle one, [`reuse`](#reuse) governs the other two:

1. **Same-URL shortcut.** If the relevant slot (see below) is already showing data for that exact same URL right now, and `reuse` is not `false`, MSX reuses it immediately — no backend/cache check and no network request at all. Otherwise, continue to step 2.
2. **Backend cache.** MSX asks its backend service whether that URL's data is already cached; if so, and `cache` is not `false`, the cached JSON is returned instead of a fresh fetch. Otherwise the data is (re-)loaded over the network.
3. **DOM reuse.** Once the JSON data is available (from step 2, cached or freshly fetched), MSX compares it against the JSON instance the slot's *currently* present DOM was built from — not a separate stored DOM cache, just a live identity check against whatever is in place right now; if it's the same instance, and `reuse` is not `false`, that existing DOM is kept as-is instead of rebuilt — which as a side effect preserves focus. Otherwise the DOM is (re)built fresh.

MSX keeps four separate slots for this, each keyed by URL: `mainMenu` (a Menu Root Object shown in the main scene), `mainContent` (a Content Root Object shown in the main scene, i.e. not in a panel), `panelContent` (a Content Root Object shown in a panel), and `sliderContent` (the templated image data behind a slideshow, i.e. `slideshow:{URL}`). Because these are separate slots, loading into one never evicts another — a menu and its currently-displayed content, for instance, are governed fully independently, each by its own object's `cache`/`reuse` properties.

`cache` never comes into play for data loaded via `data` (e.g. `menu:data`, `content:data`, `panel:data`, `slideshow:data`) — there is no URL, so **neither step 1 nor step 2 runs at all** for it; only step 3 applies. `reuse` is therefore the *only* one of the two properties with any effect on `data`-loaded content: if the same `data` object (the exact same instance — e.g. a Content Item's unchanged `data` triggering a `panel:data` action repeatedly) is passed again, `reuse: true` keeps the already-built DOM instead of rebuilding it — preserving not just focus but also any live state applied on top since (e.g. via `update:` actions); `reuse: false` always rebuilds fresh, discarding both — and since step 1/2 don't exist for `data` in the first place, `reuse: false` alone is already enough to guarantee a fresh rebuild here, no `cache` setting needed. `json:` (e.g. `menu:json:{BASE64}`) behaves differently again: the base64 payload itself is treated like a URL, so it goes through the full three-step check (including `cache`) like any other URL.

**To guarantee data is always freshly loaded and freshly built, set both `cache: false` and `reuse: false` — regardless of whether it's loaded via a URL, `data`, or inline `json:`.** `cache: false` alone still leaves step 1's same-URL-already-showing shortcut (governed by `reuse`) able to short-circuit the reload before the cache/network step is ever reached.

**At runtime, [Release](#release)** (`release:menu`/`release:content`/`release:panel`) is the action-level equivalent: it clears the target slot's cached data and any built-DOM reference outright, forcing the next matching load through the full network-fetch-and-DOM-rebuild path regardless of that object's own `cache`/`reuse` values — useful when you can't simply set `cache: false`/`reuse: false` in the JSON itself (e.g. the data was already loaded once with caching enabled, and now needs a one-off forced refresh).

See [Reuse](#reuse), [Release](#release), [Content Root Object](../main-api/content/content-root-object.md), [Menu Root Object](../main-api/menu/menu-root-object.md).

---

## `compress`

A boolean property (since `0.1.123`) primarily used on the Content Root Object, where it expands the layout grid from 12×6 to 16×8 (content) or 8×6 to 10×8 (panel) — the Content Root is the only place that determines the grid size. The same property can also be set on a Content Page, Content Item, Header, Footer, Overlay/Underlay, or Template object, but there it only controls font-size adjustments for that scope and does **not** change the grid. See [Content Root Object](../main-api/content/content-root-object.md), [Compress Property](../experts-api/hidden-features/compress-property.md).

---

## Content Flag

An identifier string associated with a loaded content page, used in `replace:content:{CONTENT_FLAG}:{REQUEST_ACTION}` and `close:{CONTENT_FLAG}` to target specific loaded pages. See [Replace Action](../experts-api/hidden-features/replace-action.md).

---

## Content Item Object

The smallest unit of visible content in MSX. Contains the display properties (`label`, `image`, `icon`, `layout`, etc.) and the `action` to execute when selected. Lives inside a Content Page Object or directly in a templated Content Root Object.

See [Content Item Object](../main-api/content/content-item-object.md).

---

## Content Page Object

A container for one "screen" of content items. Both `"pages"` and `"list"` type Content Root Objects use `pages[]` arrays of Content Page Objects — the `type` only affects navigation direction and page stacking, not the structure.

See [Content Page Object](../main-api/content/content-page-object.md).

---

## Content Root Object

The top-level JSON object returned by a content URL. Has an optional `type` property (`"pages"` or `"list"`, defaults to `"pages"`). Both types use the same 12 × 6 grid; the difference is navigation direction: `"pages"` = horizontal (pages side by side), `"list"` = vertical (pages stacked — vertical gaps between pages are removed). Contains the content items either via `pages[]`, or via `template` + `items` (works with both types).

See [Content Root Object](../main-api/content/content-root-object.md).

---

## `data` Property

The `data` property has different meanings depending on where it appears:

1. **On a Menu Item Object:** The URL (string) or embedded Content Root Object (object) to load when the item is focused. Does **not** accept `content:{URL}` prefix — that is action syntax for the `action` property of content items (menu items have no `action` property). If left unset (or explicitly `null`), MSX shows a default content page with the headline *"Content Not Available"* — this is a graceful fallback, not an error. An invalid/malformed `data` value (e.g. a broken URL), by contrast, does produce an error.
2. **On a Content Item Object:** Multi-purpose payload property. Used as:
   - Supplemental data submitted with server actions (`execute:…`).
   - The embedded target object for `:data` action variants: `panel:data` (Content Root Object), `content:data` (Content Root Object), `menu:data` (Menu Root Object).
   - The structure resolved by `{context:{PROPERTY}}` when using `template` + `items`.
3. **On a Menu Root Object or Content Root Object:** The data for the Start Action — the `action` executed once when that root object's data is loaded at startup (since `0.1.0`). See [Start Action](../experts-api/hidden-features/start-action.md).
4. **On a Content Page Object:** The data for the Page Action — the `action` executed when that page becomes active (since `0.1.112`). See [Page Action](../experts-api/hidden-features/page-action.md).
5. **On a Live Object (or one of its `coming`/`running`/`over` state objects):** The data for the live action set via that same object's own `action` property (since `0.1.70`). See [Live Object](../experts-api/live/live-object.md).
6. **On a Selection Object:** The data for the selection action set via that same object's own `action` property (since `0.1.110`). See [Selection Object](#selection-object).

See [Menu Item Object](../main-api/menu/menu-item-object.md), [Content Item Object](../main-api/content/content-item-object.md).

---

## Dictionary

A key-value JSON structure that MSX can load to translate the application's own user interface and other services (e.g. the Launcher MSX service). Set declaratively via the `dictionary` property on a Content Root Object or Menu Root Object (since `0.1.120`, only takes effect if that root object is loaded at startup), or at runtime via the internal `dictionary:{URL}` action / `dictionary:none` to reset (since `0.1.160+`). As an additional feature on top of this, a JSON author can also add custom entries to a dictionary file and look them up in their own content via `{dic:{KEY}}` / `{dix:{KEY}}` inline expressions — or reuse the entries the MSX app itself already ships with.

See [Dictionary Inline Expressions](../experts-api/hidden-features/dictionary-inline-expressions.md), [Dictionary Structure](../experts-api/special/dictionary-structure.md).

---

## Emergency Combination

A recovery mechanism for when a bad change to Layout, Scale Factor, Zoom Factor, Transform, Remote Control, or Input Type makes the UI unusable: entering `911` or the key sequence `↑↑↓↓←→←→` followed by `OK` resets settings and reloads the application. Available since `0.1.153` — one version before the settings-validation panel (`0.1.154`) that guards those same six settings after a restart; the two mechanisms are independent (neither depends on the other), not sequential.

See [Tips & Tricks — Emergency Combination](../overview/tips-tricks.md#emergency-combination), [In-App Settings Reference](settings-reference.md).

---

## Extended API

The portion of the MSX API that integrates third-party media sources: YouTube/Vimeo, M3U/PLS playlists, and MRSS feeds. These are consumed by MSX as special URL patterns without the developer needing to build a custom Content Root Object.

See [extended-api/](../extended-api/).

---

## Experts API

The portion of the MSX API intended for experienced users: Special, Live, Selection, Plugin, Hidden Features, and Benchmark sub-APIs. These extend the main API with advanced behaviors.

See [experts-api/](../experts-api/).

---

## Execute (Server Action)

An action that sends an HTTP POST request to a server URL, submitting the item's `data` property as a JSON body. By default, MSX sends `Content-Type: application/x-www-form-urlencoded` (to avoid CORS preflight) even though the body is JSON — parse the body as JSON regardless of the header. Use `execute:accurate:{URL}` (since `0.1.90`) to send the correct `Content-Type: application/json` header at the cost of a CORS preflight. The server must respond with a `response` object containing `response.data.action` (the action to execute next).

Without the `service:` prefix, only one server action can be running at a time on the application side — parallel execution is not allowed. Use `execute:service:{URL}` (since `0.1.90`, max 128 concurrent) when multiple server actions need to run at once.

Syntax: `execute:{URL}` (since `0.1.30`). Variants: `execute:code:`, `execute:user:`, `execute:accurate:`, `execute:service:`, etc.

See [Actions](../main-api/common/actions.md), [Responses](../main-api/common/responses.md).

---

## Focus

The currently highlighted item in the content grid or menu. Actions `focus:{ID}` and `focus:index:{INDEX}` move focus programmatically. The `selection` property on content items can control focus behavior — see [Selection Object](#selection-object).

---

## Forward Compatibility

Not guaranteed in MSX. A JSON page that uses features introduced in a newer MSX version will not function correctly on devices running an older version. The failure mode varies by severity: silent degradation (feature ignored, UI still renders but looks different or falls back), a warning state (unrecognized action string — a warning is shown, but the action just does not execute; an outright error is rare for a pure version mismatch), or, worse, not rendered at all (a layout/position value out of range for the client's grid — the affected item, or in the worst case the whole page, simply isn't displayed, e.g. `"compress": true` items placed beyond the pre-`0.1.123` grid). See [Versioning → Forward Compatibility](versioning.md#forward-compatibility) and [AGENTS.md → Forward Behavior](../AGENTS.md#forward-behavior-newer-features-on-older-msx-versions) for the worked `compress` example.

---

## Grid

The layout coordinate system for content items. The grid size is the same for `"pages"` and `"list"` type — `type` only affects navigation direction and page stacking, not the grid dimensions.

| Context | compress: false (default) | compress: true (since 0.1.123) |
|---|---|---|
| Content (`"pages"` or `"list"`) | 12 columns × 6 rows | 16 columns × 8 rows |
| Panel (always list-type behavior) | 8 columns × 6 rows | 10 columns × 8 rows |

Item position is set by the `layout` property as `"col,row,width,height"`. In a template object, only `w` and `h` are used; `x` and `y` are ignored.

The `compress` cell-shrink factor (`0.75`×) is identical for content and panels — only the resulting column/row counts differ. Content's counts (12→16, 6→8) are chosen so the *total* grid width/height stays pixel-for-pixel identical to the uncompressed grid (`16 × 0.75 = 12`, `8 × 0.75 = 6`). A panel's rows follow the same exact-fit rule (`8 × 0.75 = 6`), but its columns don't: `8 ÷ 0.75 = 10.67` isn't a whole number, so the panel simply uses `10` whole compressed-size columns and renders slightly narrower overall (re-centered on screen) rather than gaining a fractional 11th column to preserve its original width.

---

## Hidden Feature

A feature that was not originally intended as part of the official MSX API but has been exposed and is safe to use in production. Examples: `{num:…}`, `{dic:…}`, `compress`, `replace:…`, `video:resolve:…`/`background:resolve:…` (Resolve Action), the Start Action, the Page Action, `execute` (content items auto-execute on load since `0.1.0`; on menu items, since `0.1.120`, it instead auto-enters that item's content on load — see [Common Misconceptions → Hidden features](common-misconceptions.md#hidden-features) for the full chain).

See [experts-api/hidden-features/](../experts-api/hidden-features/).

---

## HbbTV

Hybrid Broadcast Broadband TV — a standard for smart TV broadcast apps. MSX has an HbbTV-specific version that supports broadcast-channel actions: `video:broadcast:current` and `system:hbbtv:launch:{URL}` are documented Main Actions (both since `0.1.30`). Their internal-only sibling variants — including the entire `audio:broadcast:...` family, which has no individually-documented Main Action variant at all — share the same `0.1.30` version; none of the three families (`audio:broadcast:...`, `video:broadcast:...`, `system:hbbtv:...`) fall back to the `0.1.160+` blanket that otherwise applies to internal-only actions. These actions are functional but practically obsolete — no currently supported platform relies on them in practice, which is also why [Internal Actions](../experts-api/special/internal-actions.md) does not mark the family rows as Main Actions. See [Actions](../main-api/common/actions.md), [Actions Reference](actions-reference.md#broadcast-and-hbbtv-actions-a-third-case).

---

## `important`

A boolean property name reused on **three different objects with three genuinely different meanings** — do not treat it as one concept:

- **Content Root Object** (since `0.1.58`): if `true`, the Content Root's own `headline` is shown even when the content was loaded via a menu item (normally the menu item's `label` is shown instead in that case).
- **Content Page Object** (since `0.1.110`): if `true`, this page hides its corresponding overlay/underlay page while active; if the page itself *is* an overlay/underlay page, `true` means it is never hidden.
- **Selection Object** (since `0.1.110`): only relevant for mouse/touch control — if `true`, the item's action executes only while the item actually has focus (not just cursor-over, unless the hover effect is set to "Preselect", since `0.1.136`). This `hover_effect` setting value "Preselect" is unrelated to the `preselect` property (since `0.1.160`) — same-sounding name, two different things. See [Versioning](versioning.md#version-baseline-010).

See [Content Root Object](../main-api/content/content-root-object.md), [Content Page Object](../main-api/content/content-page-object.md), [Selection Object](../experts-api/selection/selection-object.md).

---

## Inline Expression

A text substitution token embedded in free-text, visual-output string properties (`label`, `headline`, `text`, `caption`, etc.), resolved at render time by MSX. Examples: `{sp}`, `{br}`, `{ico:star}`, `{txt:msx-red:Hello}`, `{context:{PROPERTY}}`. Not every string property qualifies — fixed-enum properties (e.g. `type`, `imageFiller`) and structural/technical strings (URLs, IDs, action strings, colors, icon names) do not support inline expressions; each property reference states explicitly whether it does.

See [Inline Expressions](../main-api/common/inline-expressions.md).

---

## Interaction Plugin

An HTML page loaded by MSX into a background iframe via a plugin URL, used to handle custom events, data, and requests (e.g. loading content/playlists/slideshows, executing actions). Requires MSX `0.1.82`+. Technically, MSX accepts any HTML page as an interaction plugin even without JavaScript — but without it, the plugin can't actually do anything, since every interaction-plugin task requires calling into the `TVXInteractionPlugin` interface via JavaScript. It receives no key, mouse, or touch input of its own — all interaction is still managed by MSX itself. Distinct from video/audio plugins. Accessed via `interaction:…` action variants.

See [Interaction Plugin](../experts-api/plugins/interaction-plugin.md).

---

## Internal Action

An action string used the same way as a Main Action (e.g. in the `action` property), but undocumented/internal-only and not part of the official, version-stable Main Actions list. Internal actions extend the standard action vocabulary with hundreds of advanced behaviors. Unlike Main Actions, they typically have no individual "since version" and carry only a blanket minimum of MSX `0.1.160+` — see [Versioning](versioning.md) for why no finer version control exists and why this rarely matters in practice. Not to be confused with the Plugin API (`TVXVideoPlugin`, etc.), which is a separate JavaScript API for writing plugin pages. Also distinct from documented hidden-feature actions (e.g. Replace Action, Start Action, Resolve Action), which are neither Main Actions nor blanket-`0.1.160+` internal actions — each has its own individually documented since-version. A handful of further internal-only action families are also exceptions to the blanket floor despite not being Main Actions or Hidden Feature Actions either: the entire Broadcast/HbbTV family (`0.1.30` — `audio:broadcast:...` has no Main Action counterpart at all, yet still isn't blanket `0.1.160+`) and `execute:service:...` (`0.1.90`). See [Actions Reference](actions-reference.md#internal-only-actions--overview-require-01160) for the complete, precise list of exceptions.

See [Internal Actions](../experts-api/special/internal-actions.md).

---

## Invalidate

An internal action family (`invalidate:content`, `invalidate:menu`, `invalidate:panel`, plus `:{ID}`, `:overlay:{ID}`, `:underlay:{ID}`, and `:flag:{FLAG}` variants) that is **purely visual**: it makes the current content/menu/panel (or a specific item) appear semi-transparent to signal to the user that a refresh is about to happen. The base and `:flag:{FLAG}` variants signal a full content/menu/panel refresh; the `:{ID}`/`:overlay:{ID}`/`:underlay:{ID}` variants only make that one item semi-transparent, signaling just an item-level refresh, not a full one. It does **not** itself reload, refresh, or clear any data — pair it with a `reload:`, `replace:`, or `execute:` action that performs the actual refresh (see the tab-switching and EPG patterns in [Cookbook](cookbook.md)). Not to be confused with [Release](#release), which actually clears caches and references.

See [Actions Reference](actions-reference.md), [Internal Actions](../experts-api/special/internal-actions.md).

---

## JSON Hierarchy

The nested structure of MSX JSON objects:

```
Start Object
└── parameter (exactly one of):
      ├── "menu:{URL}" → Menu Root Object
      │     └── menu[] → Menu Item Object
      │           └── data → Content Root Object (see below)
      └── "content:{URL}" → Content Root Object (directly, no menu)
            ├── template + items[]  (templated)
            │     └── Content Item Object
            └── pages[]             (manual)
                  └── Content Page Object
                        └── items[] → Content Item Object
```

See [JSON Building Guide](json-building-guide.md), [AGENTS.md](../AGENTS.md).

---

## Launcher MSX

A showcase service that manages many start parameters as selectable, app-tile-like entries — set up via the start parameter `id:x` (or `msx.benzac.de` / `launcher.msx.benzac.de`), requires MSX `0.1.132`+. It defines its own `launcher` object on the Start Object with `type`, `icon`, `image`, `color`, and optionally overriding `name`/`version`/`parameter` — controlling how that start parameter's tile appears in the Launcher MSX service's own selection screen — separate from whatever menu/content JSON that start parameter itself actually loads once launched.<br><br>Since `0.1.164`, MSX itself adopted the same app-tile look natively for its own Setup-completion/Settings screen — but only reads the visual subset of that object (`icon`, `image`, `color`) for this native use. `type` and the `name`/`version`/`parameter` overrides are meaningful only within the separate Launcher MSX service (which decides how/whether a stored parameter launches, and what it displays there), not for MSX's native tile rendering. This native adoption is a small visual borrowing, not a replacement — the Launcher MSX service's own management capabilities (storing/syncing start parameters per device, the Welcome Pages entry, and the pre-configured **MSX Showcases** it initially ships with — each individually useful on its own, see [Showcases](../overview/showcases.md) — etc.) remain independently needed and are still the recommended setup entry point. The two uses are fully compatible — the same `launcher` object works for both.

See [Tips & Tricks — Launcher MSX](../overview/tips-tricks.md#launcher-msx), [Start Object](../main-api/start/start-object.md).

---

## `layout`

A string property on Content Item Objects that specifies grid position as `"col,row,width,height"`. Column and row are 0-based. Example: `"0,0,4,3"` = top-left corner, 4 columns wide, 3 rows tall. Required for non-templated items (inside `pages[].items`). For templated items (via `template` + `items`), set once on the template — only `w` and `h` are used; MSX auto-positions each item on the grid.

---

## Live Inline Expression

An inline expression that is re-evaluated on every render tick (typically every second), used to display dynamic time-based data derived from a Content Item's Live Object. There is no raw-timestamp parameter — `now`, `creation`, `from`, and `to` each read a timestamp already known to the live object (current time, creation time, schedule start/end) and take a `date:{LIVE_DATE_FORMAT}`, `time:{LIVE_TIME_FORMAT}`, or `formatter:{LIVE_FORMATTER_ID}` sub-form, e.g. `{now:date:mm/dd/yy}`, `{from:formatter:date_time}`. `duration`, `progress`, `countdown`, and `overflow` instead take `position`, `percentage` (only `progress`/`countdown`), `time:{LIVE_DURATION_FORMAT}`, or `text:{LIVE_DURATION_TEXT}`, e.g. `{duration:position}`, `{progress:percentage}`. All families since `0.1.70`; the `formatter:` sub-form for `now`/`creation`/`from`/`to` since `0.1.160`.

See [Live Inline Expressions](../experts-api/live/live-inline-expressions.md).

---

## Live Object

A JSON sub-object on a Content Item Object (since `0.1.70`) that tracks time-based state (`init` → `coming`/`running`/`over`) — via schedule (`from`/`to`), lifetime (`duration`/`delay`), airtime (`duration`), or playback state — updating content properties (`title`, `progress`, etc.) and triggering an `action` on each state transition. Evaluated every second while the content item is visible. Each state's `action` fires once per entry into that state — but this is enough to implement polling: e.g. an `over.action` (for `type: "airtime"`) can call a server action whose response updates the content item with a fresh live object (new `duration`/schedule). Assigning this new live object re-arms the state machine, so it eventually re-enters `"over"` and fires the action again — creating a self-sustaining polling loop driven by the server. Used for EPG tiles, countdowns, and playback progress.

A fifth type, `setup`, does not run this `init`/`coming`/`running`/`over` state machine at all — it simply fires its `action` once, the moment the content item becomes visible. This is commonly used to kick off a server request whose response replaces the live object with a real `schedule` or `playback` one (see the EPG backend deep dive in [Cookbook](cookbook.md#deep-dive--the-live-channels--epg-backend-live_test_4)). Valid `type` values are exactly: `"schedule"`, `"lifetime"`, `"airtime"`, `"playback"`, `"setup"`.

See [Live Object](../experts-api/live/live-object.md).

---

## Logger Action

An internal-only action family (`logger:debug:{MESSAGE}`, `logger:info:{MESSAGE}`, `logger:success:{MESSAGE}`, `logger:warn:{MESSAGE}`, `logger:error:{MESSAGE}`) that writes a message to the internal MSX log **and** (except for `debug`) also shows it to the user as a visible message. This is the key difference from the plain message actions `info:`/`success:`/`warn:`/`error:`, which only show the message to the user without logging it. See [Silent Mode](#silent-mode) for how to suppress user-visible messages.

See [Internal Actions](../experts-api/special/internal-actions.md).

---

## Main Action

An official, version-stable action from the primary Actions reference. Has an exact "since version" (from `0.1.0` to `0.1.112`, the highest Main Action since-version in this KB). Distinguished from internal-only actions.

See [Actions](../main-api/common/actions.md), [Actions Reference](actions-reference.md).

---

## Media Station X (MSX)

A white-label TV app that renders its UI entirely from JSON served over HTTP. The app connects to a developer-supplied JSON API; there is no native code for the developer to write. Runs on Smart TVs, Android TV, Fire TV, and more.

See [Home](../overview/home.md), [Quick-Start Guide](../overview/quick-start-guide.md).

---

## Menu Item Object

One entry in a Menu Root Object's `menu` array. Has an `id`, `label`, and `data` (URL or embedded Content Root Object). Can be visually enriched with `icon`, `image`, `background`, `extensionIcon`, `extensionLabel`, `lineColor`, `options`.

See [Menu Item Object](../main-api/menu/menu-item-object.md).

---

## Menu Root Object

The JSON object loaded when the Start Object's `parameter` property starts with `menu:`. Contains a list of Menu Item Objects. Rendered as a vertical list on the left side of the MSX screen.

See [Menu Root Object](../main-api/menu/menu-root-object.md).

---

## MSX Color

Color properties in MSX accept both standard CSS color syntax (e.g. `"#ff0000"`, `"rgb(255,0,0)"`, `"red"`) and the 11 predefined `msx-*` color identifiers:

`msx-red`, `msx-green`, `msx-yellow`, `msx-blue`, `msx-white`, `msx-gray`, `msx-black`, `msx-white-soft`, `msx-gray-soft`, `msx-black-soft`, `msx-glass`.

The `msx-*` colors are the recommended choice — they are theme-aware and consistent across platforms.

See [Colors](../main-api/common/colors.md).

---

## Multi-Action

A special action syntax (since `0.1.58`) that executes multiple actions in sequence: `[action1|action2|…]`. Actions separated by `|`. If the triggering content item provides a `data` property, it is shared by **all** actions in the list. Use `{pipe}` inside action arguments when a literal `|` is needed.

Not to be confused with the [`data` Action](#data-action), a second, same-version (`0.1.58`) way to execute multiple actions in a row — more verbose, but each action gets its own `action`/`data` pair instead of one shared `data` property.

See [Actions](../main-api/common/actions.md).

---

## `data` Action

An action string, literally `"data"` (since `0.1.58`), that executes multiple actions in a row — an alternative to the `[action1|action2|…]` [Multi-Action](#multi-action) syntax. The actions are listed as an `actions` array (of `{ "action": …, "data": … }` objects) inside the triggering item's `data` property, so each action can carry its own `data` instead of sharing one. Useful whenever the actions in the sequence need different data — e.g. updating two different content items with two different texts in one action. As a side benefit, since there is no `|` separator between actions here, a literal `|` inside an action string needs no `{pipe}` escaping (unlike the `[…|…]` syntax):

```json
{
    "action": "data",
    "data": {
        "actions": [
            { "action": "update:content:overlay:info", "data": { "text": "Overlay updated." } },
            { "action": "update:content:underlay:info", "data": { "text": "Underlay updated." } }
        ]
    }
}
```

See [Actions](../main-api/common/actions.md), [AGENTS.md](../AGENTS.md).

---

## Options

A JSON sub-object (a Content Page Object, or since `0.1.130` also a Content Root Object) that MSX displays as a panel when the currently selected item/page/menu item is active and the menu button is pressed. Available on five different object types (all since `0.1.120`), each overriding the fallback below it:

- **Content Item Object** `options` — highest priority; used if the currently selected content item has its own `options`.
- **Content Page Object** `options` — fallback if the selected item has none.
- **Content Root Object** `options` — fallback for the whole content if neither the active page nor the selected item has its own `options`.
- **Menu Item Object** `options` — used if the currently selected menu item has its own `options` (independent chain from the content side).
- **Menu Root Object** `options` — fallback if the selected menu item has none.

`options` has no effect if the content is displayed in a panel. The availability of options for the current selection is indicated in the lower right corner of the content screen, using the caption `"opt/menu"` by default — customizable via the `caption` (since `0.1.130`) / `captionUnderlay` (since `0.1.153`) properties of the Content Page/Root Object used as the `options` value. These two properties set the indicator's *text* only; its overall visibility (shown or hidden entirely, regardless of text) is a separate, launch-time-only `caption` **Framework Setting** (since `0.1.157`) — same name, different mechanism, do not confuse the two. See [In-App Settings Reference](settings-reference.md#4b-launchstart-parameter-only-14-fields). Individual items inside an options page can be given keyboard shortcuts via the `key` property, see [Option Shortcut](../experts-api/hidden-features/option-shortcut.md). The options panel can also be toggled programmatically via the internal `options` action, see [Internal Actions](../experts-api/special/internal-actions.md).

See [Content Root Object](../main-api/content/content-root-object.md), [Content Page Object](../main-api/content/content-page-object.md), [Content Item Object](../main-api/content/content-item-object.md), [Menu Item Object](../main-api/menu/menu-item-object.md), [Menu Root Object](../main-api/menu/menu-root-object.md).

---

## Panel

A Content Root Object rendered as a narrow, centered overlay — full screen height, only part of the width, not docked to a screen edge — floating on top of whatever is currently shown (the main content page, a running video/audio, or another panel) without leaving it. Panels have their own history stack (opening a panel from within a panel pushes onto it, `back` pops within it first), but only one panel is ever visually shown at a time — a new panel replaces the previous one on screen, they are not layered/visible simultaneously. Loaded via `panel:{URL}` or `panel:data` action. Because of this tall-and-narrow layout, panels always use `"list"` behavior — the `type` property of the Content Root Object is ignored. Grid: 8 × 6 (or 10 × 8 with `compress: true`).

---

## Plugin

A web page loaded by MSX into an iframe to handle specific functionality: video/audio playback, interaction, image display, background scenes, etc. JavaScript (and the TVX Framework API) is optional — a plugin can be a plain HTML page. All plugins documented in this KB (e.g. `youtube.html`, `html5x.html`, `template.html`, …) are open source — their HTML/JS/CSS source can be freely viewed in the browser (distinct from the closed-source Media Station X application itself). Showcases may use additional custom plugins internally that are not documented in this KB.

See [experts-api/plugins/](../experts-api/plugins/).

---

## Plugin API Reference

The technical reference for the TVX Framework classes available in a plugin. Available in three access forms: Classical ES5 global (`TVXVideoPlugin`, `TVXInteractionPlugin`), Classic Module (`tvx.VideoPlugin`, `tvx.InteractionPlugin`), or TypeScript Module (`tvx.TVXVideoPlugin` type, `tvx.VideoPlugin` instance). All three forms are functionally equivalent.

**Not to be confused with the [TVX Plugin Library](#tvx-plugin-library) below:** this entry is the API *surface* — which classes/methods exist and how to access them; the Library is the actual distributed `.js` file(s) that implement that API.

See [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md).

---

## Page Action

A hidden-feature action variant (since `0.1.112`) that executes an `action` (with optional `data`) set on a Content Page Object once that page becomes active. Executed before a Selection Object's action; for overlay/underlay pages, executed when the whole content becomes active/visible, before any regular page action. Since `0.1.130`, the **underlay** page action is also executed when the content becomes *inactive* while still visible (e.g. a corresponding menu regains focus) — one way to reset lasting [Selection Object](#selection-object) side effects (e.g. `quiet` to stop a running video/audio, or reloading a resource with a neutral value) once the user navigates away, since there is no dedicated "on focus leaves" action (an interaction-plugin content observer is another option, depending on the use case). Distinct from the Start Action (executed once at startup, on the root object) and from a content item's own `action` (executed on user selection).

See [Page Action](../experts-api/hidden-features/page-action.md).

---

## Ready

A special object (`ready`, since `0.1.142`) on the Content Root Object and Menu Root Object that executes an `action` (with optional `data`) whenever that content/menu becomes ready. **Distinct from the [Start Action](#start-action) in exactly one, important way — how often it fires:** the Start Action's `action`/`data` pair fires only **once**, specifically when that root object's data is loaded at startup; `ready` instead fires **every time** the menu/content (including when shown in a panel) is freshly displayed — regardless of whether the data came from the network, was served from [`cache`](#cache), or its DOM was kept via [`reuse`](#reuse) — not only on a true, first-ever startup load. This makes `ready` the right tool for behavior that must re-arm itself on every appearance of a page, not just once: [xp/next_video_test.json](../examples/xp/next_video_test.json) uses it to (re-)set up `trigger:` actions (`[trigger:stop:close:next_video|trigger:complete:player:goto:random]`) each time its flagged panel is shown — necessary because that panel can be redisplayed many times over an app session, not only at startup — whereas the Start Action is exclusively a one-time startup hook.

See [Content Root Object](../main-api/content/content-root-object.md), [Menu Root Object](../main-api/menu/menu-root-object.md), [Start Action](#start-action).

---

## Release

An internal action family (`release:content`, `release:menu`, `release:panel`) that actually clears the cache and references for the current content/menu/panel data, forcing a real reload the next time the same URL is requested. Concretely, this clears that object's own slot in the [`Cache`](#cache)/[`Reuse`](#reuse) mechanism (`mainMenu`, `mainContent`, or `panelContent`) — both the cached JSON and any already-built DOM reference — so the next matching load runs the full network-fetch-and-DOM-rebuild path regardless of that object's own `cache`/`reuse` values. Once released, the data cannot be reloaded via `reload:` anymore, but `replace:` still works. **There is no `release:slideshow`/`sliderContent` counterpart** — the Slider Scene (behind `slideshow:{URL}`) has `cache`/`reuse` like the others, but is far simpler (fullscreen images slid left/right, with no significant interaction, live, or update functionality), so there was no need for its own release action. Not to be confused with [Invalidate](#invalidate), which is purely visual and clears nothing.

See [Cache](#cache), [Actions Reference](actions-reference.md), [Internal Actions](../experts-api/special/internal-actions.md).

---

## Replace

A hidden-feature action variant (since `0.1.144`) that replaces and reloads existing menu, content, or panel data at runtime — useful for session-less tabs, filters, sortings, and pagination. Only works on data that already has a `flag` property, and the flag indicated in the action must match it, otherwise the data is not replaced. Pattern: `replace:content:{CONTENT_FLAG}:{REQUEST_ACTION}` (also `replace:menu:`, `replace:panel:`).

See [Replace Action](../experts-api/hidden-features/replace-action.md).

---

## Resolve

A hidden-feature mechanism (since `0.1.107`) that dynamically resolves the actual URL for a video, audio, image, or background item via a server request — used to serve a platform-specific format/codec or to generate temporary/access-token-protected links at runtime. The server response must contain `response.data.url` (or `response.data.error` on failure). Since `0.1.145`, the response data can also contain `label`, `background`, and `properties` (an object that extends/overrides, not replaces, existing properties) to extend/override the corresponding video/audio info.

There are two distinct usage forms:

1. **As an `action`-string** — always requires a prefix, there is no bare `resolve:` action: `video:resolve:{URL}` (also `video:auto:resolve:{URL}`), `audio:resolve:{URL}` (also `audio:auto:resolve:{URL}`), `image:resolve:{URL}`, `background:resolve:{URL}` — each with `user:` and `request:interaction:{DATA_ID}@{URL}` sub-variants.
2. **As a bare `resolve:{URL}` property value** (no `action` property involved) — only on two properties: the `image` property (**content context only** — does not work on a Menu Item's `image` or the Menu Root's `logo`), and the `background` property, which works **everywhere it appears**, independent of context or nesting level (Content Item Object, Content Page Object, Content Root Object, Selection Object, Menu Item Object, and Menu Root Object all have their own `background` property).

Despite also appearing in [Internal Actions](../experts-api/special/internal-actions.md), the Resolve Action is **not** covered by that list's blanket `0.1.160+` minimum — its own dedicated wiki page documents the more precise `0.1.107` since-version for all forms above, which takes precedence. This includes the `request:interaction:{DATA_ID}@{URL}` sub-variant (e.g. `video:resolve:request:interaction:{DATA_ID}@{URL}`) — also `0.1.107`, since the interaction-plugin request mechanism already existed by then and all combinations with Resolve worked from the start.

See [Resolve Action](../experts-api/hidden-features/resolve-action.md), [Actions Reference](actions-reference.md#hidden-feature-actions-replace-start--resolve).

---

## Restore

A `boolean` property (default `true`, since `0.1.120`) on the Content Root Object and Menu Root Object, controlling whether that menu/content data is restored when the app returns from a `link:{URL}` action — not `link:window:{URL}`, which opens externally and never actually leaves the MSX app itself, so there is nothing to restore in that case. Only the single most recently loaded menu/content link (or the embedded JSON itself, if loaded inline via `json:`/`data`) is saved when a `link:{URL}` action runs, together with whichever item was focused at that moment — not the full navigation history stack. On return, exactly that saved state is freshly loaded/set. Example: `menu (start) → content 1 → content 2 → content 3 → open link` saves content 3 plus its focused item; returning restores exactly that — a subsequent `back` from content 3 then goes straight to the menu (start), skipping content 1/2 entirely, since they were never part of the saved restore state. Setting `restore: false` on the relevant root object skips this save/restore step entirely. If content is loaded via a menu, the menu's own `restore` value takes precedence over the content's.

See [Content Root Object](../main-api/content/content-root-object.md), [Menu Root Object](../main-api/menu/menu-root-object.md).

---

## Reuse

A `boolean` property (default `true`, since `0.1.0`) on the Content Root Object and Menu Root Object. `reuse` governs two of the three checks MSX runs through when loading a menu/content URL (see [Cache](#cache) for the full three-step sequence):

1. **Same-URL shortcut (step 1):** if the relevant slot is already showing data for that exact same URL right now, `reuse: true` (default) reuses it immediately, bypassing the backend cache check and any network request entirely.
2. **DOM reuse (step 3):** once the JSON data is available — whether freshly fetched or served from the [`cache`](#cache) step — MSX compares it against the JSON instance the slot's *currently* present DOM was built from (a live identity check, not a separate stored DOM cache); if it's the same instance, `reuse: true` keeps that DOM as-is instead of rebuilding it. This preserves focus as a side effect, since the same DOM elements (including whichever one was focused) simply stay in place.

`reuse: false` disables both shortcuts: MSX never short-circuits on a same-URL-already-showing match, and always rebuilds the DOM fresh once the data is available, discarding focus state. Both `cache` and `reuse` default to `true`, and in most everyday cases that's the right choice. Real reasons to disable one without the other: `reuse: false` alone (keeping `cache: true`) is a cheap way to force a UI reset — discarding stale focus and any live state applied via `update:` actions since the last build — every time, *without* paying for a network round-trip, since the JSON itself still comes from the cache; conversely, live/frequently-changing content, or deliberately wanting fresh server data on every visit even though the request URL stays constant, are reasons to disable `cache` instead. For a guaranteed-always-fresh reload of both the data and the UI, set both together — see [Cache](#cache).

See [Cache](#cache), [Content Root Object](../main-api/content/content-root-object.md), [Menu Root Object](../main-api/menu/menu-root-object.md).

---

## Selection Object

A JSON sub-object on a Content Item Object (since `0.1.110`) that controls the behavior of the item when it receives focus: an `action` to execute (with optional `data`), plus a `background` image and sub-`headline` to display while focused. Used to show previews or trigger behavior on focus change. There is no companion "on focus leaves" action — `background`/`headline` revert automatically when focus moves on, but a lasting side effect of the `action` (e.g. a running audio, a shown backdrop) needs to be reset some other way, depending on the use case: typically via a [Page Action](#page-action) on the underlay page (see [Backdrop Plugin](../experts-api/plugins/backdrop-plugin.md) for a worked example), or, on interaction-plugin-driven pages, via a content observer (`addContentObserver`) reporting focus changes directly to the plugin (with a ~1 second delay).

See [Selection Object](../experts-api/selection/selection-object.md).

---

## Server Action

An action that sends a request to a server endpoint (HTTP POST). The server processes the request and returns an action for MSX to execute. Main server action: `execute:{URL}`. See also [Responses](../main-api/common/responses.md).

---

## Silent Mode

An internal-only `silent:` prefix (e.g. `execute:silent:{URL}`, also combinable as `execute:silent:user:`, `execute:silent:accurate:`, `execute:silent:fetch:`, `execute:silent:request:player:`, `execute:silent:request:interaction:`, etc.) that executes the action without showing a busy indicator and without displaying error/warning messages to the user. Without `silent:`, errors and warnings are always both logged internally and shown to the user; with `silent:`, they are still written to the internal log — only the busy indicator and the user-visible error/warning message are suppressed.

See [Internal Actions](../experts-api/special/internal-actions.md).

---

## Start Action

A hidden-feature action variant (since `0.1.0`) that automatically executes an `action` (with optional `data`) set directly on the Menu Root Object or Content Root Object that is loaded at startup, once that data has finished loading (default action is `home` if none is set). Not to be confused with the `start` Main Action (since `0.1.30`), which sets a *new* start parameter at runtime — a completely different mechanism despite the similar name.

See [Start Action](../experts-api/hidden-features/start-action.md).

---

## Start Object

The root JSON object that bootstraps an MSX application. Loaded via the app's "Setup Start Parameter" mechanism. Three mandatory properties: `name`, `version`, `parameter`.

See [Start Object](../main-api/start/start-object.md).

---

## Start Parameter

The URL passed to the MSX app as the initial entry point. MSX fetches this URL to get the Start Object, which in turn provides the menu or content URL via its `parameter` property (`menu:{URL}` or `content:{URL}`).

See [Setup Start Parameter](../main-api/start/setup-start-parameter.md).

---

## Template

A partial Content Item Object used as a blueprint for an array of `items`. The `template` property on a Content Root Object, combined with `items`, is an alternative to `pages` for repeated content of the same shape — works with both `"type": "pages"` and `"type": "list"`. In the template's `layout`, only `w` and `h` are used; `x` and `y` are ignored. A template-only `decompress` boolean (since `0.1.155`) lets the template's `layout` be authored using standard (uncompressed) grid coordinates while the content itself is compressed (`compress: true`) — MSX automatically converts those coordinates into the compressed grid. It does not mix compressed and uncompressed grids within one content (that is never possible); it only changes the coordinate scale the template is authored in. The `{context:{PROPERTY}}` inline expression substitutes item-specific values inside `data` (and since `0.1.110`: `properties`, `live`, `selection`, `options`).

---

## Trigger

A video/audio playback hook set via the `trigger:{TRIGGER_KEY}` extended property (since `0.1.58`) — fires an action string when the named condition occurs during the current playback (e.g. `load`, `start`, `end`, `complete`, a specific second/percentage/tick count, or its negative-numbered "seconds remaining" form). By default, a trigger can fire more than once per playback if its condition recurs — most relevant for the numeric second/percentage/tick-based keys, since seeking back past the same point can retrigger them. Prefixing the action with `shot:` (e.g. `"trigger:20": "shot:info:20 seconds of video passed (shot)."`) makes that trigger a one-time event instead: it fires once, then never again for the rest of that playback — useful for cues that shouldn't repeat if the user seeks back over the same spot.

See [Extended Properties](../experts-api/special/extended-properties.md), [Actions Reference → Internal-Only Actions Overview](actions-reference.md#internal-only-actions--overview-require-01160).

---

## TVX Framework

A JavaScript framework — "a composition of tools and services to build high-performance and high-compatible TV (and mobile-usable) applications" — that Media Station X itself is built on, see [About → Technology Stack](../overview/about.md#technology-stack). Current version: `0.1.35` (no per-feature changelog). An MSX plugin and the MSX app itself are both ultimately built on this same TVX Framework — a plugin just works with a subset of it, since it doesn't need every class the app itself does.

---

## TVX Plugin Library

The pre-built JavaScript library distributed for plugin developers, current version `0.0.79` — **six** files in total, across two variants (base and UX) and three forms each (global script, module, and TypeScript definitions):

- `tvx-plugin.min.js` — classical ES5 global form (`<script src="//msx.benzac.de/js/tvx-plugin.min.js">`). Provides `TVXPluginTools`, `TVXVideoPlugin`, and `TVXInteractionPlugin` — classes developed specifically for plugin development — alongside a few core TVX Framework classes also used internally by the MSX app itself and exposed here for plugin code, such as `TVXSettings`, `TVXTools`, and `TVXStorage`.
- `tvx-plugin-module.min.js` — the same library as `tvx-plugin.min.js`, packaged as an AMD/CommonJS module instead of a global script.
- `tvx-plugin-module.min.d.ts` — TypeScript type definitions for `tvx-plugin-module.min.js`, plus the `MSX*` JSON object types (see [Type Definitions](#type-definitions)) — what makes the TypeScript Module access form possible.
- `tvx-plugin-ux.min.js` — same as `tvx-plugin.min.js`, plus additional TVX Framework effects and UI classes (`TVXEffects`, `TVXAnimationController`, `TVXColorTools`, `TVXTransit`, `TVXParticles`, `TVXDrawing`, `TVXRenderer`, `TVXImageTools`, `TVXStyleTools`). Requires jQuery, unlike the dependency-free base files.
- `tvx-plugin-ux-module.min.js` — the module form of the UX variant.
- `tvx-plugin-ux-module.min.d.ts` — TypeScript type definitions for `tvx-plugin-ux-module.min.js`: all of `tvx-plugin-module.min.d.ts`'s definitions, plus types for the extra UX effects/UI classes.

Documentation depth varies across two different reference channels, not by a simple documented/undocumented split. This KB's [Plugin API Reference](#plugin-api-reference) page lists every base-library class — from `TVXSettings`/`TVXTools`/`TVXStorage` through `TVXVideoPlugin`/`TVXInteractionPlugin` — at the same shallow depth: bare method/property signatures with no explanations (the page states outright it is "not yet fully documented"). [Type Definitions](#type-definitions), however, adds real JSDoc-style explanations with parameter descriptions specifically for the plugin-development interfaces `TVXVideoPlugin`/`TVXVideoPluginPlayer` and `TVXInteractionPlugin`/`TVXInteractionPluginHandler` — the rest of the base classes are typed there too, but without that extra explanatory layer. The UX variant's extra effects/UI classes have neither: no Plugin API Reference listing and no JSDoc, only bare `.d.ts` type signatures — which is why they require expert-level knowledge to use correctly and remain less practical for external developers. In practice, the MSX developer has used the UX variant exclusively so far, for a handful of his own plugins/showcases (e.g. the [Backdrop Plugin](../experts-api/plugins/backdrop-plugin.md), the [RBTV MSX](../overview/showcases.md#rbtv-msx) showcase) — worth knowing it exists, but not relevant for MSX JSON authors.

**Not to be confused with the [Plugin API Reference](#plugin-api-reference) above:** that entry is the API surface itself (classes, methods, the three access forms); this entry is the concrete distributed file(s) that implement it.

---

## Type Definitions

TypeScript interfaces generated from the TVX Plugin Library source (`tvx-plugin-module.min.d.ts`, `0.0.79`) that describe all MSX JSON object shapes (`MSXMenuRoot`, `MSXMenuItem`, `MSXContentRoot`, `MSXContentItem`, etc.). Authoritative reference for property names and types.

**Broader scope than [Plugin API Reference](#plugin-api-reference) and [TVX Plugin Library](#tvx-plugin-library) above:** the same `.d.ts` file also types the TVX plugin classes those two entries describe, but its main practical use in this KB is different — it's the authoritative reference for the MSX JSON object structure when building JSON pages, a use case neither of the other two entries covers at all.

See [Type Definitions](type-definitions.md).

---

## Version (MSX App)

A string in `"0.1.X"` format identifying the MSX app release. The `version` property in the Start Object is the **developer's own start parameter version** (e.g. `"1.0.0"`), not the MSX app version. See [Versioning](versioning.md).

---

## Web Version Loader

A service that lets an already-installed, older MSX app load and run the latest version from the web instead — set up via the start parameter `web.msx.benzac.de`, or `id:web` for a variant that also supports automatic loading via a [Start Action](#start-action). Also usable to load MSX in a secure (`https://`) context on platforms that need it for a particular service (e.g. Twitch MSX).

See [Tips & Tricks — Web Version Loader](../overview/tips-tricks.md#web-version-loader).

---

## `{context:{PROPERTY}}`

Template-only inline expression that substitutes the value of a named property from the current item when rendering via `template` + `items`. Resolved inside the `data` property of the template object (directly or anywhere in its nested structure — including as the value of any nested JSON string, e.g. `"action": "info:{context:message}"`), and since `0.1.110` also in the `properties`, `live`, `selection`, and `options` template properties. The `data` property is always a JSON object or `null`. Does not work in standard template properties like `label`, `image`, or `action` — override those directly per item.

See [Inline Expressions](../main-api/common/inline-expressions.md).

---

## `{pipe}`

A keyword used inside multi-action strings to represent a literal `|` character, preventing it from being interpreted as an action separator. Example: `[reload:content|info:A{pipe}B]` displays "A|B" in the info message.

See [Actions](../main-api/common/actions.md).
