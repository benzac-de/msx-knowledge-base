---
title: JSON Building Guide
category: Reference
summary: Step-by-step guide to building valid MSX JSON pages from a minimal skeleton to a full content page, with progressive examples referencing real example files.
---

# JSON Building Guide

This guide walks you through building a valid MSX JSON page from scratch. Every step adds one concept and can be tested in MSX immediately.

**Before you start:** Read [AGENTS.md](../AGENTS.md) for the mental model. MSX is a white-label TV app driven entirely by JSON served over HTTP. You supply the JSON; MSX renders it.

---

## Step 0 — Hosting and Setup

Before MSX can load your JSON, you need a server and a configured start parameter.

1. **Host your Start Object** at the fixed path `http://{SERVER}/msx/start.json` — MSX always looks for it there.
2. **Open the MSX app** → **Settings** → **Start Parameter** → **Setup**, enter your `{SERVER}` (hostname or IP address, e.g. `192.168.0.10:8080`), and confirm.

Once set up, your content loads automatically every time MSX starts. Since `0.1.88`, HTTPS is also supported (`https://{SERVER}/msx/start.json`) by enabling the security lock during setup.

> **CORS:** The Start Object and every other JSON endpoint it references (menu/content data URLs, `execute:`/`reference` endpoints, etc.) must return the HTTP header `Access-Control-Allow-Origin: *` (or the app's specific origin). Missing CORS headers are one of the most common reasons MSX fails to load content — the TV app will show a load error even though the URL works fine in a browser. This applies to URLs that are *fetched as data* — plain resource URLs like `image`/`background`/`logo` or `plugin:` URLs do not need CORS; see [Cookbook](cookbook.md#m3upls-vs-mrss-two-different-playlist-import-mechanisms) for the full rule of thumb.

**Source:** [Setup Start Parameter](../main-api/start/setup-start-parameter.md)

---

## Step 1 — Minimal Start Object

A valid MSX entry point is a **Start Object**. It must have exactly three mandatory properties.

```json
{
  "name": "My First App",
  "version": "1.0.0",
  "parameter": "menu:http://example.com/menu.json"
}
```

| Property | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes | Display name shown in the start parameter setup |
| `version` | string | Yes | Version of the start parameter (your own versioning, e.g. `"1.0.0"`) |
| `parameter` | string | Yes | Specifies which menu or content is loaded at startup. Must start with `menu:` or `content:` followed by the URL (e.g. `"menu:http://example.com/menu.json"` or `"content:http://example.com/content.json"`). |

The `parameter` value must begin with `menu:` or `content:` — the prefix tells MSX which JSON type to expect. The Start Object also supports `welcome` (since `0.1.117`) and `launcher` (since `0.1.164`) as additional top-level properties — see [Start Object](../main-api/start/start-object.md) for details.

**Source:** [Start Object](../main-api/start/start-object.md)

---

## Step 2 — Menu Root Object

If `parameter` starts with `menu:` (as in the example above), that URL must return a **Menu Root Object** — the container for the app's top-level menu items.

```json
{
  "headline": "My Menu",
  "menu": [
    {
      "label": "Videos",
      "data": "http://example.com/videos.json"
    }
  ]
}
```

Key menu properties:

| Property | Type | Description |
|---|---|---|
| `headline` | string | Visible menu title (supports Inline Expressions; ignored if `logo` is set) |
| `menu` | array | Array of Menu Item Objects (mandatory) |
| `menu[].label` | string | Displayed text in menu |
| `menu[].data` | string or object | URL of the Content Root Object **or** an embedded Content Root Object directly |
| `menu[].id` | string | Optional — assign only when you need to target this item with `update:menu:{ID}` or `focus:{ID}`. If two menu items share the same `id`, MSX removes the second one and throws a warning — so only add `id` when you actually need it. |

> **Auto-fetch:** When a menu item is focused, MSX automatically fetches the `data` URL after a short delay. The delay ensures that fast navigation through the menu does not trigger a load for every item passed — content is only fetched once the user pauses on an item. This makes the menu feel smooth even over slow connections.

> **Important:** `menu[].data` accepts either a raw URL string or a Content Root Object embedded directly as a JSON object. It does **not** accept the `content:{URL}` action string prefix — that prefix is only valid in `action` properties and in the Start Object's `parameter` property (e.g. `"parameter": "content:http://example.com/content.json"`). If `data` is left unset (or `null`), MSX shows a default content page with the headline *"Content Not Available"* — a graceful fallback, not an error. An invalid/malformed `data` value (e.g. a broken URL), by contrast, does produce an error.

**Source:** [Menu Root Object](../main-api/menu/menu-root-object.md), [Menu Item Object](../main-api/menu/menu-item-object.md)
**Example:** [guide/menu.json](../examples/guide/menu.json)

---

## Step 3 — Content Root Object (Pages Layout)

The Content Root Object is what a menu item's `menu[].data` points to (from Step 2) — or, without a menu at all, what `parameter` points to directly when it starts with `content:` (from Step 1). The simplest layout is `"pages"`:

```json
{
  "type": "pages",
  "pages": [
    {
      "items": [
        {
          "layout": "0,0,6,6",
          "label": "Video 1",
          "action": "video:http://example.com/video1.mp4"
        },
        {
          "layout": "6,0,6,6",
          "label": "Video 2",
          "action": "video:http://example.com/video2.mp4"
        }
      ]
    }
  ]
}
```

The layout grid is **12 columns × 6 rows** for all content types (panels use 8 × 6). The `"pages"` type means content is displayed in **horizontal** direction — pages appear side by side, navigation moves left/right. From `0.1.123`, add `"compress": true` to the Content Root to expand the grid to **16 × 8** (panels: 10 × 8). This applies to both `"pages"` and `"list"` type.

> **Panels always use `"list"` behavior — the `type` property is ignored.** A panel is a narrow overlay that covers the full height of the screen but only part of the width, centered horizontally, floating on top of the main content or playing video/audio. Because of this layout — tall and narrow, centered in the middle of the screen — horizontal page navigation makes no UX sense. MSX therefore always renders panel content as a stacked vertical list, regardless of what `type` you set in the Content Root Object.

**Source:** [Content Root Object](../main-api/content/content-root-object.md)
**Example:** [guide/pages.json](../examples/guide/pages.json)

---

## Step 4 — Content Root Object (List Layout)

For a vertical list, use `"type": "list"`:

```json
{
  "type": "list",
  "pages": [
    {
      "items": [
        { "layout": "0,0,12,1", "label": "Item 1", "action": "info:Hello" },
        { "layout": "0,1,12,1", "label": "Item 2", "action": "info:World" }
      ]
    }
  ]
}
```

The `"list"` type uses the **same 12 × 6 grid** as `"pages"`. The difference is navigation direction and page stacking: with `"list"`, pages are arranged in **vertical** direction and are stacked — vertical gaps at the bottom of a page are removed so the next page's items follow immediately without empty space. Navigation moves up/down. With `"pages"`, each page occupies a full horizontal screen and pages appear side by side; bottom gaps stay, since pages scroll horizontally.

> **Structural limits (generous, but real):** a Content Root Object's `pages[]` can hold at most **56** pages for `"pages"` type or **1024** pages for `"list"` type — `header`/`footer`/`overlay`/`underlay` are independent single pages and don't count toward this. Each Content Page Object's `items[]` can hold at most **256** items (every `type`, including `"space"`, counts). Exceeding either ceiling doesn't break the page — MSX shows the user a warning message and simply ignores everything beyond the limit, the rest still renders normally. For UX reasons, stay well below both ceilings in practice. See [AGENTS.md](../AGENTS.md#2-json-hierarchy-start--menu--content--item).

**Mechanically either type works, but which one should you actually pick?** See [Best Practices & Good to Know → Best Practices](best-practices.md#best-practices-learned-from-building-real-pages) for practical guidance — in short, `"list"` fits classic scrollable lists and sees more real-world use overall, `"pages"` fits special one-off screens, and `"list"`'s stacking becomes a real advantage as soon as content spans more than one page.

**Source:** [Content Root Object](../main-api/content/content-root-object.md)
**Example:** [guide/list.json](../examples/guide/list.json), [guide/stacked_list.json](../examples/guide/stacked_list.json)

---

## Step 5 — Compress Property

For denser layouts, add `"compress": true` to the Content Root Object (since `0.1.123`). This reduces each cell by 25% and expands the grid from **12 × 6** to **16 × 8** (panels: 8 × 6 → 10 × 8), fitting more items per page.

The example below uses `template` + `items` (explained in detail in Step 6):

```json
{
  "compress": true,
  "type": "list",
  "template": {
    "type": "control",
    "layout": "0,0,16,1"
  },
  "items": [
    { "label": "Item 1" },
    { "label": "Item 2" },
    { "label": "Item 3" }
  ]
}
```

Key rules for `compress`:

| Where | Effect |
|---|---|
| **Content Root** | Changes the grid for the entire content (all pages, items, header, footer, overlay, underlay). This is the only place that controls the grid size. |
| Content Page / Item / Template | Only adjusts font sizes — does **not** change the grid. |

**Backward compatibility warning:** Items placed at grid coordinates only valid in the expanded grid (column ≥ 12, row ≥ 6) will produce "out of range" warnings and not render on MSX versions before `0.1.123`. Only use the extended coordinates when you can guarantee the minimum version.

**`decompress` — authoring a template in uncompressed coordinates (since `0.1.155`):** When combining `compress: true` with `template` + `items` (Step 6), the template's own `layout` normally has to be written in *compressed* grid coordinates too. Setting a template-only `"decompress": true` lets you instead write the template's `layout` using standard, uncompressed grid coordinates (e.g. `"0,0,3,2"` instead of the compressed equivalent) — MSX automatically converts those coordinates into the compressed grid at render time. This does not mix compressed and uncompressed grids within one content (that's never possible); it only changes the coordinate scale the template is authored in.

```json
{
  "compress": true,
  "template": {
    "decompress": true,
    "type": "default",
    "layout": "0,0,3,2"
  },
  "items": [
    { "label": "Item 1" },
    { "label": "Item 2" }
  ]
}
```

**Source:** [Compress Property](../experts-api/hidden-features/compress-property.md)
**Example:** [xp/hidden_feature_12.json](../examples/xp/hidden_feature_12.json)

---

## Step 6 — Templated Content (template + items)

For repeated content of the same shape, use `template` + `items` instead of individual `pages`. This is mutually exclusive with the `pages` property. The `template` + `items` pattern works with both `"type": "pages"` and `"type": "list"`.

> **Name collision:** `"type": "pages"` (a string value controlling navigation direction) and the `pages` property (an array of Content Page Objects) share the same word but are completely independent. You can have `"type": "list"` with a `pages` array, or `"type": "pages"` with `template` + `items` and no `pages` array at all.

The `template` defines the **static visual skeleton** (layout, type, color, icon, tag, …). Each item in `items[]` provides its own **standard properties** (label, image, action, …) directly — MSX merges template and item for each rendered cell.

**When to use `template`+`items` vs. a manual `pages` array?** Both work for either `"list"` or `"pages"`, but in practice `template`+`items` is the more common choice for `"list"`-type content, while `"pages"`-type content is more often hand-built with a manual `pages` array instead. See [Best Practices & Good to Know → Best Practices](best-practices.md#best-practices-learned-from-building-real-pages) for the full practical breakdown, including why some real examples still pair `"pages"` with `template`+`items`.

In the `template`, the `layout` property only uses the `w` and `h` values (width and height) — the `x` and `y` values are ignored, because MSX auto-positions each templated item on the grid.

```json
{
  "type": "pages",
  "headline": "My Videos",
  "template": {
    "type": "separate",
    "layout": "0,0,2,4",
    "icon": "msx-white-soft:movie",
    "color": "msx-glass"
  },
  "items": [
    { "label": "Video 1", "image": "http://example.com/thumb1.jpg", "action": "video:http://example.com/video1.mp4" },
    { "label": "Video 2", "image": "http://example.com/thumb2.jpg", "action": "video:http://example.com/video2.mp4" }
  ]
}
```

**`{context:{PROPERTY}}` — why only in `data`?**

Standard template properties (`label`, `image`, `action`, …) are simple strings. Each item can already override them directly — no special syntax needed. MSX simply merges item properties on top of the template.

`data`, however, can be a **complex nested JSON object** (e.g. an entire panel layout with pages, items, and sub-items). Without `{context:{PROPERTY}}`, every item would have to carry a full copy of that JSON structure, with only one value different per copy. The `{context:{PROPERTY}}` expression solves this: you write the complex structure once in `template.data`, and embed a placeholder wherever an item-specific value is needed.

`{context:{PROPERTY}}` works anywhere **inside** a `data` object, however deeply nested — including as the value of any JSON string property within that structure. It doesn't matter whether the string is a label, an icon, or even an action string: `"action": "info:{context:message}"` is perfectly valid as long as it appears inside `template.data`. The `data` property is always a JSON object (or `null`) — never a plain string — so this is where the expression adds the most value: the complex structure is written once in `template.data` and `{context:{PROPERTY}}` substitutes item-specific values wherever needed.

The canonical example is [guide/icons.json](../examples/guide/icons.json), which shows an icon browser where each cell opens a detail panel. Without `{context:...}`, every one of the ~900 icon items would need to embed the entire panel structure. With it, the panel is written once in `template.data` and `{context:icon}` substitutes the current icon name:

```json
{
  "type": "pages",
  "template": {
    "type": "default",
    "layout": "0,0,1,1",
    "color": "msx-glass",
    "icon": "blank",
    "iconSize": "small",
    "action": "panel:data",
    "data": {
      "pages": [{
        "items": [{
          "type": "default",
          "layout": "2,1,4,4",
          "color": "msx-glass",
          "iconSize": "large",
          "label": "{context:icon}",
          "icon": "{context:icon}",
          "action": "back"
        }]
      }]
    }
  },
  "items": [
    { "icon": "home" },
    { "icon": "star" },
    { "icon": "movie" }
  ]
}
```

Each item provides only `"icon": "{NAME}"`. This value does double duty: it overrides the template's `"icon": "blank"` so the actual icon appears on the grid cell, and it is simultaneously substituted via `{context:icon}` into both `label` and `icon` inside the embedded panel. The full panel JSON is shared across all items.

> `{context:{PROPERTY}}` is resolved inside the `data` property of a template object (directly or anywhere in its nested structure), and since `0.1.110` also in the `properties`, `live`, `selection`, and `options` template properties. `data` is always a JSON object or `null`. Standard template properties like `label`, `image`, `action` **cannot** contain `{context:...}` — override those directly per item. The referenced property must be a string value in the item; if missing, the expression stays as-is; if not a string, it is removed.

**Source:** [Content Root Object](../main-api/content/content-root-object.md), [Inline Expressions](../main-api/common/inline-expressions.md)
**Examples:** [guide/videos.json](../examples/guide/videos.json), [guide/audios.json](../examples/guide/audios.json), [guide/icons.json](../examples/guide/icons.json)

---

## Step 7 — Content Item Properties

Content items support many visual properties. Start with just `label` and `action`; add others progressively.

The `type` property controls the visual style of the item:

| Type | Description |
|---|---|
| `"default"` | Default item (default). |
| `"teaser"` | Similar to `"default"` but with a larger title. |
| `"button"` | Frame with transparent background. Typically used with `icon` and `label`. |
| `"separate"` | Separated titles — title area is visually detached from the image/icon area. |
| `"space"` | Not selectable; used for background or foreground decoration items. |
| `"control"` | Similar to `"button"`, designed for menus and settings. Supports `extensionLabel` / `extensionIcon` on the right side. |

> **Important:** A content page must have at least one selectable item to be displayed. A page containing only `"space"` items cannot be shown — except overlay/underlay pages, which may only contain `"space"` items.

> **`"separate"` reserves less image space than its `layout` height implies**, and `image`/`icon` behave differently for `"control"`/`"button"` items than for the others — see [Common Misconceptions → Content & layout](common-misconceptions.md#content--layout) for the exact breakdown before templating items with images and icons.

```json
{
  "id": "my_item",
  "label": "My Item",
  "headline": "Subtitle / description",
  "image": "http://example.com/thumb.jpg",
  "icon": "star",
  "tag": "NEW",
  "tagColor": "msx-red",
  "progress": 0.5,
  "action": "video:http://example.com/video.mp4"
}
```

`layout` controls grid position as `"col,row,width,height"` (e.g. `"0,0,4,3"`). For non-templated items (inside `pages[].items`) it is **required** — see Common Mistake #7. For templated items (via `template` + `items`) it is set once on the template and only `w` and `h` are used — `x` and `y` are ignored, MSX auto-positions each item on the grid.

> **`id` is optional.** Assign it only when you need to target this item with `update:content:{ID}` or `focus:{ID}`. If two items share the same `id`, MSX removes the second one and throws a warning — so only add `id` when you actually need it.

The `icon` property accepts a plain icon name (e.g. `"star"`), optionally prefixed with a color and/or the `outline` variant:

| Syntax | Example | Since |
|---|---|---|
| `{ICON}` | `"movie"` | 0.1.0 |
| `{COLOR}:{ICON}` | `"msx-red:movie"` | 0.1.0 |
| `outline:{ICON}` | `"outline:movie"` | 0.1.161 |
| `{COLOR}:outline:{ICON}` | `"msx-red:outline:movie"` | 0.1.161 |

The special icon `"blank"` is invisible and can be used as a placeholder.

The `iconSize` property controls icon rendering size: `"small"`, `"medium"` (default), `"large"`, `"extra-large"`. Ignored for `"control"` type items.

**`headline`** appears on three levels, each with a different meaning:

| Where | What it shows |
|---|---|
| **Content Root Object** | Page title in the top-left corner of the screen. When the content is loaded via a menu, the menu item's `label` is used as headline instead — set `"important": true` on the Content Root (since `0.1.58`) to enforce the Content Root's own headline. Has no effect in panels. Supports Inline Expressions. |
| **Content Page Object** | Sub-headline attached to the content headline. If the content has no headline, this is used as the content headline. Has no effect on overlay/underlay pages. Supports Inline Expressions. |
| **Content Item Object** | Positioned in the upper-left corner of the item itself — within the item's layout boundaries, just like all other visual properties of the item. Supports Inline Expressions. |

`"important": true` on the Content Root is commonly used when the content is meant to display its own title regardless of which menu item triggered it — for example, a detail page that should always show its own name rather than the menu entry that opened it.

Color properties (`tagColor`, `badgeColor`, `stampColor`, `color`, etc.) accept either standard CSS color syntax (e.g. `"#ff0000"`, `"rgb(255,0,0)"`, `"red"`) or one of the 11 predefined `msx-*` color identifiers:
`msx-red`, `msx-green`, `msx-yellow`, `msx-blue`, `msx-white`, `msx-gray`, `msx-black`, `msx-white-soft`, `msx-gray-soft`, `msx-black-soft`, `msx-glass`.

The `msx-*` colors are the recommended choice — they are theme-aware and consistent across platforms.

**Source:** [Content Item Object](../main-api/content/content-item-object.md), [Colors](../main-api/common/colors.md), [Icons](../main-api/common/icons.md)
**Examples:** [guide/images.json](../examples/guide/images.json), [guide/panel.json](../examples/guide/panel.json)

---

## Step 8 — Actions

Every `action` property is an **action string**. Common patterns:

```json
"action": "video:http://example.com/video.mp4"
"action": "info:Hello, World!"
"action": "content:http://example.com/more.json"
"action": "link:http://example.com/"
```

| Action | Description |
|---|---|
| `video:{URL}` | Plays a video |
| `audio:{URL}` | Plays audio |
| `content:{URL}` | Loads a new content page (pushes to history) |
| `panel:{URL}` | Opens a panel overlay |
| `menu:{URL}` | Loads a new menu |
| `link:{URL}` | Opens a link in-app (use `link:window:{URL}` to open externally in a separate window/device browser) |
| `info:{TEXT}` | Shows an info message overlay |
| `success:{TEXT}` | Shows a success message overlay |
| `warn:{TEXT}` | Shows a warning message overlay |
| `error:{TEXT}` | Shows an error message overlay |
| `back` | Navigates back in history |
| `reload:content` | Reloads the current content from its URL |
| `reload:menu` | Reloads the current menu from its URL |
| `execute:{URL}` | Server action — POSTs `data` to the URL, executes returned action |
| `update:menu:{ID}` | Updates visual properties of the menu item with `{ID}` — new values are taken from this item's `data` property; only properties already set on the origin item can be updated (except `background`, `lineColor`, `options`, which can always be updated) (since `0.1.58`) |
| `update:content:{ID}` | Updates visual properties of the content item with `{ID}` — new values are taken from this item's `data` property; only properties already set on the origin item (the item being updated) can be updated (except the `color`-type properties `color`/`tagColor`/`badgeColor`/`stampColor`/`progressColor`/`progressBackColor`/`wrapperColor`, plus `live`, `selection`, `options`, which can always be updated) (since `0.1.58`) |
| `update:content:overlay:{ID}` | Updates visual properties of the overlay item with `{ID}` — new values are taken from this item's `data` property (since `0.1.110`) |
| `update:content:underlay:{ID}` | Updates visual properties of the underlay item with `{ID}` — new values are taken from this item's `data` property (since `0.1.112`) |
| `update:panel:{ID}` | Updates visual properties of the panel item with `{ID}` — new values are taken from this item's `data` property (since `0.1.58`) |

Multi-action (since `0.1.58`): wrap in `[...]`, separate with `|`:

```json
"action": "[reload:content|success:Done!]"
```

The triggering item's `data` property is shared by **all** actions in the list — use this form when the actions need the same data (or none at all). A literal `|` inside one of the actions needs the `{pipe}` keyword (e.g. `[reload:content|info:[P{pipe}I{pipe}P{pipe}E]]`), otherwise it's read as a separator.

If the actions need **different** data each, use the `data` action instead (also since `0.1.58`) — each entry in its `actions` array carries its own `action`/`data` pair, and no `{pipe}` escaping is needed since there's no `|` separator:

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

Server action — MSX POSTs to your endpoint and executes the returned `action`. The item's `data` property is submitted as the POST body:

```json
{
  "label": "Run Action",
  "action": "execute:http://example.com/service.php",
  "data": { "id": "my_item_id" }
}
```

The server must respond with a `response` wrapper. The `response.data.action` value is the action MSX executes next:

```json
{
  "response": {
    "status": 200,
    "text": "OK",
    "message": null,
    "data": {
      "action": "success:Item saved!"
    }
  }
}
```

In a server action response, `response.data.action` must always be set to something — it is not optional the way a content item's own `action` is (a content item with no `action` just shows an info message, *"No action available."*, when executed). If `response.data.action` is missing or empty, MSX throws a hard execution error (*"Execution error: Action is missing or empty."*); if the `response` object itself is missing entirely, MSX throws a separate error (*"Execution error: Response data is missing."*). If you want the response to deliberately do nothing, return the no-op multi-action `"action": "[]"` instead of omitting `action`. For error responses in general, return `"status": 4xx` (or similar) with `"data": null` and set `"message"` to an error description — MSX will show the message to the user. See [Responses](../main-api/common/responses.md) for the full format.

> **POST encoding:** The POST body is always JSON (e.g. `{ "id": "my_item_id" }`). By default, MSX sends `Content-Type: application/x-www-form-urlencoded` instead of `application/json` — this avoids CORS preflight requests but can cause some backends to reject the request. If your server requires the correct header, use the `accurate:` prefix (since `0.1.90`): `execute:accurate:{URL}` — this sends `Content-Type: application/json` at the cost of a CORS preflight. Choose based on your server setup. Without `service:`, only one server action can be running at a time on the application side — parallel execution is not allowed. Since `0.1.90`, the `service:` prefix executes multiple server actions in parallel instead (max 128): `execute:service:{URL}`.
>
> **Timeout:** All server action requests time out after **30 seconds**. If no response is received within that window, MSX discards the request and throws an internal error. Design your endpoints to respond well within this limit.
>
> **Error visibility:** Every error or warning thrown by MSX — including the timeout above — is written to the internal log **and** shown to the user as a visible message; these two things always happen together by default. To suppress the busy indicator and hide the error/warning message from the user, use the `silent:` prefix, e.g. `execute:silent:{URL}` (also `execute:silent:user:`, `execute:silent:accurate:`, `execute:silent:fetch:`, etc.) — the error/warning is still written to the internal log even in silent mode, only the user-visible busy indicator and message are suppressed. This is separate from the distinction between message actions and their `logger:` counterparts: `error:{TEXT}`/`info:{TEXT}`/`success:{TEXT}`/`warn:{TEXT}` only show the message to the user, while `logger:error:{MESSAGE}`/`logger:info:{MESSAGE}`/`logger:success:{MESSAGE}`/`logger:warn:{MESSAGE}` do both — log the message internally and show it to the user. See [Internal Actions](../experts-api/special/internal-actions.md) for the full `logger:` and `execute:silent:` syntax.

**Navigation and history**

MSX maintains a **main history stack** shared by content pages and menus. Both `content:{URL}` and `menu:{URL}` push onto it; `back` pops one level regardless of whether that level was content or a menu. Panels have a **separate secondary stack**: navigating into a panel pushes to the panel stack, and `back` pops within it first before returning to the main stack.

If a new content page is opened from inside a panel, the entire panel stack is discarded — the back path then follows the main content stack only.

Example back path: `content 1 → content 2 → panel 1 → panel 2 → back → panel 1 → back → content 2 → back → content 1`.
But if panel 2 opens `content 3`: `content 3 → back → content 2 → back → content 1` (panels cleared).

For in-place replacement without a history entry (tabs, filters, pagination) use `replace:content:{FLAG}:{REQUEST_ACTION}` (since `0.1.144`) — see [Replace Action](../experts-api/hidden-features/replace-action.md).

`offset` (format `"x,y,w,h"`, same shape as `layout`) adds/subtracts a purely visual delta on top of an item's `layout` position/size — it never changes the item's logical grid footprint. Each component is a relative floating-point number; `x`/`w` can go from `-11.0` to `11.0` (panels: `-7.0` to `7.0`), `y`/`h` from `-5.0` to `5.0`. **Version note:** these ranges only apply since `0.1.110` — before that, every component was capped at `-1.0`–`1.0`. An `offset` value beyond `±1.0` on a pre-`0.1.110` client silently misbehaves, so state `0.1.110+` whenever you use an offset component outside that smaller range.

**Large offsets for a true full-screen background (since `0.1.110`):** an item's `layout` alone can only ever reach the grid's own bounds (e.g. `"0,0,12,6"` — the entire grid, but not necessarily the physical screen edge, since the grid sits inside the app's own margins/safe area). Stretching an offset *beyond* `±1.0` lets a background `"space"` item bleed past that grid edge to fill the actual screen. [Large Offsets](../experts-api/hidden-features/large-offsets.md) demonstrates this on a full-grid item: `"layout": "0,0,12,6"` combined with `"offset": "-0.75,-1,1.5,1.67"` — shifted 0.75 units further left and 1 unit further up than the grid's own top-left corner, and 1.5/1.67 units wider/taller than the full grid size, so the image reaches every physical screen edge instead of stopping at the grid boundary. See [xp/hidden_feature_9.json](../examples/xp/hidden_feature_9.json) for the complete working example. It also sets `"offset": "0,0,0,1.5"` on the Content Page itself — this has no effect on the page's static, settled-in layout at all (it looks identical with or without it once a transition finishes); it only smooths the page-to-page *transition* animation, so the two stretched full-screen background images of the current and next page align perfectly underneath each other instead of overlapping or leaving a gap mid-transition. Page offsets like this are normally used to widen/narrow the gap between stacked pages (`"list"` type); these two pages are effectively fullscreen (the `"space"` item's `"layout": "0,0,12,6"` fills the whole grid) and don't visually stack, but the offset still has a small, easy-to-miss effect on the transition itself.

MSX also handles **focus navigation automatically**: directional key presses (up/down/left/right) select the spatially nearest item based on `layout` coordinates only — `offset` has no effect on navigation at all, it is a purely visual nudge. This means `layout` and `offset` can be deliberately mismatched (a narrower `layout` compensated by a wider `offset`, or vice versa) to steer which item gets focused next without changing how anything looks — see [Cookbook → Deep dive: `layout` drives navigation, `offset` doesn't](cookbook.md#deep-dive--layout-drives-navigation-offset-doesnt-list0json) for a worked example. You do not need to wire up navigation yourself — MSX's navigation engine handles both within-page and between-page focus movement.

**Source:** [Actions](../main-api/common/actions.md)
**Examples:** the "Actions" section of [guide/menu.json](../examples/guide/menu.json) — [guide/videos.json](../examples/guide/videos.json), [guide/audios.json](../examples/guide/audios.json), [guide/images.json](../examples/guide/images.json), [guide/links.json](../examples/guide/links.json), [guide/mixed.json](../examples/guide/mixed.json), [guide/references.json](../examples/guide/references.json), [guide/messages.json](../examples/guide/messages.json)

---

## Step 9 — Inline Expressions

Inline expressions enrich labels, headlines, and text. They are processed at render time. The table below is a practical subset for JSON building — for the complete list of standard inline expressions (plus the Number/Color/Dictionary/Live families), see [AGENTS.md](../AGENTS.md#4-inline-expressions--complete-overview) and [Inline Expressions](../main-api/common/inline-expressions.md).

| Expression | Result | Since |
|---|---|---|
| `{sp}` | Non-breaking space | 0.1.0 |
| `{tb}` | Tab stop | 0.1.0 |
| `{br}` | Line break | 0.1.0 |
| `{ico:{ICON}}` | Inline icon by name (e.g. `{ico:star}`) | 0.1.0 |
| `{ico:{COLOR}:{ICON}}` | Colored inline icon (e.g. `{ico:msx-red:star}`) | 0.1.0 |
| `{txt:{COLOR}:{TEXT}}` | Colored text segment (e.g. `{txt:msx-yellow:Featured}`) | 0.1.0 |
| `{chr:{CHARACTER}}` | HTML character entity by name or decimal (e.g. `{chr:copy}` or `{chr:#169}`) — without `&` / `;` | 0.1.145 |
| `{chr:{COLOR}:{CHARACTER}}` | Colored character entity (e.g. `{chr:msx-red:copy}`) | 0.1.145 |
| `{context:{PROPERTY}}` | Substitutes item property value — only valid inside a template object's `data` (and since `0.1.110`: `properties`, `live`, `selection`, `options`) | 0.1.0 |
| `{unknown}` | Unknown/misspelled expression names are returned without braces (e.g. a typo silently becomes plain text) | 0.1.0 |

Example label combining expressions:

```
"{ico:star}{sp}{txt:msx-yellow:Featured}{sp}My Show"
```

> `{txt:{COLOR}:{TEXT}}` is the documentation pattern. In actual usage, replace `{COLOR}` with the color name and `{TEXT}` with your text — no extra braces: `{txt:msx-red:Hello}`, not `{txt:{msx-red}:Hello}`.

**Source:** [Inline Expressions](../main-api/common/inline-expressions.md), [Colors](../main-api/common/colors.md)

---

## Step 10 — A Complete Working Page

Combining all steps: a start object that loads a menu, which has one item that returns a templated video list.

**`start.json`** (your MSX start parameter):
```json
{
  "name": "My Video App",
  "version": "1.0.0",
  "parameter": "menu:http://example.com/menu.json"
}
```

**`menu.json`**:
```json
{
  "headline": "Main Menu",
  "menu": [
    {
      "label": "{ico:video-collection}{sp}Videos",
      "data": "http://example.com/videos.json"
    }
  ]
}
```

**`videos.json`**:
```json
{
  "type": "pages",
  "headline": "My Videos",
  "template": {
    "type": "separate",
    "layout": "0,0,3,4",
    "icon": "msx-white-soft:movie",
    "color": "msx-glass"
  },
  "items": [
    { "label": "Episode 1", "image": "http://example.com/t1.jpg", "action": "video:http://example.com/ep1.mp4" },
    { "label": "Episode 2", "image": "http://example.com/t2.jpg", "action": "video:http://example.com/ep2.mp4" }
  ]
}
```

> **Headline and menu interaction:** In this example, menu and content are always visible at the same time — the menu on the left, the content preview already fading in on the right after the auto-fetch delay. When the user presses right or Enter, the screen animates left, the menu disappears, and the menu item's label becomes the content headline. This means `"headline": "My Videos"` in `videos.json` is **not shown** while the content is loaded via the menu — the menu item's label `"{ico:video-collection}{sp}Videos"` is displayed instead.
>
> It still makes sense to set a `headline`: if the same content URL is ever loaded independently (e.g. via `content:http://example.com/videos.json`), the headline **is** used. If you know for certain the content will only ever be loaded from this specific menu item, the headline can be omitted. If the menu item's `data` is an embedded Content Root Object rather than a URL, a `headline` has no practical purpose — unless `important: true` is also set on that Content Root Object, which enforces the content's own headline regardless of what the menu item shows.

---

## Step 11 — Loading Panels and Inline Data

A panel is a content page shown as an overlay without leaving the current content. Use `panel:{URL}` to load it from a URL:

```json
{
  "label": "Show Details",
  "action": "panel:http://example.com/details.json"
}
```

**Embedding data inline with `:data`**

Instead of a URL you can embed the target object directly in the item's `data` property using the `:data` variant. The same pattern works for panels, content pages, and menus:

| Action | `data` must be | Description |
|---|---|---|
| `panel:data` | Content Root Object | Opens a panel with the embedded content |
| `content:data` | Content Root Object | Navigates to a full content page with the embedded content |
| `menu:data` | Menu Root Object | Opens a menu with the embedded menu data |

```json
{
  "label": "Open Panel",
  "action": "panel:data",
  "data": {
    "type": "list",
    "pages": [{
      "items": [
        { "layout": "0,0,8,2", "label": "Detail A", "action": "info:A" },
        { "layout": "0,2,8,2", "label": "Detail B", "action": "info:B" }
      ]
    }]
  }
}
```

There is also a base64 variant for each (since `0.1.0`, same as the corresponding base action): `panel:json:{BASE64}`, `content:json:{BASE64}`, `menu:json:{BASE64}` — useful when the data needs to be embedded in a plain action string without a `data` property.

**Source:** [Actions](../main-api/common/actions.md)
**Example:** [guide/panel.json](../examples/guide/panel.json)

---

## Testing Your JSON in the Web Version

You can test any JSON page instantly in the web version of Media Station X by embedding it as base64 directly in the start URL — no server or hosting required:

```
https://msx.benzac.de/?start=menu:json:{BASE64}
```

- `{BASE64}` is the standard base64 encoding of the full JSON string.
- Use `menu:json:{BASE64}` to load the JSON as a **Menu Root Object**, or `content:json:{BASE64}` to load it as a **Content Root Object**.
- The `start` URL parameter is the web-version equivalent of the app's Setup Start Parameter (available on the web version of Media Station X at `msx.benzac.de` only). You can append the focus separator `>` to focus and execute an item on load, e.g. `…>index:2>execute`.
- For large pages, putting the full JSON into the URL as a base64 payload isn't reliable — a sufficiently long URL can simply fail to load. Don't rely on the base64 URL variant for a big page; either host the JSON on a CORS-enabled server and use `start=menu:{URL}` / `start=content:{URL}` instead, or use the Demo page's payload-less copy-paste workflow described next.

**Alternative: the Demo page editor.** The same base64 payload also works via the info site's Demo page, using a `tab=Demo` parameter instead of loading MSX directly:

```
https://msx.benzac.de/info/?tab=Demo&start=menu:json:{BASE64}
```

(or `content:json:{BASE64}` for a Content Root Object). Instead of starting MSX immediately, this opens a simple JSON editor pre-filled with the given JSON, where it can be reviewed and edited before launching. A **Launch** button then sets the (possibly edited) JSON as the Start Object and starts the web version of MSX — exactly as if it had been entered through the app's own Setup Start Parameter. Because this sets a new start parameter for the web version, it can overwrite one that is already configured there; the Demo page detects that case and shows a warning first, so nothing is silently lost — but this still makes it a less ideal *first* choice for a quick one-off test than the direct link above. Where it genuinely helps: iterating on many properties or media URLs at once — instead of generating a fresh test link for every tweak, hand the user one Demo-page link and let them edit and re-test the JSON directly in the browser.

**No-payload mode — the workaround for JSON too large for any URL.** The Demo page also works with no `start` parameter at all:

```
https://msx.benzac.de/info/?tab=Demo
```

Opened this way, the editor loads whichever JSON currently sits in the browser's own HTML5 web storage, or a built-in default/example JSON if storage is empty. Pressing **Launch** writes the (possibly hand-edited) JSON from the editor back into that same HTML5 web storage, and the web version of MSX reads its start parameter from there once launched — the same underlying storage mechanism the native app itself uses after its own Setup Start Parameter process (storing and reading the configured start parameter locally, not via a URL). Practical consequence: a JSON page too large to safely embed as a base64 URL doesn't need the URL at all — paste the plain, un-encoded JSON text by hand into the editor (e.g. copied straight out of a chat message), then press Launch. No URL encoding is involved, so there's no length constraint to run into.

Treat this as an expert option, not the default recommendation: editing raw JSON directly in the browser assumes the user already has a solid understanding of the Setup Start Parameter mechanism and of the actual JSON structure being edited (Start/Menu/Content Root Object, item properties, action syntax, …) — without that, a hand-edit is more likely to produce invalid JSON than a working tweak. It also assumes the user is comfortable with the overwrite risk above. For a first test, or for a user who just wants to see the result, offer the direct launch link; only reach for the Demo page once the user specifically wants to tweak the JSON themselves — or once the JSON is simply too large to set directly as a URL parameter in the first place, since the payload-less copy-paste variant above is otherwise the only quick way left to test it at all.

> **Only `start` carries over — no other URL Parameters.** Unlike the direct launch URL above, the Demo page's own URL only understands `tab=Demo` and `start=...`; none of the other [URL Parameters](../experts-api/special/url-parameters.md) (`secure`, `layout`, `scale`, …) can be appended to it. Those only take effect once you actually reach the running web version of MSX — either via the direct launch URL, or after pressing **Launch** on the Demo page (which lands on the plain, parameter-less app URL).

> **HTTP links & mixed-content policy:** For resource URLs (media, images, plugins, referenced JSON) inside your JSON, prefer the insecure `http://` variant over hardcoding `https://`. When the app runs in a secure (HTTPS) context — such as the web version of Media Station X at `https://msx.benzac.de` — the browser's mixed-content policy would block plain `http://` subresources. MSX handles this via the `secure` URL parameter, which auto-updates `http://` URLs to `https://` in a secure context: by default (`secure=0`) it upgrades JSON files and plugins (but **not** media files); `secure=1` also upgrades media file URLs. Writing `http://` lets this mechanism pick the right protocol per context, whereas a hardcoded `https://` can break on servers that only serve `http://`. Do **not** use protocol-relative URLs (`//host/…`) for resource URLs in built JSON — on a TV the app runs in a local context, where a protocol-relative URL resolves against that local context instead of `http`/`https` and fails. Always write an explicit `http://` scheme. See [URL Parameters](../experts-api/special/url-parameters.md).

**Presenting a live preview:** When you (an LLM) generate a JSON page for a user, you can hand them a ready-to-click test link built this way, or embed the web version of Media Station X directly in an HTML `<iframe>` for an instant live preview:

```html
<iframe src="https://msx.benzac.de/?start=menu:json:{BASE64}" width="1280" height="720"></iframe>
```

**Source:** [URL Parameters](../experts-api/special/url-parameters.md), [Actions](../main-api/common/actions.md)

---

## Common Mistakes

1. **Using `content:{URL}` as the value of menu item `data`** — wrong. `data` takes a plain URL or a Content Root Object; `content:` prefix only goes in `action` strings.
2. **Inventing `msx-*` color names** — only the 11 predefined `msx-*` identifiers are valid (e.g. `msx-red`, `msx-glass`). Standard CSS syntax (`"#ff0000"`, `"red"`) is also accepted, but arbitrary `msx-*` names like `msx-orange` do not exist.
3. **Mixing `pages` and `items` at the same level** — `template`+`items` and `pages` are mutually exclusive on the Content Root Object.
4. **Adding extra braces around the color in `{txt:...}`** — write `{txt:msx-red:Hi}`, not `{txt:{msx-red}:Hi}`. The documentation pattern `{txt:{COLOR}:{TEXT}}` uses `{COLOR}` as a placeholder label; those braces do not appear in actual JSON.
5. **Using multi-action without `[...]` wrapper** — `action1|action2` is not valid; it must be `[action1|action2]`.
6. **Using `{context:{PROPERTY}}` in the wrong template property** — it is only resolved inside the template object's `data` property (and since `0.1.110`: `properties`, `live`, `selection`, `options`). It does **not** work in the template's own top-level `label`, `image`, `action`, or other standard properties — override those directly per item. Note: `{context:...}` is valid anywhere inside `template.data`, including in nested `label`, `icon`, or `action` strings of embedded objects, because those live inside `data`.
7. **Omitting `layout` on non-templated items** — items inside `pages[].items` require an explicit `layout` property (`"col,row,width,height"`) to position them on the grid. Only templated items (via `template` + `items`) are auto-positioned.
8. **Putting `items` on the Content Root without `template`** — `items` at the Content Root level requires `template` to also be set. Without `template`, `items` is silently ignored. If your items carry individual `layout` values, that's the tell you want `pages[].items[]` instead — not top-level `items`, which is only the templated path.
9. **Expecting forward compatibility** — features introduced in later versions silently degrade or error on older MSX clients.
10. **Adding an `action` to a menu item** — menu items have no `action` property. Associate content with a menu item only via `data` (raw URL or embedded Content Root Object).
11. **A literal `|` inside a multi-action without `{pipe}`** — inside `[action1|action2]`, a `|` that is part of an action argument (e.g. message text) must be written as `{pipe}`, otherwise MSX treats it as an action separator.
12. **Omitting mandatory Start Object properties** — `name`, `version`, and `parameter` are all required on the Start Object (Step 1).
13. **Using `{SERVER}` or `{PREFIX}` outside the start parameter file** — these placeholders only work in the Start Object's start parameter (`{SERVER}` since `0.1.65`, `{PREFIX}` since `0.1.97`). All other JSON files must use full, absolute URLs.
14. **Placing items in the extended (compressed) grid without noting the minimum version** — with `compress: true`, items at a 0-based column index ≥ 12 or row index ≥ 6 render only on MSX `0.1.123+`; older clients drop them with "out of range" warnings, potentially blanking the page (Step 5).
15. **Guessing icon names** — use a verified icon name from [Icons](../main-api/common/icons.md) or the live reference at [https://msx.benzac.de/info/icons.html](https://msx.benzac.de/info/icons.html); an unknown icon name silently renders nothing.
16. **A content page with only `"space"` items** — a page needs at least one selectable item to be displayed; a page containing only non-selectable `"space"` items cannot be shown (except overlay/underlay pages, which may contain only `"space"` items). See Step 7.
17. **Assuming inline expressions work in any string property** — they only resolve in free-text, visual-output properties (`label`, `title`, `headline`, `text`, `tag`, `badge`, `stamp`, `caption`, `extension`, etc.), each individually documented as supporting them. Fixed-enum properties (`type`, `imageFiller`, `alignment`, …) and structural strings (URLs, IDs, action strings, colors, icon names) never render inline expressions.
18. **Using an `offset` component beyond `±1.0` without noting the minimum version** — each `offset` value (`x`/`w`: `-11.0`–`11.0`, panels `-7.0`–`7.0`; `y`/`h`: `-5.0`–`5.0`) only supports that full range since `0.1.110`; before that, every component was capped at `-1.0`–`1.0`, so a larger value silently misbehaves on older clients (Step 8).

> This is the full build-time catalog. For a shorter set of cross-cutting conceptual gotchas, see [AGENTS.md → Common JSON-Building Mistakes](../AGENTS.md#9-common-json-building-mistakes).

---

## Going Further

Three commonly used advanced features not covered in this guide:

**Live Object** — A time-based state machine (`init` → `coming`/`running`/`over`) on a content item, evaluated every second while the item is visible. Set a `live` property with a `type`: `"schedule"` (start/end via `from`/`to`), `"lifetime"` (`duration`/`delay` from creation), `"airtime"` (`duration` from becoming visible), `"playback"` (tracks the player), or `"setup"` (no state machine, just fires once on visibility). Each state (`coming`/`running`/`over`) can override the item's own visual properties (`title`, `progress`, etc.) and fires its own `action` once per entry into that state. There is no built-in server re-fetch — to actually refresh data, chain a server action from a state's `action` (e.g. an `over.action` calling `execute:{URL}`) whose response supplies a fresh live object. Used for EPG tiles, countdowns, and playback progress. See [Live Object](../experts-api/live/live-object.md) and [Live Inline Expressions](../experts-api/live/live-inline-expressions.md).

**Selection Object** — Controls what happens when a content item receives focus. Set a `selection` property on an item with an `action` (plus optional `data`) to trigger when the item becomes focused (e.g. update a preview overlay, switch the background) — `background` and `headline` on the same object apply only while the item is focused and revert automatically once focus moves on. There is no built-in delay or "on focus leaves" action; a delay before the selection action fires can be layered on top using the generic `delay:{ID}:{DELAY}:{ACTION}` internal action (see the "Delayed" example on the source page). If a selection action has a *lasting* side effect beyond `background`/`headline` (e.g. playing an audio, showing an atmospheric backdrop), you need some way to undo it once focus moves elsewhere — there's more than one way to do this. One option is a Page Action, typically on the underlay page, since (`0.1.130`+) its page action also fires when the content becomes inactive while still visible (e.g. the corresponding menu regains focus); see [Backdrop Plugin](../experts-api/plugins/backdrop-plugin.md) for a worked example (`quiet` + reloading the backdrop with a neutral value on the underlay page action). Another option, for pages built around an interaction plugin, is to skip the Selection Object mostly or entirely and instead register a content observer (`addContentObserver` in the [Plugin API](../experts-api/plugins/plugin-api-reference.md)), which reports focus changes directly to the plugin so it can react on its own — note that this callback fires with a delay of roughly 1 second, so it suits a plugin-driven page better than a precise, immediate reaction. Which approach fits best depends on the use case and how the page is already structured. See [Selection Object](../experts-api/selection/selection-object.md) and [Page Action](../experts-api/hidden-features/page-action.md).

**Advanced Templated Items** — Extends the `template` + `items` auto-flow with explicit breaks and inserted pages. Set `break` on an item to control layout flow: `"line"` (new line, or new page if no line fits), `"page"` (force a new page), or `"context"` / `"context:{CONTEXT_ID}"` (new page, marking a content-driven break point). Separately, the Content Root Object's `inserts` array defines extra pages to splice into that auto-generated flow, each placed via its own `position` property: `"page:{INDEX}"` (once, at a fixed page), `"offset:{N}"` (repeating, every `N`+1 pages), or `"context:{CONTEXT_ID}"` (exactly where a matching `break: "context:{CONTEXT_ID}"` occurs) — combinable with `|`. An insert page can also absorb some of the auto-generated items into itself via its own `area` (plus an optional `template` override, which cannot override `layout`/`break` and does not support `{context:...}`). Useful for category headers or section breaks inside an otherwise auto-flowing list/grid. See [Advanced Templated Items](../experts-api/hidden-features/advanced-templated-items.md).

---

## See Also

- [AGENTS.md](../AGENTS.md) — mental model, full JSON hierarchy, mistake catalog
- [Cookbook](cookbook.md) — task-oriented recipes for real-world scenarios
- [Best Practices & Good to Know](best-practices.md) — practical recommendations and rendering quirks learned from building real pages
- [Start Object](../main-api/start/start-object.md)
- [Menu Item Object](../main-api/menu/menu-item-object.md)
- [Content Root Object](../main-api/content/content-root-object.md)
- [Content Item Object](../main-api/content/content-item-object.md)
- [Actions](../main-api/common/actions.md)
- [Actions Reference](actions-reference.md)
- [Inline Expressions](../main-api/common/inline-expressions.md)
- [Colors](../main-api/common/colors.md)
- [URL Parameters](../experts-api/special/url-parameters.md) — including the `start=menu:json:{BASE64}` web-version test URL
- [Examples Index](../examples/index.md)
