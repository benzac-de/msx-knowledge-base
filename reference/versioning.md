---
title: Versioning Model
category: Reference
summary: Explains the three independent version systems (MSX app, TVX Framework, TVX Plugin Library), backward/forward compatibility rules, and how to read "since version" annotations throughout this KB.
---

# Versioning Model

MSX has three independent version systems. They are completely unrelated; each has its own numbering and release cadence.

---

## The Three Version Systems

### 1. MSX App (`0.1.0` – `0.1.166`)

The MSX TV app itself. This is the version system used for **all "Since Version" annotations** throughout this knowledge base.

- Version range covered by this KB: **`0.1.0`** to **`0.1.166`**.
- This is the **only** version system that has a per-feature changelog: [Release Notes](../overview/release-notes.md).
- When a property, action, or behavior says "since `0.1.X`", that refers to this version number.
- The platform-specific MSX app version currently deployed to each platform (Smart TV OS, Android, etc.) can differ; see [Platform Support](../overview/platform-support.md).

### 2. TVX Framework (`0.1.35`)

A JavaScript framework for building MSX video/audio plugins, interaction plugins, and other extensions. Referenced in [Type Definitions](type-definitions.md) and the Plugin API.

- No changelog is available for this version system.
- `0.1.35` is the version mentioned in the source documents; it has no per-feature version table within this KB.

### 3. TVX Plugin Library (`0.0.79`)

The pre-built JavaScript library used when writing MSX plugins — six files across two variants (base and UX) and three forms each: `tvx-plugin.min.js` (Classical ES5 global form), `tvx-plugin-module.min.js` (Classic Module form, AMD/CommonJS) with `tvx-plugin-module.min.d.ts` (its TypeScript Module type definitions, also typing the `MSX*` JSON objects), and the UX-variant counterparts `tvx-plugin-ux.min.js` / `tvx-plugin-ux-module.min.js` / `tvx-plugin-ux-module.min.d.ts`.

- No changelog is available for this version system.
- `0.0.79` is the version mentioned in the source documents, shared by all six files.
- The UX variant (`tvx-plugin-ux*`) is the base library plus additional effects/UI classes (`TVXEffects`, `TVXAnimationController`, `TVXColorTools`, `TVXTransit`, `TVXParticles`, `TVXDrawing`, `TVXRenderer`, `TVXImageTools`, `TVXStyleTools`), and requires jQuery, unlike the dependency-free base files. These extra classes need expert-level knowledge and are not documented further in this KB. See [Glossary: TVX Plugin Library](glossary.md#tvx-plugin-library) for the complete file-by-file breakdown and documentation-depth notes.

---

## Backward Compatibility

The MSX engine is **fully backward-compatible**. A JSON page that works in `0.1.0` still works identically in `0.1.166`.

**Practical rule:** Targeting a broad audience (smart TVs with auto-update disabled)? Keep `action` strings and object structures to features available in `0.1.0`. Add newer features only when your minimum supported MSX version allows it.

---

## Forward Compatibility

Forward compatibility is **not guaranteed**. A JSON page that uses features introduced in `0.1.160` will not work correctly on a device running `0.1.80`.

The behavior on older clients depends on the specific feature:

| Severity | Behavior | Examples |
|---|---|---|
| Silent degradation | Feature is silently ignored; UI still renders but looks different or uses a fallback | Missing optional properties, unrecognized inline expressions, unknown colors |
| Warning state | The action string is unrecognized; a warning message is shown, but the action itself simply does not execute (an outright error is rare for a pure version mismatch — an unknown action string always surfaces as a warning) | Internal actions on pre-0.1.160 clients, multi-action `[a\|b]` on pre-0.1.58 clients |
| Not rendered / blank page | A layout or position value is out of range for the client's grid; a warning is shown, but the affected item (or, in the worst case, the whole page) is simply not displayed | `"compress": true` (`0.1.123`) items at 0-based column index ≥ 12 or row index ≥ 6 on pre-`0.1.123` clients — see [AGENTS.md → Forward Behavior](../AGENTS.md#forward-behavior-newer-features-on-older-msx-versions) for the worked example |

When using newer features, document the minimum supported MSX version for your app. Use [Release Notes](../overview/release-notes.md) to verify which version introduced each feature.

---

## How "Since Version" Annotations Work

Throughout this KB, version annotations appear in two forms:

**1. Exact since-version** — available for all Main Actions (from `0.1.0` to latest) and properties with an explicit changelog entry. Example: "since `0.1.58`".

**2. Blanket `0.1.160+`** — internal actions that are not also official Main Actions carry only this minimum version, without a finer per-feature version. See [Internal Actions](../experts-api/special/internal-actions.md).

> **In practice this rarely matters:** nearly all platforms run the latest MSX version at any given time (currently `0.1.165`–`0.1.166`). The main exception is older Samsung TV models: **2014–2021** models, which Samsung no longer allows to be updated, can be stuck on versions from `0.1.58` up to `0.1.163` depending on model year — see [Platform Support](../overview/platform-support.md). **2011–2013** Samsung models never ran MSX at all: they were already outside Samsung's app-submission-eligible model range by the time MSX first launched, so no MSX version was ever available for them. Internal actions were simply never put under individual per-feature version tracking; the blanket `0.1.160+` is a conservative safe-minimum, not proof a given action is unavailable earlier. If you hit an unrecognized-action warning, check the target device's actual MSX version first.

**Extended Properties carry a similar retroactive pattern.** Before `0.1.111`, [Extended Properties](../experts-api/special/extended-properties.md) (the `properties` bag on a Content Item) also had no per-feature version control. When version tracking was introduced at `0.1.111`, every Extended Property that already existed by then was labeled `0.1.111` in one retroactive pass, regardless of how much earlier its underlying mechanism actually worked — the one exception is `trigger:{TRIGGER_KEY}`, introduced together with Extended Properties themselves, which kept its true original `0.1.58`. A `0.1.111` label on an Extended Property therefore means "versioned starting here," not necessarily "introduced here."

**[Attached Data Examples](../experts-api/special/attached-data-examples.md) carry the same blanket-`0.1.160+` pattern as internal actions, for the same reason — but the mechanism itself is much older.** The attached-data mechanism (system/app/framework info attached to `execute:`/commit actions and sent to a server or plugin) exists since `0.1.30`, introduced together with the `execute:{URL}` action family — it was simply never put under its own per-field version tracking, so the source page carries only the same conservative `0.1.160+` safe-minimum label as Internal Actions, not a literal "introduced in `0.1.160`" claim. The practical consequence is on the receiving end, not the sending end: individual fields inside the attached data were added progressively over many versions as the corresponding feature shipped — e.g. a `visualExecution` field cannot appear in the data before that setting itself existed (`0.1.160`, see [In-App Settings Reference](settings-reference.md#4a-runtime-adjustable-and-launch-configurable-7-fields)). A server or plugin reading MSX's attached data should therefore not assume a given field is present just because it's documented, unless it can guarantee every client runs `0.1.160+` — check that a value actually exists before using it, rather than presuming its presence.

**Exceptions** — a handful of actions/features outside the Internal Actions' blanket `0.1.160+` nonetheless have their own documented since-version well below `0.1.160`. Most rows below are internal actions marked "Main Action: No"; the exception is Start Action (first row), which per [Hidden Feature Actions](actions-reference.md#hidden-feature-actions-replace-start--resolve) isn't an action string at all and so doesn't appear in Internal Actions — it's listed here purely for since-version completeness alongside its Replace/Resolve siblings:

| Exception | Since Version |
|---|---|
| Replace / Start / Resolve Action ("Hidden Feature Actions") | `0.1.144` / `0.1.0` / `0.1.107` — [Actions Reference](actions-reference.md#hidden-feature-actions-replace-start--resolve) |
| Broadcast/HbbTV family (`audio:broadcast:...`, `video:broadcast:...`, `system:hbbtv:...`) | `0.1.30` — [Actions Reference](actions-reference.md#broadcast-and-hbbtv-actions-a-third-case) |
| `execute:service:...` | `0.1.90` — [Actions Reference](actions-reference.md#server-actions) |
| `content:json:`/`menu:json:`/`panel:json:`/`playlist:json:`/`slideshow:json:` (base64-embedding) | same as base action, `0.1.0` |
| `system:tvx:launch`/`system:tvx:launch:{APP_ID}`, `system:netcast:menu` | `0.1.136` — [Android Player](../experts-api/special/android-player.md), [NetCast Menu](../experts-api/special/netcast-menu.md) |
| Runtime-settable form of a "Dynamic" [Extended Property](../experts-api/special/extended-properties.md) (`player:...`, `slider:...`, `resume:position`, `trigger:{KEY}:{ACTION}`) | `0.1.58`–`0.1.156` per property — [Actions Reference](actions-reference.md#actions-that-mirror-a-dynamic-extended-property) |
| `player:commit`/`player:commit:message:{MESSAGE}` / `interaction:commit:response:...` (built for video/audio plugins, reused by Tizen Player) | `0.1.74` / `0.1.128` — [Actions Reference](actions-reference.md#plugin-commit-actions-playercommit-interactioncommitresponse-and-tizen-player) |
| `request:interaction:{DATA_ID}@{URL}` combined with `content:`/`menu:`/`panel:`/`playlist:`/`slideshow:`/`execute:` | `0.1.82` — [Actions Reference](actions-reference.md#request-actions-requestinteraction-is-0182-requestplayer-remains-01160) |

None of these are necessarily the action's true original introduction — only the earliest point the KB can independently confirm. Full reasoning for the less directly-stated findings (plugin-commit actions, `request:interaction:`) is kept in `_meta/developer-confirmations.md` for internal reference.

**For the full per-version feature log**, read [Release Notes](../overview/release-notes.md).

---

## Version Baseline: 0.1.0

Unless a "since" annotation says otherwise, assume a feature is available from `0.1.0`. The following are the key version milestones where significant features were introduced:

| Version | Feature area |
|---|---|
| `0.1.0` | All basic API (Start, Menu, Content, basic Actions, Inline Expressions, Colors) |
| `0.1.30` | Server actions (`execute:`, `execute:code:`, `execute:user:`), `video:broadcast:current`, `system:hbbtv:launch:`, and their internal-only broadcast/HbbTV sibling variants — including the entire `audio:broadcast:...` family, which has no individually-documented Main Action of its own but shares the same `0.1.30` (see [Broadcast and HbbTV Actions: A Third Case](actions-reference.md#broadcast-and-hbbtv-actions-a-third-case)); the `start` action (sets a new start parameter — not to be confused with the "Start Action" hidden feature, available since `0.1.0`, see [Actions Reference](actions-reference.md#hidden-feature-actions-replace-start--resolve)) |
| `0.1.40` | Plugin actions (`video:plugin:`, `audio:plugin:`) |
| `0.1.58` | Extended actions: multi-action `[a|b]`, `reload`, `update:*`, `data`; `cache` (Content Root, Menu Root Object) and `important` (Content Root Object only) |
| `0.1.65` | `{SERVER}` placeholder in the Start Object's `parameter` (start file only) |
| `0.1.70` | Live inline expressions: `{now:…}`, `{from:…}`, `{to:…}`, `{duration:…}`, `{progress:…}`, `{countdown:…}`, `{overflow:…}`, `{creation:…}` |
| `0.1.74` | `~`-prefixed rounded duration text format (`~dhms`, etc.) |
| `0.1.82` | `extension` property (Content Root, Menu Root Object) |
| `0.1.88` | HTTPS support for the Start Object's `parameter` (`https://{SERVER}/msx/start.json`) |
| `0.1.90` | `accurate:` prefix (`Content-Type: application/json` instead of form-urlencoded, at the cost of a CORS preflight); `service:` prefix (parallel server actions, max 128 concurrent) |
| `0.1.91` | Number inline expressions: `{num:…}`; Color inline expressions: `{col:…}` |
| `0.1.92` | `preload` property (Content Root Object) |
| `0.1.97` | `{PREFIX}` placeholder in the Start Object's `parameter` (start file only); `redirect:` prefix for the Start Object's `parameter` to load the actual Start Object from another location (e.g. `redirect:{PREFIX}{SERVER}/msx/start.php`) |
| `0.1.102` | `wrap` property (Content Root, Content Page Object) |
| `0.1.107` | Resolve Action hidden feature — as `action`-strings (always prefixed: `video:resolve:`/`video:auto:resolve:`, `audio:resolve:`/`audio:auto:resolve:`, `image:resolve:`, `background:resolve:`) and as a bare `resolve:{URL}` value on the `image` property (content context only) and on the `background` property (works everywhere it appears — Content Item, Content Page, Content Root, Selection Object, Menu Item, Menu Root) — see [Actions Reference](actions-reference.md#hidden-feature-actions-replace-start--resolve) |
| `0.1.110` | Selection Object (`important`, `background`, `action`, `data`); `update:content:overlay:`, `update:panel:overlay:`; `offset` value range increased from `-1.0`–`1.0` (each component) to `-11.0`–`11.0` (`x`/`w`; panels `-7.0`–`7.0`) / `-5.0`–`5.0` (`y`/`h`) — an `offset` beyond `±1.0` on any component silently clamps/misbehaves on pre-`0.1.110` clients |
| `0.1.111` | Selection Object `headline`; `resume:key` / `resume:position` extended properties |
| `0.1.112` | `update:content:underlay:`, `update:panel:underlay:`; Page Action hidden feature (the `action` executed when a Content Page becomes active) |
| `0.1.117` | Start Object `welcome` property |
| `0.1.120` | Dictionary inline expressions: `{dic:…}`; `transparent` (boolean-only) on Content Root and Content Page Object; `restore` (Content Root, Menu Root Object); `options` (Content Item, Content Page, Content Root, Menu Item, and Menu Root Object); `execute` property on menu items (hidden feature — auto-*enters* that menu item's content data on load, rather than executing anything directly; the same `execute` property directly auto-executes a content item, since `0.1.0`) |
| `0.1.123` | Content Root `compress: true` (expands content grid from 12×6 to 16×8, panel grid from 8×6 to 10×8); `{dix}` wrapper; dictionary inline `{dix:…}` |
| `0.1.130` | `image` (menu item), `caption`; `options` now also accepts a Content Root Object as value (previously only Content Page Object); Page Action's underlay variant now also executes when the content becomes inactive while still visible (e.g. a corresponding menu regains focus), not just when it becomes active |
| `0.1.134` | `reference` property (Content Root, Menu Root Object) — loads the actual startup data from another location; `reload:menu` / `reload:content` also work with data set via `reference` |
| `0.1.136` | `"Preselect"` value of the `hover_effect` setting — **not** the same as the `preselect` property below (`0.1.160`); see the note after this table |
| `0.1.142` | `transparent` newly added to Menu Root and Menu Item Object; `transparent` on Content Root and Content Page Object extended from boolean-only to `number\|boolean` (the 3-state form documented on those pages); `ready` (content root, menu root objects); `lineColor` (menu item objects); `style` (menu root object) |
| `0.1.144` | `replace:menu/content/panel:…` hidden feature actions |
| `0.1.145` | `{chr:…}` inline expression; Resolve Action response can also contain `label`, `background`, `properties` to extend/override video/audio info |
| `0.1.146` | `imageBoundary` property (Content Item Object); `badge-*`/`stamp-*` `alignment` variants; Dynamic Extended Properties `info:overlay`, `info:size`, `progress:duration`, `progress:position` |
| `0.1.153` | `refocus`, `captionUnderlay`; Emergency Combination recovery mechanism (`911` / `↑↑↓↓←→←→` + `OK`) |
| `0.1.154` | Settings-validation panel (guards Layout, Scale Factor, Zoom Factor, Transform, Remote Control, and Input Type after a restart); `info:headline` Dynamic Extended Property mirror action |
| `0.1.155` | Template-only `decompress` property (author template `layout` in uncompressed grid coordinates while content is compressed) |
| `0.1.156` | `separation` property and the `separation-*` `alignment` variants (only take effect once `separation` is set); `"space"` items can share a grid position with other items (excluded from the selection engine); `info:round` Dynamic Extended Property |
| `0.1.160` | All internal actions; `preselect` property — **not** the same as the `"Preselect"` hover-effect setting value above (`0.1.136`); see the note below; `{#1}` / `{#2}` insertion expressions; basic format expressions for `{num:…}` |
| `0.1.161` | `outline:{ICON}` icon prefix variant (e.g. `"outline:movie"`, `"msx-red:outline:movie"`) |
| `0.1.164` | Start Object `launcher` property; `navigationOffset`; `navigationSpan` |

> **`preselect` (`0.1.160`) vs. `"Preselect"` (`0.1.136`) — two unrelated things that happen to share a name.** The `"Preselect"` value (since `0.1.136`) is one possible value of the `hover_effect` **app setting** — it changes how mouse/touch hover interacts with a Selection Object's `important` flag (see [Glossary: `important`](glossary.md#important)). The `preselect` **property** (since `0.1.160`) is a separate, unrelated JSON property. Do not confuse the two or assume they share a version.

This is not a complete changelog — only the milestones most relevant to building JSON pages are listed. For the complete, unabridged per-version changelog, see [Release Notes](../overview/release-notes.md).

---

## Checking the MSX App Version at Runtime

The current MSX app version can be determined in several ways:

- **Programmatically, via a server request:** the current MSX app version is available to plugins and server actions via the info-submission feature (`execute:info:application:{URL}`). See [Internal Actions](../experts-api/special/internal-actions.md) for the data format.
- **Programmatically, on every JSON request:** MSX automatically appends its own version as a `v` URL query parameter (e.g. `?v=0.1.166`, alongside a `t` timestamp parameter) to every JSON URL it requests — so a server can read the requesting MSX version directly without a dedicated service call. See [Requests](../main-api/common/requests.md) for the exact query-parameter format.
- **Visually, on startup:** the splash screen shows the version in the lower right corner, e.g. `Media Station X 0.1.166 (web)`.
- **Visually, in the app itself:** **Settings → About** shows it as well, e.g. `Application: Media Station X 0.1.166 (web)`.

---

## See Also

- [Release Notes](../overview/release-notes.md) — full per-version changelog for the MSX app
- [Platform Support](../overview/platform-support.md) — which MSX version is deployed on which platform
- [Actions Reference](actions-reference.md) — exact since-versions for all actions
- [Internal Actions](../experts-api/special/internal-actions.md) — blanket `0.1.160+` actions
