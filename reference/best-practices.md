---
title: Best Practices & Good to Know
category: Reference
summary: Practical recommendations and rendering quirks learned from building and testing real MSX pages — not spelled out on any single API reference page.
---

# Best Practices & Good to Know

Practical guidance surfaced by actually building and testing real JSON pages in MSX — not spelled out on any single API reference page. Split into two parts:

- **[Best Practices](#best-practices-learned-from-building-real-pages):** recommendations worth internalizing before you start building — item-type/`"list"`-vs-`"pages"` choices, `transparent`/`background` chains, image sizing math, `imageOverlay`/icon-contrast pitfalls, and more.
- **[Good to Know](#good-to-know-rendering-details-worth-knowing):** rendering behaviors and quirks worth knowing about — background video/audio persistence, player UI timing, item-type sizing details — that don't fit neatly into a single recommendation.

---

## Best Practices (Learned From Building Real Pages)

A recommendation worth internalizing before you start building — surfaced by actually testing generated JSON in MSX, not spelled out on any single API reference page.

### Choosing `"list"` vs `"pages"`, and `template`+`items` vs manual `pages`

Both are valid for either type (see [JSON Building Guide → Step 6](json-building-guide.md#step-6--templated-content-template--items)), but in practice they skew by use case and by how much content there is:

- **`"list"`** fits classic, scrollable lists best — often built with `template`+`items` for uniform rows, though a manual `pages` array is also common for `"list"`.
- **`"pages"`** fits special, one-off screens — homescreens, dashboards, settings-style pages — and is more often built with a manual `pages` array than with `template`+`items`.
- **Why `github/base/`'s `audios.json`/`videos.json`/`images.json`/`youtube.json` use `"pages"` with `template`+`items` anyway, despite that being the less common combination:** each has only 3-4 items that fit entirely on one page. With only one page, `"list"` and `"pages"` behave virtually identically — no navigation or display difference at all, except the small page-indicator dot `"pages"` shows for the current page (`"list"` shows nothing, since there's nothing to scroll). At that scale, either type is a reasonable choice.
- **As soon as content grows past one page, `"list"` is usually the more natural choice** — especially for layouts that don't fill a full page on their own (e.g. a `template` `layout` like `"0,0,2,4"`, which only leaves room for one row of items per "page"): `"list"`'s stacking behavior (see [JSON Building Guide → Step 4](json-building-guide.md#step-4--content-root-object-list-layout)) lets the next row/page peek in at the bottom of the screen, so the user already sees what's coming next — a natural, continuous scroll feel that `"pages"` (fully separate horizontal pages, no preview of the next one) doesn't offer.
- Overall, `"list"` sees more real-world use than `"pages"` — in the [github examples](../examples/github/), which were built to mirror practical real-world usage, `"list"`-type files outnumber `"pages"`-type files roughly 3 to 1 (14 vs. 4 across `github/base/` and `github/extended/`).

In the end it always comes down to the specific use case and amount of content — check the [github examples](../examples/github/) for a feel of how each combination is used in practice.

### `transparent` and background images/videos

> **Version note:** `transparent` on Content Root/Page was boolean-only until `0.1.142`, which added the `0`/`1`/`2` numeric form described below; on Menu Root/Item, `transparent` doesn't exist at all before `0.1.142`. State `0.1.142+` when using `transparent: 2` or setting `transparent` on a Menu Root/Item Object. See [Versioning → Version Baseline](versioning.md#version-baseline-010).

`transparent` (Content Root/Page, Menu Root/Item — see [Common Misconceptions → Allowed values](common-misconceptions.md#allowed-values-enum-traps)) controls whether MSX's default background dimming is applied. By default (`transparent: 0`/`false`), the background layer — which renders a `background` image and a running background video/audio the same way — sits behind a fixed, translucent dark overlay (80% opacity dark gray). This is why any background (even a fully white or very colorful one) is always safe to use out of the box: the overlay dims it enough to keep MSX's default white text legible. The tradeoff is that dark background images or generated backdrops barely show through that same overlay.

**Precondition: is the background actually dark enough?** Both non-default values (`1`/`true` and `2`) remove the dimming overlay while a still image is showing — that is the whole point of setting either one. Neither adds any *new* protection against a bright background; they only ever remove the existing dimming. So before picking either value, confirm the background image itself (black/gray tones, not white or highly colorful) is dark enough to keep the default white text legible without the overlay. **If you're not sure, don't set `transparent` at all** — the default (`0`/`false`) always works safely over any background, so leaving it unset beats setting it and getting it wrong; an unreadable page is a worse outcome than the safety dimming's minor cosmetic cost.

**Once the background is confirmed dark enough, choose between `1` and `2`:**

- `transparent: 1`/`true` removes the overlay unconditionally — full brightness at all times, including while video/audio happens to be playing in the background.
- `transparent: 2` also removes the overlay over a still image, but reverts to the same dimmed overlay used at `transparent: 0`/`false` the instant video/audio becomes active — whether that video/audio was started on this very page or carried over from a completely different, earlier context. This matters on *any* page, not only ones that themselves start a background video via a selection/live action or a background plugin: because MSX keeps a previously started video/audio playing in the background by default (see [Good to Know → Video/audio keeps playing in the background after `back`](#videoaudio-keeps-playing-in-the-background-after-back-by-default)), a page can never tell from its own JSON alone whether such a carried-over video is currently active. The overlay itself is translucent, not a solid block, so the video stays visible (just dimmed), never fully hidden. This reversion can be overridden with the item's static extended property `control:transparent` to `true` (see [Extended Properties](../experts-api/special/extended-properties.md)), which keeps the overlay removed during playback too — see [Common Misconceptions → Allowed values](common-misconceptions.md#allowed-values-enum-traps).
- **`2` is therefore the safer of the two whenever a dark-enough background is used:** `1`/`true` risks exposing a full-brightness video that was never meant to be shown at full brightness on this page, while `2` automatically falls back to the safe, dimmed overlay the moment any video/audio is active, from anywhere in the app.

**When is `1`/`true` actually worth it, then?** Primarily on a page that builds its own dimming directly into the content instead of relying on MSX's fixed 80%-opacity overlay — full control over exactly how much (and where) to dim, instead of an all-or-nothing switch. A concrete pattern: a full-size `"space"` item with `imageOverlay` set (or a semi-transparent image) for general dimming, combined with a second, narrower `"space"` item using a custom `color` (e.g. `"rgba(51,51,51,0.8)"`) as a stronger dark bar behind a specific area such as a row of buttons — placed directly on the content page or split out into an `underlay`. Because this dimming is baked into the page's own structure rather than depending on MSX's built-in overlay, it doesn't matter whether video/audio happens to be running in the background — `1`/`true` is safe here because the page supplies its own protection instead of relying on MSX's. See the [Drawing Plugin](../experts-api/benchmark/drawing-plugin.md) for a real example of this technique — its interactive drawing-tools panel (not the static demo JSON shown on that page) uses exactly this `underlay` + two-`"space"`-item dimming pattern behind `transparent: true`.

**Priority chain (`transparent` and `background` alike).** Both properties exist on the same four objects — Content Page, Content Root, Menu Item, Menu Root — and resolve the same way: **Content Page → Content Root → Menu Item → Menu Root**, highest priority first, within the currently-active menu+content pairing. Panels never have `transparent` or `background` at all (a panel's background is always its own fixed, solid color), and the chain doesn't carry across navigation — a sub-menu/sub-content loaded via `menu:`/`content:` is evaluated fresh against its own chain. In practice, Content Page/Menu Item are rarely the level actually used — most pages set `transparent`/`background` on Content Root or Menu Root instead; the lower two exist mainly for completeness, since each level can independently override its own `background` too. (Content Item Object's `background` — audio-only, only for `action: "audio:{URL}"` — and Selection Object's `background` — focus-only — are separate, condition-tied mechanisms, not part of this chain. Selection Object also has no `transparent` property of its own at all, by deliberate UX design: unlike a `background` swap, a `transparent` change applies instantly/hard rather than smoothly, which would look jarring on a focus-preview background meant to feel smooth — the item-level `control:transparent` extended property above is the intended way to affect transparency during audio/video playback instead.)

### Sizing `image` sources for the grid vs. for full-screen

Oversized source images cost rendering performance and load time for no visual benefit — no current TV platform renders the MSX **UI** above 1080p (this is about the UI layer only; a video played at the same time can still be 4K/8K).

**Grid item images** (`image` on a Content Item). For a `"type": "default"` item with `offset` unset/zero, the rendered image size in pixels follows the item's `layout` `w`/`h` (in grid units), independently per axis, via:

`pixels = 144 × units − 24` at layout resolution 1080p — the same 1080p/720p × 1.5 scaling convention already used elsewhere in the KB (e.g. `logoSize` on the Menu Root Object); divide by `1.5` for the 720p equivalent. With `compress: true` the grid cells shrink by 25% (per [Compress Property](../experts-api/hidden-features/compress-property.md)/[JSON Building Guide → Step 5](json-building-guide.md#step-5--compress-property)), so the same fixed gutter applies against a 25%-smaller pitch: `pixels = 108 × units − 24` at 1080p (108 being 144 × 0.75).

| Layout size (grid units) | Image size @ 1080p | Image size @ 720p | @ 1080p (`compress: true`) | @ 720p (`compress: true`) |
|---|---|---|---|---|
| 1 | 120 px | 80 px | 84 px | 56 px |
| 2 | 264 px | 176 px | 192 px | 128 px |
| 3 | 408 px | 272 px | 300 px | 200 px |
| 4 | 552 px | 368 px | 408 px | 272 px |
| 6 | 840 px | 560 px | 624 px | 416 px |
| 8 | 1128 px | 752 px | 840 px | 560 px |
| 12 | 1704 px | 1136 px | 1272 px | 848 px |

Width and height each use this same per-unit formula independently — e.g. a `"layout": "0,0,4,2"` item wants a **552 × 264** source at 1080p (uncompressed). Since new TV platforms all ship a 1080p UI, optimize source images for 1080p: they render sharp there and only need a mild ×1.5 downscale on 720p platforms.

For `"type": "separate"` items, use `h - 1` instead of `h` for the height side of the formula — only that many rows belong to the image/focus frame, since the last row is the separated title strip (see [Good to Know](#good-to-know-rendering-details-worth-knowing) below).

**Panels use the same formula.** A panel's grid is narrower (8 columns instead of 12), but its cells are the same absolute size as the main content grid's — the formula and table above apply unchanged, just with fewer columns available.

**Full-screen images** (`action: "image:{URL}"` / `image:context`, the slideshow view) render at the UI's actual resolution, so **1920×1080 is the practical ceiling** — a larger source is simply downscaled with no visual benefit.

**On `image:context`:** [guide/images.json](../examples/guide/images.json) uses `"action": "image:context"` (show the item's own `image` full-screen) for simplicity, reusing one file for both the grid thumbnail and the full-screen view — fine for small source images or a handful of items, as in that example. **Avoid it with large/high-resolution source images**, since the same oversized file then also gets loaded and decoded for every grid thumbnail, which only needs a fraction of that resolution (per the grid-cell sizing above) — the wasted decode time and GPU memory use multiply with every item on screen — on weaker TV hardware this can mean real slowdowns or memory pressure, not just wasted bandwidth. In that case, use a separate, smaller thumbnail image (sized to the grid cell per the sizing above) for the item's `image`, and point the slideshow action (`image:{URL}`) at the original large image directly instead of `image:context`.

### Choosing `"teaser"` over `"default"` — reserve it for large items

`"teaser"` items behave exactly like `"default"` items in every respect except one: `title` renders in a noticeably larger font (see [Content Item Object](../main-api/content/content-item-object.md)). That larger font size doesn't scale down with a smaller `layout` — so `"teaser"` only looks right on items with enough room for the bigger title; on small items the title tends to look oversized/cramped relative to the rest of the item. [pages1.json](../examples/github/extended/pages1.json) is a good reference: every `"teaser"` item there uses a large `layout` (e.g. `"0,0,4,6"`, `"0,0,8,4"`, `"4,0,8,4"` — 24-32 grid units), never a small one. When in doubt, check real `"teaser"` usage in the examples before applying it to a small item.

### Image sizing, overlay, and boundaries within an item (`imageWidth`, `imageHeight`, `imageOverlay`, `imageBoundary`)

**Reducing `imageWidth`/`imageHeight`.** `imageWidth`/`imageHeight` (both since `0.1.0`) shrink the image within its own item boundary — in practice almost always one direction at a time (height *or* width, rarely both together). Reducing `imageHeight`, for example, keeps `title`/`titleHeader`/`titleFooter` (which sit at the item's bottom edge) from covering the image — visually similar to `"type": "separate"`, but not the same: the whole item still keeps its own background color/border, which `"separate"` items don't. Both properties always shrink from a fixed anchor — `imageWidth` keeps the image left-anchored (removing width from the right), `imageHeight` keeps it top-anchored (removing height from the bottom); neither can put the image on the opposite side (right-anchored or bottom-anchored) instead — that's what the `imageFiller` + `separation` technique further below is for.

**Why the useful values look like `0.83`, `1.83`, `2.83`, … instead of plain integers.** An item's own *default* image (no `imageWidth`/`imageHeight` set) already renders at `units × pitch − gap` — the same `144×units−24`/`108×units−24` formula from above, gap included. An *explicit* `imageWidth`/`imageHeight` value, however, is **not** run through that same "minus gap" step — it multiplies the pitch directly (`value × pitch`), so a plain integer `N` (matching the item's own `layout` size) actually renders *larger* than that item's own default image would — though any overflow past the item's own boundary is simply clipped, not shown, so it doesn't visibly spill onto neighboring items. To make an explicit value render at the exact same size as a default `N`-unit image, subtract the gap's share of one unit — `gap ÷ pitch = 24 ÷ 144 = 1/6 ≈ 0.1667` for a standard 12×6 grid, so `N − 0.1667` (`24 ÷ 108 = 2/9 ≈ 0.2222` with `compress: true`, so `N − 0.2222`, since the gap-to-pitch ratio is bigger on the smaller compressed pitch) — e.g. to match a 3-unit default image, the explicit value to use is `2.83` (`3 − 0.1667 = 2.8333`, rounded to 2 decimal places — the sub-pixel residual is visually irrelevant, and `2.83` is exactly the form the real example files below use), not `3`. This is the number you can't easily guess without knowing the underlying gap math, which is exactly why it's worth remembering.

Two practical uses for this: matching the image size of a **differently-sized** sibling item — e.g. a `"default"` item at `"layout": "0,0,4,3"` with no `imageHeight` set (its image already renders at the natural size for 3 units) next to a taller `"default"` item at `"layout": "4,0,4,4"`; setting the taller item's `imageHeight` to `2.83` (not `3`, and not `4`) makes its image match the first item's exactly, giving both identical image frames despite the different `layout` heights. Or aligning an image's edge with a same-sized sibling at a specific row/column boundary, as demonstrated in [guide/image_height.json](../examples/guide/image_height.json) and [guide/image_width.json](../examples/guide/image_width.json) — their third page sets `imageHeight`/`imageWidth: 0.83`/`1.83`/`2.83`/`3.83`/`4.83` on items next to a column of same-sized `"button"` items, each explicitly labeled "aligned with the edge of item N". A value greater than the item's own `layout` width/height is silently ignored (both example files demonstrate this too).

In practice this technique doesn't come up constantly — most pages just use uniformly-sized items to begin with, where it's moot — but it's a real tool for pages that deliberately mix item sizes and still want their images to line up visually. Whether a "clean" default size, a gap-adjusted (`.83`-ending) matched size, or something else entirely looks best is, like most of these style choices, use-case dependent.

**`imageWidth` repositions all five text properties; `imageHeight` repositions none of them — it only changes the image itself.** By default, `headline`/`text`/`title`/`titleHeader`/`titleFooter` all sit directly on top of the (full-size) image — `headline`/`text` in the upper-left corner, `title`/`titleHeader`/`titleFooter` in the lower-left corner. Reducing `imageWidth` shifts *all five* to the right by that same amount, since the image is anchored on the left and freed-up space appears on the right — so none of them overlap the now-narrower image. Reducing `imageHeight` only frees up space at the *bottom* (the image stays anchored at the top and simply doesn't reach as far down), so only `title`/`titleHeader`/`titleFooter` — already positioned at the bottom — stop overlapping the image; they aren't actually shifted, the image just no longer reaches that far. `headline`/`text` sit at the *top*, exactly where the (still full-width, top-anchored) image continues to be, so a reduced `imageHeight` doesn't move them out of the image's way the way `imageWidth` does. `imageWidth`/`imageHeight` are primarily a **style** choice. Plenty of images work perfectly well with text overlapping them directly (a dark backdrop, a photo with empty sky/space in the upper area, …), and an "image on one side, text on the other" layout via `imageWidth` is just as often chosen because it looks better for that particular design. `imageOverlay` (see below) is the separate tool for cases where the image should stay full-size but the overlapping text still needs to read clearly on top of it. [guide/text_image.json](../examples/guide/text_image.json) is a good side-by-side illustration: the same `headline`/`text`/`icon`/`title`-family content is repeated across `"default"`, `"separate"`, `"teaser"`, and `"button"` items, plus `imageHeight`/`imageWidth` variants of `"default"`, so the visual difference between overlapping text, a `"separate"` split, and an `imageWidth`-driven side-by-side layout is directly comparable.

`label` and `icon` are a separate case in both directions — they always stay within the image frame (always overlapping it) and are never repositioned by `imageWidth`/`imageHeight`.

**A third technique: `imageFiller` + `separation`, without touching `imageWidth`/`imageHeight` at all.** This isn't just an alternative — it's the *only* way to put the image on the right or at the bottom in the first place, since `imageWidth`/`imageHeight` are always left-/top-anchored (see above) and can never flip to the opposite side. Instead of shrinking the image, position it to one side with a directional `imageFiller` value (`"height-left"`/`"height-right"`/`"width-top"`/`"width-bottom"`/etc. — since `0.1.0`, keeps the image's own aspect ratio instead of stretching it) and bound the text properties to the opposite side with `separation` (since `0.1.156`) — a value from `0.0` (all text area on the right) to `1.0` (all text area on the left) that keeps the `title` family (`title`/`titleHeader`/`titleFooter`) and `headline`/`text` from crossing into the image's side. (`label`/`extensionLabel` are a separate case: per [Content Item Object](../main-api/content/content-item-object.md), `separation` only separates *those two* on `"control"` items — see [Set `separation` on a `"control"` item](#set-separation-on-a-control-item-combining-label-with-extensioniconextensionlabel) below; and on `"separate"` items, the `title` family is excluded from separation entirely.) The `separation-*` values of the `alignment` property (also since `0.1.156`, see [Common Misconceptions → Allowed values](common-misconceptions.md#allowed-values-enum-traps)) are directly tied to this same `separation` property — they only take effect once `separation` itself is set on the item. [Advanced Text & Image](../experts-api/hidden-features/advanced-text-image.md) demonstrates exactly this:

```json
{
    "type": "default",
    "layout": "6,4,6,1",
    "image": "http://msx.benzac.de/img/test.jpg",
    "imageFiller": "height-right",
    "imageOverlay": 0,
    "separation": 0.75,
    "truncation": "titleHeader|titleFooter",
    "titleHeader": "Header header header header header header header header",
    "titleFooter": "Footer footer footer footer footer footer footer footer"
}
```

`imageFiller: "height-right"` fills the item's height (keeping the image's own aspect ratio) and anchors it to the right, leaving empty space on the left whose width depends entirely on that image's aspect ratio — a wide (landscape) image leaves less room than a narrow (portrait) one at the same height. `separation` and `imageFiller` aren't linked automatically, so the two values have to be tuned together by hand for a given image shape; there's no single `separation` value that works for every aspect ratio. This is why `separation` is most useful for pages driven by a database or service, where text lengths vary and you don't want to hand-trim every string — before `0.1.156`, the only alternative was trimming text server-side or in a plugin by character count, which is unreliable since character count doesn't map cleanly to rendered pixel width. If your text is fixed and you can shorten it yourself to avoid overlap, `separation` isn't strictly necessary.

**`imageOverlay` — the non-repositioning way to keep overlapping text readable.** Instead of moving text out of the image's way, `imageOverlay` (since `0.1.0`) darkens the part of the image *behind* the text so it stays legible while still overlapping — no `imageWidth`/`imageHeight` wrapping needed. By default (`-1`, automatic), MSX darkens the *lower* part of the image whenever `title`/`titleHeader`/`titleFooter` are actually **on top of** the image — i.e. genuinely overlapping it (`0` forces the overlay off, `1` forces it on regardless). This means the automatic default stops applying as soon as `imageWidth`/`imageHeight` wrapping is used: once the title text is repositioned (or the image recedes) out of the overlap, there's nothing left for the automatic logic to detect, so no overlay is added even if `title`-family text is still present on the item — if an overlay is still wanted in that wrapped case (e.g. for style, or where the overlap is only partial), it must be set explicitly (`1`). Since `0.1.97`, an **inverted** overlay darkens the *upper* part instead — precisely for `headline`/`text`, which (per the point above) `imageHeight` can never help keep readable: `2` adds the inverted (upper) overlay on top of the automatic default (lower) behavior, `3` adds only the inverted (upper) overlay with the default (lower) one forced off, and `4` forces both the inverted (upper) *and* the default (lower) overlay on together — useful when an item has both `headline`/`text` at the top and `title`-family text at the bottom of the same image.

**Recommendation:** whenever `headline` and/or `text` is combined with an `image`, set `imageOverlay` explicitly to `2`, `3`, or `4` (`2` covers most cases). Unlike the lower area — which MSX darkens automatically whenever `title`-family text overlaps — the upper area is **never** darkened automatically, so without an explicit value `headline`/`text` can end up unreadable against a bright image.

`imageOverlay` isn't only for legibility, either — it's just as valid as a pure style choice with no overlapping text involved at all. A bright, colorful `image` on a background `"space"` item, for instance, can feel visually overwhelming next to more muted content around it; setting `imageOverlay: 4` darkens it top and bottom regardless of any text, toning it down to sit more calmly in the overall page.

**`imageBoundary` (since `0.1.146`) lets `tag`/`badge`/`stamp`/`progress`/`imageOverlay` bind to the (possibly reduced) image area instead of the full item.** By default these five are positioned relative to the *entire item's* area, regardless of whether `imageWidth`/`imageHeight` has reduced the image itself. Setting `"imageBoundary": true` binds them to the image area instead — but only has an effect once the item is already "wrapped" (i.e. `imageWidth` or `imageHeight` is set); otherwise it's ignored. A `"type": "separate"` item behaves as if `imageBoundary` were already `true` for these five properties, without needing to set it. On `"button"`/`"control"` items, `imageBoundary` is ignored outright: their own `image`/`icon` boundary is already much smaller there (see [Good to Know](#good-to-know-rendering-details-worth-knowing) below) — too small to meaningfully host `tag`/`badge`/`stamp`/`progress`/`imageOverlay` within it. See [Advanced Text & Image](../experts-api/hidden-features/advanced-text-image.md) for a worked example contrasting default vs. image boundaries side by side. (Not to be confused with the fixed, unrelated `"control"`/`"button"` boundary quirk described in [Good to Know](#good-to-know-rendering-details-worth-knowing) below — that one is about where `image`/`icon` themselves render on those two types, with no corresponding JSON property at all; `imageBoundary` is a real, settable property affecting a different set of five properties instead.)

### Don't combine `image` + `icon` on the same item — contrast risk `imageOverlay` can't fix

`icon` renders centered on the item (see [Good to Know](#good-to-know-rendering-details-worth-knowing) below), while `imageOverlay` only ever darkens the image's top or bottom — never its center. If the image happens to be bright right where the icon sits, the icon's own default color (white, like the rest of MSX's text) can lose contrast against it, with no property available to fix that spot specifically. This is likely why the combination is used rarely in practice: `label`/`icon` are typically reserved for `"button"`/`"control"` items (which normally don't also carry a full-size background `image` competing for contrast), while `image` together with `headline`/`text`/`title`-family text is typically used on `"default"`/`"teaser"`/`"separate"`/`"space"` items instead. `tag`/`badge`/`stamp`/`progress` don't have this restriction and combine freely with either.

### Give `"button"` items enough height when combining `icon` + `label`

On `"button"` items specifically, `icon` and `label` both render centered on the item; if the item's height (`layout`/`offset`) is too small, they can overlap each other. Give `icon`+`label` buttons enough height to keep the two visually separated — [guide/types.json](../examples/guide/types.json)'s `"button"` item (`"layout": "10,0,2,2"`, `iconSize: "small"`) is a real example that comfortably fits both. (`"control"` items place `icon` left-aligned rather than centered — see [Good to Know](#good-to-know-rendering-details-worth-knowing) below — so this particular overlap risk doesn't apply the same way there; see below for `"control"`'s own `layout`/`separation` guidance instead.)

Concretely, how much height a `"button"` combining `icon`+`label` needs depends on `iconSize`:

- **Height `1`:** don't combine `icon`+`label` here at all — the two always overlap regardless of `iconSize` (see above).
- **Height `2`:** keep `iconSize` at `"small"` or `"medium"` — `"large"`/`"extra-large"` are already too tall and overlap `label`.
- **Height `3`:** `"large"` also fits, but only once the button is at least `2` columns wide; `"extra-large"` still doesn't fit at this height.
- **Height `4`:** every `iconSize` fits, but `"extra-large"` needs at least `3` columns of width. In practice `"extra-large"` is rarely used on buttons at all — it renders the icon very large, which suits an `image` with its own built-in transparent margin far better than a typical icon+label button.

These figures aren't `"button"`-specific — they're really about how much room `icon` itself needs to render without clipping, so the same numbers apply to `icon` on any type that renders it (`"default"`, `"teaser"`, `"separate"`, `"space"`, `"button"`; `iconSize` has no effect on `"control"` at all, see [Good to Know](#good-to-know-rendering-details-worth-knowing) below).

Without a `label`, the width minimums above apply symmetrically to height too — `icon` alone needs roughly a *square* footprint matching its size: `"small"`/`"medium"` fit comfortably even at `1`×`1`, `"large"` needs at least `2`×`2`, `"extra-large"` needs at least `3`×`3`. A `2`×`2` item, for example, comfortably fits `iconSize: "large"` as long as no `label` is set. Adding a `label` — which renders below `icon` — costs one extra row underneath that square footprint — which is exactly where the height-`3`-needs-`"large"` and height-`4`-needs-`"extra-large"` rules above come from (a `2`×`2` icon-only footprint plus one row for `label` is height `3`; a `3`×`3` footprint plus one row is height `4`).

### Give `"control"` items a `layout` height of `1`

`"control"` items are designed to render at exactly **one grid row** of height (e.g. `"layout": "0,0,8,1"`) — that's the height every `"control"` item across the MSX app itself and its showcases/plugins uses, and it's the height `icon`/`label` and a possible `extensionIcon`/`extensionLabel` look best at. Real examples confirm this in practice: [hidden_feature_14.json](../examples/xp/hidden_feature_14.json), [list1.json](../examples/github/extended/list1.json), and [plugin_test_11.json](../examples/xp/plugin_test_11.json) all place their `"control"` items at height `1`, whether compressed or not.

### Set `separation` on a `"control"` item combining `label` with `extensionIcon`/`extensionLabel`

`separation` (since `0.1.156`) is the property built specifically to prevent `label` from overlapping `extensionLabel` — per [Content Item Object](../main-api/content/content-item-object.md), `"control"` is the one item type where `separation` acts on `label`/`extensionLabel` at all (compare the unrelated `imageFiller`+`separation` use on other item types, [above](#image-sizing-overlay-and-boundaries-within-an-item-imagewidth-imageheight-imageoverlay-imageboundary)). Left at its default (`-1`, no separation), a long `label` can grow right into `extensionIcon`/`extensionLabel` instead of stopping short of it — an overlap that always looks worse than a cleanly truncated `label`.

- **Whenever a `"control"` item sets `extensionIcon` or `extensionLabel` alongside `label`, also set `separation`** — don't leave it at the default in that combination.
- With **`extensionIcon`** (a small, fixed-size icon), `separation: 0.9` works well — it reserves just enough of the right edge for the icon and gives `label` the rest, truncating cleanly instead of colliding.
- With **`extensionLabel`** (variable-length text), `0.9` reserves too little room and over-truncates the extension label — something like `0.65`, or nearby depending on how long `extensionLabel` actually is, is a better starting point.
- Either way, a set `separation` value — even one that isn't perfectly tuned — always beats leaving it unset: it guarantees `label` is truncated instead of visually overlapping `extensionIcon`/`extensionLabel`.

### `tag`/`stamp`/`badge` — short, single-line text, and default colors that already fit

**Keep `tag` text short — 1 to 4 characters.** `tag` renders as an angled corner ribbon in the upper-right, in uppercase (`"HD"`, `"4K"`, `"8K"`, `"UHD"`, `"NEW"` are good examples) — there's very little room for anything longer. [Inline Expression](../main-api/common/inline-expressions.md) icon references (`{ico:...}`) tend to look odd here too, precisely because of the angled presentation.

**`stamp`/`badge` also want short text, just without `tag`'s strict character count.** Neither property supports line breaks — both always render as a single line — so, like `tag`, they were designed for short labels rather than sentences. How much actually fits in practice depends on the item's own `layout` size, not a fixed character count the way `tag` has.

**Don't set `tagColor`/`stampColor` to `msx-white` — or any other near-white/light color (e.g. `msx-white-soft`, `msx-glass`, a light gray)** — unless you also change the tag/stamp text itself, both already render their own text in `msx-white` by default. `tagColor`'s default background is `msx-blue`; `stampColor`'s default background is `msx-black`.

**Don't set `badgeColor` to `msx-black` — or any other near-black/dark color** — for the same reason: `badge`'s own default text color is `msx-black`, unlike every other text-bearing property here (all of which default to white or semi-transparent white). `badgeColor`'s default background is `msx-white`; `badge` is the one exception in this list where the default text color isn't white.

**The rest of MSX's text is white or semi-transparent white by default:** `title`/`headline` render in `msx-white`, while `titleHeader`/`titleFooter`/`text` render in `msx-white-soft` (see [Colors](../main-api/common/colors.md)).

**`badge` overlaps `headline`/`text` unless you adjust the alignment.** Both are positioned in the upper-left corner by default (see [Content Item Object](../main-api/content/content-item-object.md)), so combining `badge` with `headline`/`text` without any adjustment lets the badge sit directly on top of them. Fix it with the `alignment` property on either side: `"alignment": "text-right"` (since `0.1.97`) moves `headline`/`text` out of the way, or `"alignment": "badge-right"` (since `0.1.146`) moves the badge instead — from a UX standpoint, moving the badge (`badge-right`) is the recommended choice of the two. `"badge-center"` doesn't help here: a centered badge still renders across the item's full width, just centered within it, rather than shrinking to the text's own size — only `badge-left`/`badge-right` size the badge to its actual text width.

**Combining `alignment: "badge-right"` with a `tag`?** Add `{tb}{tb}` at the end of the `badge` text. `tag` renders as a diagonal ribbon in the upper-right corner, and without extra spacing a right-aligned badge text can disappear behind it. Three non-breaking tabs push enough space toward the right edge that the tag no longer covers the badge text, letting both render cleanly side by side:

```json
{
    "tag": "NEW",
    "badge": "Editor's Tip{tb}{tb}",
    "alignment": "badge-right"
}
```

### Making a compressed header/footer look uncompressed

`compress: true` shrinks every grid cell (width and height) by a factor of `0.75` — but the *gap between items*, at a given layout resolution, stays exactly the same absolute size either way, compressed or not. (This is the same fixed gutter constant that appears unchanged between the `144×units−24` and `108×units−24` variants of the grid `image` sizing formula above — `-24`px at 1080p — the gap doesn't scale with `compress`. It does still scale with the layout *resolution* itself, same as everything else in that formula: `-24`px at 1080p becomes `-16`px at 720p, i.e. the same `÷1.5` — it's only compress-invariant, not resolution-invariant.) Because item spacing is compress-invariant but item size isn't, a `header`/`footer` object's items can be deliberately stretched with `offset` to visually match *uncompressed* proportions, even while the rest of the page renders compressed — letting a compressed page and a "fake decompressed" header blend together with matching, harmonious spacing.

The recipe, worked through [Compress Property](../experts-api/hidden-features/compress-property.md)'s own header example:

```json
"header": {
    "compress": false,
    "offset": "0,0,0,0.666",
    "items": [{
        "type": "control",
        "layout": "0,0,16,1",
        "offset": "0,0,0,0.333",
        "icon": "adb",
        "label": "Header"
    }]
}
```

1. **Compensate the item's own height first.** A compressed grid cell is `0.75×` the size of an uncompressed one, so to make a header item's `layout` height *look* like a full uncompressed row, add `1 / 0.75 − 1 = 0.333` to its own `offset` height — here on the header's `"control"` item (`"offset": "0,0,0,0.333"`). This `0.333` is a constant for any 1-row header item; it holds regardless of any further styling. Note that the header's own `"compress": false` only resets its *font sizes* back to full size (per [Compress Property](../experts-api/hidden-features/compress-property.md) — a non-root `compress` never changes the grid) — it does **not** compensate this height difference by itself; that's what the `offset` is for.
2. **Add your usual visual gap, scaled up to compressed units.** Independent of `compress`, it's good practice to reserve a little breathing room below a header before the content starts — e.g. `0.25` grid units in a normal, uncompressed 12×6 layout (or `0.5` if the header also carries extra info). Since `offset` is always measured in the *current* (here: compressed) grid's own unit size, a raw `0.25` would render smaller than the uncompressed `0.25` you actually want — scale it by the same `1 / 0.75 = 1.333` factor: `0.25 × 1.333 = 0.333`.
3. **Add both together on the header object's own `offset` height.** `0.333` (item-height compensation) `+ 0.333` (scaled gap) `= 0.666` — exactly the `"offset": "0,0,0,0.666"` set on the `header` object above.

The same `1 / 0.75` scaling factor applies to any other deliberately-uncompressed-looking spacing added elsewhere on a compressed page (footer, other decoration objects) — only the specific numbers change depending on how many rows/columns and how much extra gap are involved.

---

## Good to Know (Rendering Details Worth Knowing)

Not a recommendation, just rendering behavior that's easy to get wrong if you assume it works the same as other types — surfaced the same way, by testing generated JSON in MSX.

### Video/audio keeps playing in the background after `back` (by default)

Selecting a `video:{URL}`/`audio:{URL}` item opens the player; pressing `back` returns to the content, but playback itself doesn't stop — the video or audio keeps running in the background exactly as if it were a `background` video (see the `transparent` guidance above, which assumes this scenario). This mirrors how live TV works on a real television: switching away from the live view doesn't stop the broadcast. It's a deliberate feature, not an oversight — MSX is built around exactly this use case (start a video/audio, then keep browsing), which is why it's the default everywhere rather than something you have to opt into.

Playback still stops the normal ways, without any JSON needed: the eject button in the player controls (`player:eject` — identical to `player:stop`, either can be used), the stop button on the remote control, or the **Player** entry of the Context Menu (reachable anytime via the menu key). Selecting **Player** briefly switches to the player itself (to pause/resume or skip forward/back there), but pressing `back` from it returns you exactly to wherever you called the player from — so in effect you never lose your place in the content, even though you do pass through the player screen to get to those controls. See [In-App Settings Reference](settings-reference.md#1-how-this-screen-relates-to-json-building) for the Context Menu.

This background-playback default doesn't make sense for every page, though — e.g. a one-off info video that shouldn't linger once the user moves on. To opt out and actually stop playback on `back`, add the extended property `"trigger:back": "player:stop"` to the item instead:

```json
{
    "action": "video:http://example.com/video.mp4",
    "properties": {
        "trigger:back": "player:stop"
    }
}
```

Used this way in several real examples, e.g. the [Benchmark Tests](../examples/xp/benchmark_test_1.json). See [Extended Properties](../experts-api/special/extended-properties.md) (`trigger:{TRIGGER_KEY}`, since `0.1.58`) and [Internal Actions](../experts-api/special/internal-actions.md) (`player:stop`).

### The player UI shows for several seconds by default when starting video/audio — several mechanisms shorten or suppress it

Starting a `video:{URL}`/`audio:{URL}` item normally shows the player UI for several seconds before it settles, even if the item is meant to feel like a seamless, in-context playback start. The player UI is not only playback controls — it also displays the item's own metadata: `playerLabel`, and, once the extended property `control:type` is set to `"extended"` (see [Extended Properties](../experts-api/special/extended-properties.md)), `info:headline`, `info:text`, and `info:image` as well. The default display duration exists specifically to give the user enough time to actually see and read that information. Several mechanisms address this default, each solving a different part of the problem:

- **`auto:{URL}`** (`video:auto:{URL}`, `audio:auto:{URL}` — internal-only, `0.1.160+`, see [Internal Actions](../experts-api/special/internal-actions.md)) suppresses the player UI while starting playback, but only while the user is actually on a menu/content page (not inside the player itself). It's the prefix an automatically-continuing playlist uses under the hood: when one enumerated item finishes and the next starts on its own, popping up the player UI would needlessly pull the user out of whatever menu/content they're currently looking at. If the player is already in the foreground (fullscreen) when the next playlist item starts, the player UI is shown anyway, for several seconds — specifically to surface the new item's own `playerLabel`/`info:*` metadata and make the item change visible, since otherwise the transition to new content would be invisible.
- **`"control:load": "silent"`** (extended property, since `0.1.111`, see [Extended Properties](../experts-api/special/extended-properties.md)) is the more complete fix for the same problem: it suppresses the player UI whenever the video/audio is loaded in "auto mode" — i.e. whenever MSX auto-advances a playlist internally (via the internal `player:auto:next` action) — including the case `auto:` alone still misses above, where the player UI is already in the foreground when the next item starts.
- **`background`** (internal-only, `0.1.160+`) closes all currently visible scenes — including the player UI, and, unlike `cleanup`, the main scene itself — so that whatever sits behind them (the background image, or a running video/audio) is fully visible. It doesn't only apply to the player: it's a general "close every scene" action, broader than `cleanup` (which only closes overlay scenes — panels, notifications, the volume view — leaving the main scene/player visible; see [Actions Reference](actions-reference.md#internal-only-actions--overview-require-01160)). Triggered at load time via the extended property `trigger:load` (see [Extended Properties](../experts-api/special/extended-properties.md), since `0.1.58`), `"trigger:load": "background"` fires on **every** load — the very first click as well as every subsequent auto-advance — and unconditionally closes down to the background layer regardless of where the user currently is. That means it necessarily reintroduces the exact effect `auto:`/`"control:load": "silent"` are meant to avoid on auto-advance (pulling the user out of whatever they're currently browsing, even on a completely unrelated page) — whether that's wanted or not depends entirely on the use case.
- **`"trigger:start": "player:hide"`** (extended property, `trigger:{TRIGGER_KEY}` family since `0.1.58`, see [Extended Properties](../experts-api/special/extended-properties.md)) hides the player once the `start` trigger key fires — i.e. once playback actually begins advancing, not merely once it has loaded. This shows the player UI only briefly (just long enough to cover the load), then hides it — a good fit for very short videos/audios where the several-second default would otherwise dominate the whole playback.

**Which combination fits depends entirely on the use case:**

- **A genuine background playlist that should never interrupt browsing** — no player UI, and the user is never pulled away from whatever they're currently looking at, not even on auto-advance: use `auto:` on the action, with no `trigger:load`/`background` at all.
- **Suppress the player UI only during automatic transitions, but still land in the player UI on the initial, user-triggered click**: set `"control:load": "silent"` and leave the `auto:` prefix off the action — the first click then still opens the normal player UI, while every internal auto-advance (which always runs in "auto mode", regardless of the item's own action prefix) stays silent.
- **Force the video to always take over the screen as the background layer** — on the first click and on every subsequent auto-advance, no matter where the user currently is: use `"trigger:load": "background"`. Combine it with `"control:load": "silent"` to also avoid the brief player-UI flash the closing transition would otherwise leave behind.
- **Show the player UI only briefly** for very short videos/audios: `"trigger:start": "player:hide"`.

To start a video/audio directly into fullscreen from a content item click, playing it as the background layer with no player-UI flash at all, combine `auto:` with `"trigger:load": "background"` and `"control:load": "silent"`:

```json
{
    "type": "pages",
    "headline": "Videos",
    "template": {
        "type": "separate",
        "layout": "0,0,2,4",
        "icon": "msx-white-soft:movie",
        "color": "msx-glass",
        "properties": {
            "trigger:load": "background",
            "control:load": "silent"
        }
    },
    "items": [
        {
            "title": "Video 1",
            "playerLabel": "Video 1",
            "action": "video:auto:http://msx.benzac.de/media/video1.mp4"
        },
        {
            "title": "Video 2",
            "playerLabel": "Video 2",
            "action": "video:auto:http://msx.benzac.de/media/video2.mp4"
        },
        {
            "title": "Video 3",
            "playerLabel": "Video 3",
            "action": "video:auto:http://msx.benzac.de/media/video3.mp4"
        }
    ]
}
```

Since `trigger:load` fires every time loading starts (not just on the very first load), this same `"background"` trigger also closes down to the background layer on every subsequent playlist item — pressing "next" as well as an auto-tuned transition to the next item (once the current one ends) both drop straight to the background layer again, regardless of where the user is at that moment. Without `"control:load": "silent"`, this closing transition would still leave a brief player-UI flash behind each time; with it set (as in the JSON above), the player UI never appears at all, not even for that flash.

This whole pattern depends on the items actually forming a playlist, which needs two things together: enumeration, and the item's own `action` starting directly with `video:`/`audio:`. A `template`-generated item defaults `enumerate` to `true` (vs. `false` for a non-templated item), and enumerated items are exactly the ones that play in a row — see `enumerate` on [Content Item Object](../main-api/content/content-item-object.md). Without `enumerate`, finishing an item simply ejects back to the last opened menu/content page instead of auto-advancing. `enumerate` is not only a playlist-mechanics switch, though — it also adds a position label (e.g. `(1/10)`) to the content headline while that item is focused, so there can be a reason to set (or deliberately leave off) `enumerate` purely for that visible numbering, independent of whether auto-advance is actually wanted. The [`group`](../main-api/content/content-item-object.md) property (which itself requires `enumerate: true`) appends its own label right after that same enumeration info in the headline — see [Cookbook → Part B — Beautiful layouts](cookbook.md#part-b--beautiful-layouts) for a real example (`list0.json`'s `id:`-prefixed `group` values, which only split the enumeration counter per page without showing a label).

The second requirement — a direct `video:`/`audio:` action — is also why `background` is triggered here via `trigger:load` instead of simply appended to the action itself, e.g. `"action": "[video:auto:http://msx.benzac.de/media/video1.mp4|background]"`. That wrapped form does execute, but it breaks playlist formation, since the item's `action` then starts with `[`, not `video:`/`audio:` directly. It also stops MSX from picking up the item's own `playerLabel`/`properties` automatically: per the `data` action and `[{ACTION}|{ACTION}|...]` notes on [Actions](../main-api/common/actions.md), `playerLabel`, `background`, and `properties` are only read directly from an item whose `action` *is* `video:{URL}`/`audio:{URL}` — wrapped inside a multi-action, they would instead have to be duplicated into the item's `data` property (the single `data` object `[...]` shares across all bracketed actions, as opposed to the `data` action's array where each entry gets its own separate `data`). `"trigger:load": "background"` avoids both problems, since it runs alongside the item's own unwrapped `video:auto:{URL}` action rather than replacing it.

A related but distinct extended property is **`control:return`** (since `0.1.140`, see [Extended Properties](../experts-api/special/extended-properties.md)): by default, returning with `back` to an already-running player — after having opened a menu/content page while video/audio was playing — shows the player UI first (which usually makes sense, since it surfaces what's playing and its progress). Setting `"control:return": "silent"` on the item suppresses that specific case too, so `back` lands directly in fullscreen instead. Unlike `auto:`/`background` above (which affect *starting* playback), `control:return` only affects *returning* to playback that was already running — useful, for example, when a transparent content page is overlaid on top of a video and `back` should drop straight back to fullscreen without the player UI flashing in between, or to jump straight to a rendered surface without the player UI ever getting in the way — exactly how the [Drawing Plugin](../experts-api/benchmark/drawing-plugin.md) benchmark test uses it, going straight back to the drawing canvas on `back`.

### `"space"` items needed a layout/offset workaround to share a position before `0.1.156`

Per [Content Item Object](../main-api/content/content-item-object.md): "Since version 0.1.156, it is possible to register multiple items of type `\"space\"` on the same position, because they are now excluded from the selection engine (e.g. you can register a button and multiple space items on the same position)." Before that version, `"space"` items were still subject to the same one-position-per-item rule as every other item type — registering two items (of any type) at overlapping `layout` coordinates was not possible.

That means the workaround only matters when a `"space"` item's `layout` would otherwise genuinely overlap another item's. A page headline registered at row 0 collides if the row-1 selectable items below it were *also* registered at row 0 and pushed down visually with `offset` — for example:

```json
{
    "type": "space",
    "layout": "0,0,12,1",
    "offset": "0,0,0,-0.5",
    "headline": "First Page {txt:msx-white-soft:(30)}"
},
{
    "type": "default",
    "layout": "0,0,3,3",
    "offset": "0,0.5,0,0",
    "titleHeader": "{col:msx-white}First Item"
}
```

Here both the headline and the selectable item are registered at `y: 0` (the selectable item only *renders* half a row down, via `offset`) — a real position clash pre-`0.1.156`. MSX shows the user a "Position is already registered" warning and simply doesn't add the second item, so it never appears at all. The defensive form sidesteps it by giving the headline a `layout` that doesn't actually reach column 0 or the full row, then using `offset`'s `x`/`w` components to restore the intended full-width look:

```json
{
    "type": "space",
    "layout": "1,0,11,1",
    "offset": "-1,0,1,-0.5",
    "headline": "First Page {txt:msx-white-soft:(30)}"
}
```

The `offset` height component (`h: -0.5`, present in every version above) is unrelated to the collision workaround itself, but it isn't merely cosmetic either — it's what keeps the headline's rendered box from overlapping the selectable item below it. In the reconstructed example above, the selectable item's `offset` y-value of `+0.5` renders it half a row further down than its logical `y: 0` — flush against the thin headline strip, with no gap. Without the headline's own `h: -0.5`, its box would still claim the full row and visually overlap that same space. On a short, single-line headline like these examples, that overlap isn't visible, because a `"space"` item with no `color`/`image` of its own renders nothing but its text — there's no visible box to clash with, only the text sitting in the upper half. It becomes visible the moment the headline needs a second line — from wrapping (a longer, dynamically-generated string from a backend API) or an explicit `{br}` — since that second line then renders straight into the area the selectable item occupies. (The newer `truncation` property, since `0.1.128`, can force a headline to stay on one line instead of wrapping — but that only stops *automatic* wrapping; a `{br}` inserted deliberately would still push a second line down into the same overlap.) It's included in `list0.json` defensively for exactly this reason, independent of whichever `layout`/`offset` form is used for the position itself.

[list0.json](../examples/github/extended/list0.json)'s per-page headlines still use this defensive form today — but its selectable items are currently registered at `y: 1`, with `"offset": "0,-0.5,0,0"` (`"layout": "0,1,3,3"`, etc.), not at `y: 0` with a `+0.5` offset like the reconstructed example above. These items move from their logical `y: 1` to a rendered `y: 0.5` — the same final position the reconstructed example reaches from the other direction (`y: 0` shifted down by `+0.5`), just approached from one row lower. Either way there is **no actual collision with the current item layout**: nothing else is registered at `y: 0` on these pages. The headline's defensive form most likely predates a later revision that moved the selectable items down to `y: 1` — plausibly because an earlier version of this same example registered them at `y: 0` (as reconstructed above) — and the headline's `layout`/`offset` was simply never revisited once the collision it was guarding against stopped existing. It's harmless where it stands, just no longer functionally necessary for *this* file's current item positions.

The lesson generalizes: don't assume a `layout`/`offset` workaround you find in an example is still functionally required just because it's there — check it against what actually shares (or no longer shares) that position in the current JSON. An example's items can evolve while a defensive workaround elsewhere quietly outlives the reason it was added.

### Item `type: "separate"` reserves less image/focus space than its `layout` height suggests

A templated item's `layout` `h` sets the total rows reserved for the item, but how much of that is actual image/focus-frame area depends on `type`:

- **`"separate"`** detaches the title (and `titleFooter`) into their own strip below the image/icon/focus frame — that strip takes exactly 1 row. A template with `"layout": "0,0,2,3"` reserves 2 columns × 3 rows in total, but the image/focus-highlight frame itself only fills 2×2; the remaining row is the separated title strip. This is why `"separate"` items with a title typically render visually "square"-ish even though their `layout` height looks taller.
- **`"default"`** (and every other type) overlays title/text directly on top of the image instead, so the full `h` rows belong to one unified image+focus frame — no row is set aside separately.

**Consequence: give `"separate"` items a `layout`/`offset` height of at least `2`.** At height `1`, the entire row is consumed by the title strip (`h - 1 = 0`), leaving no room at all for the image/icon/focus frame.

Two more things worth knowing while templating items with images and icons:

- `image` and `icon` combine rather than conflict — `icon` renders as an overlay layered on top of `image`, not a replacement for it.
- For `"control"` and `"button"` type items, `image` doesn't get the full item as its boundary — the boundary is set the same way `icon` is placed for these types (centered for `"button"`, left-aligned for `"control"`), a smaller area than the whole item. `imageFiller` is still applied exactly as normal inside that smaller boundary (`"cover"` still covers, `"fit"` still fits, etc.) — it isn't skipped for these types, only the area it fills is different. For `"button"` items specifically, `iconSize` (`"small"`/`"medium"`/`"large"`/`"extra-large"`) resizes this `image` boundary too, not just the `icon` — see [guide/icon_sizes.json](../examples/guide/icon_sizes.json), which demonstrates all four sizes with both `icon` and `image`+`imageFiller: "cover"` side by side. (`iconSize` has no effect at all on `"control"` items — see [Content Item Object](../main-api/content/content-item-object.md).)

**Source:** [Content Item Object](../main-api/content/content-item-object.md), [Common Misconceptions](common-misconceptions.md)

---

## See Also

- [Cookbook](cookbook.md) — curated tour of real examples referenced throughout this page
- [JSON Building Guide](json-building-guide.md) — step-by-step basics
- [AGENTS.md](../AGENTS.md) — mental model and gotchas
- [Content Item Object](../main-api/content/content-item-object.md) · [Content Root Object](../main-api/content/content-root-object.md) · [Common Misconceptions](common-misconceptions.md) · [Versioning](versioning.md)
