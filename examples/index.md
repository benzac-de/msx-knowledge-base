---
title: Examples Index
category: Examples
summary: Searchable catalog of all 101 MSX JSON example files with extracted metadata for each.
---

# Examples Index

This catalog covers all 101 JSON example files in `docs/examples/`. Each entry lists the top-level type, item types, structural properties, plugins, inline-expression families, and multi-action syntax used — so an LLM can quickly find a suitable template for a given JSON-building task. Descriptions are derived from file content and folder annotations; entries that have a deeper walk-through in the [Cookbook](../reference/cookbook.md) carry a **Cookbook** cross-link with the key mechanism folded into the description.

> **`type` disambiguation:** The **Item types** field lists only values of a *content item's* `type` property (`default`, `teaser`, `button`, `separate`, `space`, `control`) or a *menu item's* `type` (`default`, `separator`, `settings`). Values that belong to a **`live` sub-object** (`schedule`, `lifetime`, `airtime`, `playback`, `setup`) are listed separately under **Live object type** — they are *not* valid content-item `type` values. See [Live Object](../experts-api/live/live-object.md) and [Content Item Object](../main-api/content/content-item-object.md).

**Version annotations:**

The **Version** field states the *functional* minimum — the lowest MSX version on which the file's core structure and actions work correctly. A file may additionally use a handful of properties or inline expressions from higher versions purely for visual polish (e.g. `{chr:...}` character entities, `captionUnderlay`, `navigationSpan`); on an MSX version below those, the expression is shown literally or the property is simply ignored — the layout does not break, it just looks a little less polished here and there. Such purely cosmetic extras are not reflected in the Version field below.

- `guide/` — available since `0.1.0`.
- `github/base/` — available since `0.1.0`, except [github/base/youtube.json](github/base/youtube.json) which requires `0.1.40` (its core action is `video:plugin:{URL}`).
- `github/extended/` — require `0.1.112` or higher for correct operation, except [list3_section0.json](github/extended/list3_section0.json)–[list3_section3.json](github/extended/list3_section3.json) which require `0.1.144` (their tab-switching relies on the `replace:content:` action, which is unrecognized and a no-op — with a warning shown — before `0.1.144`).
- `github/` (root) — available since `0.1.0`.
- `xp/` — experimental tests; minimum version varies per file; use the Experts API documentation to determine version requirements for specific features used.

---

## github/

### [github/menu.json](github/menu.json)

- **Top-level type:** *(menu root — no `type` field)*
- **Item types:** `separator`
- **Description:** Minimal MSX menu root object demonstrating a multi-item menu with separator entries.
- **Related API:** [Menu Root Object](../main-api/menu/menu-root-object.md), [Menu Item Object](../main-api/menu/menu-item-object.md)
- **Version:** `0.1.0`

### [github/welcome.json](github/welcome.json)

- **Top-level type:** *(no `type` field — content root)*
- **Item types:** `default`
- **Inline expressions:** `{ico:...}`, `{txt:...}`, `{chr:...}`, `{br}`
- **Description:** Welcome screen content using `default` items enriched with icon, color-text, character-entity, and line-break inline expressions.
- **Related API:** [Inline Expressions](../main-api/common/inline-expressions.md), [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.0`

---

## github/base/

All four base examples use `type: "pages"` with a `template` property and are the public baseline examples from https://github.com/benzac-de/msx.

### [github/base/audios.json](github/base/audios.json)

- **Top-level type:** `pages`
- **Item types:** `separate`
- **Structural properties:** `template`
- **Description:** Audio content page — one of the cleanest starting points: `template` + `items`, each `"action": "audio:{URL}"`. Audio plays with no video screen; use a `background` property for a backdrop.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md), [Content Item Object](../main-api/content/content-item-object.md)
- **Version:** `0.1.0`
- **Cookbook:** [Media lists](../reference/cookbook.md#media-lists)

### [github/base/images.json](github/base/images.json)

- **Top-level type:** `pages`
- **Item types:** `default`
- **Structural properties:** `template`
- **Description:** Image gallery using `default` items with a template; each item uses `"action": "image:context"`, which shows the item's own `image` property full-screen.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.0`
- **Cookbook:** [Media lists](../reference/cookbook.md#media-lists)

### [github/base/videos.json](github/base/videos.json)

- **Top-level type:** `pages`
- **Item types:** `separate`
- **Structural properties:** `template`
- **Description:** Video content pages using `separate` items and a template; each item `"action": "video:{URL}"` plus a `playerLabel` shown in the player.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md), [Content Item Object](../main-api/content/content-item-object.md)
- **Version:** `0.1.0`
- **Cookbook:** [Media lists](../reference/cookbook.md#media-lists)

### [github/base/youtube.json](github/base/youtube.json)

- **Top-level type:** `pages`
- **Item types:** `separate`
- **Structural properties:** `template`
- **Description:** YouTube integration using `separate` items and a template; each item `"action": "video:plugin:{URL}"` (since `0.1.40`) routes playback through the `youtube.html` plugin, with parameters (`?id=…`) riding in the URL.
- **Related API:** [YouTube, Vimeo & Co.](../extended-api/youtube-vimeo-co.md)
- **Version:** `0.1.40` (core action is `video:plugin:{URL}`, since `0.1.40`)
- **Cookbook:** [Media lists](../reference/cookbook.md#media-lists)

---

## github/extended/

These extended examples require MSX `0.1.112` or higher. They demonstrate advanced list layouts with grouping, enumeration, preloading, selection, image overlays, and multi-action syntax.

### [github/extended/list0.json](github/extended/list0.json)

- **Top-level type:** `list`
- **Item types:** `button`, `default`, `space`, `teaser`
- **Structural properties:** `enumerate`, `group`, `imageOverlay`, `important`, `preload`, `selection`
- **Multi-action syntax:** `[a|b]`
- **Inline expressions:** `{chr:...}`, `{col:...}`, `{txt:...}`
- **Description:** Rich content list — one of the best all-round layout references: a vertical list combining `space`-item page headers with `group: "id:{PAGE_ID}"` (invisible, only resets each page's `enumerate` counter), `enumerate`, `imageOverlay`, `teaser`/`default`/`space`/`button` items, `preload`, `important`, `selection`, and multi-action actions.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md), [Actions](../main-api/common/actions.md)
- **Version:** `0.1.112`
- **Cookbook:** [Part B — Beautiful layouts](../reference/cookbook.md#part-b--beautiful-layouts)

### [github/extended/list1.json](github/extended/list1.json)

- **Top-level type:** `list`
- **Item types:** `button`, `control`, `separate`, `space`
- **Structural properties:** `enumerate`, `group`, `imageOverlay`, `important`, `preload`, `selection`
- **Multi-action syntax:** `[a|b]`
- **Inline expressions:** `{chr:...}`, `{col:...}`, `{txt:...}`
- **Description:** Extended list layout variant adding `control` and `separate` items; same advanced structural features as list0 (`group`, `enumerate`, `imageOverlay`, `preload`, `selection`).
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.112`
- **Cookbook:** [Part B — Beautiful layouts](../reference/cookbook.md#part-b--beautiful-layouts)

### [github/extended/list2.json](github/extended/list2.json)

- **Top-level type:** `list`
- **Item types:** `button`, `control`, `default`, `space`
- **Structural properties:** `enumerate`, `group`, `imageOverlay`, `important`, `preload`, `selection`
- **Multi-action syntax:** `[a|b]`
- **Inline expressions:** `{chr:...}`, `{col:...}`, `{txt:...}`
- **Description:** Extended list variant with `control` and `default` items alongside the full set of advanced structural properties (`group`, `enumerate`, `imageOverlay`, `preload`, `selection`).
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.112`
- **Cookbook:** [Part B — Beautiful layouts](../reference/cookbook.md#part-b--beautiful-layouts)

### [github/extended/list3_section0.json](github/extended/list3_section0.json)

- **Top-level type:** `list`
- **Item types:** `default`, `space`
- **Structural properties:** `enumerate`, `imageOverlay`, `important`, `preload`
- **Multi-action syntax:** `[a|b]`
- **Inline expressions:** `{col:...}`, `{txt:...}`
- **Description:** Section 0 of a four-tab layout (list3) that swaps content in place via `flag: "tabs"` + `[invalidate:content|replace:content:tabs:{URL}]` — no history entry per tab. Demonstrates partial list data split across multiple JSON files, loaded via `preload`.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md), [Replace Action](../experts-api/hidden-features/replace-action.md)
- **Version:** `0.1.144` (tab-switching relies on `replace:content:`, since `0.1.144`)
- **Cookbook:** [Part B — Beautiful layouts](../reference/cookbook.md#part-b--beautiful-layouts)

### [github/extended/list3_section1.json](github/extended/list3_section1.json)

- **Top-level type:** `list`
- **Item types:** `default`, `space`
- **Structural properties:** `enumerate`, `imageOverlay`, `important`, `preload`
- **Multi-action syntax:** `[a|b]`
- **Inline expressions:** `{col:...}`, `{txt:...}`
- **Description:** Section 1 of the four-tab list3 (tab-swap via `flag: "tabs"` + `[invalidate:content|replace:content:tabs:{URL}]`).
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md), [Replace Action](../experts-api/hidden-features/replace-action.md)
- **Version:** `0.1.144` (tab-switching relies on `replace:content:`, since `0.1.144`)
- **Cookbook:** [Part B — Beautiful layouts](../reference/cookbook.md#part-b--beautiful-layouts)

### [github/extended/list3_section2.json](github/extended/list3_section2.json)

- **Top-level type:** `list`
- **Item types:** `default`, `space`
- **Structural properties:** `enumerate`, `imageOverlay`, `important`, `preload`
- **Multi-action syntax:** `[a|b]`
- **Inline expressions:** `{col:...}`, `{txt:...}`
- **Description:** Section 2 of the four-tab list3 (tab-swap via `flag: "tabs"` + `[invalidate:content|replace:content:tabs:{URL}]`).
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md), [Replace Action](../experts-api/hidden-features/replace-action.md)
- **Version:** `0.1.144` (tab-switching relies on `replace:content:`, since `0.1.144`)
- **Cookbook:** [Part B — Beautiful layouts](../reference/cookbook.md#part-b--beautiful-layouts)

### [github/extended/list3_section3.json](github/extended/list3_section3.json)

- **Top-level type:** `list`
- **Item types:** `default`, `space`
- **Structural properties:** `enumerate`, `imageOverlay`, `important`, `preload`
- **Multi-action syntax:** `[a|b]`
- **Inline expressions:** `{col:...}`, `{txt:...}`
- **Description:** Section 3 of the four-tab list3 (tab-swap via `flag: "tabs"` + `[invalidate:content|replace:content:tabs:{URL}]`).
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md), [Replace Action](../experts-api/hidden-features/replace-action.md)
- **Version:** `0.1.144` (tab-switching relies on `replace:content:`, since `0.1.144`)
- **Cookbook:** [Part B — Beautiful layouts](../reference/cookbook.md#part-b--beautiful-layouts)

### [github/extended/list4.json](github/extended/list4.json)

- **Top-level type:** `list`
- **Item types:** `default`, `space`
- **Structural properties:** `important`, `preload`, `selection`, `template`
- **Multi-action syntax:** `[a|b]`
- **Inline expressions:** `{br}`, `{col:...}`, `{txt:...}`
- **Description:** List with footer & caption: `template` + `items` with an `underlay` decoration page, a `footer`, and `captionUnderlay` (since `0.1.153`); uses `{br}` for multi-line text alongside color/text expressions.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md), [Selection Object](../experts-api/selection/selection-object.md)
- **Version:** `0.1.112`
- **Cookbook:** [Part B — Beautiful layouts](../reference/cookbook.md#part-b--beautiful-layouts)

### [github/extended/list5.json](github/extended/list5.json)

- **Top-level type:** `list`
- **Item types:** `default`, `space`
- **Structural properties:** `important`, `preload`, `selection`, `template`
- **Multi-action syntax:** `[a|b]`
- **Inline expressions:** `{br}`, `{col:...}`, `{txt:...}`
- **Description:** List with overlay & narrowed navigation: `overlay` decoration, a `footer`, and `navigationSpan` to narrow the list scroll indicators under the scrollable list column (the `overlay` preview panel occupies the other half of the screen); another template+selection example.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.112`
- **Cookbook:** [Part B — Beautiful layouts](../reference/cookbook.md#part-b--beautiful-layouts)

### [github/extended/pages0.json](github/extended/pages0.json)

- **Top-level type:** `pages`
- **Item types:** `default`, `space`
- **Structural properties:** `enumerate`, `imageOverlay`, `important`, `preload`, `selection`, `template`
- **Multi-action syntax:** `[a|b]`
- **Inline expressions:** `{txt:...}`
- **Description:** Paged (horizontal) layout combining `template` + `underlay` + `selection` with enumeration, image overlays, preloading, and multi-action actions.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.112`
- **Cookbook:** [Part B — Beautiful layouts](../reference/cookbook.md#part-b--beautiful-layouts)

### [github/extended/pages1.json](github/extended/pages1.json)

- **Top-level type:** `pages`
- **Item types:** `default`, `teaser`
- **Structural properties:** `important`, `preload`
- **Multi-action syntax:** `[a|b]`
- **Inline expressions:** `{txt:...}`
- **Description:** Paged (horizontal) layout with `teaser` items across pages, importance marking, preloading, and multi-action actions; simpler variant without template.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.112`
- **Cookbook:** [Part B — Beautiful layouts](../reference/cookbook.md#part-b--beautiful-layouts)

### [github/extended/sublist0.json](github/extended/sublist0.json)

- **Top-level type:** `list`
- **Item types:** `default`
- **Structural properties:** `preload`, `template`
- **Inline expressions:** `{col:...}`
- **Description:** Nested sub-list (rows within rows) loaded as a section of a parent list for catalog-style browsing; uses `template` + `items` and color expressions.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.112`
- **Cookbook:** [Part B — Beautiful layouts](../reference/cookbook.md#part-b--beautiful-layouts)

### [github/extended/sublist1.json](github/extended/sublist1.json)

- **Top-level type:** `list`
- **Item types:** `separate`
- **Structural properties:** `preload`, `template`
- **Inline expressions:** `{col:...}`
- **Description:** Nested sub-list variant with `separate` items in a sub-navigable list (rows within rows) for catalog-style browsing.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.112`
- **Cookbook:** [Part B — Beautiful layouts](../reference/cookbook.md#part-b--beautiful-layouts)

### [github/extended/sublist3.json](github/extended/sublist3.json)

- **Top-level type:** `list`
- **Item types:** `default`
- **Structural properties:** `preload`, `template`
- **Inline expressions:** `{col:...}`
- **Description:** Third nested sub-list variant (rows within rows); similar to sublist0 with `template` and `preload`.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.112`
- **Cookbook:** [Part B — Beautiful layouts](../reference/cookbook.md#part-b--beautiful-layouts)

---

## guide/

All guide examples are available since `0.1.0`.

### [guide/actions.json](guide/actions.json)

- **Top-level type:** *(no `type` field — start or menu root)*
- **Description:** A menu grouping the other guide example categories (Videos, Audios, Images, Links, Mixed). Despite the filename, this file itself contains no `action` strings — it only links via `data` to the content files that actually demonstrate action usage.
- **Related API:** [Actions](../main-api/common/actions.md), [Menu Root Object](../main-api/menu/menu-root-object.md)
- **Version:** `0.1.0`

### [guide/audios.json](guide/audios.json)

- **Top-level type:** `pages`
- **Item types:** `separate`
- **Structural properties:** `template`
- **Description:** Audio playback content using `separate` items with a template for each audio entry.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.0`

### [guide/icon_names.json](guide/icon_names.json)

- **Top-level type:** `pages`
- **Item types:** `default`
- **Structural properties:** `template`
- **Inline expressions:** `{context:...}`
- **Description:** Showcases icon names via template context expressions; each item references `{context:label}` to display its own icon name.
- **Related API:** [Icons](../main-api/common/icons.md), [Inline Expressions](../main-api/common/inline-expressions.md)
- **Version:** `0.1.0`

### [guide/icon_sizes.json](guide/icon_sizes.json)

- **Top-level type:** `pages`
- **Item types:** `button`
- **Description:** Demonstrates different icon sizes available in MSX using `button` items.
- **Related API:** [Icons](../main-api/common/icons.md)
- **Version:** `0.1.0`

### [guide/icons.json](guide/icons.json)

- **Top-level type:** `pages`
- **Item types:** `default`
- **Structural properties:** `template`
- **Inline expressions:** `{context:...}`
- **Description:** Icon showcase using template iteration with `{context:...}` expressions to display all available icons.
- **Related API:** [Icons](../main-api/common/icons.md)
- **Version:** `0.1.0`

### [guide/image_filler.json](guide/image_filler.json)

- **Top-level type:** `pages`
- **Item types:** `default`
- **Inline expressions:** `{br}`
- **Description:** Demonstrates the image filler feature using `{br}` for multi-line text alongside images.
- **Related API:** [Content Item Object](../main-api/content/content-item-object.md)
- **Version:** `0.1.0`

### [guide/image_height.json](guide/image_height.json)

- **Top-level type:** `pages`
- **Item types:** `button`, `default`
- **Inline expressions:** `{txt:...}`
- **Description:** Demonstrates image height configuration options across different item types.
- **Related API:** [Content Item Object](../main-api/content/content-item-object.md)
- **Version:** `0.1.0`

### [guide/image_width.json](guide/image_width.json)

- **Top-level type:** `pages`
- **Item types:** `button`, `default`
- **Inline expressions:** `{txt:...}`
- **Description:** Demonstrates image width configuration options alongside colored text expressions.
- **Related API:** [Content Item Object](../main-api/content/content-item-object.md)
- **Version:** `0.1.0`

### [guide/images.json](guide/images.json)

- **Top-level type:** `pages`
- **Item types:** `default`
- **Structural properties:** `template`
- **Description:** Simple image gallery using `default` items with a template.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.0`

### [guide/links.json](guide/links.json)

- **Top-level type:** `pages`
- **Item types:** `separate`
- **Structural properties:** `template`
- **Description:** Demonstrates external link items using `separate` items with a template for each link entry.
- **Related API:** [Content Item Object](../main-api/content/content-item-object.md)
- **Version:** `0.1.0`

### [guide/list.json](guide/list.json)

- **Top-level type:** `list`
- **Item types:** `button`
- **Inline expressions:** `{txt:...}`
- **Description:** Minimal list content example using `button` items with colored text expressions.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.0`

### [guide/menu.json](guide/menu.json)

- **Top-level type:** *(menu root — no `type` field)*
- **Item types:** `separator`
- **Description:** Basic menu structure example with `default` (unset `type`) and `separator` menu items. The "Panel" entry's `data` is an embedded Content Root Object (`type: "pages"`) containing one `button` content item — that `pages`/`button` pairing belongs to the embedded content, not to the menu items themselves.
- **Related API:** [Menu Root Object](../main-api/menu/menu-root-object.md), [Menu Item Object](../main-api/menu/menu-item-object.md)
- **Version:** `0.1.0`

### [guide/messages.json](guide/messages.json)

- **Top-level type:** `pages`
- **Item types:** `button`
- **Description:** Demonstrates message/dialog actions triggered from `button` items.
- **Related API:** [Actions](../main-api/common/actions.md)
- **Version:** `0.1.0`

### [guide/mixed.json](guide/mixed.json)

- **Top-level type:** `pages`
- **Item types:** `default`, `separate`
- **Structural properties:** `enumerate`
- **Description:** Mixed content with `default` and `separate` items, demonstrating the `enumerate` property.
- **Related API:** [Content Page Object](../main-api/content/content-page-object.md)
- **Version:** `0.1.0`

### [guide/pages.json](guide/pages.json)

- **Top-level type:** `pages`
- **Item types:** `button`
- **Inline expressions:** `{txt:...}`
- **Description:** Minimal pages content with `button` items and colored text expressions.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.0`

### [guide/panel.json](guide/panel.json)

- **Top-level type:** *(no `type` field — panel/start context)*
- **Item types:** `button`
- **Inline expressions:** `{txt:...}`
- **Description:** Panel layout example with `button` items and colored text; demonstrates panel-style content presentation.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.0`

### [guide/panel_wrapped.json](guide/panel_wrapped.json)

- **Top-level type:** `pages`
- **Item types:** `button`
- **Inline expressions:** `{txt:...}`
- **Description:** Wrapped panel variant inside a `pages` root; demonstrates panel layout within a standard pages structure.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.0`

### [guide/references.json](guide/references.json)

- **Top-level type:** `pages`
- **Item types:** `button`
- **Structural properties:** `enumerate`, `template`
- **Description:** Demonstrates content item references using template and enumeration to link items across pages.
- **Related API:** [Content Item Object](../main-api/content/content-item-object.md)
- **Version:** `0.1.0`

### [guide/special.json](guide/special.json)

- **Top-level type:** `pages`
- **Item types:** `button`, `default`, `space`
- **Description:** Special MSX features showcase using a mix of `button`, `default`, and `space` item types.
- **Related API:** [Content Item Object](../main-api/content/content-item-object.md)
- **Version:** `0.1.0`

### [guide/stacked_list.json](guide/stacked_list.json)

- **Top-level type:** `list`
- **Item types:** `button`
- **Description:** Stacked list layout demonstrating vertically stacked `button` items.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.0`

### [guide/template.json](guide/template.json)

- **Top-level type:** `pages`
- **Item types:** `button`
- **Description:** Basic template usage demonstration with `button` items.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md), [Content Item Object](../main-api/content/content-item-object.md)
- **Version:** `0.1.0`

### [guide/template_item.json](guide/template_item.json)

- **Top-level type:** `pages`
- **Item types:** `separate`
- **Description:** Template item example using `separate` items to demonstrate per-item template application.
- **Related API:** [Content Item Object](../main-api/content/content-item-object.md)
- **Version:** `0.1.0`

### [guide/template_list.json](guide/template_list.json)

- **Top-level type:** `list`
- **Item types:** `separate`
- **Structural properties:** `template`
- **Description:** Template applied to a `list` type with `separate` items; shows template reuse in list layout.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.0`

### [guide/template_pages.json](guide/template_pages.json)

- **Top-level type:** `pages`
- **Item types:** `separate`
- **Structural properties:** `template`
- **Description:** Template in a `pages` layout with `separate` items.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.0`

### [guide/template_panel.json](guide/template_panel.json)

- **Top-level type:** `pages`
- **Item types:** `button`, `separate`
- **Structural properties:** `template`
- **Description:** Template applied to a panel-style pages structure mixing `button` and `separate` items.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.0`

### [guide/text.json](guide/text.json)

- **Top-level type:** `pages`
- **Item types:** `default`
- **Inline expressions:** `{ico:...}`, `{txt:...}`, `{br}`, `{sp}`, `{tb}`
- **Description:** Comprehensive text formatting showcase: icons, colored text, line breaks, spaces, and tabs inside `default` items.
- **Related API:** [Inline Expressions](../main-api/common/inline-expressions.md)
- **Version:** `0.1.0`

### [guide/text_image.json](guide/text_image.json)

- **Top-level type:** `pages`
- **Item types:** `button`, `default`, `separate`, `space`, `teaser`
- **Description:** Combined text and image example using five of the six standard item types (all but `control`, which was originally reserved for internal use and only later saw general adoption).
- **Related API:** [Content Item Object](../main-api/content/content-item-object.md)
- **Version:** `0.1.0`

### [guide/types.json](guide/types.json)

- **Top-level type:** `pages`
- **Item types:** `button`, `default`, `separate`, `space`, `teaser`
- **Description:** Demonstrates five of the six standard content item types side by side (all but `control`, which was originally reserved for internal use and only later saw general adoption).
- **Related API:** [Content Item Object](../main-api/content/content-item-object.md)
- **Version:** `0.1.0`

### [guide/videos.json](guide/videos.json)

- **Top-level type:** `pages`
- **Item types:** `separate`
- **Structural properties:** `template`
- **Description:** Video gallery using `separate` items with a template for each video entry.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md)
- **Version:** `0.1.0`

---

## xp/

Experimental tests. Minimum version requirements depend on the specific features used; consult the Experts API documentation for each feature.

### [xp/android_test.json](xp/android_test.json)

- **Top-level type:** *(no `type` field)*
- **Description:** Android-specific integration test for the Android Player feature.
- **Related API:** [Android Player](../experts-api/special/android-player.md), [Android Plugin](../experts-api/plugins/android-plugin.md)

### [xp/benchmark_test_1.json](xp/benchmark_test_1.json)

- **Top-level type:** `pages`
- **Item types:** `separate`
- **Structural properties:** `template`
- **Inline expressions:** `{ico:...}`, `{txt:...}`
- **Description:** Benchmark performance test 1 using a templated pages layout with icon and text expressions.
- **Related API:** [Renderer Plugin](../experts-api/benchmark/renderer-plugin.md)

### [xp/benchmark_test_2.json](xp/benchmark_test_2.json)

- **Top-level type:** `pages`
- **Item types:** `default`
- **Live object type:** `playback`
- **Structural properties:** `live`
- **Inline expressions:** `{ico:...}`, `{txt:...}`
- **Description:** Benchmark test 2: a `default` item whose embedded `live` object (`type: "playback"`) tracks the running plugin video for performance measurement. Note: `playback` is the type of the `live` sub-object, not of the content item.
- **Related API:** [Live Object](../experts-api/live/live-object.md), [Renderer Plugin](../experts-api/benchmark/renderer-plugin.md)

### [xp/benchmark_test_3.json](xp/benchmark_test_3.json)

- **Top-level type:** `pages`
- **Item types:** `separate`
- **Structural properties:** `template`
- **Description:** Benchmark performance test 3 with templated `separate` items.
- **Related API:** [Renderer Plugin](../experts-api/benchmark/renderer-plugin.md)

### [xp/hidden_feature_1.json](xp/hidden_feature_1.json)

- **Top-level type:** *(no `type` field)*
- **Description:** Demonstrates hidden feature 1 (start action or menu-level hidden feature).
- **Related API:** [Start Action](../experts-api/hidden-features/start-action.md)

### [xp/hidden_feature_2.json](xp/hidden_feature_2.json)

- **Top-level type:** *(no `type` field — menu root)*
- **Item types:** `separator`, `settings`
- **Description:** Demonstrates the Settings Menu Item hidden feature: a menu with `settings` items and separators.
- **Related API:** [Settings Menu Item](../experts-api/hidden-features/settings-menu-item.md)

### [xp/hidden_feature_3.json](xp/hidden_feature_3.json)

- **Top-level type:** `pages`
- **Item types:** `separate`
- **Structural properties:** `execute`, `template`
- **Description:** Demonstrates the Execute Property hidden feature with `execute` triggering side effects on item activation.
- **Related API:** [Execute Property](../experts-api/hidden-features/execute-property.md)

### [xp/hidden_feature_4.json](xp/hidden_feature_4.json)

- **Top-level type:** `pages`
- **Item types:** `button`
- **Description:** Focus separator hidden feature demonstration using `button` items.
- **Related API:** [Focus Separator](../experts-api/hidden-features/focus-separator.md)

### [xp/hidden_feature_5.json](xp/hidden_feature_5.json)

- **Top-level type:** `pages`
- **Item types:** `default`
- **Structural properties:** `template`
- **Description:** Hide Images hidden feature: `default` items in a template layout where image display is controlled.
- **Related API:** [Hide Images](../experts-api/hidden-features/hide-images.md)

### [xp/hidden_feature_6.json](xp/hidden_feature_6.json)

- **Top-level type:** `pages`
- **Item types:** `separate`
- **Structural properties:** `template`
- **Inline expressions:** `{num:...}`, `{txt:...}`
- **Description:** Number Inline Expressions hidden feature: demonstrates `{num:...}` expressions for numeric formatting.
- **Related API:** [Number Inline Expressions](../experts-api/hidden-features/number-inline-expressions.md)

### [xp/hidden_feature_7.json](xp/hidden_feature_7.json)

- **Top-level type:** *(no `type` field)*
- **Inline expressions:** `{br}`, `{col:...}`, `{ico:...}`, `{txt:...}`
- **Description:** Color Inline Expressions hidden feature: standalone content demonstrating `{col:...}` color expressions.
- **Related API:** [Color Inline Expressions](../experts-api/hidden-features/color-inline-expressions.md)

### [xp/hidden_feature_8.json](xp/hidden_feature_8.json)

- **Top-level type:** *(no `type` field)*
- **Item types:** `button`, `space`
- **Inline expressions:** `{ico:...}`, `{txt:...}`
- **Description:** Key Property hidden feature: demonstrates the `key` property on items for keyboard shortcut binding.
- **Related API:** [Key Property](../experts-api/hidden-features/key-property.md)

### [xp/hidden_feature_9.json](xp/hidden_feature_9.json)

- **Top-level type:** `list`
- **Item types:** `separate`, `space`
- **Structural properties:** `imageOverlay`
- **Description:** Large Offsets hidden feature: list with `separate` and `space` items using `imageOverlay`.
- **Related API:** [Large Offsets](../experts-api/hidden-features/large-offsets.md), [Image Options](../experts-api/hidden-features/image-options.md)

### [xp/hidden_feature_10.json](xp/hidden_feature_10.json)

- **Top-level type:** `pages`
- **Description:** Page Action hidden feature: demonstrates triggering actions at page navigation level.
- **Related API:** [Page Action](../experts-api/hidden-features/page-action.md)

### [xp/hidden_feature_11.json](xp/hidden_feature_11.json)

- **Top-level type:** *(no `type` field)*
- **Inline expressions:** `{br}`, `{col:...}`, `{dic:...}`, `{dix:...}`, `{ico:...}`, `{txt:...}`
- **Description:** Dictionary Inline Expressions hidden feature: demonstrates `{dic:...}` and `{dix:...}` expressions for value lookup.
- **Related API:** [Dictionary Inline Expressions](../experts-api/hidden-features/dictionary-inline-expressions.md)

### [xp/hidden_feature_12.json](xp/hidden_feature_12.json)

- **Top-level type:** `list`
- **Item types:** `button`, `control`, `space`
- **Structural properties:** `compress`, `template`
- **Description:** Compress Property hidden feature: uses `compress: true` to expand the layout grid from 12×6 to 16×8, demonstrating the extended grid capacity. Requires `0.1.123` or higher.
- **Related API:** [Compress Property](../experts-api/hidden-features/compress-property.md)
- **Version:** `0.1.123`

### [xp/hidden_feature_13.json](xp/hidden_feature_13.json)

- **Top-level type:** `list`
- **Item types:** `separate`
- **Structural properties:** `template`
- **Description:** Resolve Action hidden feature (resolve since `0.1.107`): asks a server for the actual media URL at playback time — used to serve a platform-specific codec or to mint temporary/token-protected links. Resolves `video:`/`audio:` URLs via a server (`video:resolve:{URL}`) and via the Interaction Plugin (`video:resolve:request:interaction:...@resolve.html`). No `video:plugin:`/`audio:plugin:` action is used, so this does not demonstrate the Video/Audio Plugin itself.
- **Related API:** [Resolve Action](../experts-api/hidden-features/resolve-action.md), [Interaction Plugin](../experts-api/plugins/interaction-plugin.md)
- **Cookbook:** [Adaptive & dynamic playback](../reference/cookbook.md#adaptive--dynamic-playback)

### [xp/hidden_feature_14.json](xp/hidden_feature_14.json)

- **Top-level type:** `list`
- **Item types:** `control`, `separate`
- **Structural properties:** `enumerate`, `options`, `template`
- **Multi-action syntax:** `[a|b]`
- **Inline expressions:** `{context:...}`, `{ico:...}`, `{tb}`, `{txt:...}`
- **Description:** Option Shortcut hidden feature: items inside an `options` panel get a `key` property so a single remote key triggers them directly (very common in practice for quick actions) — here `control` items with `options` and multi-action, selected via `{context:...}` expressions.
- **Related API:** [Option Shortcut](../experts-api/hidden-features/option-shortcut.md)
- **Cookbook:** [Interaction & UX](../reference/cookbook.md#interaction--ux)

### [xp/hidden_feature_15_section1.json](xp/hidden_feature_15_section1.json)

- **Top-level type:** `list`
- **Item types:** `default`, `space`
- **Structural properties:** `imageOverlay`, `preload`, `template`
- **Inline expressions:** `{col:...}`
- **Description:** Replace Action hidden feature — section 1: list section loaded dynamically via the replace action pattern.
- **Related API:** [Replace Action](../experts-api/hidden-features/replace-action.md)

### [xp/hidden_feature_15_section2.json](xp/hidden_feature_15_section2.json)

- **Top-level type:** `list`
- **Item types:** `default`, `space`
- **Structural properties:** `imageOverlay`, `preload`, `template`
- **Inline expressions:** `{col:...}`
- **Description:** Replace Action — section 2.
- **Related API:** [Replace Action](../experts-api/hidden-features/replace-action.md)

### [xp/hidden_feature_15_section3.json](xp/hidden_feature_15_section3.json)

- **Top-level type:** `list`
- **Item types:** `default`, `space`
- **Structural properties:** `imageOverlay`, `preload`, `template`
- **Inline expressions:** `{col:...}`
- **Description:** Replace Action — section 3.
- **Related API:** [Replace Action](../experts-api/hidden-features/replace-action.md)

### [xp/hidden_feature_15_section4.json](xp/hidden_feature_15_section4.json)

- **Top-level type:** `list`
- **Item types:** `default`, `space`
- **Structural properties:** `imageOverlay`, `preload`, `template`
- **Inline expressions:** `{col:...}`
- **Description:** Replace Action — section 4.
- **Related API:** [Replace Action](../experts-api/hidden-features/replace-action.md)

### [xp/hidden_feature_16.json](xp/hidden_feature_16.json)

- **Top-level type:** `pages`
- **Item types:** `default`
- **Structural properties:** `template`
- **Description:** Image Options hidden feature: a simple image list that enables the image options panel via `properties` (`"image:options": true`, `"image:icon"`, `"image:action"`), letting the user rotate/adjust the shown image; applied via template.
- **Related API:** [Image Options](../experts-api/hidden-features/image-options.md)
- **Cookbook:** [Interaction & UX](../reference/cookbook.md#interaction--ux)

### [xp/hidden_feature_17.json](xp/hidden_feature_17.json)

- **Top-level type:** `pages`
- **Item types:** `control`, `default`, `separate`
- **Structural properties:** `imageOverlay`
- **Inline expressions:** `{br}`, `{col:...}`, `{ico:...}`
- **Description:** Advanced Text & Image hidden feature: pages mixing `control`, `default`, and `separate` items with image overlays and formatted text.
- **Related API:** [Advanced Text & Image](../experts-api/hidden-features/advanced-text-image.md)

### [xp/hidden_feature_18.json](xp/hidden_feature_18.json)

- **Top-level type:** `pages`
- **Item types:** `control`, `default`, `space`
- **Structural properties:** `important`, `options`, `selection`, `template`
- **Multi-action syntax:** `[a|b]`
- **Inline expressions:** `{col:...}`, `{context:...}`, `{ico:...}`
- **Description:** Keep Ratio Keyword hidden feature: pages with template, selection, and options; demonstrates `keep-ratio` in image configuration.
- **Related API:** [Keep Ratio Keyword](../experts-api/hidden-features/keep-ratio.md)

### [xp/hidden_feature_19.json](xp/hidden_feature_19.json)

- **Top-level type:** `list`
- **Item types:** `button`, `default`, `space`
- **Structural properties:** `group`, `selection`, `template`
- **Inline expressions:** `{context:...}`
- **Description:** Advanced Templated Items hidden feature: list with grouping, selection, and `{context:...}` for advanced template-driven content.
- **Related API:** [Advanced Templated Items](../experts-api/hidden-features/advanced-templated-items.md)

### [xp/live_test_1.json](xp/live_test_1.json)

- **Top-level type:** `pages`
- **Item types:** `button`
- **Live object type:** `lifetime`
- **Structural properties:** `execute`, `live`, `template`
- **Inline expressions:** `{br}`
- **Description:** Live API test 1 — the clearest introduction to the live state machine: a `lifetime` `live` object (relative `delay` → `duration`) walks through `coming` → `running` → `over`, each state firing its own `execute.action`. Here `lifetime` is the type of the `live` sub-object; the content item is a `button`.
- **Related API:** [Live Object](../experts-api/live/live-object.md), [Live Examples](../experts-api/live/live-examples.md)
- **Cookbook:** [Live data (EPG, progress, countdowns)](../reference/cookbook.md#live-data-epg-progress-countdowns)

### [xp/live_test_2.json](xp/live_test_2.json)

- **Top-level type:** `pages`
- **Item types:** `button`, `separate`
- **Live object type:** `playback`
- **Structural properties:** `enumerate`, `live`, `template`
- **Description:** Live API test 2: a `playback` `live` object bound to the running video shows live progress, combined with `resume:clear` / a `resume:key` so positions persist across sessions. `playback` is the `live` sub-object type; the items are `button`/`separate`.
- **Related API:** [Live Object](../experts-api/live/live-object.md)
- **Cookbook:** [Live data (EPG, progress, countdowns)](../reference/cookbook.md#live-data-epg-progress-countdowns)

### [xp/live_test_3.json](xp/live_test_3.json)

- **Top-level type:** `pages`
- **Item types:** `button`, `separate`, `space`
- **Live object type:** `playback`
- **Structural properties:** `enumerate`, `live`, `selection`, `template`
- **Inline expressions:** `{context:...}`, `{ico:...}`
- **Description:** Live API test 3: a fuller "now playing" screen — adds a `selection` object and an `overlay` page on top of the `playback`+resume pattern from test 2. `playback` is the `live` sub-object type.
- **Related API:** [Live Object](../experts-api/live/live-object.md), [Selection Object](../experts-api/selection/selection-object.md)
- **Cookbook:** [Live data (EPG, progress, countdowns)](../reference/cookbook.md#live-data-epg-progress-countdowns)

### [xp/live_test_4.json](xp/live_test_4.json)

- **Top-level type:** *(no `type` field — menu root)*
- **Item types:** `separator`
- **Inline expressions:** `{ico:...}`, `{tb}`
- **Description:** Live test 4 — a live-TV / EPG shell: a menu root whose `extension` property shows a live clock (live + icon + tab expressions), and whose menu items' `data` point to server services (`live.php`, `guide.php`) that build the actual channel/EPG content with `schedule`/`setup` `live` objects. The file itself is only the shell; the reusable server pattern (self-sustaining EPG via `setup` → `execute:service:` → `update` + `schedule` with an `over` action that fetches the next program) is detailed in the Cookbook deep dives.
- **Related API:** [Live Object](../experts-api/live/live-object.md)
- **Cookbook:** [Live channels / EPG backend](../reference/cookbook.md#deep-dive--the-live-channels--epg-backend-live_test_4) · [TV Guide (EPG grid) backend](../reference/cookbook.md#deep-dive--the-tv-guide-epg-grid-backend-guidephp)

### [xp/netcast_test.json](xp/netcast_test.json)

- **Top-level type:** `list`
- **Item types:** `separate`
- **Structural properties:** `template`
- **Description:** LG NetCast integration test: list of `separate` items loaded via the NetCast-specific setup.
- **Related API:** [NetCast Menu](../experts-api/special/netcast-menu.md)

### [xp/next_video_test.json](xp/next_video_test.json)

- **Top-level type:** `list`
- **Item types:** `separate`, `space`
- **Live object type:** `playback`
- **Structural properties:** `enumerate`, `imageOverlay`, `live`, `template`
- **Multi-action syntax:** `[a|b]`
- **Inline expressions:** `{col:...}`, `{txt:...}`
- **Description:** Auto-jump to the next video: a `playback` `live` object plus a `player:goto` action and a `ready` action (fires on every fresh display, not just startup — see [Glossary: Ready](../reference/glossary.md#ready)) move focus/playback to the next item automatically — a good template for "play all" / autoplay-next behavior. `playback` is the `live` sub-object type; the items are `separate`/`space`.
- **Related API:** [Live Object](../experts-api/live/live-object.md), [Actions](../main-api/common/actions.md)
- **Cookbook:** [Adaptive & dynamic playback](../reference/cookbook.md#adaptive--dynamic-playback)

### [xp/plugin_test_1.json](xp/plugin_test_1.json)

- **Top-level type:** `list`
- **Item types:** `separate`
- **Structural properties:** `template`
- **Inline expressions:** `{txt:...}`
- **Description:** Video plugins overview — one page wiring up many ready-made video plugins (`youtube.html`, `vimeo.html`, `twitch.html`, `dailymotion.html`, `facebook.html`, `soundcloud.html`, `wistia.html`, `html5.html`, `template.html`). The go-to reference for "which plugin URL do I use for source X". Basic list with `separate` items and text expressions.
- **Related API:** [Video/Audio Plugin](../experts-api/plugins/video-audio-plugin.md)
- **Cookbook:** [Plugins (media, immersive, platform, ads)](../reference/cookbook.md#plugins-media-immersive-platform-ads)

### [xp/plugin_test_2.json](xp/plugin_test_2.json)

- **Top-level type:** `list`
- **Item types:** `button`, `separate`
- **Structural properties:** `enumerate`, `template`
- **Description:** Interaction Plugin: `interaction:load/reload/unload` and `content:request:interaction:...` to a headless background handler page (Template, Search, Settings, Observer). The JS handler contract (`handleRequest`/`handleData` → `reload:content` round-trip for stateless, server-less UIs) is walked through in the Cookbook deep dive. Enumerated list of `separate` and `button` items.
- **Related API:** [Interaction Plugin](../experts-api/plugins/interaction-plugin.md)
- **Cookbook:** [Deep dive — building an interaction plugin](../reference/cookbook.md#deep-dive--building-an-interaction-plugin-plugin_test_2)

### [xp/plugin_test_3.json](xp/plugin_test_3.json)

- **Top-level type:** `pages`
- **Item types:** `separate`
- **Structural properties:** `template`
- **Description:** Image plugin in a mixed playlist: mixes `video:{URL}`, `audio:{URL}`, and the `image.html` plugin (`video:plugin:`) so images, audio and video sit together in one playlist. Pages layout with `separate` items.
- **Related API:** [Image Plugin](../experts-api/plugins/image-plugin.md)
- **Cookbook:** [Adaptive & dynamic playback](../reference/cookbook.md#adaptive--dynamic-playback)

### [xp/plugin_test_4.json](xp/plugin_test_4.json)

- **Top-level type:** `pages`
- **Item types:** `separate`
- **Structural properties:** `template`
- **Inline expressions:** `{context:...}`
- **Description:** HTML5X Plugin (build your own): `video:plugin:...html5x.html` with audio/subtitle tracks configured via `html5x:*` item `properties` (multi-language `html5x:subtitle:{lang}:{Label}` tracks) and a content button opening a `panel:request:player:options` menu. A great reference for writing your own video plugin — the JS player contract (`setupPlayer`, media-event → `TVXVideoPlugin` mapping, `player:commit:message:` round-trip) is in the Cookbook deep dive. Pages with template and `{context:...}`.
- **Related API:** [HTML5X Plugin](../experts-api/plugins/html5x-plugin.md)
- **Cookbook:** [Deep dive — building a video plugin (HTML5X)](../reference/cookbook.md#deep-dive--building-a-video-plugin-plugin_test_4-html5x)

### [xp/plugin_test_5.json](xp/plugin_test_5.json)

- **Top-level type:** `pages`
- **Item types:** `default`
- **Structural properties:** `group`, `template`
- **Inline expressions:** `{br}`, `{col:...}`, `{ico:...}`, `{txt:...}`
- **Description:** Panorama Plugin: `video:plugin:...panorama.html` displays a wide (>16:9, e.g. 2:1) panorama *image* as a sliding pan (not a video, despite the `video:plugin:` mechanism); template with `default` items and rich inline expressions.
- **Related API:** [Panorama Plugin](../experts-api/plugins/panorama-plugin.md)
- **Cookbook:** [Plugins (media, immersive, platform, ads)](../reference/cookbook.md#plugins-media-immersive-platform-ads)

### [xp/plugin_test_6.json](xp/plugin_test_6.json)

- **Top-level type:** *(no `type` field — content root, defaults to `pages`)*
- **Description:** Plugin test 6, part 1 of 3 (Background Plugin demo): a content root with a single "Load Test" item whose `menu:` action loads plugin_test_6_menu.json. Not a Start Object despite being the entry point of the demo.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md), [Background Plugin](../experts-api/plugins/background-plugin.md)

### [xp/plugin_test_6_content.json](xp/plugin_test_6_content.json)

- **Top-level type:** *(no `type` field — content root, defaults to `pages`)*
- **Item types:** `default`
- **Structural properties:** `template`
- **Inline expressions:** `{col:...}`, `{context:...}`, `{ico:...}`
- **Description:** Plugin test 6, part 3 of 3: the content behind the transparent menu — `video:auto:plugin:...background.html` with template and `{context:...}` expressions (including a `{context:controlTransparent}`-fed `control:transparent`), loaded via a menu item's `data` from plugin_test_6_menu.json.
- **Related API:** [Content Root Object](../main-api/content/content-root-object.md), [Background Plugin](../experts-api/plugins/background-plugin.md)
- **Cookbook:** [Plugins (media, immersive, platform, ads)](../reference/cookbook.md#plugins-media-immersive-platform-ads)

### [xp/plugin_test_6_menu.json](xp/plugin_test_6_menu.json)

- **Top-level type:** *(no `type` field — menu root)*
- **Description:** Plugin test 6, part 2 of 3 (Background Plugin demo — a subtle full-screen looping video behind a transparent menu, a real "wow" effect): a menu root loaded via `menu:` from plugin_test_6.json; each menu item's `data` loads plugin_test_6_content.json. The `ready` action calls `execute:service:video:data:...background.php` to auto-execute a server-driven background video on load.
- **Related API:** [Menu Root Object](../main-api/menu/menu-root-object.md), [Background Plugin](../experts-api/plugins/background-plugin.md)
- **Cookbook:** [Plugins (media, immersive, platform, ads)](../reference/cookbook.md#plugins-media-immersive-platform-ads)

### [xp/plugin_test_7.json](xp/plugin_test_7.json)

- **Top-level type:** *(no `type` field)*
- **Description:** MRSS + Ad plugin: loads an MRSS feed via the `reference` property (`interaction/mrss.html`), combined with the Ad Plugin (`ad.html`) for ad insertion, using a pipe-separated compound `request:interaction:` reference.
- **Related API:** [MRSS Feeds](../extended-api/mrss-feeds.md), [Interaction Plugin](../experts-api/plugins/interaction-plugin.md), [Ad Plugin](../experts-api/plugins/ad-plugin.md)
- **Cookbook:** [Plugins (media, immersive, platform, ads)](../reference/cookbook.md#plugins-media-immersive-platform-ads)

### [xp/plugin_test_8.json](xp/plugin_test_8.json)

- **Top-level type:** `list`
- **Item types:** `default`, `space`
- **Structural properties:** `important`, `preload`, `selection`, `template`
- **Multi-action syntax:** `[a|b]`
- **Inline expressions:** `{br}`, `{col:...}`, `{context:...}`
- **Description:** Backdrop Plugin — a high-impact visual: a cross-fading backdrop behind content, loaded via `interaction:load:.../backdrop.html` on item selection (`underlay`, `selection`, `delay:backdrop`). List with selection, preload, importance, template, multi-action, and context expressions.
- **Related API:** [Backdrop Plugin](../experts-api/plugins/backdrop-plugin.md), [Interaction Plugin](../experts-api/plugins/interaction-plugin.md), [Selection Object](../experts-api/selection/selection-object.md)
- **Cookbook:** [Plugins (media, immersive, platform, ads)](../reference/cookbook.md#plugins-media-immersive-platform-ads)

### [xp/plugin_test_9.json](xp/plugin_test_9.json)

- **Top-level type:** `list`
- **Item types:** `separate`
- **Live object type:** `playback`
- **Structural properties:** `live`, `template`
- **Description:** Plugin test 9: the native player path for Android/Fire TV devices — `video:plugin:` loading `android.html` — with a `playback` `live` object driving live-updating playback progress. `playback` is the `live` sub-object type; the items are `separate`.
- **Related API:** [Live Object](../experts-api/live/live-object.md), [Android Plugin](../experts-api/plugins/android-plugin.md)
- **Cookbook:** [Plugins (media, immersive, platform, ads)](../reference/cookbook.md#plugins-media-immersive-platform-ads)

### [xp/plugin_test_10.json](xp/plugin_test_10.json)

- **Top-level type:** `list`
- **Item types:** `separate`
- **Live object type:** `playback`
- **Structural properties:** `live`, `template`
- **Description:** Plugin test 10 — runtime player selection: an interaction plugin picks the right player at playback time via `video:resolve:request:interaction:{URL}@.../interaction/play.html`. Each item also carries a `playback` `live` object for live progress and a `properties.resume:key` so the position resumes. `playback` is the `live` sub-object type; the items are `separate`.
- **Related API:** [Live Object](../experts-api/live/live-object.md), [Play Plugin](../experts-api/plugins/play-plugin.md), [Resolve Action](../experts-api/hidden-features/resolve-action.md)
- **Cookbook:** [Adaptive & dynamic playback](../reference/cookbook.md#adaptive--dynamic-playback)

### [xp/plugin_test_11.json](xp/plugin_test_11.json)

- **Top-level type:** `list`
- **Item types:** `control`
- **Structural properties:** `compress`, `template`
- **Description:** IMA ads: `video:plugin:...ima.html` to insert Google IMA advertising into your content (most of the ad setup is done server-side). Uses `compress: true` for the extended 16×8 grid with `control` items. Requires `0.1.123` or higher.
- **Related API:** [Compress Property](../experts-api/hidden-features/compress-property.md), [IMA Plugin](../experts-api/plugins/ima-plugin.md)
- **Cookbook:** [Plugins (media, immersive, platform, ads)](../reference/cookbook.md#plugins-media-immersive-platform-ads)
- **Version:** `0.1.123`

### [xp/plugin_test_12.json](xp/plugin_test_12.json)

- **Top-level type:** *(no `type` field — menu root)*
- **Description:** Paging Plugin ("Paging Examples"): a menu that pages through large data sets via `request:interaction:...?offset={OFFSET}&limit={LIMIT}|{LIMIT}@.../interaction/paging.html` (needs a matching server endpoint that returns `items` for offsets > 0).
- **Related API:** [Paging Plugin](../experts-api/plugins/paging-plugin.md), [Interaction Plugin](../experts-api/plugins/interaction-plugin.md)
- **Cookbook:** [Plugins (media, immersive, platform, ads)](../reference/cookbook.md#plugins-media-immersive-platform-ads)

### [xp/plugin_test_13.json](xp/plugin_test_13.json)

- **Top-level type:** `pages`
- **Item types:** `button`
- **Structural properties:** `enumerate`, `template`
- **Inline expressions:** `{br}`, `{col:...}`, `{ico:...}`, `{txt:...}`
- **Description:** Plugin test 13: drives the Input/Interaction plugin (`content:request:interaction:...@.../interaction/input.html`, `execute:fetch:...`) for text entry — logins, search boxes, codes (requires a matching server counterpart). Enumerated pages of `button` items with template and rich inline expressions.
- **Related API:** [Input Plugin](../experts-api/plugins/input-plugin.md), [Interaction Plugin](../experts-api/plugins/interaction-plugin.md)
- **Cookbook:** [Interaction & UX](../reference/cookbook.md#interaction--ux)

### [xp/properties_test.json](xp/properties_test.json)

- **Top-level type:** `list`
- **Item types:** `separate`
- **Structural properties:** `template`
- **Multi-action syntax:** `[a|b]`
- **Inline expressions:** `{br}`, `{ico:...}`, `{txt:...}`
- **Description:** Extended properties showcase (player buttons, extensions, per-item overrides) — the best single file to skim when you need "is there a property for…?". List of `separate` items with template, multi-action, and text/icon expressions.
- **Related API:** [Extended Properties](../experts-api/special/extended-properties.md)
- **Cookbook:** [Interaction & UX](../reference/cookbook.md#interaction--ux)

### [xp/selection_test_1.json](xp/selection_test_1.json)

- **Top-level type:** `pages`
- **Item types:** `separate`, `space`
- **Structural properties:** `important`, `selection`, `template`
- **Inline expressions:** `{col:...}`, `{ico:...}`
- **Description:** Selection API test 1: the `selection` object (since `0.1.110`) runs an action when an item *receives focus* (auto-preview, load a backdrop, update another item) with a configurable delay — here pages with `selection`, template, `important` flag, and color/icon expressions.
- **Related API:** [Selection Object](../experts-api/selection/selection-object.md), [Selection Examples](../experts-api/selection/selection-examples.md)
- **Cookbook:** [Interaction & UX](../reference/cookbook.md#interaction--ux)

### [xp/selection_test_2.json](xp/selection_test_2.json)

- **Top-level type:** `pages`
- **Item types:** `separate`, `space`
- **Structural properties:** `enumerate`, `important`, `selection`, `template`
- **Multi-action syntax:** `[a|b]`
- **Inline expressions:** `{col:...}`, `{context:...}`, `{ico:...}`
- **Description:** Selection API test 2: adds enumeration and multi-action to the selection test, plus an `underlay`, a `background`, and a `delay:selection`; demonstrates combined selection + multi-action patterns.
- **Related API:** [Selection Object](../experts-api/selection/selection-object.md)
- **Cookbook:** [Interaction & UX](../reference/cookbook.md#interaction--ux)

### [xp/tizen_test.json](xp/tizen_test.json)

- **Top-level type:** `list`
- **Item types:** `separate`
- **Structural properties:** `template`
- **Inline expressions:** `{ico:...}`
- **Description:** Samsung Tizen player: configures the Samsung native player via `tizen:*` extended properties (`tizen:buffer:size`, `tizen:load/ready/start`) and adds a button that loads the Tizen interaction plugin (`content:request:interaction:init@.../tizen.html`). Samsung Tizen 2016+ models, min MSX `0.1.128`. List with `separate` items and icon expressions.
- **Related API:** [Tizen Player](../experts-api/special/tizen-player.md)
- **Cookbook:** [Plugins (media, immersive, platform, ads)](../reference/cookbook.md#plugins-media-immersive-platform-ads)

### [xp/tizen_test_subtitles.json](xp/tizen_test_subtitles.json)

- **Top-level type:** *(no `type` field)*
- **Item types:** `control`
- **Structural properties:** `enumerate`, `template`
- **Inline expressions:** `{col:...}`, `{context:...}`, `{ico:...}`
- **Description:** Tizen subtitle control test: demonstrates subtitle track selection for the Tizen player using `control` items with context expressions. Samsung Tizen 2016+ models, min MSX `0.1.128`.
- **Related API:** [Tizen Player](../experts-api/special/tizen-player.md)
