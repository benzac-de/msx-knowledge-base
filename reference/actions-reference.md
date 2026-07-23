---
title: Actions Reference
category: Reference
summary: Consolidated reference for all MSX main actions, internal actions, and hidden feature actions (Replace, Start, Resolve), with version information and Main Action status for each.
---

# Actions Reference

This document consolidates actions from three source categories with different versioning models:

- **Main Actions** ([Actions](../main-api/common/actions.md)): Official, version-stable actions. Each has an exact "Since Version" from `0.1.0` onwards. These are the recommended actions for most use cases.
- **Internal Actions** ([Internal Actions](../experts-api/special/internal-actions.md)): All internal actions, valid for **`0.1.160+` only** (no finer version control for internal-only entries). The column "Main Action: Yes" in the internal actions file marks entries that are also official Main Actions (and thus carry the exact since-version from the Main Actions list below).
- **Hidden Feature Actions** (Replace Action, Start Action, Resolve Action — see [Hidden Feature Actions](#hidden-feature-actions-replace-start--resolve) below): documented on their own dedicated wiki pages, each with its own exact individual since-version. Neither part of the Main Actions list nor covered by the Internal Actions' blanket `0.1.160+`.

**Rule for choosing the version to use:**
- If an action appears in the Main Actions list below → use the "Since Version" from that list.
- If an action is a documented hidden-feature action (Replace Action, Start Action, Resolve Action) → use that page's own exact since-version.
- If an action is internal-only and not documented elsewhere (not in the Main Actions list, not a documented hidden feature) → treat `0.1.160` as the safe minimum version, since internal actions have no individual version control (see note below).
- When in doubt, check the dedicated source pages linked above.

> **Practical note on internal actions and versions:** The blanket `0.1.160+` minimum is a conservative default, not proof that an internal action is unavailable earlier — MSX's internal actions were never put under individual per-feature version control, mainly because there are so many of them and many cover very specific, rarely-used cases. Some internal actions may well work correctly on MSX versions earlier than `0.1.160` (possibly with different syntax); if you hit an error/warning, check the target MSX version first. In practice this rarely matters: nearly all platforms run the latest MSX version at any given time (currently `0.1.165`–`0.1.166`) — the main exception is older Samsung TV models, which Samsung no longer allows to be updated and which can be stuck on versions from `0.1.58` up to `0.1.163` depending on model year. See [Platform Support](../overview/platform-support.md) for the full breakdown.

For a live, searchable action index, see: [https://msx.benzac.de/info/xp/actions.html](https://msx.benzac.de/info/xp/actions.html)

---

## Multi-Action Syntax (since `0.1.58`)

You can execute multiple actions in a single `action` property by wrapping them in `[...]` and separating with `|`:

```
[reload:content|info:Action executed.]
```

- Starts with `[`, ends with `]`.
- Actions separated by `|`.
- All actions share the triggering item's `data` property.
- To include a literal `|` **inside** an action argument (not as separator), use the `{pipe}` keyword: `[reload:content|info:[P{pipe}I{pipe}P{pipe}E]]`
- Empty list `[]` = no-op (NOP).
- **Each action executes independently — the bracket is not transactional.** If one action in the list fails, warns, or is a no-op (e.g. an internal action unsupported on the client's current MSX version), the remaining actions still execute; nothing aborts the rest of the sequence.

---

## `data` Action (since `0.1.58`)

The `data` action is a second way to execute multiple actions in a row — an alternative to the `[...]` Multi-Action Syntax above. Instead of a shared `data` property, each action gets its **own** `action`/`data` pair, listed in an `actions` array inside the triggering item's `data` property:

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
- **`[...]` Multi-Action Syntax:** simpler, but the triggering item's `data` property is shared by **all** actions. Only use this when the actions need the same data, or none at all. A literal `|` inside one of the actions needs the `{pipe}` keyword (see above).
- **`data` action:** more verbose, but lets each action carry its own `data`, which is required as soon as the actions need different data (like the overlay/underlay example above). Since there is no `|` separator here, a literal `|` inside an action's text needs no `{pipe}` escaping — it's just a normal JSON string.

**Shared `data` with `[...]` can still be useful on purpose:** since `update:` actions only touch properties that are already set (non-`null`) on the origin item, a single shared `data` object can safely feed several `update:` actions at once, each one just picking out the properties it already has:

```json
{
    "action": "[update:content:info|update:content:cover]",
    "data": {
        "image": "https://example.com/img.jpg",
        "text": "Info text."
    }
}
```

Here `update:content:info` only picks up `text` and `update:content:cover` only picks up `image` (assuming each origin item only has the matching property set) — one compact action updates both items with a single `data` object.

---

## Main Actions — Complete Reference

### Basic Actions

| Syntax | Example | Since Version | Description |
|---|---|---|---|
| `video:{URL}` | `video:http://msx.benzac.de/media/video1.mp4` | 0.1.0 | Plays a video. |
| `audio:{URL}` | `audio:http://msx.benzac.de/media/audio1.mp3` | 0.1.0 | Plays audio (no video screen; uses `background` property if set). |
| `image:{URL}` | `image:http://msx.benzac.de/img/bg1.jpg` | 0.1.0 | Shows an image. |
| `image:context` | `image:context` | 0.1.0 | Shows an image using the current context (`image` property of the content item). |
| `link:{URL}` | `link:http://msx.benzac.de/info/` | 0.1.0 | Opens a link (in-app). |
| `link:window:{URL}` | `link:window:http://msx.benzac.de/info/` | 0.1.0 | Opens a link in a separate window or external browser. |
| `playlist:{URL}` | `playlist:http://msx.benzac.de/info/data/guide/videos.json` | 0.1.0 | Loads a playlist (no content page). Data structure: Templated Items (Content API). |
| `playlist:data` | `playlist:data` | 0.1.0 | Starts a playlist from the item's `data` property. Also supports `playlist:json:{BASE64_ENCODED_JSON}`. |
| `slideshow:{URL}` | `slideshow:http://msx.benzac.de/info/data/guide/images.json` | 0.1.0 | Loads a slideshow (no content page). Data structure: Templated Items. |
| `slideshow:data` | `slideshow:data` | 0.1.0 | Starts a slideshow from the item's `data` property. Also supports `slideshow:json:{BASE64_ENCODED_JSON}`. |
| `menu:{URL}` | `menu:http://msx.benzac.de/info/data/guide/actions.json` | 0.1.0 | Loads a menu. Also supports `menu:json:{BASE64_ENCODED_JSON}` and `menu:data`. |
| `menu:data` | `menu:data` | 0.1.0 | Shows a menu from the item's `data` property. |
| `content:{URL}` | `content:http://msx.benzac.de/info/data/guide/videos.json` | 0.1.0 | Loads a content page. Also supports `content:json:{BASE64_ENCODED_JSON}`. |
| `content:data` | `content:data` | 0.1.0 | Shows a content page from the item's `data` property. |
| `panel:{URL}` | `panel:http://msx.benzac.de/info/data/guide/panel.json` | 0.1.0 | Loads a content page in a panel. Also supports `panel:json:{BASE64_ENCODED_JSON}`. |
| `panel:data` | `panel:data` | 0.1.0 | Shows a content page in a panel from the item's `data` property. |
| `success:{TEXT}` | `success:This is a success message.` | 0.1.0 | Shows a success message. Text supports Inline Expressions. |
| `info:{TEXT}` | `info:This is an info message.` | 0.1.0 | Shows an info message. Text supports Inline Expressions. |
| `warn:{TEXT}` | `warn:This is a warning message.` | 0.1.0 | Shows a warning message. Text supports Inline Expressions. |
| `error:{TEXT}` | `error:This is an error message.` | 0.1.0 | Shows an error message. Text supports Inline Expressions. |
| `focus:{ID}` | `focus:content_item_id` | 0.1.0 | Sets focus to a specific item by ID in the current context. |
| `focus:index:{INDEX}` | `focus:index:0` | 0.1.0 | Sets focus to a specific item by index (0 = first item). |
| `start` | `start` | **0.1.30** | Sets up a new start parameter. Data must be provided as `data` property (Start Object structure). |
| `video:plugin:{URL}` | `video:plugin:http://msx.benzac.de/plugins/template.html` | **0.1.40** | Plays a plugin video. |
| `audio:plugin:{URL}` | `audio:plugin:http://msx.benzac.de/plugins/template.html` | **0.1.40** | Plays plugin audio (no video screen). |

### HbbTV Actions (HbbTV version only)

| Syntax | Example | Since Version | Description |
|---|---|---|---|
| `video:broadcast:current` | `video:broadcast:current` | **0.1.30** | Plays the current broadcast channel. |
| `system:hbbtv:launch:{URL}` | `system:hbbtv:launch:http://msx.benzac.de/hbbtv.html` | **0.1.30** | Launches a new HbbTV application (HTML page or XML AIT URL). |

### Server Actions

Server actions are sent as HTTP POST requests, by default with `Content-Type: application/x-www-form-urlencoded` (see the `accurate:` prefix below for the `application/json` alternative). The server must return a `response` object with `response.data.action` (and optionally `response.data.data`). See [Responses](../main-api/common/responses.md) for the response format.

Note: Without the `service:` prefix, only one server action can be running at a time on the application side — parallel execution is not allowed. Since `0.1.90`, an `accurate:` prefix before `{URL}` uses accurate headers; a `service:` prefix enables parallel execution instead (max 128 parallel service actions).

| Syntax | Example | Since Version | Description |
|---|---|---|---|
| `execute:{URL}` | `execute:http://msx.benzac.de/services/echo.php` | **0.1.30** | Executes an action on server side; item's `data` property is submitted. Server returns `action` (and optionally `data`). |
| `execute:code:{URL}` | `execute:code:http://msx.benzac.de/services/echo.php` | **0.1.30** | Executes server action with user-entered code (submitted as `code`). The `data` property can customize the code panel (`headline`, `extension`, `value`, `placeholder`, `secret`). |
| `execute:user:{URL}` | `execute:user:http://msx.benzac.de/services/echo.php` | **0.1.30** | Server action with user credentials enabled (`withCredentials: true`). The `user:` prefix can also be used inside menu item `data` and before `{URL}` in `execute:code:user:`, `playlist:user:`, `slideshow:user:`, `menu:user:`, `content:user:`, `panel:user:`. |

### `user:` and `accurate:` Prefix Variants

These are the individual actions produced by combining the `user:` prefix (enables user credentials / third-party cookies, `withCredentials: true`) and the `accurate:` prefix (sends `Content-Type: application/json` instead of the default `application/x-www-form-urlencoded`, at the cost of a CORS preflight) with their base actions. They are official Main Action variants — each documented in prose on [Actions](../main-api/common/actions.md) rather than as its own table row there, but each has an exact since-version derived from the base action / prefix it uses.

| Syntax | Example | Since Version | Description |
|---|---|---|---|
| `content:user:{URL}` | `content:user:http://msx.benzac.de/info/data/guide/videos.json` | **0.1.30** | Loads a content page with user credentials enabled — same as `content:{URL}` otherwise. |
| `menu:user:{URL}` | `menu:user:http://msx.benzac.de/info/data/guide/actions.json` | **0.1.30** | Loads a menu with user credentials enabled — same as `menu:{URL}` otherwise. |
| `panel:user:{URL}` | `panel:user:http://msx.benzac.de/info/data/guide/panel.json` | **0.1.30** | Loads a content page in a panel with user credentials enabled — same as `panel:{URL}` otherwise. |
| `playlist:user:{URL}` | `playlist:user:http://msx.benzac.de/info/data/guide/videos.json` | **0.1.30** | Loads a playlist with user credentials enabled — same as `playlist:{URL}` otherwise. |
| `slideshow:user:{URL}` | `slideshow:user:http://msx.benzac.de/info/data/guide/images.json` | **0.1.30** | Loads a slideshow with user credentials enabled — same as `slideshow:{URL}` otherwise. |
| `execute:code:user:{URL}` | `execute:code:user:http://msx.benzac.de/services/echo.php` | **0.1.30** | Combines `code:` and `user:` — server action with an entered code, with user credentials enabled. |
| `execute:accurate:{URL}` | `execute:accurate:http://msx.benzac.de/services/echo.php` | **0.1.90** | Same as `execute:{URL}`, but with the `accurate:` prefix. |
| `execute:code:accurate:{URL}` | `execute:code:accurate:http://msx.benzac.de/services/echo.php` | **0.1.90** | Combines `code:` and `accurate:` prefixes. |
| `execute:user:accurate:{URL}` | `execute:user:accurate:http://msx.benzac.de/services/echo.php` | **0.1.90** | Combines `user:` and `accurate:` prefixes. |
| `execute:code:user:accurate:{URL}` | `execute:code:user:accurate:http://msx.benzac.de/services/echo.php` | **0.1.90** | Combines `code:`, `user:`, and `accurate:` prefixes. |

> **Note:** `execute:service:{URL}` and its variants (parallel execution, max 128 concurrent, since `0.1.90`) are **not** included above because they are not listed among the Main Action variants in [Internal Actions](../experts-api/special/internal-actions.md) — they are internal-only. They nonetheless share the **`0.1.90`** since-version of this `service:`/`accurate:` prefix feature area — **not** the blanket `0.1.160+` that otherwise applies to internal-only actions. See the `execute:service:...` row in the Internal-Only Actions table below.

### Extended Actions

| Syntax | Example | Since Version | Description |
|---|---|---|---|
| `reload` | `reload` | **0.1.58** | Reloads the application. |
| `reload:menu` | `reload:menu` | **0.1.58** | Reloads the current menu data. (Only reloads if not loaded at startup or set via data action; since `0.1.134` also works with `reference` property; since `0.1.144` use `replace:menu:…` for startup-loaded data.) |
| `reload:content` | `reload:content` | **0.1.58** | Reloads the current content data. (Same conditions as `reload:menu`.) |
| `reload:panel` | `reload:panel` | **0.1.58** | Reloads the current panel content data. |
| `update:menu:{ID}` | `update:menu:menu_item_id` | **0.1.58** | Updates a menu item's visual data (`icon`, `image`, `label`, `background`, `extensionIcon`, `extensionLabel`, `lineColor`, `options`). Update data from item's `data` property. Only properties that were originally set (non-null) can be updated, except `background`, `lineColor`, and `options` which can always be updated. |
| `update:content:{ID}` | `update:content:content_item_id` | **0.1.58** | Updates a content item's data. Updatable properties: `color`, `title`, `titleHeader`, `titleFooter`, `label`, `icon`, `headline`, `text`, `tag`, `tagColor`, `badge`, `badgeColor`, `stamp`, `stampColor`, `progress`, `progressColor`, `progressBackColor`, `wrapperColor`, `image`, `extensionIcon`, `extensionLabel`, `live`, `selection`, `options`. `color`, `tagColor`, `badgeColor`, `stampColor`, `progressColor`, `progressBackColor`, `wrapperColor`, `live`, `selection`, and `options` can always be updated; all others (`title`, `titleHeader`, `titleFooter`, `label`, `icon`, `headline`, `text`, `tag`, `badge`, `stamp`, `progress`, `image`, `extensionIcon`, `extensionLabel`) only if originally set. |
| `update:content:overlay:{ID}` | `update:content:overlay:content_item_id` | **0.1.110** | Updates a content overlay item. See `update:content:{ID}` for updatable properties. |
| `update:content:underlay:{ID}` | `update:content:underlay:content_item_id` | **0.1.112** | Updates a content underlay item. |
| `update:panel:{ID}` | `update:panel:content_item_id` | **0.1.58** | Updates a panel content item. See `update:content:{ID}`. |
| `update:panel:overlay:{ID}` | `update:panel:overlay:content_item_id` | **0.1.110** | Updates a panel content overlay item. |
| `update:panel:underlay:{ID}` | `update:panel:underlay:content_item_id` | **0.1.112** | Updates a panel content underlay item. |
| `data` | `data` | **0.1.58** | Executes multiple actions in a row. Item's `data` property must contain `"actions": [{action, data}, ...]`. |
| `[{ACTION}\|{ACTION}\|...]` | `[reload:content\|info:Action executed.]` | **0.1.58** | Multi-action syntax. Actions separated by `\|`; use `{pipe}` for literal `\|` inside arguments. |

### Request Actions (`request:interaction:` is `0.1.82`; `request:player:` remains `0.1.160+`)

Many URL-accepting actions accept a `request:interaction:{DATA_ID}@{URL}` pattern instead of a plain URL, letting an interaction plugin supply the URL/data dynamically. Use **`0.1.82`** for this pattern combined with any base action (`content:`, `menu:`, `panel:`, `playlist:`, `slideshow:`, `execute:` and its sub-variants) — not the blanket `0.1.160+` ([Interaction Plugin](../experts-api/plugins/interaction-plugin.md) itself states "version `0.1.82` or higher is needed"; [Paging Plugin](../experts-api/plugins/paging-plugin.md), own minimum version also `0.1.82`, uses this exact pattern as its core mechanism) — unless combined with a prefix that itself has a later since-version (e.g. `playlist:auto:request:interaction:...` stays `0.1.160+`, floored by `auto:`; Resolve's `request:interaction:` sub-variant is `0.1.107`, floored by Resolve itself — see below).

The sibling `request:player:{DATA_ID}` pattern (requesting from the video/audio **player** plugin instead) is a separate mechanism — see [Plugin-Commit Actions](#plugin-commit-actions-playercommit-interactioncommitresponse-and-tizen-player) below — and remains blanket `0.1.160+`. Full syntax in [Internal Actions](../experts-api/special/internal-actions.md).

> **Classification note:** `request:interaction:...` is documented here, under "Main Actions," purely for proximity to the base actions it modifies — it is **not itself a Main Action** and does not appear in the ["Which Actions Are Both Main Actions AND Internal Actions?"](#which-actions-are-both-main-actions-and-internal-actions) list below. The `interaction:` extension point was added to these base actions later than the actions themselves (hence its own, later `0.1.82` since-version, tracked separately rather than inherited from the base action's `0.1.0`).

---

## Hidden Feature Actions (Replace, Start & Resolve)

These are neither official Main Actions nor covered by the Internal Actions' blanket `0.1.160+` — each has its own documented, exact individual since-version.

### Replace Actions (since `0.1.144`)

| Syntax | Since Version | Description |
|---|---|---|
| `replace:menu:{CONTENT_FLAG}:{REQUEST_ACTION}` | **0.1.144** | Replaces and reloads menu data (even startup-loaded data) by content flag. |
| `replace:content:{CONTENT_FLAG}:{REQUEST_ACTION}` | **0.1.144** | Replaces and reloads content data by content flag. |
| `replace:panel:{CONTENT_FLAG}:{REQUEST_ACTION}` | **0.1.144** | Replaces and reloads panel data by content flag. |

See [Replace Action](../experts-api/hidden-features/replace-action.md) for details.

### Start Action (since `0.1.0`)

Not an action-string family like the others above, but a hidden feature: setting an `action` property (plus an optional `data` property) directly on the Menu Root Object or Content Root Object that is loaded at startup makes MSX execute that action automatically once the data has loaded (default action is `home`). Available since `0.1.0`.

See [Start Action](../experts-api/hidden-features/start-action.md) for details.

### Resolve Action (since `0.1.107`)

Resolves the actual URL for a video, audio, image, or background item dynamically via a server request — used to serve a platform-specific format/codec, or to generate temporary/access-token-protected links at runtime. The server response must contain `response.data.url` (`string`), or `response.data.error` (`string`) on failure. Since **`0.1.145`**, the response data can also contain `label` (`string`), `background` (`string`), and `properties` (`object`) to extend/override the corresponding video/audio info (e.g. to set up player buttons for specific plugins) — these `properties` extend/override existing properties rather than fully replacing them.

There are **two distinct ways** to use Resolve — as an action string, or as a bare property value. Do not confuse them.

**1. As an action string (`action` property) — `resolve:` always requires a prefix, there is no bare `resolve:` action:**

| Syntax | Since Version | Description |
|---|---|---|
| `video:resolve:{REQUEST_ACTION}` | **0.1.107** | Resolves a video URL dynamically before playback. |
| `video:auto:resolve:{REQUEST_ACTION}` | **0.1.107** | Same, played in auto mode (see `video:auto:...`). |
| `audio:resolve:{REQUEST_ACTION}` | **0.1.107** | Resolves an audio URL dynamically before playback. |
| `audio:auto:resolve:{REQUEST_ACTION}` | **0.1.107** | Same, played in auto mode (see `audio:auto:...`). |
| `image:resolve:{REQUEST_ACTION}` | **0.1.107** | Shows an image by resolving its URL dynamically. |
| `background:resolve:{REQUEST_ACTION}` | **0.1.107** | Loads a temporary background scene by resolving its URL dynamically. |

Each of these six families also supports a `user:` sub-variant (e.g. `video:resolve:user:{URL}`) and a `request:interaction:{DATA_ID}[@{URL}]` sub-variant (e.g. `video:resolve:request:interaction:{DATA_ID}@{URL}`) — both share the same `0.1.107` minimum as the base form (it's Resolve itself, not the `request:interaction:` mechanism, which is `0.1.82` on its own — see Request Actions above — that sets the floor here).

**2. As a bare `resolve:{URL}` property value — no `action` property involved, and only for two specific properties:**

| Property | Where it works | Since |
|---|---|---|
| `image` | **Content context only** — a Content Item Object's `image` property. Does **not** work for a Menu Item Object's `image` or the Menu Root Object's `logo`. | **0.1.107** |
| `background` | **Always, on any `background` property, regardless of context or nesting level** — Content Item Object, Content Page Object, Content Root Object, Selection Object, Menu Item Object, Menu Root Object all have their own `background` property, and `resolve:{URL}` works as its value on every one of them. | **0.1.107** |

Example: `"background": "resolve:http://msx.benzac.de/services/resolve.php?type=image"`.

> **Note:** All action-string variants above, and their `auto:`/`user:`/`request:interaction:` sub-variants in any combination, share the `0.1.107` minimum — not the Internal Actions' blanket `0.1.160+`, even though they also appear in that list.

See [Resolve Action](../experts-api/hidden-features/resolve-action.md) for details.

---

## Internal-Only Actions — Overview (require `0.1.160+`)

The following action families exist in [Internal Actions](../experts-api/special/internal-actions.md) but are **not** official Main Actions. Most require MSX `0.1.160+` with no finer version control.

> **In practice this rarely matters:** nearly all platforms run the latest MSX version at any given time (currently `0.1.165`–`0.1.166`). The only real exception is older Samsung TV models: **2014–2021** models, which Samsung no longer allows to be updated, can be stuck on versions from `0.1.58` up to `0.1.163` (**2011–2013** models never ran MSX at all — they were already outside Samsung's app-submission-eligible model range by the time MSX first launched) — see [Versioning → How "Since Version" Annotations Work](versioning.md#how-since-version-annotations-work). Treat `0.1.160+` as a safe default; only chase a tighter version below if you specifically need to support an old device.

A handful of action families have their own documented since-version well below `0.1.160` anyway — summarized here, with full detail at the linked section:

| Exception | Since Version |
|---|---|
| Resolve Action family (`*:resolve:...`, incl. `user:`/`request:interaction:` sub-variants) — [Resolve Action](#resolve-action-since-01107) | **0.1.107** |
| Broadcast/HbbTV family (`audio:broadcast:...`, `video:broadcast:...`, `system:hbbtv:...`) — [Broadcast and HbbTV Actions](#broadcast-and-hbbtv-actions-a-third-case) | **0.1.30** |
| `execute:service:...` — [Server Actions](#server-actions) | **0.1.90** |
| `content:json:`/`menu:json:`/`panel:json:`/`playlist:json:`/`slideshow:json:` (base64-embedding) | same as base action, **0.1.0** |
| `request:interaction:{DATA_ID}@{URL}` combined with `content:`/`menu:`/`panel:`/`playlist:`/`slideshow:`/`execute:` — [Request Actions](#request-actions-requestinteraction-is-0182-requestplayer-remains-01160) | **0.1.82** |
| `system:tvx:launch`/`system:tvx:launch:{APP_ID}` — [Android Player](../experts-api/special/android-player.md) | **0.1.136** |
| `system:netcast:menu` — [NetCast Menu](../experts-api/special/netcast-menu.md) | **0.1.136** |
| Runtime-settable form of a "Dynamic" Extended Property (`player:...`, `slider:...`, `resume:position`, `trigger:{KEY}:{ACTION}`) — [Actions That Mirror a "Dynamic" Extended Property](#actions-that-mirror-a-dynamic-extended-property) | **0.1.58**–**0.1.156** (per property) |
| `player:commit`/`player:commit:message:{MESSAGE}` / `interaction:commit:response:...` — [Plugin-Commit Actions](#plugin-commit-actions-playercommit-interactioncommitresponse-and-tizen-player) | **0.1.74** / **0.1.128** |

The sibling `request:player:{DATA_ID}` pattern and combinations floored higher by a later prefix (e.g. `playlist:auto:request:interaction:...`) stay at blanket `0.1.160+` or their own higher floor — not covered by the `0.1.82` row above.

For the complete syntax, examples, and data requirements of each, load [experts-api/special/internal-actions.md](../experts-api/special/internal-actions.md).

| Action Family | Key Variants | Data | Brief Purpose |
|---|---|---|---|
| `audio:auto:...` | `audio:auto:{URL}`, `audio:auto:plugin:{URL}`, `audio:auto:resolve:...` (**0.1.107**, see Resolve Action above), `audio:auto:broadcast:...` | Optional | Play audio in auto mode (various sources) |
| `audio:broadcast:...` | `audio:broadcast:current`, `audio:broadcast:next`, `audio:broadcast:prev`, `audio:broadcast:release`, `audio:broadcast:name:{NAME}`, `audio:broadcast:triplet:{ONID}.{TSID}.{SID}` | Optional | Platform-specific broadcast audio. **`0.1.30`** (same feature area as `video:broadcast:...`/`system:hbbtv:...`, see below), not blanket `0.1.160+`. |
| `audio:resolve:...` | `audio:resolve:{URL}`, `audio:resolve:user:{URL}`, `audio:resolve:request:interaction:{DATA_ID}@{URL}` (all **0.1.107**, see Resolve Action above — not blanket `0.1.160+`) | Optional | Resolve audio URL dynamically (response must have `url` property) |
| `awake` | `awake` | None | Wake screen, restart sleep/eject timeout |
| `back` | `back` | None | Goes back in history |
| `background` / `background:{URL}` | `background`, `background:{URL}`, `background:none`, `background:default`, `background:resolve:...` (**0.1.107**, see Resolve Action above) | None | The bare `background` action closes **every** currently visible scene — overlays *and* the main scene alike — leaving only the background image or a running video/audio visible. Broader than `cleanup` below, which only closes overlay scenes. `background:{URL}`/`background:none`/`background:default`/`background:resolve:...` instead show or load a (temporary) background. |
| `busy:start:{ID}` / `busy:stop:{ID}` | `busy:start:loading`, `busy:stop:loading` | None | Start/stop a busy indicator (max 30s) |
| `cleaning` | `cleaning` | None | Clear cache and reload |
| `cleanup` | `cleanup` | None | Closes all overlay scenes only — panels, notification messages, and the volume view — leaving whatever was underneath (the main scene, or a running video/audio) visible. Not to be confused with the bare `background` action above, which also closes the main scene itself. |
| `close` / `close:{FLAG}` | `close`, `close:content_flag` | None | Close visible content/panel |
| `content:json:{BASE64_ENCODED_JSON}` | — | None | Show content from inline base64-encoded JSON. **`0.1.0`** (same as `content:{URL}`). |
| `content:request:interaction:...` | `content:request:interaction:{DATA_ID}`, `content:request:interaction:{DATA_ID}@{URL}` | None | Load content from interaction plugin. **`0.1.82`**, not blanket `0.1.160+` — see [Request Actions](#request-actions-requestinteraction-is-0182-requestplayer-remains-01160) above. |
| `content:request:player:...` | `content:request:player:{DATA_ID}` | None | Load content from player (video/audio plugin) |
| `delay:{ID}:{DELAY}:{ACTION}` | `delay:id:2:info:Message` | Optional | Execute action after delay (max 30s); also `delay:id:cancel`, `delay:id:execute`, `delay:id:restart` |
| `dialog:{DIALOG_ID}` | `dialog:reload`, `dialog:exit`, `dialog:none`, … | None | Show a system dialog; `none` closes active notification |
| `dictionary:{URL}` / `dictionary:none` | — | None | Set or reset a custom dictionary |
| `eject` | `eject` | None | Show auto-eject panel |
| `enter:{CODE}` | — | **Required** | Execute action if code is correct |
| `event:{EVENT_ID}` | `event:custom_event_id` | Optional | Trigger custom event (handled by interaction/video plugin) |
| `execute:accurate:{URL}` | — | Optional | Server action with accurate headers (also: `execute:code:accurate:`, `execute:user:accurate:`, `execute:code:user:accurate:`, etc.). Note: `execute:accurate:{URL}`, `execute:code:accurate:{URL}`, `execute:user:accurate:{URL}`, and `execute:code:user:accurate:{URL}` are all Main Action variants (**0.1.90**, see the "`user:` and `accurate:` Prefix Variants" section above) — the `execute:service:accurate:...` combination is the internal-only exception. |
| `execute:fetch:{URL}` | — | None | Execute by simply fetching the URL (no POST body) |
| `execute:info:{URL}` | Also: `execute:info:extended:`, `execute:info:base:`, `execute:info:application:`, `execute:info:framework:`, `execute:info:content:`, `execute:info:dictionary:`, `execute:info:screen:`, `execute:info:time:` | Optional | Execute server action with attached system info |
| `execute:request:interaction:{DATA_ID}[@{URL}]` | — | Optional | Execute action on interaction plugin. **`0.1.82`**, not blanket `0.1.160+` — see [Request Actions](#request-actions-requestinteraction-is-0182-requestplayer-remains-01160) above. |
| `execute:request:player:{DATA_ID}` | — | Optional | Execute action on player |
| `execute:response:...` | `execute:response:{REQUEST_ACTION}` | **Required** | Execute with attached response from a request action |
| `execute:resume:{URL}` | — | Optional | Execute server action with attached resume keys |
| `execute:service:...` | All `execute:service:*` variants | Optional/None | Parallel service actions (max 128); mirrors all `execute:*` variants. Shares the **0.1.90** since-version of the `service:`/`accurate:` prefix feature area (see Server Actions above) — **not** the blanket `0.1.160+`. |
| `execute:silent:...` | All `execute:silent:*` variants | Optional | Execute in silent mode (no busy indicator, no error messages shown) |
| `execute:slider:{URL}` | — | Optional | Execute server action with attached slider info |
| `execute:video:{URL}` | Also: `execute:video:info:`, `execute:video:data:`, `execute:video:resume:`, `execute:video:volume:`, `execute:video:scene:` | Optional | Execute server action with attached video info |
| `exit` | `exit` | None | Exit the application |
| `focus:clear:{FLAG}` | — | None | Clear focus cache for content/panel with flag |
| `focus:control:{CONTROL}` | `focus:control:menu`, `focus:control:content` | None | Focus a UI control |
| `focus:execute:{ID}` / `focus:execute:index:{INDEX}` | — | None | Focus and execute an item |
| `fullscreen` | `fullscreen` | None | Toggle fullscreen |
| `history:back` / `history:clear` / `history:start` / `history:validate` | — | None | Web history navigation |
| `home` | `home` | None | Show main scene (or context menu) |
| `image:resolve:...` | `image:resolve:{URL}`, `image:resolve:user:{URL}`, `image:resolve:request:interaction:{DATA_ID}@{URL}` (all **0.1.107**, see Resolve Action above — not blanket `0.1.160+`; this is the `action`-property action string, distinct from the bare `resolve:{URL}` value usable directly on the `image` *property* in content context) | Optional | Resolve image URL dynamically |
| `interaction:...` | `interaction:busy:start:{ID}`, `interaction:busy:stop:{ID}`, `interaction:commit`, `interaction:commit:code`, `interaction:commit:dictionary`, `interaction:commit:info[:{TYPE}]`, `interaction:commit:message:{MESSAGE}`, `interaction:commit:response:...`, `interaction:commit:slider`, `interaction:commit:string:{TYPE}:{STRING}`, `interaction:commit:video[:{TYPE}]`, `interaction:load:{URL}`/`interaction:load:none`, `interaction:reload`, `interaction:unload` | Various (see below) | Manage and commit data to the interaction plugin. **`interaction:commit:response:{REQUEST_ACTION}` and `interaction:commit:response:request:player:{DATA_ID}` (incl. the Tizen-specific `...:tizen:info/property/properties:...` forms) are `0.1.128`**, not blanket `0.1.160+` — see [Plugin-Commit Actions (`player:commit`, `interaction:commit:response:...`) and Tizen Player](#plugin-commit-actions-playercommit-interactioncommitresponse-and-tizen-player) below. |
| `invalidate:...` | `invalidate:content[:{ID}]`, `invalidate:content:overlay:{ID}`, `invalidate:content:underlay:{ID}`, `invalidate:content:flag:{FLAG}`, `invalidate:menu[:{ID}]`, `invalidate:menu:flag:{FLAG}`, `invalidate:panel[:{ID}]`, `invalidate:panel:overlay:{ID}`, `invalidate:panel:underlay:{ID}`, `invalidate:panel:flag:{FLAG}` | None | **Purely visual** — makes the current content/menu/panel (or a specific item) appear semi-transparent to signal to the user that a refresh is imminent. The base and `:flag:{FLAG}` variants signal a full content/menu/panel refresh; the `:{ID}`/`:overlay:{ID}`/`:underlay:{ID}` variants only make that one item semi-transparent, signaling just an item-level refresh, not a full one. It does not itself reload or refresh any data; pair it with a `reload:`/`replace:`/`execute:` action that performs the actual refresh (see the EPG/tabs patterns in [Cookbook](../reference/cookbook.md)). Not to be confused with `release:`, which actually clears caches/references — see below. |
| `key:...` | `key:{REMOTE_KEY}` (e.g. `key:left`, `key:execute`, `key:red`), `key:code:{REMOTE_KEY_CODE}` | None | Trigger a remote key (by name or key code) |
| `lazy:{ACTION}` | `lazy:info:Lazy action executed.` | Optional | Execute an action once all running animations complete (executes immediately if none are running) |
| `leave` | `leave` | None | Leave the application via the web history back function |
| `link:replace:{URL}` / `link:validate:{URL}` | `link:replace:http://msx.benzac.de/info/`, `link:validate:http://msx.benzac.de/info/`, `link:validate:window:http://msx.benzac.de/info/` | None | Open a link by replacing the current URL, or with enforced validation. Internal-only, **`0.1.160+`**. Distinct from the plain `link:{URL}` and `link:window:{URL}` **Main Actions** (both `0.1.0`, see Basic Actions above). |
| `live` | `live` | None | Executes the live action |
| `log` / `log:clear` | `log`, `log:clear` | None | Toggle the log scene / clear the log |
| `logger:...` | `logger:debug:{MESSAGE}`, `logger:error:{MESSAGE}`, `logger:info:{MESSAGE}`, `logger:success:{MESSAGE}`, `logger:warn:{MESSAGE}` | Optional (except `debug`: None) | Log a message at the given level (non-debug levels also show the message) |
| `main` | `main` | None | Show the main scene (no-op if already active) |
| `menu` / `menu:json:...` / `menu:request:...` | `menu` (toggles context menu), `menu:json:{BASE64_ENCODED_JSON}`, `menu:request:interaction:{DATA_ID}[@{URL}]`, `menu:request:player:{DATA_ID}` | None | Internal-only menu variants (`menu:{URL}`, `menu:data`, `menu:user:{URL}` are Main Actions, see above). **`menu:json:{BASE64_ENCODED_JSON}` is `0.1.0`** — same since-version as `menu:{URL}`, not the blanket `0.1.160+`. **`menu:request:interaction:...` is `0.1.82`** — see [Request Actions](#request-actions-requestinteraction-is-0182-requestplayer-remains-01160) above. The bare `menu` (context-menu toggle) and `menu:request:player:...` genuinely require `0.1.160+`. |
| `notification:{MESSAGE}` | `notification:This is a notification message.` | Optional | Commit a notification message to the video/audio and interaction plugin |
| `options` | `options` | None | Toggle the content options panel |
| `panel:json:...` / `panel:request:...` | `panel:json:{BASE64_ENCODED_JSON}`, `panel:request:interaction:{DATA_ID}[@{URL}]`, `panel:request:player:{DATA_ID}` | None | Internal-only panel variants (`panel:{URL}`, `panel:data`, `panel:user:{URL}` are Main Actions, see above). **`panel:json:{BASE64_ENCODED_JSON}` is `0.1.0`** — same since-version as `panel:{URL}`, not the blanket `0.1.160+`. **`panel:request:interaction:...` is `0.1.82`** — see [Request Actions](#request-actions-requestinteraction-is-0182-requestplayer-remains-01160) above. `panel:request:player:...` genuinely requires `0.1.160+`. |
| `playback` | `playback` | None | Show video/audio playback, closing any overlaying content |
| `playlist:auto:...` / `playlist:json:...` / `playlist:request:...` | `playlist:auto:{URL}`, `playlist:auto:data`, `playlist:auto:json:...`, `playlist:auto:request:interaction/player:...`, `playlist:auto:user:{URL}`, `playlist:json:{BASE64_ENCODED_JSON}`, `playlist:request:interaction/player:...` | Varies | Internal-only playlist variants, incl. auto mode (`playlist:{URL}`, `playlist:data`, `playlist:user:{URL}` are Main Actions, see above). **`playlist:json:{BASE64_ENCODED_JSON}` is `0.1.0`** — same since-version as `playlist:data`, not the blanket `0.1.160+`. **`playlist:request:interaction:...` (without `auto:`) is `0.1.82`** — see [Request Actions](#request-actions-requestinteraction-is-0182-requestplayer-remains-01160) above. All `playlist:auto:...` combinations (incl. `playlist:auto:request:interaction:...`, floored by the `auto:` prefix itself) and `playlist:request:player:...` genuinely require `0.1.160+`. |
| `player:...` | Large family (30+ variants), e.g. `player`, `player:play`/`pause`/`play_pause`/`stop`/`eject`/`restart`, `player:goto:{ID/first/last/next/prev/random/index/number}`, `player:seek:{VALUE}`, `player:button:{ID}:{enable/disable/execute/focus/reset/setup}`, `player:progress:...`, `player:label:...`, `player:info:...`, `player:speed:...`, `player:video:{clear/duration/position/speed/state}`, `player:commit...`, `player:content[:...]`, `player:menu[:...]` | Varies | Full internal control surface for the video/audio player (playback, buttons, progress/labels, custom overrides). None of these are Main Actions. **Many `player:button:*`/`player:info:*`/`player:label:*`/`player:progress:*`/`player:video:*`/`player:control:action` variants are the runtime-settable form of a "Dynamic" Extended Property and share that property's since-version (`0.1.111`–`0.1.156`, not blanket `0.1.160+`)** — see [Actions That Mirror a "Dynamic" Extended Property](#actions-that-mirror-a-dynamic-extended-property) below for the exact per-action version. **`player:commit` and `player:commit:message:{MESSAGE}` (generally, not just for Tizen) are `0.1.74`**, not blanket `0.1.160+` — see [Plugin-Commit Actions (`player:commit`, `interaction:commit:response:...`) and Tizen Player](#plugin-commit-actions-playercommit-interactioncommitresponse-and-tizen-player) below. |
| `quiet` | `quiet` | None | Stop a running video/audio |
| `release:content` / `release:menu` / `release:panel` | `release:content`, `release:menu`, `release:panel` | None | Actually clears the cache and references for the current content/menu/panel data, forcing a reload the next time the same URL is used; cannot be reloaded via `reload` afterwards, but `replace` still works. Not to be confused with `invalidate:`, which is purely visual and does not clear anything — see above. |
| `reopen:{PARAMS}` | `reopen:key1=value1&key2=value2` | None | Reopen the application link with new URL parameters |
| `response:{REQUEST_ID}` | `response:rq10_1541593729708` | Optional | Handle a response for a pending request |
| `restart` | `restart` | None | Restart the application |
| `resume:...` | `resume:cancel`, `resume:clear[:{KEY}/current/context:.../hash:...]`, `resume:import`, `resume:position:{VALUE/none}` | Varies (`resume:import` requires data) | Manage resume keys and positions. **`resume:position:{VALUE/none}` is `0.1.111`** — the runtime-settable form of the "Dynamic" `resume:position` Extended Property, not blanket `0.1.160+`; the other `resume:...` variants remain blanket `0.1.160+`. |
| `selection` | `selection` | None | Executes the selection action |
| `settings:...` | Large family, one `settings:{TOPIC}` action to show each settings panel (`about`, `animate`, `eject_timeout`, `hover_effect`, `immersive_mode`, `input`, `layout`, `menu_button`, `random_playback`, `remote`, `reset`, `resolution`, `rounded_style`, `scale`, `screen_saver`, `sleep_timeout`, `slideshow_interval`, `start`, `transform`, `validate`, `validate_links`, `visual_effects`, `visual_execution`, `zoom`) plus a `settings:{TOPIC}:{VALUE}` variant to set most of them directly — `resolution`, `screen_saver`, and `visual_effects` are grouping panels (see [In-App Settings Reference](../reference/settings-reference.md#2-settings-screen-map)) with no `:{VALUE}` form of their own; their members are set individually one level down | None | Show or set a specific app settings panel/value |
| `sleep` | `sleep` | None | Force the screen saver to be active |
| `slider:...` | Large family, e.g. `slider:play`/`pause`/`play_pause`/`stop`/`eject`/`restart`, `slider:goto:{ID/first/last/next/prev/index/number}`, `slider:rotate:{VALUE}`, `slider:color:{COLOR}`, `slider:icon:{ICON}`, `slider:label:{LABEL}`, `slider:action:{ACTION}`, `slider:options[:execute/select:{ID}]`, `slider:labels:{show/hide/toggle/play_pause}` | Varies | Full internal control surface for the slideshow (playback, image overrides, options). **`slider:action:{ACTION}`/`slider:extension:{LABEL}` are `0.1.111`, `slider:icon:{ICON}`/`slider:rotate:{ROTATION_VALUE}` are `0.1.145`** — runtime-settable forms of "Dynamic" Extended Properties (`image:action`/`image:extension`/`image:icon`/`image:rotation:value`), not blanket `0.1.160+`; the other `slider:...` variants remain blanket `0.1.160+`. |
| `slideshow:auto...` / `slideshow:json:...` / `slideshow:request:...` | `slideshow:json:{BASE64_ENCODED_JSON}`, `slideshow:request:interaction/player:...` | None | Internal-only slideshow variants (`slideshow:{URL}`, `slideshow:data`, `slideshow:user:{URL}` are Main Actions, see above). **`slideshow:json:{BASE64_ENCODED_JSON}` is `0.1.0`** — same since-version as `slideshow:data`, not the blanket `0.1.160+`. **`slideshow:request:interaction:...` is `0.1.82`** — see [Request Actions](#request-actions-requestinteraction-is-0182-requestplayer-remains-01160) above. `slideshow:auto:...` and `slideshow:request:player:...` genuinely require `0.1.160+`. |
| `still` | `still` | None | Stop a running slideshow |
| `system:...` | `system:hbbtv:{show/hide/launch:{ID}/foreground/background/transient/update}`, `system:lg:launch:{ID}`, `system:netcast:{menu/ratio}`, `system:options`, `system:samsung:launch:{ID}`, `system:tizen:{launch[:{ID}]/keys:mapped/keys:unmapped}`, `system:tvx:{about/settings/restart/exit/log[:clear]/system_log[:clear]/options/launch[:{ID}]}`, `system:uwp:launch:{ID}` | Varies | Platform-specific system actions (HbbTV, LG, Netcast, Samsung, Tizen, TVX/Android/iOS, UWP). All `system:hbbtv:...` variants share **`0.1.30`** (same feature area as `audio:broadcast:...`/`video:broadcast:...` above), not blanket `0.1.160+`. **`system:tvx:launch`/`system:tvx:launch:{APP_ID}`** (Android/Fire TV external-app launch — see [Android Player](../experts-api/special/android-player.md)) and **`system:netcast:menu`** (see [NetCast Menu](../experts-api/special/netcast-menu.md)) are **`0.1.136`**, not blanket `0.1.160+`; other `system:tvx:...`/`system:...` sub-actions remain blanket `0.1.160+`. |
| `test` | `test` | None | Toggle the test scene |
| `time:...` | `time:clear`, `time:init`, `time:set` | `time:init`/`time:set`: Required | Manage a fixed time/zone offset |
| `trigger:{KEY}:{ACTION}` | `trigger:load/ready/start/ending/end/complete/play/pause/stop/active/inactive/player/foreground/background/back:{ACTION}`, `trigger:{SECONDS}[%\|t]:[shot:]{ACTION}`, `trigger:quartile1-4:{ACTION}`, `trigger:first_quartile/midpoint/third_quartile:{ACTION}`, `trigger:back:none` (removes trigger) | None | Set up a video/audio playback trigger for the current content (must be called after `video:`/`audio:`/`playlist:`). **This whole family is `0.1.58`** — the runtime-settable form of the "Dynamic" `trigger:{TRIGGER_KEY}` Extended Property (same version), not blanket `0.1.160+`. |
| `video:auto:...` | `video:auto:{URL}`, `video:auto:plugin:{URL}`, `video:auto:resolve:...` (**0.1.107**, see Resolve Action above), `video:auto:broadcast:...` | Optional | Play video in auto mode (various sources) |
| `video:broadcast:...` | `video:broadcast:current/next/prev/release/name:{NAME}/triplet:{ONID}.{TSID}.{SID}` | Optional | Platform-specific broadcast video. `video:broadcast:current` is a Main Action (`0.1.30`); all variants share that same **`0.1.30`** minimum (same feature area as `audio:broadcast:...`/`system:hbbtv:...`), not blanket `0.1.160+`. |
| `video:resolve:...` | `video:resolve:{URL}`, `video:resolve:user:{URL}`, `video:resolve:request:interaction:{DATA_ID}[@{URL}]` (all **0.1.107**, see Resolve Action above — not blanket `0.1.160+`) | Optional | Resolve video URL dynamically (response must have `url` property) |
| `volume:...` | `volume`, `volume:action`, `volume:dec`/`inc`, `volume:hide`/`show`/`toggle_show`, `volume:mute`/`unmute`/`toggle`, `volume:set:{VALUE}` | None | Show/hide/toggle the volume scene and adjust volume |

> **For the complete, authoritative list of all internal actions with full syntax, all variant examples, and data requirements**, see [Internal Actions](../experts-api/special/internal-actions.md). The table above is a summary overview only.
>
> **Note on `image:resolve:{URL}` vs. bare `resolve:{URL}`:** These are two distinct syntaxes. `image:resolve:{URL}` is an **`action`-property action string** (see Resolve Action above). Bare `resolve:{URL}` is a **property value**, used directly as the value of the `image` property (content context only) or the `background` property (always) — not an action at all. Both share the `0.1.107` since-version.

### Actions That Mirror a "Dynamic" Extended Property

[Extended Properties](../experts-api/special/extended-properties.md) marks certain properties `Dynamic: Yes` — meaning, per that page's own introduction, they "can also be set via an action at runtime" instead of only in the static `properties` object at content-authoring time. Each such action is one of the internal actions documented in [Internal Actions](../experts-api/special/internal-actions.md), and shares **exactly the same since-version as its property** — not the blanket `0.1.160+`.

This table is the complete cross-reference, derived by matching each `Dynamic: Yes` property's row in Extended Properties against the action with a near-identical description in Internal Actions:

| Extended Property | Dynamic Action | Since Version |
|---|---|---|
| `trigger:{TRIGGER_KEY}` | `trigger:{TRIGGER_KEY}:{ACTION}` | `0.1.58` |
| `button:{BUTTON_ID}:action` | `player:button:{BUTTON_ID}:setup` (`data.action`) | `0.1.111` |
| `button:{BUTTON_ID}:enable` | `player:button:{BUTTON_ID}:enable` / `:disable` | `0.1.111` |
| `button:{BUTTON_ID}:focus` | `player:button:{BUTTON_ID}:focus` | `0.1.111` |
| `button:{BUTTON_ID}:icon` | `player:button:{BUTTON_ID}:setup` (`data.icon`) | `0.1.111` |
| `image:action` | `slider:action:{ACTION}` | `0.1.111` |
| `image:extension` | `slider:extension:{LABEL}` | `0.1.111` |
| `label:duration` | `player:label:duration:{LABEL}` | `0.1.111` |
| `label:extension` | `player:label:extension:{LABEL}` | `0.1.111` |
| `label:position` | `player:label:position:{LABEL}` | `0.1.111` |
| `progress:color` | `player:progress:color:{COLOR}` | `0.1.111` |
| `progress:marker:enable` | `player:progress:marker:enable` / `:disable` | `0.1.111` |
| `progress:type` | `player:progress:type:{PROGRESS_TYPE}` | `0.1.111` |
| `resume:position` | `resume:position:{RESUME_POSITION}` | `0.1.111` |
| `video:duration` | `player:video:duration:{VIDEO_DURATION}` | `0.1.128` |
| `video:position` | `player:video:position:{VIDEO_POSITION}` | `0.1.128` |
| `video:speed` | `player:video:speed:{VIDEO_SPEED}` | `0.1.128` |
| `video:state` | `player:video:state:{VIDEO_STATE}` | `0.1.128` |
| `info:image` | `player:info:image:{URL}` | `0.1.130` |
| `info:text` | `player:info:text:{TEXT}` | `0.1.130` |
| `label:speed` | `player:label:speed:{LABEL}` | `0.1.130` |
| `button:{BUTTON_ID}:key` | `player:button:{BUTTON_ID}:setup` (`data.key`) | `0.1.132` |
| `control:action` | `player:control:action:{ACTION}` | `0.1.140` |
| `image:icon` | `slider:icon:{ICON}` | `0.1.145` |
| `image:rotation:value` | `slider:rotate:{ROTATION_VALUE}` | `0.1.145` |
| `info:overlay` | `player:info:overlay:{OVERLAY}` | `0.1.146` |
| `info:size` | `player:info:size:{SIZE}` | `0.1.146` |
| `progress:duration` | `player:progress:duration:{PROGRESS_DURATION}` | `0.1.146` |
| `progress:position` | `player:progress:position:{PROGRESS_POSITION}` | `0.1.146` |
| `info:headline` | `player:info:headline:{HEADLINE}` | `0.1.154` |
| `info:round` | `player:info:round:enable` / `:disable` | `0.1.156` |

> **`button:{BUTTON_ID}:action`/`:icon`/`:key`:** all three properties are set dynamically through the **same** action, `player:button:{BUTTON_ID}:setup`, which accepts `action`/`icon`/`key` (and `enable`) as separate `data` fields — there is no individually-named action per property.
>
> **`image:*` → `slider:*`:** the property namespace (`image:`) and the action namespace (`slider:`) differ in name — the match was confirmed by near-identical description wording between the two source pages, not by name alone.
>
> Properties **not** listed here (`button:{BUTTON_ID}:display`, `control:dim`/`load`/`retune`/`return`/`reuse`/`transparent`/`type`, `image:options`/`rotation:trigger`/`trigger`, `progress:display`, `resume:context`/`key`) are marked `Dynamic: No` in Extended Properties — they can only be set statically in `properties`, with no corresponding runtime action at all.

### Plugin-Commit Actions (`player:commit`, `interaction:commit:response:...`) and Tizen Player

`player:commit`, `player:commit:message:{MESSAGE}`, `interaction:commit:response:{REQUEST_ACTION}`, and `interaction:commit:response:request:player:{DATA_ID}` are generic plugin-commit actions, built for a video/audio **plugin** loaded via `video:plugin:{URL}` (see [Video/Audio Plugin](../experts-api/plugins/video-audio-plugin.md)). Despite being flagged "Main Action: No" in [Internal Actions](../experts-api/special/internal-actions.md), each has a confirmed since-version below `0.1.160` (earliest point independently confirmable — the true original introduction may be earlier still):

| Action | Since Version |
|---|---|
| `player:commit` | `0.1.74` |
| `player:commit:message:{MESSAGE}` | `0.1.74` |
| `interaction:commit:response:{REQUEST_ACTION}` | `0.1.128` |
| `interaction:commit:response:request:player:{DATA_ID}` | `0.1.128` |

> `0.1.74` per [Video/Audio Plugin](../experts-api/plugins/video-audio-plugin.md)'s own "full-featured plugin" minimum version; `0.1.128` per [Tizen Player](../experts-api/special/tizen-player.md)'s explicit "Since Version" statement.

**Tizen Player reuses, rather than invents, this pre-existing plugin-commit mechanism** — it's MSX's native player, bridged internally to the Tizen `AVPlay` API, but addressed via the same commit syntax as a real plugin. Its Tizen-specific forms (`player:commit:message:tizen:{PROPERTY}:{VALUE}`, `interaction:commit:response:request:player:tizen:...`) share `0.1.128` — Tizen Player's own introduction version, later than the `0.1.74` floor of the generic actions themselves. None of these Tizen-specific forms get their own rows in Internal Actions.

**Tizen also has its own parallel "Dynamic" Extended Properties**, documented only on [Tizen Player](../experts-api/special/tizen-player.md) (too platform-specific for the main Extended Properties table), each set at runtime via the mechanism above:

| Tizen Extended Property | Since Version |
|---|---|
| `tizen:buffer:timeout` | `0.1.128` |
| `tizen:display:area` | `0.1.128` |
| `tizen:display:mode` | `0.1.128` |
| `tizen:ready` | `0.1.128` |
| `tizen:start` | `0.1.128` |
| `tizen:track:audio` | `0.1.128` |
| `tizen:track:text` | `0.1.128` |
| `tizen:subtitle:delay` | `0.1.141` |
| `tizen:subtitle:silent` | `0.1.141` |
| `tizen:subtitle:url` | `0.1.141` |
| `tizen:subtitle:hidden` | `0.1.145` |
| `tizen:subtitle:type` | `0.1.145` |
| `tizen:subtitle:style:color` | `0.1.153` |
| `tizen:subtitle:style:size` | `0.1.153` |
| `tizen:subtitle:style:type` | `0.1.153` |

> Properties **not** listed here (`tizen:buffer:size`, `tizen:buffer:size:init`, `tizen:buffer:size:resume`, `tizen:stream:{STREAM_TYPE}`, `tizen:load`) are marked `Dynamic: No` on Tizen Player — static only (set once, at content-authoring time, in `properties`), with no corresponding runtime action.

---

## Which Actions Are Both Main Actions AND Internal Actions?

This is the complete list of actions marked "Main Action: Yes" in [Internal Actions](../experts-api/special/internal-actions.md). Each also appears as an official Main Action with an exact since-version — always use that version, not the generic `0.1.160+`:

`audio:{URL}` (0.1.0), `audio:plugin:{URL}` (0.1.40), `content:{URL}` (0.1.0), `content:data` (0.1.0), `content:user:{URL}` (0.1.30), `data` (0.1.58), `error:{TEXT}` (0.1.0), `execute:{URL}` (0.1.30), `execute:accurate:{URL}` (**0.1.90** — the `accurate:` modifier itself is only available since 0.1.90, even though the base `execute:{URL}` action is 0.1.30), `execute:code:{URL}` (0.1.30), `execute:code:accurate:{URL}` (**0.1.90**), `execute:code:user:{URL}` (0.1.30), `execute:code:user:accurate:{URL}` (**0.1.90**), `execute:user:{URL}` (0.1.30), `execute:user:accurate:{URL}` (**0.1.90**), `focus:{ID}` (0.1.0), `focus:index:{INDEX}` (0.1.0), `image:{URL}` (0.1.0), `image:context` (0.1.0), `info:{TEXT}` (0.1.0), `link:{URL}` (0.1.0), `link:window:{URL}` (0.1.0), `menu:{URL}` (0.1.0), `menu:data` (0.1.0), `menu:user:{URL}` (0.1.30), `panel:{URL}` (0.1.0), `panel:data` (0.1.0), `panel:user:{URL}` (0.1.30), `playlist:{URL}` (0.1.0), `playlist:data` (0.1.0), `playlist:user:{URL}` (0.1.30), `reload` (0.1.58), `reload:content` (0.1.58), `reload:menu` (0.1.58), `reload:panel` (0.1.58), `slideshow:{URL}` (0.1.0), `slideshow:data` (0.1.0), `slideshow:user:{URL}` (0.1.30), `start` (0.1.30), `success:{TEXT}` (0.1.0), `update:content:{ID}` (0.1.58), `update:content:overlay:{ID}` (0.1.110), `update:content:underlay:{ID}` (0.1.112), `update:menu:{ID}` (0.1.58), `update:panel:{ID}` (0.1.58), `update:panel:overlay:{ID}` (0.1.110), `update:panel:underlay:{ID}` (0.1.112), `video:{URL}` (0.1.0), `video:plugin:{URL}` (0.1.40), `warn:{TEXT}` (0.1.0), `[{ACTION}|{ACTION}|...]` (0.1.58).

> **This list intentionally excludes `video:broadcast:current` and `system:hbbtv:launch:{URL}`** even though both are Main Actions — see the next section for why, and for the related `audio:broadcast:...` family.
>
> **It also excludes `content:json:{BASE64_ENCODED_JSON}`, `menu:json:{BASE64_ENCODED_JSON}`, `panel:json:{BASE64_ENCODED_JSON}`, `playlist:json:{BASE64_ENCODED_JSON}`, and `slideshow:json:{BASE64_ENCODED_JSON}`.** These base64-embedding variants are `0.1.0` — the same since-version as their respective base action (`content:{URL}`, `menu:{URL}`, `panel:{URL}`, `playlist:{URL}`/`playlist:data`, `slideshow:{URL}`/`slideshow:data`), not the blanket `0.1.160+`. They don't appear in this Main Actions list itself because the Main Actions tables above document them only inline (as "Also supports ...") rather than as their own dedicated row — but their since-version is nonetheless `0.1.0`. See the corresponding rows in the Internal-Only Actions Overview above.

---

## Broadcast and HbbTV Actions: A Third Case

The Main Actions tables and the "Yes"-flag list above are both literal, source-faithful — but **neither fully captures the broadcast/HbbTV feature area**, so it gets its own explanation.

`video:broadcast:current` and `system:hbbtv:launch:{URL}` **are** official Main Actions (`0.1.30`) that also appear in Internal Actions, bundled into family rows (`video:broadcast:{BC_ACTION}`, `system:hbbtv:{SYSTEM_ACTION}`) flagged **"Main Action: No"** overall because most of their sibling variants (`next`, `prev`, `show`, `hide`, …) aren't individually documented as Main Actions. That flag is misleading: `0.1.30` covers the **entire** family regardless, including `audio:broadcast:...` (no individually-documented Main Action variant at all, but the same feature area, introduced together).

**In short:** for the broadcast/HbbTV feature area, always use `0.1.30` for every variant of `audio:broadcast:...`, `video:broadcast:...`, and `system:hbbtv:...` — never the `0.1.160+` blanket.

---

## See Also

- [Actions](../main-api/common/actions.md) — primary source for all Main Actions
- [Internal Actions](../experts-api/special/internal-actions.md) — full list of all internal actions
- [Responses](../main-api/common/responses.md) — response format for server actions
- [Requests](../main-api/common/requests.md) — request mechanics
- [Replace Action](../experts-api/hidden-features/replace-action.md) — replace/reload hidden feature
- [Start Action](../experts-api/hidden-features/start-action.md) — startup-action hidden feature
- [Resolve Action](../experts-api/hidden-features/resolve-action.md) — resolve hidden feature
