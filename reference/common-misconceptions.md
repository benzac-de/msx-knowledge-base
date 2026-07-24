---
title: Common Misconceptions & Things That Don't Exist
category: Reference
summary: Anti-hallucination reference — plausible-sounding MSX properties, values, and behaviors that do NOT exist, and the false assumptions to avoid, each corrected against the KB.
---

# Common Misconceptions & Things That Don't Exist

An **anti-hallucination** reference. Each entry pairs a plausible-but-wrong assumption with the verified reality. If you are about to output a property, value, action, version, or behavior that resembles a ❌ below, stop and use the ✅ instead. If something you need is not documented anywhere in this KB, say so — do not invent it.

This complements the build-time checklists in [AGENTS.md → Common Mistakes](../AGENTS.md#9-common-json-building-mistakes) and [JSON Building Guide → Common Mistakes](json-building-guide.md#common-mistakes); here the focus is *negative knowledge* — what does **not** exist.

---

## Colors

- ❌ `msx-orange`, `msx-purple`, `msx-cyan`, `msx-pink`, … do **not** exist.
  ✅ There are exactly **11** predefined colors: `msx-red`, `msx-green`, `msx-yellow`, `msx-blue`, `msx-white`, `msx-gray`, `msx-black`, `msx-white-soft`, `msx-gray-soft`, `msx-black-soft`, `msx-glass`. For anything else use standard CSS (`"#ff8800"`, `"orange"`, `"rgb(...)"`). See [Colors](../main-api/common/colors.md).

---

## Inline expressions

- ❌ `{txt:{msx-red}:{Hello}}` — the placeholder braces are **not** part of real values.
  ✅ In documentation the pattern is `{txt:{COLOR}:{TEXT}}` (braces mark placeholders); in actual JSON substitute plain values: `{txt:msx-red:Hello}`. See [Inline Expressions](../main-api/common/inline-expressions.md).
- ❌ Nested expressions like `{txt:{ico:home}}` are **not** supported.
  ✅ But the **prefix-combination** form is valid and is not nesting: `{txt:msx-blue:num:...}`, `{txt:msx-blue:dic:key}`.
- ❌ A misspelled expression does **not** throw an error.
  ✅ Unknown expressions are returned **without** their braces (`{unknwon}` → `unknwon`), so typos silently become plain text.
- ❌ `{context:...}` does **not** work in a template's own `label`/`image`/`action` or in standalone items.
  ✅ It resolves only inside a template object's `data` (and since `0.1.110` also `properties`/`live`/`selection`/`options`). See [Inline Expressions](../main-api/common/inline-expressions.md).
- ❌ Inline expressions do **not** work in every string-valued property just because it holds text.
  ✅ They only resolve in free-text, visual-output properties (`label`, `title`, `headline`, `text`, `tag`, `badge`, `stamp`, `caption`, `extension`, message-action text, etc.) — each individually documented as supporting them. Fixed-enum properties (`type`, `imageFiller`, `alignment`, `iconSize`, …) and structural/technical strings (URLs, IDs, action strings, colors, icon names) are parsed as literal values and never render inline expressions.
- ❌ Live Inline Expressions (`{now:...}`, `{from:...}`, `{duration:...}`, `{progress:...}`, `{countdown:...}`, etc.) do **not** resolve just by being placed in a regular item's `headline`/`title`/`text`/etc. — even though those properties otherwise support standard Inline Expressions.
  ✅ A Live Inline Expression only resolves in exactly two places: (1) inside a `live` object's own properties (e.g. `live.headline`, not the item's own `headline`), with the `live` object's `type` set to any valid value — `"airtime"` or `"lifetime"` are the most fitting choices for a plain, non-schedule/non-playback use like a clock, though `"schedule"`/`"playback"` also work since `{now:...}` doesn't depend on the specific type; or (2) directly in the `extension` property of a Content Root Object or Menu Root Object, the one place a Live Inline Expression works **without** any `live` object at all — but there only `{now:...}` makes sense, since `{creation:...}`/`{from:...}`/`{to:...}`/`{duration:...}`/`{progress:...}`/`{countdown:...}`/`{overflow:...}` all reference a specific live object's own timing state, which doesn't exist without one. Placed anywhere else, the expression is left unresolved/literal on screen. See [Live Inline Expressions](../experts-api/live/live-inline-expressions.md), [AGENTS.md → Inline Expressions](../AGENTS.md#4-inline-expressions--complete-overview).

---

## Menu objects

- ❌ `"data": "content:https://…"` on a menu item does **not** work — `content:` is action syntax, not a menu `data` value.
  ✅ A Menu Item's `data` is a raw URL string **or** an embedded Content Root Object. See [Menu Item Object](../main-api/menu/menu-item-object.md).
- ❌ Menu items do **not** have an `action` property.
  ✅ Associate content with a menu item only via `data`.

---

## Content & layout

- ❌ Grid columns/rows are **not** 1-based, and the `compress` out-of-range edge is **not** "column 13".
  ✅ Coordinates are **0-based**. Default grid is 12×6 (panels 8×6); with `compress: true` (since `0.1.123`) it is 16×8 (panels 10×8). On pre-`0.1.123` clients, items at 0-based **column index ≥ 12 or row index ≥ 6** are dropped as "out of range". See [Compress Property](../experts-api/hidden-features/compress-property.md).
- ❌ `"type": "pages"` and the `pages` array are **not** the same thing.
  ✅ `type` (`"pages"`/`"list"`) sets navigation direction; `pages` is an array of Content Page Objects. You can have `"type": "list"` with a `pages` array, or `"type": "pages"` with `template`+`items` and no `pages` at all.
- ❌ `template`+`items` and `pages` do **not** combine.
  ✅ They are mutually exclusive; `pages` is ignored when `template`+`items` are present. And `items` at the Content Root **without** a `template` is silently ignored.
- ❌ In a template's `layout`, the `x`/`y` (col/row) are **not** used.
  ✅ Only `w`/`h` matter in a template; MSX auto-positions each item. Non-templated items (in `pages[].items`) **require** a full `layout`.
- ❌ A page of only `"space"` items does **not** render.
  ✅ A page needs at least one selectable item (the exception is an `overlay`/`underlay` page, which may contain only `"space"` items). See [Content Page Object](../main-api/content/content-page-object.md).
- ❌ `pages[]`/`items[]` do **not** have unlimited capacity, and exceeding the limit does **not** break the page or throw a hard error.
  ✅ A Content Root Object's `pages[]` caps at **56** pages for `"pages"` type or **1024** pages for `"list"` type (`header`/`footer`/`overlay`/`underlay` don't count toward this). Each Content Page Object's `items[]` caps at **256** items (every `type` counts). Exceeding either ceiling only shows the user a warning message — everything beyond the limit is simply ignored, the rest of the content still renders normally. Generous ceilings — for UX reasons, stay well below both in practice. See [AGENTS.md](../AGENTS.md#2-json-hierarchy-start--menu--content--item).
- ❌ `wrap` does **not** control text wrapping.
  ✅ On a Content Root/Page, `wrap: true` (since `0.1.102`) prevents the menu from being entered via the left/right navigation keys — it is about menu navigation, not line breaks (for multi-line text use a `text` array or `{br}`). Only relevant for content loaded via a menu — no effect in panels, on overlay/underlay pages, or (for `"pages"`-type content) on any page other than the first/last. A Content Page Object's own `wrap` overrides the Content Root Object's value while that page is active. Useful, for example, on a page showing a virtual keyboard or similar on-screen control, so left/right at the edge stays inside the content instead of jumping into the menu. See [Content Root Object](../main-api/content/content-root-object.md), [Content Page Object](../main-api/content/content-page-object.md).
- ❌ A templated item's `layout` height (`h`) is **not** entirely image/focus-frame space for every `type`.
  ✅ For `"separate"`, the title (and `titleFooter`) is detached into its own strip below the image/icon/focus frame, and that strip takes exactly 1 row out of `h` — a template `"layout": "0,0,2,3"` reserves 2×3 in total, but the image/focus frame itself is only 2×2. Every other type (`"default"`, `"teaser"`, `"button"`, `"control"`) overlays title/text directly on the image, so the entire `h` belongs to one unified image+focus frame. See [Best Practices & Good to Know → Good to Know](best-practices.md#good-to-know-rendering-details-worth-knowing).
- ❌ `image` on a `"control"` or `"button"` item does **not** get the full item as its boundary like it does elsewhere.
  ✅ For those two types, the `image` boundary is set the same way `icon` is placed — centered for `"button"`, left-aligned for `"control"` — a smaller area than the whole item. `imageFiller` still applies normally inside that smaller boundary; it is not bypassed. For `"button"` items, `iconSize` resizes this `image` boundary too, not just `icon` (`iconSize` has no effect on `"control"` items). See [Best Practices & Good to Know → Good to Know](best-practices.md#good-to-know-rendering-details-worth-knowing).
- ❌ Setting both `image` and `icon` on the same item does **not** mean one silently overrides the other.
  ✅ They combine: `icon` renders as an overlay layered on top of `image`.
- ❌ `color` does **not** give a `"button"` or `"control"` item a visible background color.
  ✅ Both types always render with a transparent background, so `color` is ignored on both — there is no opaque background left to color. See [Content Item Object](../main-api/content/content-item-object.md).

---

## Allowed values (enum traps)

These properties accept only a fixed set of values. Common wrong guesses — often CSS habits or generic names — do **not** exist.

- ❌ `imageFiller` / `imageScreenFiller`: `"contain"`, `"stretch"`, `"fill"`, `"scale-down"`, `"none"` do **not** exist.
  ✅ Valid: `"default"` (stretch to fill), `"fit"`, `"cover"`, `"smart"`, plus the `"width"`/`"width-top"`/`"width-center"`/`"width-bottom"` and `"height"`/`"height-left"`/`"height-center"`/`"height-right"` variants. See [Content Item Object](../main-api/content/content-item-object.md).
- ❌ `iconSize`: `"xl"`, `"xlarge"`, `"big"`, `"tiny"` do **not** exist.
  ✅ Valid: `"small"`, `"medium"` (default), `"large"`, `"extra-large"`.
- ❌ A Content Item `type` is **not** `"text"`, `"image"`, `"video"`, `"card"`, `"list"`, `"grid"`, or `"separator"`.
  ✅ Valid: `"default"`, `"teaser"`, `"button"`, `"separate"`, `"space"`, `"control"`. For a non-selectable spacer use `"space"` (there is no `"separator"` on a content item).
- ❌ A Menu Item `type` is **not** `"button"`, `"control"`, `"space"`, or `"divider"`.
  ✅ Valid: `"default"`, `"separator"`, `"settings"` — a **different** enum from content items. See [Menu Item Object](../main-api/menu/menu-item-object.md).
- ❌ Menu `style` is **not** `"dark"`, `"light"`, `"transparent"`, or `"hidden"`.
  ✅ Valid: `"default"`, `"flat"`, `"flat-separator"`, `"overlay"`, `"overlay-separator"`.
- ❌ A Live Object `type` is **not** `"interval"`, `"timer"`, `"poll"`, `"countdown"`, or `"clock"`.
  ✅ Valid: `"schedule"`, `"lifetime"`, `"airtime"`, `"playback"`, `"setup"`. Its states are `"init"` / `"coming"` / `"running"` / `"over"` (not `"started"` / `"ended"` / `"finished"`). See [Live Object](../experts-api/live/live-object.md).
- ❌ A Live Object has no `url` property, and MSX does **not** automatically re-fetch or poll anything for it.
  ✅ It is a purely client-side, time-based state machine (`from`/`to`, `duration`/`delay`, or playback state), evaluated every second while the item is visible. To actually refresh data from a server, chain a server action from a state's `action` (e.g. an `over.action` calling `execute:{URL}`) whose response supplies a fresh live object. See [Live Object](../experts-api/live/live-object.md).
- ❌ `alignment` is **not** `"start"`, `"end"`, or `"middle"`.
  ✅ Base values: `"left"`, `"center"`, `"right"`, `"justify"` (plus `title-*` / `text-*` since `0.1.97`, `badge-*` / `stamp-*` since `0.1.146`, and `separation-*` since `0.1.156` variants, combinable with `|`). See [Content Item Object](../main-api/content/content-item-object.md).
- ❌ The `alignment` value `separation-*` and the standalone `separation` property (both since `0.1.156`) are **not** two unrelated features that merely share a version number.
  ✅ They are directly connected: the `separation-*` alignment variants only have an effect once the `separation` property is also set on the item — without a `separation` value, they do nothing. See [Best Practices & Good to Know → Image sizing, overlay, and boundaries within an item](best-practices.md#image-sizing-overlay-and-boundaries-within-an-item-imagewidth-imageheight-imageoverlay-imageboundary) for how `separation` (paired with a directional `imageFiller`) is used in practice.
- ❌ `break` is **not** `"newline"`, `"br"`, or `"column"`.
  ✅ Valid: `"line"`, `"page"`, `"context"`, `"context:{CONTEXT_ID}"`.
- ❌ `preload` is **not** `"all"`, `"true"`, or a boolean.
  ✅ Valid: `"none"` (default), `"next"`, `"prev"`, `"full"` (Content Root Object).
- ❌ `transparent` is **not** a string, a percentage, or `"semi"`.
  ✅ It is `number`|`boolean`: `0`/`false` (default — a fixed, translucent 80%-opacity dark overlay over the background, not a solid block), `1`/`true` (overlay removed, background shown at full brightness), or `2` (overlay removed only while the background is a still image; reverts to the same `0` overlay the moment video/audio becomes active — unless the item's static extended property `control:transparent` is set to `true`, in which case the overlay stays removed during playback too). A running background video always stays at least visible (just dimmed by the overlay when not removed), never fully hidden. Valid on Content Root/Page and Menu Root/Item. See [Best Practices & Good to Know → Best Practices](best-practices.md#best-practices-learned-from-building-real-pages) for when to use which.

---

## Date / time / duration format tokens

Used in `{now:…}`, `{from:…}`, `{to:…}`, `{creation:…}`, `{duration:…}` and `{num:…:date/time:…}`.

- ❌ MSX does **not** use Java/moment-style tokens like `YYYY`, `HH`, `SS`, or `MM`/`DD` for a zero-padded month/day.
  ✅ Tokens are **lowercase and case-sensitive**: `yyyy` (2019) / `yy` (19) year; `mm` (01) month **number**; `dd` (24) day **number**; time `hh`/`mm`/`ss` (padded) or `h`/`m`/`s`, with a `/ampm` suffix for 12-hour (e.g. `h:mm/ampm` → `2:16 PM`). See [Live Inline Expressions](../experts-api/live/live-inline-expressions.md).
- ❌ `MM` and `DD` are **not** padded month/day, and `mm` is not always minutes.
  ✅ `MM` = month **name** (`January`; short `M` = `Jan`), `DD` = **weekday** name (`Thursday`; short `D` = `Thu`), `ddd` = ordinal day (`24th`). `mm` means the **month** in a date context and **minutes** in a time context.
- ❌ Duration text is **not** free-form.
  ✅ It uses fixed families like `dhms` (`1 hr 34 min`), `DHMS`, `hms`, `hm`, `ms`, `d`/`h`/`m`/`s`, and the `~`-prefixed rounded forms (`~dhms`, since `0.1.74`).

---

## Dictionary file

- ❌ A dictionary is **not** a flat top-level `{ "key": "value" }` map.
  ✅ It is `{ "name": "…", "version": "…", "properties": { "key": "value" } }` (optional `language`). All lookup entries live inside `properties`; a value is a string, or (since `0.1.160`) an array of strings. See [Dictionary Structure](../experts-api/special/dictionary-structure.md).
- ❌ A startup dictionary does **not** apply if it is set on data loaded later (e.g. deep in a navigation).
  ✅ A `dictionary` property only takes effect if that menu/content is loaded **at startup**; otherwise set it at runtime with the internal `dictionary:{URL}` action (since `0.1.160`).

---

## Right property, wrong object

Many properties are real but only valid on a *specific* object type. Placing them on a Content Item is a common trap.

- ❌ `important` and `transparent` are **not** Content Item properties.
  ✅ `important` belongs on the Content Root / Content Page / Selection Object; `transparent` on the Content Root / Content Page / Menu Root / Menu Item. **`important` is not one concept reused three times** — each object gives it a different meaning (headline enforcement on Content Root since `0.1.58`; overlay/underlay visibility on Content Page since `0.1.110`; mouse/touch focus-only execution on Selection Object since `0.1.110`). See [Glossary: `important`](glossary.md#important) for all three.
- ❌ `background` on a Content Item is **not** the same general-purpose page background as `background` on Content Root/Content Page/Menu Root/Menu Item Object.
  ✅ On those four objects, `background` is unconditional (used whenever no lower-level background is set). On a **Content Item**, `background` only applies while that item's own action uses the `audio:` prefix (`audio:{URL}`, `audio:plugin:{URL}`, `audio:resolve:...`, etc. — the whole `audio:` family, not only the plain `audio:{URL}` form) — it has no effect for any `video:`-prefixed action (`video:{URL}`, `video:plugin:{URL}`, etc.) or other action type. For a page-level background behind a video/plugin item, set `background` on the Content Root or Content Page Object instead, not on the item. See [Content Item Object](../main-api/content/content-item-object.md).
- ❌ `lineColor` is **not** a Content Item property.
  ✅ It is a Menu Item property (also updatable via `update:menu:{ID}`).
- ❌ `logo` / `logoSize` are **not** Content Root properties.
  ✅ They exist only on the Menu Root Object.
- ❌ `style` is **not** a Content Root property.
  ✅ It exists only on the Menu Root Object (since `0.1.142`) — valid values `"default"`, `"flat"`, `"flat-separator"`, `"overlay"`, `"overlay-separator"`. There is no equivalent property for styling a Content Root/Page. See [Menu Root Object](../main-api/menu/menu-root-object.md).
- ❌ Page/root structure (`header`, `footer`, `overlay`, `underlay`, `caption`, `captionUnderlay`, `navigationSpan`, `flag`, `preload`, `dictionary`, `reference`, `restore`, `ready`) is **not** set on individual items.
  ✅ These belong on the Content Root (some also on the Content Page) or Menu Root object.
- ❌ A Selection Object has no `text`, `image`, `title`, `delay`, or `preview` property.
  ✅ It has **exactly five**: `action`, `data`, `background`, `headline`, `important` (all since `0.1.110`; `headline` since `0.1.111`) — nothing else. See [Selection Object](../experts-api/selection/selection-object.md).
- ❌ There is no built-in "on focus leaves" / "unselect" action — moving focus away does **not** automatically undo a selection `action`'s side effects (e.g. a still-playing audio, a shown backdrop).
  ✅ `background` and `headline` are the exception: MSX resets those two automatically once focus moves on, no cleanup needed. For anything else with a lasting effect, there's more than one way to reset it, depending on the use case: a Page Action, typically on the underlay page — whose page action (since `0.1.130`) also fires when the content becomes inactive while still visible (e.g. the corresponding menu regains focus), not just when it becomes active (see [Backdrop Plugin](../experts-api/plugins/backdrop-plugin.md) for a worked example: `quiet` + reloading the backdrop with a neutral value); or, on pages built around an interaction plugin, a content observer (`addContentObserver`, see [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md)) that reports focus changes directly to the plugin — with a delay of roughly 1 second, so it fits a plugin-driven page better than an instant reaction. See [Page Action](../experts-api/hidden-features/page-action.md).

---

## Hidden features

- ❌ The `execute` property does **not** run on selection/activation, and on a **menu item** it does **not** execute that menu item (or its content) directly.
  ✅ On a **content item** (since `0.1.0`, requires `focus: true`), `execute` auto-executes that item on load. On a **menu item** (since `0.1.120`, requires `focus: true`), `execute` instead auto-*enters* that menu item's content data on load — it does not execute anything by itself. Getting a content item to auto-execute when its menu is freshly loaded therefore needs `focus`+`execute` set to `true` at **both** levels: Menu Root loads → the focused Menu Item (`focus`+`execute: true`) auto-enters its content → the focused Content Item there (`focus`+`execute: true`) auto-executes. Before `0.1.120`, a content item loaded via a menu could never be auto-executed this way — only a content root's own items, loaded directly/standalone, supported `execute` (since `0.1.0`). See [Execute Property](../experts-api/hidden-features/execute-property.md).

---

## Extended properties (the `properties` bag)

- ❌ `resume`, `resume:key`, `resume:position` are **not** top-level Content Item properties.
  ✅ They are extended properties inside the item's `properties` object: `"properties": { "resume:key": "url", "resume:position": "102" }` (since `0.1.111`). Many advanced knobs (player buttons, `tizen:*`, `html5x:*`, `image:*`, `label:*`) live here too. See [Extended Properties](../experts-api/special/extended-properties.md).
- ❌ Not every `key:value` inside a `properties` object is an MSX-defined extended property — keys like `live:channel` / `live:program` (seen inside a `properties` block in some live-stream examples) are **not** MSX properties.
  ✅ A `properties` bag mixes three things: real MSX **extended properties** (the fixed set in [Extended Properties](../experts-api/special/extended-properties.md) — `button:*`, `control:*`, `progress:*`, `trigger:*`, `resume:*`, `image:*`, `label:*`, `info:*`, `video:*`); **plugin properties** documented on a plugin's own page (e.g. `tizen:*`, `html5x:*`); and **free-form custom keys** that MSX only carries — they are read by a **plugin or a server-side service**, not by MSX itself. (`live:channel`/`live:program` are read by the `live.html` interaction plugin — loaded via `trigger:load: interaction:load:…` — which pulls the item's video info and looks them up; a server reached via `execute:…` could read the same custom keys the same way.) MSX does not define or act on custom keys. Rule: if a `properties` key is not documented in the Extended Properties reference or a plugin page, treat it as plugin/server data — not MSX API — and do not invent behavior for it.

---

## Custom & unknown properties

MSX silently **ignores** any property it does not recognize, at every object level — so adding custom properties never makes a page invalid, and their presence is not proof that MSX does anything with them. This is not limited to the `properties` bag above; it applies to top-level object properties too.

- ❌ An unknown or custom property (e.g. a `note` field on a Start Object) does **not** make the JSON invalid, nor does it mean MSX acts on it.
  ✅ It is valid and simply ignored by MSX. Custom properties are allowed on any object; MSX evaluates only the properties it defines and passes over the rest. A custom key is consumed (if at all) by something **other than MSX** — a human reader, a plugin, a server, or an external tool. When asked *"is this valid MSX JSON?"*, answer **yes**, name the object type (the example below is a **Start Object**), and point out which keys are non-official-but-allowed instead of rejecting them. Speculating about a custom key's likely purpose is fine — just state that MSX itself does not act on it.
  ```json
  {
      "name": "Euronews MSX",
      "version": "2.0.0",
      "parameter": "menu:{PREFIX}{SERVER}/msx/service.php?id={ID}",
      "note": "For this service, Media Station X 0.1.146 or higher is needed.",
      "launcher": { "image": "http://launcher.msx.benzac.de/assets/en.png", "color": "#005586" }
  }
  ```
  Here `name`, `version`, `parameter`, and `launcher` are all official [Start Object](../main-api/start/start-object.md) properties (`launcher` since `0.1.164` — it defines a launcher tile in the settings / setup-completion panel). Only `note` is custom: not an official property, but a widely-used convention in real showcases to document the minimum MSX version. MSX ignores `note` — its only consumer is a human reader (or an external tool).
- ❌ `{context:{PROPERTY}}` is **not** limited to official MSX property names.
  ✅ It references **any** property of the templated item by name — including a custom one you invented — as long as the name does not collide with an official property. Example (`xp/hidden_feature_19.json`, Advanced Templated Items): each item carries a custom `"context": "Context 1"` field, surfaced via the template's `"selection": { "headline": "{context:context}" }` (the outer `context:` is the expression prefix, the inner `context` is the custom field name — two unrelated things that happen to share the word). See [Advanced Templated Items](../experts-api/hidden-features/advanced-templated-items.md).

---

## Actions

- ❌ `link:{URL}` does **not** open an external browser.
  ✅ `link:{URL}` opens **in-app**; `link:window:{URL}` opens externally. See [Actions](../main-api/common/actions.md).
- ❌ There is **no** bare `resolve:` action, and Resolve is **not** a blanket `0.1.160+` internal action.
  ✅ Resolve is always prefixed (`video:resolve:…`, `audio:resolve:…`, `image:resolve:…`, `background:resolve:…`) and is a documented Hidden Feature since **`0.1.107`** — even though it also appears in the Internal Actions list. Its `user:` and `request:interaction:` sub-variants share that same `0.1.107` minimum, not `0.1.160+`. See [Resolve Action](../experts-api/hidden-features/resolve-action.md), [Actions Reference](actions-reference.md#hidden-feature-actions-replace-start--resolve).
- ❌ Broadcast/HbbTV actions are **not** `0.1.160+`.
  ✅ Every `audio:broadcast:…`, `video:broadcast:…`, and `system:hbbtv:…` variant shares **`0.1.30`**, regardless of whether it is individually listed as a Main Action. See [Actions Reference](actions-reference.md#broadcast-and-hbbtv-actions-a-third-case).
- ❌ `execute:service:{URL}` is **not** blanket `0.1.160+`.
  ✅ It shares the **`0.1.90`** since-version of the `service:`/`accurate:` prefix feature area — not the `0.1.160+` floor that otherwise applies to internal-only actions. See [Actions Reference](actions-reference.md#internal-only-actions--overview-require-01160).
- ❌ `content:json:{BASE64}`, `menu:json:{BASE64}`, `panel:json:{BASE64}`, `playlist:json:{BASE64}`, and `slideshow:json:{BASE64}` are **not** blanket `0.1.160+`, despite appearing in [Internal Actions](../experts-api/special/internal-actions.md) without a Main Action flag.
  ✅ Each shares the **`0.1.0`** since-version of its respective base action (`content:{URL}`, `menu:{URL}`, `panel:{URL}`, `playlist:{URL}`/`playlist:data`, `slideshow:{URL}`/`slideshow:data`). Their sibling `:auto:` and `:request:player:...` variants in the same internal-source rows genuinely require `0.1.160+`, but the `:request:interaction:...` variants are `0.1.82`, not `0.1.160+` either — see [Actions Reference](actions-reference.md#internal-only-actions--overview-require-01160).
- ❌ `request:interaction:{DATA_ID}@{URL}` combined with `content:`, `menu:`, `panel:`, `playlist:`, `slideshow:`, or `execute:` is **not** blanket `0.1.160+`.
  ✅ It shares the **`0.1.82`** since-version of the interaction-plugin request mechanism itself, developer-confirmed via [Paging Plugin](../experts-api/plugins/paging-plugin.md). The sibling `request:player:{DATA_ID}` variant remains `0.1.160+`, as does any combination floored higher by a prefix like `playlist:auto:...` or by Resolve. See [Actions Reference](actions-reference.md#request-actions-requestinteraction-is-0182-requestplayer-remains-01160).
- ❌ The `start` Main Action and the "Start Action" are **not** the same thing.
  ✅ `start` (Main Action, `0.1.30`) sets a *new* start parameter at runtime; the **Start Action** (hidden feature, `0.1.0`) is an `action` on the root object that auto-runs once at startup. See [Start Action](../experts-api/hidden-features/start-action.md).
- ❌ A bare `action1|action2` is **not** a multi-action, and a literal `|` inside an argument is **not** written as `|`.
  ✅ Wrap multi-actions in `[…]` (since `0.1.58`): `[action1|action2]`. For a literal pipe inside an argument use the `{pipe}` keyword.
- ❌ `invalidate:content` / `invalidate:menu` / `invalidate:panel` do **not** themselves reload, refresh, or clear any data.
  ✅ `invalidate:…` is **purely visual** — it makes the current content/menu/panel (or a specific item) appear semi-transparent to signal to the user that a refresh is imminent. The actual refresh must come from a paired `reload:`, `replace:`, or `execute:` action. Do not confuse it with `release:content` / `release:menu` / `release:panel`, which **does** clear the cache and references, forcing a real reload on next fetch (but disables plain `reload:` afterwards — `replace:` still works). See [Glossary: Invalidate](glossary.md#invalidate), [Glossary: Release](glossary.md#release), [Actions Reference](actions-reference.md#internal-only-actions--overview-require-01160).
- ❌ A blanket `0.1.160+` on an internal action is **not** proof it is unavailable earlier.
  ✅ It is a conservative floor; internal actions were simply never put under per-feature version control (some work earlier, possibly with different syntax). See [Versioning](versioning.md).
- ❌ `update:content:{ID}`/`update:menu:{ID}` (and their `overlay:`/`underlay:`/panel variants) do **not** let you set a property that was never set (or was `null`) on the target item's own origin data.
  ✅ Per [Actions](../main-api/common/actions.md), an update only applies to a property "if the properties have been set (to a non-`null` value) in the origin data" — otherwise that part of the update is silently ignored. Exceptions that always work regardless of origin data: `background`/`lineColor`/`options` for `update:menu:{ID}`; `color`-type properties (`color`, `tagColor`, `badgeColor`, `stampColor`, `progressColor`, `progressBackColor`, `wrapperColor`) plus `live`/`selection`/`options` for `update:content:{ID}` — the same `color`-type exemption as the `live` object's own update mechanism. Same underlying reason in both cases: MSX only renders a dedicated element for a property when the item's own value is present initially. See [Actions Reference](actions-reference.md), [AGENTS.md → Common JSON-Building Mistakes](../AGENTS.md#9-common-json-building-mistakes).
- ❌ `menu:data:guide:menu`, `content:data:guide:videos`, `panel:data:guide:panel`, `menu:data:start:welcome`, and similar `{prefix}:data:{ID}` strings are **not** a documented action pattern you can reuse in your own JSON.
  ✅ This is an undocumented, internal-only addressing scheme that resolves to JSON bundled inside the app itself, not fetched over HTTP. It's used almost exclusively by the app's built-in Guide (the in-app help menu, cross-linking its own bundled pages) and by the Welcome Pages entry. These loads do show up in the app's own Log (e.g. "Request data for entry '{TYPE}' with ID '{ID}'"), but that's a debugging trace, not a documented feature — do not use it in your own JSON. See [In-App Settings Reference](settings-reference.md#2-settings-screen-map).

---

## Server actions (`execute`)

- ❌ MSX does **not** send `Content-Type: application/json` by default, even though the POST body is JSON.
  ✅ By default it sends `application/x-www-form-urlencoded` (to avoid a CORS preflight) — parse the body as JSON regardless. Use `execute:accurate:{URL}` (since `0.1.90`) to send `application/json`. See [Responses](../main-api/common/responses.md).
- ❌ There is no unlimited retry/wait — a request does **not** hang forever.
  ✅ Every server request times out after **30 seconds**; the error is logged **and** shown to the user (suppress with the internal `execute:silent:` prefix).

---

## Server responses

- ❌ A server does **not** reply with a bare `{ "action": "..." }`.
  ✅ The action must be wrapped in a `response` object: `{ "response": { "status": 200, "text": "OK", "data": { "action": "success:Saved" } } }`. MSX runs `response.data.action` next (optional payload in `response.data.data`). See [Responses](../main-api/common/responses.md).
- ❌ There is no `response.error` or `response.code` field.
  ✅ Signal an error with a non-`200` `response.status` plus a human-readable `message` — MSX shows the `message` to the user (the recommended cross-platform way).
- ❌ A Resolve response does **not** return the URL as `response.url` or `response.data.link`.
  ✅ It returns `response.data.url` (or `response.data.error` on failure); since `0.1.145` it may also carry `label`, `background`, `properties`. See [Resolve Action](../experts-api/hidden-features/resolve-action.md).

---

## Start object, placeholders & URLs

- ❌ The Start Object's `version` is **not** the MSX app version.
  ✅ It is the developer's own start-parameter version (e.g. `"1.0.0"`). See [Start Object](../main-api/start/start-object.md).
- ❌ `{SERVER}` and `{PREFIX}` do **not** work in arbitrary JSON files.
  ✅ They only resolve in the Start Object's start `parameter` (`{SERVER}` since `0.1.65`, `{PREFIX}` since `0.1.97`); every other file needs full, absolute URLs.
- ❌ Protocol-relative URLs (`//host/…`) do **not** work in built JSON.
  ✅ On a TV the app runs in a local context where they fail to resolve — always write an explicit scheme. Prefer `http://` (MSX auto-upgrades to `https://` in a secure context via the `secure` parameter); a hardcoded `https://` can break on http-only servers. See [URL Parameters](../experts-api/special/url-parameters.md).

---

## Versioning & compatibility

- ❌ Newer-feature JSON is **not** automatically safe on older MSX versions.
  ✅ The engine is backward-compatible (old JSON always works on new versions), but **forward** compatibility is not guaranteed — a newer feature can be silently ignored *or* leave a page blank on an older client. Always state a feature's minimum version. See [Versioning](versioning.md).
- ❌ The **TVX Framework** (`0.1.35`) and **TVX Plugin Library** (`0.0.79`) do **not** have a changelog or version-dependent behavior.
  ✅ Only the **MSX app version** carries documented per-feature "since" versions. Do not invent version history for the two TVX systems.

---

## Plugins

- ❌ A plugin (interaction **or** video/audio) does **not** receive key or mouse input, so you cannot build interactive controls inside the plugin page.
  ✅ Plugins are display/logic only: an interaction plugin *serves* MSX content (rendered and input-handled by MSX) via `handleRequest`, and commits are sent back via `interaction:commit`/`player:commit:message`. See [Interaction Plugin](../experts-api/plugins/interaction-plugin.md), [Video/Audio Plugin](../experts-api/plugins/video-audio-plugin.md).

---

## Licensing, authorship & open source

- ❌ Media Station X is **not** open source.
  ✅ It is **freeware** — free to use, but the application's own code is closed-source and may not be copied, modified, distributed, hosted, reverse-engineered, or decompiled. See [License](../overview/license.md).
- ❌ Ready-made plugins are **not** closed-source just because MSX itself is.
  ✅ All video/audio/interaction plugins documented in this KB (e.g. `youtube.html`, `html5x.html`, `template.html`, …) are open source — their HTML/JS/CSS can be freely viewed in the browser. Showcases may use additional custom plugins internally that are not documented in this KB.
- ❌ MSX Showcases are **not** generally open source.
  ✅ All Showcases were developed exclusively by the MSX developer (Benjamin Zachey); only **RBTV MSX** is open source: [https://github.com/benzac-de/rbtv-msx](https://github.com/benzac-de/rbtv-msx). See [Showcases](../overview/showcases.md).

---

## In-app settings

- ❌ MSX does **not** have a native, global setting for language selection, subtitles, parental controls, or buffer/cache size.
  ✅ None of these appear anywhere in the `settings:` internal actions or the `MSXAttachedApplicationSettings`/`MSXAttachedFrameworkSettings` structures. Subtitle/language preferences are plugin- or platform-specific extended properties instead (e.g. `html5x:subtitle:{lang}:{Label}`, `tizen:subtitle:style:*`) — not a global app setting. See [In-App Settings Reference](settings-reference.md).
- ❌ Most Framework Settings (`center`, `background`, `leave`, `exit`, `back`, `volume`, `busy`, `speed`, `playback`, `fullscreen`, `suspend`, `secure`, `caption`, `pointer`) are **not** changeable from the Settings screen.
  ✅ Only 7 of the 21 Framework Settings (`animate`, `transform`, `input`, `remote`, `layout`, `scale`, `zoom`) have a live `settings:{PANEL}` panel; the other 14 are launch-time only, set via the same-named [URL Parameter](../experts-api/special/url-parameters.md) or the app's own start-parameter setup. See [In-App Settings Reference → Framework Settings](settings-reference.md#4-framework-settings--the-two-tiers).
- ❌ The Settings screen's `layout` panel does **not** offer all 11 resolution factors MSX supports (180p–4320p, all settable via the `layout` [URL Parameter](../experts-api/special/url-parameters.md)/start parameter or the `settings:layout:{VALUE}` action), and it does **not** offer `auto`/`detect`.
  ✅ The **Settings-screen panel** is narrower than the full set of valid `layout` values: it offers only **5** of the 11 as selectable options — `360p`, `540p`, `720p`, `1080p`, `2160p`. The other 6 (`180p`, `240p`, `480p`, `576p`, `648p`, `4320p`) are equally valid `layout` values, just not shown in the panel — showing all 11 would turn the one-screen resolution picker into a multi-page list, and in practice the omitted factors rarely matter. They're still settable at runtime, but the two mechanisms don't accept the same syntax: the URL Parameter/start parameter accepts the numeric factor, the named `{N}p` form (`layout=0.25` or `layout=180p`), and `auto`/`detect`, while the `settings:layout:{VALUE}` internal action accepts **only the numeric factor** (`settings:layout:0.25`) — neither `settings:layout:180p` nor `auto`/`detect` are valid there. `auto`/`detect` specifically are **not** offered in the Settings screen at all, by either mechanism — settable only via the URL/start parameter. See [In-App Settings Reference → Runtime-adjustable AND launch-configurable](settings-reference.md#4a-runtime-adjustable-and-launch-configurable-7-fields).

---

## See Also

- [AGENTS.md](../AGENTS.md) — usage guide and mental model
- [JSON Building Guide](json-building-guide.md) — step-by-step, with a full Common Mistakes list
- [Glossary](glossary.md) · [Actions Reference](actions-reference.md) · [Versioning](versioning.md) · [In-App Settings Reference](settings-reference.md)
