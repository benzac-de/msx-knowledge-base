---
title: Cookbook — Real-World MSX Recipes
category: Reference
summary: A curated tour of the most practically-relevant real MSX examples, grouped by purpose — functional pages first, then beautiful layouts.
---

# Cookbook — Real-World MSX Recipes

A curated, opinionated tour of the **most practically-relevant real examples** in this KB, grouped by what you actually want to build. This is not the full catalog — for every example with searchable metadata, see [Examples Index](../examples/index.md). Here the focus is *"which real file do I start from for THIS task, and why does it matter in practice."*

Test any page instantly via the [web version](json-building-guide.md#testing-your-json-in-the-web-version).

The tour has two parts:

- **[Part A — Practical, usable pages](#part-a--practical-usable-pages):** functional building blocks — media lists, adaptive playback, live data, interaction/UX, and the plugin suite.
- **[Part B — Beautiful layouts](#part-b--beautiful-layouts):** how to turn functional data into good-looking TV screens.

For practical recommendations and rendering quirks learned from building real pages — item-type/`"list"`-vs-`"pages"` choices, `transparent`/`background` chains, image sizing math, `imageOverlay`/icon-contrast pitfalls, background video/audio persistence, player UI timing, and more — see the companion page [Best Practices & Good to Know](best-practices.md).

> **URLs:** all example URLs use `http://` on purpose (MSX auto-upgrades to `https://` in a secure context via the `secure` parameter) — see [Testing Your JSON](json-building-guide.md#testing-your-json-in-the-web-version). Features are `0.1.0` unless a minimum version is noted.

---

## Part A — Practical, usable pages

### Media lists

The four `github/base/` examples are the cleanest starting points — a templated grid whose items each trigger one media action.

- **Audio list** — [github/base/audios.json](../examples/github/base/audios.json): `template` + `items`, each `"action": "audio:{URL}"`. Audio plays with no video screen (use `background` for a backdrop).
- **Image list** — [github/base/images.json](../examples/github/base/images.json): each item uses `"action": "image:context"`, which shows the item's own `image` property full-screen.
- **Video list** — [github/base/videos.json](../examples/github/base/videos.json): each item `"action": "video:{URL}"` plus a `playerLabel` shown in the player.
- **YouTube list** — [github/base/youtube.json](../examples/github/base/youtube.json): `"action": "video:plugin:{URL}"` (since `0.1.40`) routes playback through the `youtube.html` plugin; parameters (`?id=…`) ride in the URL.
- **M3U/PLS playlists → MSX JSON** — a backend conversion service (`m3u.php`/`pls.php`) turns an existing M3U or PLS playlist file into a normal MSX content page, no client-side plugin needed. See [M3U/PLS Files](../extended-api/m3u-pls-files.md).
- **MRSS feeds → MSX JSON** — an interaction plugin (`mrss.html`) converts an MRSS feed into MSX content entirely client-side, optionally with a custom banner/background/plugin/dictionary. See [MRSS Feeds](../extended-api/mrss-feeds.md).

**Reference:** [Actions](../main-api/common/actions.md), [Content Root Object](../main-api/content/content-root-object.md)

#### M3U/PLS vs. MRSS: two different playlist-import mechanisms

Both exist for the same reason — turning a playlist/feed format you already have into MSX content without hand-writing JSON — but they get there in fundamentally different ways:

| | M3U/PLS | MRSS |
|---|---|---|
| Mechanism | Backend conversion service (`m3u.php`/`pls.php`, closed-source, runs on `msx.benzac.de`) | Interaction plugin (`mrss.html`, open source, runs client-side) |
| Where conversion happens | Server-side, on the conversion service | Client-side, inside the app itself |
| CORS requirement | The playlist file itself does **not** need CORS enabled — MSX only ever talks to the already-CORS-enabled conversion service, never to the original playlist URL | The MRSS feed URL itself **does** need CORS enabled, since MRSS is an XML format and the plugin fetches and parses that XML client-side via XHR (see the general rule below). An optional `dictionary` URL needs it for the same reason (it's fetched as JSON). `banner`/`background` do **not** need CORS — they end up rendered as a plain `<img>` tag. A `plugin` URL does not need CORS either — it is loaded as an iframe, not fetched |
| Metadata richness | Low: M3U/PLS are simple formats, usually just title/URL pairs. Metadata extensions exist (e.g. M3U's `#EXTINF` comment line) but are only partially standardized and sparsely documented | Higher: MRSS is a standardized RSS extension with a defined, richer set of fields (title, description, thumbnail, media info, categories, …), so the resulting content page can carry more detail |
| Launcher service | [m3u.html](https://msx.benzac.de/info/m3u.html) (M3U only — there is no equivalent launcher for PLS, which has little remaining real-world relevance; the `pls.php` conversion service itself is unaffected and still works) | [mrss.html](https://msx.benzac.de/info/mrss.html) |
| Min. version (launcher) | `0.1.145` — newer than the base `m3u.php`/`pls.php` conversion service itself (`0.1.30`) | `0.1.146`, matching the plugin's own minimum version |

Both launcher services set their parameters as a [base64-encoded JSON](json-building-guide.md#testing-your-json-in-the-web-version) start parameter (see [M3U/PLS Files](../extended-api/m3u-pls-files.md) and [MRSS Feeds](../extended-api/mrss-feeds.md) for the exact action syntax) and can be registered with a [URL Shortener Service](../main-api/start/setup-start-parameter.md#url-shortener-services) for the app's Setup Start Parameter input. Neither stores submitted URLs/files persistently: MRSS processing happens entirely in-plugin with no server-side storage at all; the M3U/PLS conversion service uses only session-based storage (deleted once the session closes, files above 25 MB truncated), plus a separate long-lived cookie for the favorites feature (expires after a year of non-use). The M3U launcher additionally restricts usage to legal content and may auto-block suspicious URLs.

**General rule of thumb for when CORS actually matters:** it only applies to a URL that gets *fetched as data* — JSON, XML, or any other payload read via XHR (`XMLHttpRequest`/`fetch`, the browser mechanism for requesting a URL's raw response in JavaScript instead of just letting the browser display it — used for a menu/content `data` URL, an `execute:`/`reference` endpoint, or the MRSS feed URL above). A URL that instead becomes the `src` of an `<img>` (`image`, `background`, `logo`, a banner, …), an `<iframe>` (any `plugin:` URL — video/audio/interaction), or native `<video>`/`<audio>` playback is loaded as a browser resource, not fetched as data, and does not need CORS enabled for basic display/playback. (Reading pixel data back out of a cross-origin `<video>`/`<img>`/`<canvas>` — e.g. a plugin's own custom effects — is the one case where CORS can still matter even for those tags, but that's an implementation detail of the specific plugin, not something MSX itself requires. The [Backdrop Plugin](../experts-api/plugins/backdrop-plugin.md) is a concrete real example: its `url` (backdrop image) property needs CORS precisely because the plugin reads the image's pixels to generate the atmospheric background effect — an ordinary `image`/`background` property on an ordinary item never needs this, since nothing reads its pixels back.)

#### Deep dive — Node Browser MSX (browsing a plain HTTP directory listing)

[Node Browser MSX](../overview/showcases.md#node-browser-msx) turns the plain directory-listing output of an HTTP server into a browsable MSX menu/content structure, purely by file/folder naming convention — no custom backend code needed beyond serving files with directory listing enabled. The showcase names Node.js/`http-server` (the same package used in the [Quick-Start Guide](../overview/quick-start-guide.md)) as its primary/default option, but per [Tips & Tricks → Node Browser MSX](../overview/tips-tricks.md#node-browser-msx) it works with **any** HTTP server that has directory listing and CORS enabled — that page gives the concrete Apache `.htaccess` recipe (`Options +Indexes` + the CORS headers), plus how to point the browser at a non-root folder via a `<meta name="node-browser-folder" content="path/to/folder"/>` tag in the loaded index file. It's a fast way to browse and test-drive a local or online file server directly in MSX: images become a slideshow, video/audio files are recognized and played directly with the standard player, and it also doubles as a way to host and open your own hand-written MSX JSON pages from a plain static file server — no purpose-built/MSX-aware backend needed, just directory listing and CORS turned on.

**Embedding it directly**, instead of going through the standalone showcase launcher: `content:request:interaction:access:{SERVER}@http://nb.msx.benzac.de/interaction`, with `{SERVER}` replaced by the target server's IP+port or hostname (e.g. `192.168.0.10:8080`) — see [Tips & Tricks → Node Browser MSX](../overview/tips-tricks.md#node-browser-msx).

**File/folder naming conventions it recognizes:**

- A file whose name starts with `background` (e.g. `background.jpg`) inside a folder — used as that folder's background. In the root folder, it's the default background for the whole browser.
- A file or folder whose name starts with `#` (e.g. `#background.jpg`) — hidden from the listing entirely.
- An image whose name starts with `pano` (e.g. `pano.jpg`) — loaded as a sliding panorama (via the [Panorama Plugin](../experts-api/plugins/panorama-plugin.md)) instead of a plain image.
- A JSON file whose name starts with `menu` / `content` / `panel` (e.g. `menu.json`, `content.json`, `panel.json`) — loadable as an MSX menu / content / panel respectively. To actually load one of these from JSON, reference it with the `menu:{asset:menu:{FILE}}` / `content:{asset:content:{FILE}}` action (per [Tips & Tricks → Cloud Storage Service Tabs](../overview/tips-tricks.md#cloud-storage-service-tabs), which documents this action pattern for the whole cloud-storage family, not just Node Browser MSX).
- A folder containing a file named exactly `index.json` — auto-loaded as that folder's MSX content, taking over from the generated directory listing.
- An `assets` folder, which must sit in the root folder — referenced from any MSX JSON file (menu/content/panel/`index.json`) via the inline expression `{asset:{TYPE}:{NAME}}` (e.g. `{asset:video:video1.mp4}`), where `{TYPE}` is one of `video`, `audio`, `image`, `menu`, `content`, `panel`, `folder`, or `id`, and `{NAME}` may only contain letters, numbers, `_`, `-`, `.`. Inside an `index.json` file specifically, assets from that same folder can also be referenced this way.

**Tabs within a folder:** since `index.json` is just the default entry point, a folder can swap it for another JSON file (e.g. `section2.json`) at runtime via the `interaction:commit:message:index:{FILE}` action — the standard way to build a tabbed layout across the whole cloud-storage family (Google Drive/OneDrive/Dropbox/Node Browser MSX alike). See [Tips & Tricks → Cloud Storage Service Tabs](../overview/tips-tricks.md#cloud-storage-service-tabs) for the full worked example.

**Player choice for video items:** by default, Node Browser MSX (like Google Drive/OneDrive/Dropbox MSX) plays video through the [Tizen Player](../experts-api/special/tizen-player.md) where it's available, not the HTML5X Plugin — so a plain `video:{asset:video:{FILE}}` action may not give you the HTML5X-specific features (subtitle/audio track selection, etc.). To force the [HTML5X Plugin](../experts-api/plugins/html5x-plugin.md) instead, use `video:plugin:http://msx.benzac.de/plugins/html5x.html?id={asset:id:{FILE}}` — see [Tips & Tricks → External Subtitles](../overview/tips-tricks.md#external-subtitles) for a worked example (built for Google Drive MSX, but explicitly stated to work the same way across all four cloud-storage services).

Combining the `index.json`/`menu`/`content`/`panel` conventions with the `assets` folder means you can hand-write and host a full MSX app on any plain static file server with directory listing on — no MSX-aware backend required at all, with server-side assets wired in cleanly via `{asset:...}` instead of hardcoded absolute URLs.

See [Node Browser MSX](../overview/showcases.md#node-browser-msx) for the setup start parameter and demo link.

### Adaptive & dynamic playback

- **Resolve action** — [xp/hidden_feature_13.json](../examples/xp/hidden_feature_13.json): `video:resolve:{…}` / `audio:resolve:{…}` (since `0.1.107`) ask a server for the actual media URL at playback time — used to serve a platform-specific codec, or to mint temporary/token-protected links. See [Resolve Action](../experts-api/hidden-features/resolve-action.md).
- **Play plugin (runtime player selection)** — [xp/plugin_test_10.json](../examples/xp/plugin_test_10.json): the very useful pattern where an interaction plugin picks the right player at runtime, from a pool of HTML5X, HLS, DASH, Shaka Player, Video.js, and the Android Plugin — plus the native Tizen Player where available (newer Samsung models). The plugin auto-selects a suitable default per platform, but the user can still switch manually via the player selection panel. The action resolves through a plugin page:
  ```
  "video:resolve:request:interaction:http://msx.benzac.de/media/video1.mp4@http://msx.benzac.de/interaction/play.html"
  ```
  The item also carries a `live` object (`type: "playback"`, since `0.1.70`) to show live progress and a `properties.resume:key` (extended property, since `0.1.111`) so the position resumes. In the Play Plugin specifically, `resume:key` pulls double duty: besides the normal cross-session resume behavior, it's also what carries the playback position over **when the user switches player engines mid-playback** via the selection panel — the new player picks up from the same `resume:key`-tracked position the previous one left off at, instead of restarting from the beginning. See [Play Plugin → Available Players](../experts-api/plugins/play-plugin.md#available-players), [Resolve Action](../experts-api/hidden-features/resolve-action.md).
- **Image plugin in a mixed playlist** — [xp/plugin_test_3.json](../examples/xp/plugin_test_3.json): mixes `video:{URL}`, `audio:{URL}`, and the `image.html` plugin so images, audio and video sit together in one playlist. See [Image Plugin](../experts-api/plugins/image-plugin.md).
- **Auto-jump to the next video** — [xp/next_video_test.json](../examples/xp/next_video_test.json): a more complex page that chains playback — a `live` (`type: "playback"`) object plus a `player:goto` action (internal action, `0.1.160+`) and a `ready` action (since `0.1.142`) move focus/playback to the next item automatically. Note that `ready` fires on *every* fresh display of this flagged panel, not only at startup — see [Glossary: Ready](glossary.md#ready). Good template for "play all" / autoplay-next behavior.

### Live data (EPG, progress, countdowns)

The `live` object (since `0.1.70`) re-evaluates every second while an item is visible, updates its visual properties, and fires a one-shot action on each state transition. Live inline expressions (`{now:…}`, `{from:…}`, `{progress:…}`, `{countdown:…}`) render the values — see [Live Object](../experts-api/live/live-object.md) and [Live Inline Expressions](../experts-api/live/live-inline-expressions.md).

> **A Live Inline Expression only works inside a `live` object's own properties, or in the `extension` property — never in a plain item's `headline`/`title`/`text` directly.** Putting `{now:date:...}` straight into an item's `headline` (with no `live` object) leaves it unresolved on screen. See [AGENTS.md → Inline Expressions](../AGENTS.md#4-inline-expressions--complete-overview) and the live clock example just below.
>
> **Separately, most (but not all) `live` object properties also require the item's own version of that property to already be set to a non-`null` value before `live` can update it at all.** Per [Live Object](../experts-api/live/live-object.md), this applies to `title`, `titleHeader`, `titleFooter`, `label`, `icon`, `headline`, `text`, `tag`, `badge`, `stamp`, `progress`, `image`, `extensionIcon`, and `extensionLabel` — MSX only renders these as their own element when the item's own value is present, so there's nothing for `live` to update otherwise. The **exception**: `color`-type properties (`color`, `tagColor`, `badgeColor`, `stampColor`, `progressColor`, `progressBackColor`, `wrapperColor`) recolor the item directly instead of needing a dedicated element, so they can always be live-updated regardless of the item's own initial value.

- **Lifetime** — [xp/live_test_1.json](../examples/xp/live_test_1.json): `type: "lifetime"` (relative `delay` → `duration`) walking through `coming` → `running` → `over`, each firing an `execute.action`. The clearest introduction to the state machine.
- **Playback + resume** — [xp/live_test_2.json](../examples/xp/live_test_2.json): `type: "playback"` bound to the running video, showing live progress and using `resume:clear` (internal action, `0.1.160+`) / a `resume:key` extended property (since `0.1.111`) so positions persist across sessions.
- **Extended playback + resume + selection** — [xp/live_test_3.json](../examples/xp/live_test_3.json): adds a `selection` object and an `overlay` page on top of playback+resume — a fuller "now playing" screen.
- **Live TV channels + EPG guide** — [xp/live_test_4.json](../examples/xp/live_test_4.json): a menu shell with a live clock in its `extension` property (since `0.1.82`); each menu item's `data` points to a server service (`live.php?type=list/grid/…`, `guide.php`) that builds the actual channel/EPG content with `type: "schedule"` live objects. A very effective real-world demo — the **backend patterns are documented in the deep dives below** (channels via `live.php`, the full EPG grid via `guide.php`).

  **The live clock itself** is a popular, simple pattern worth calling out on its own: `"extension": "{ico:msx-white:event} {now:date:D, M d, yyyy}{tb}{ico:msx-white:access-time} {now:time:hh:mm}"` renders as an event icon followed by the current date (e.g. "Mon, Jul 13, 2026"), a tab stop, then a clock icon followed by the current time (e.g. "19:20") — updating live every second since it's driven by `{now:date:…}`/`{now:time:…}` [Live Inline Expressions](../experts-api/live/live-inline-expressions.md), not a static string. Commonly used on pages where the current date/time is directly relevant (news pages, live-channel/EPG guides, …), but worth adding to any page for users who simply like having it on screen.

  **Want the same clock as a regular grid item instead of (or in addition to) the `extension`?** The item's own `headline`/`title`/etc. can't run Live Inline Expressions directly (per the warning above) — wrap it in that item's `live` object instead, e.g. a `"space"` item with both its own `headline` **and** a matching `live.headline` set:
  ```json
  {
      "type": "space",
      "headline": "{ico:msx-white:event} …",
      "live": {
          "type": "lifetime",
          "headline": "{ico:msx-white:event} {now:date:D, M d, yyyy}{tb}{ico:msx-white:access-time} {now:time:hh:mm:ss}"
      }
  }
  ```
  The item's own `headline` is **required**, not optional (per the general rule above, `headline` is one of the properties that needs it) — leaving it unset means `live.headline` is silently never applied. It doesn't need to match the live text exactly (it's just the item's initial/fallback state before the first live evaluation) but must be a real, non-null string. `"lifetime"`/`"airtime"` both work fine as the `live.type` here since only `{now:...}` is used.

### Deep dive — a minimal "resume playback" item (`live_test_2` pattern)

Leaving a video and picking it up later at the same position is one of the most commonly wanted features, and needs only a few additions layered on top of a normal video item — the pattern behind [Example 2 (Playback & Resume)](../experts-api/live/live-examples.md#example-2-playback--resume) (`xp/live_test_2.json`):

```json
{
    "template": {
        "type": "separate",
        "layout": "0,0,2,4",
        "icon": "msx-white-soft:movie",
        "color": "msx-glass",
        "titleFooter": "",
        "progress": -1,
        "live": {
            "type": "playback",
            "titleFooter": "",
            "running": {
                "titleFooter": "{progress:time:hh:mm:ss}"
            },
            "action": "player:show"
        },
        "properties": {
            "resume:key": "url"
        }
    }
}
```

What each addition does, and why it's there:

| Setting | Why |
|---|---|
| `titleFooter: ""` (template level) | The item's `titleFooter` must be a non-`null` value for the `live` object to be allowed to update it at all (see [Live Object](../experts-api/live/live-object.md)) — an empty string is the smallest value that qualifies. |
| `progress: -1` | A `progress` that is entirely unset can never be shown or updated; `-1` makes it updatable while keeping the bar hidden until there's something to show (see [Content Item Object](../main-api/content/content-item-object.md)). |
| `live.type: "playback"` (since `0.1.70`) | Binds the live object to the player's actual state instead of a fixed schedule/lifetime timer. Normally only `"init"`/`"running"`/`"over"` — but since `0.1.74`, combined with `resume:key` below, it can also enter `"coming"`: idle but resumable, not just actively playing (see [Live Object → `type`](../experts-api/live/live-object.md)). |
| `live.titleFooter: ""` | The base value — applies in every state that has no more specific override, including `"coming"` (since this example sets no `coming.titleFooter`) — so the footer stays empty both before the item has ever been resumable and while it's idle-but-resumable, and only fills in once `"running"` takes over below. A deliberate UX choice, not a technical limitation — a `coming.titleFooter` override would work just as well. |
| `live.running.titleFooter: "{progress:time:hh:mm:ss}"` | While *this* item is the one playing, shows the live playback position as `hh:mm:ss`, a [Live Inline Expression](../experts-api/live/live-inline-expressions.md). |
| `live.action: "player:show"` | While the live object is in state `"running"`, executing the item runs this action instead of the item's normal `action` — reopening the player instead of restarting/re-resolving the video from the top. |
| `properties["resume:key"]: "url"` | The [Extended Property](../experts-api/special/extended-properties.md) (since `0.1.111`) that actually makes MSX remember and restore the playback position, keyed here by the video's own URL. |

How the pieces actually relate — the part that isn't obvious from reading each property in isolation:

- **`resume:key` alone already gives you resume behavior.** Position persistence works even with no `live` object at all. What the `live` object adds on top is **on-screen confirmation in two situations**: while the item is actively playing (state `"running"` — the live `hh:mm:ss` footer, and `player:show` instead of a restart) *and*, since `0.1.74`, while it's sitting idle but resumable (state `"coming"`, which a `type: "playback"` live object can only enter at all because `resume:key` is set — see [Live Object → `type`](../experts-api/live/live-object.md)). In this example that `"coming"` state has no `titleFooter` override of its own, so the footer itself stays at the empty base value while idle — it's the `progress` bar (next point) that carries the at-rest confirmation, not the footer text.
- **`resume:key` saves the current position every second**, on the same one-second cycle the `live` object itself re-evaluates on — it isn't only written on pause/stop/back, so the stored position is always at most a second stale.
- **The progress bar doesn't need to be wired up manually — and it reflects the saved position even at rest, not only while playing.** Since `0.1.74`, a `live` object of type `"playback"` on an item that has `resume:key` auto-updates that item's `progress` on its own in *both* states above: continuously while `"running"` (the live playback position), and once, from the saved value, on entering `"coming"` (the last position from a previous session) — which is exactly why `progress: -1` (not simply omitting the property) is the setting to use here: it starts the bar hidden but updatable, and it fills in as soon as either state supplies a value, before the item is ever selected. This `0.1.74` date is the `live` object's own, directly documented since-version for the auto-update behavior (see [Live Object → `progress`](../experts-api/live/live-object.md)) — it predates `resume:key`'s own `0.1.111` label because that label marks something else (see the note below), not because either version is wrong.
- **For the best UX, always set `resume:key` together with a `live` object, not `resume:key` alone.** Without a `live` object, the resume position is tracked silently — nothing on the item itself shows the user which items they've already (partially) watched or how far they got, until they actually reopen the video and are seeked to the saved position. Adding the `live` object surfaces that state directly on the item while browsing — the `progress` bar already shows the saved position at rest (state `"coming"`), before the item is ever selected, and the `titleFooter` adds the live `hh:mm:ss` readout once it's actually playing (state `"running"`) — so the property drives the actual resume behavior and the `live` object makes it visible at both stages.
- **This pattern isn't tied to the plain `video:{URL}`/`audio:{URL}` actions — it works just as well through a plugin player** (e.g. `video:plugin:{URL}`), as long as the plugin reports the correct position and duration back to MSX. Resume and the live progress/`hh:mm:ss` footer both derive from that reported position/duration, not from which action variant started playback.
- **Why `0.1.74` (above) and `0.1.111` (`resume:key`'s own label) don't conflict:** they document two different things. `0.1.74` is the `live` object's own directly-sourced since-version for *checking* the `resume:key` extension property's presence to drive `progress` auto-update — a fact stated on `live-object.md` itself. `0.1.111` is when Extended Properties as a category first got per-feature version tracking at all; every Extended Property that already existed by then (including `resume:key`) was labeled `0.1.111` in that one retroactive pass (the sole exception is `trigger:{TRIGGER_KEY}`, introduced together with Extended Properties themselves, which kept its true original `0.1.58`). So `resume:key` the *property* is formally versioned from `0.1.111`, while this specific *consumer* of it (the `live` object's auto-update check) is independently documented as working since `0.1.74`. See [Versioning → How "Since Version" Annotations Work](versioning.md#how-since-version-annotations-work).

See [Live Object](../experts-api/live/live-object.md), [Extended Properties](../experts-api/special/extended-properties.md), and [Example 2 (Playback & Resume)](../experts-api/live/live-examples.md#example-2-playback--resume) for the complete worked example.

### Deep dive — the live channels / EPG backend (`live_test_4`)

`live_test_4.json` is only the shell; the interesting part is the **server** that generates the channel list and keeps every tile live. Here is the reusable pattern (the reference implementation is a single PHP endpoint).

**One endpoint, two response shapes.** The same URL returns either the channel content or a per-tile live update, chosen by its query parameters:

```php
if ($channel != -2 && $program != -2) {
    echoUpdateAction(...);   // -> a response object that updates ONE channel tile
} else {
    echoChannelList(...);    // -> the content root (type "list") with ALL channels
}
```

**1. Each channel tile ships a `setup` live object.** A `type: "setup"` live object is never "live" itself — it runs its `action` **once, when the tile becomes visible** (see [Live Object](../experts-api/live/live-object.md)). Here that action calls the service back for this channel:

```json
{
    "id": "channel0",
    "titleHeader": "{txt:msx-white:Channel 1}",
    "playerLabel": "Channel 1",
    "action": "video:plugin:http://msx.benzac.de/plugins/live.html?channel=0&program=-1",
    "live": {
        "type": "setup",
        "action": "execute:service:http://example.com/live.php?type=default&channel=0&program=-1"
    }
}
```

`execute:service:` (since `0.1.90`) runs in parallel, so all visible channels resolve their current program at once (up to 128 concurrent).

**2. The service answers with an `update` + a `schedule` live object.** The response replaces the tile's live object with the real current program and — crucially — arms an `over` action that will fetch the **next** program when this one ends:

```json
{
    "response": {
        "status": 200,
        "text": "OK",
        "data": {
            "action": "update:content:channel0",
            "data": {
                "live": {
                    "type": "schedule",
                    "from": 1730000000000,
                    "to": 1730000600000,
                    "titleHeader": "Program 1.1",
                    "titleFooter": "{from:time:hh:mm} - {to:time:hh:mm}",
                    "running": { "titleFooter": "{from:time:hh:mm} - {to:time:hh:mm} | {countdown:text:hm} left" },
                    "over": {
                        "action": "execute:service:http://example.com/live.php?type=default&channel=0&program=1"
                    }
                }
            }
        }
    }
}
```

**3. The `over` action closes the loop.** When the program ends, its one-shot `over.action` calls the service again with the next `program` index; the service returns a fresh `schedule` whose own `over` points to the one after that, and so on. The result is a **self-sustaining, server-driven EPG**: the client hardcodes nothing, and each tile keeps advancing as long as it stays visible. (This is the concrete form of the polling loop noted in [Live Object](../experts-api/live/live-object.md).) The `from`/`to` values are epoch-millisecond timestamps the server computes per program.

**4. Focus preview (optional).** For the `preview` layout, each tile also carries a `selection` object whose `action` is `update:content:overlay:preview` with a `schedule` live object in its `data`; a `space` item `id: "preview"` on an `overlay` page (plus `navigationSpan`) renders the highlighted program's description as you move focus — a classic EPG side panel.

**5. Player controls.** The template's `properties` give it a broadcast feel — e.g. `"progress:type": "fix:Live"`, `"label:extension": "{ico:live-tv}"`, the transport buttons disabled (`"button:play_pause:enable": "false"`, …), and `"button:content:action": "[release:panel|panel:request:player:program]"` to open program info.

> One service, many presentations: the same endpoint also serves `context=panel` and the `type` variants `list` / `grid` / `compact` / `preview` / `overview` from the same logic.

### Deep dive — the TV Guide (EPG grid) backend (`guide.php`)

The **TV Guide** menu item (`"data": "user:http://…/guide.php"`) points to a second service that renders a full EPG grid. It reuses the live-object idea but adds several patterns worth stealing.

**Stateful + credentialed.** Unlike `live.php`, the guide is *stateful* — it uses server sessions to remember which channel window and start time you are viewing. That is exactly why the menu `data` carries the **`user:` prefix** (Main Action variant, since `0.1.30`): it enables `withCredentials` so the session cookie is sent. Every action back to the service is credentialed the same way (`execute:user:…`, `panel:user:…`). See [Actions](../main-api/common/actions.md).

**Grid via `compress` + time-positioned `control` items.** The guide is `type: "pages"`, `compress: true` (16×8 grid): 2 columns hold the channel names (on an `overlay` page), and 4 half-hour slots span the remaining 14 columns. Each program is a `control` item whose `layout` column encodes its start time and whose width encodes its duration, with a fractional `offset` for sub-cell precision:

```json
{
    "id": "program0_3_0",
    "type": "control",
    "layout": "2,1,3,1",
    "offset": "0.000,-0.5,0.500,0",
    "label": "{col:msx-white-soft}...",
    "action": "panel:user:http://example.com/guide.php?type=info&channel=0&program=3",
    "live": {
        "type": "schedule",
        "from": 1730000000000,
        "to": 1730001800000,
        "label": "Program 1.4",
        "over": { "label": "{col:msx-white-soft}Program 1.4", "action": "[invalidate:content|reload:content]" }
    },
    "selection": {
        "important": true,
        "action": "data",
        "data": {
            "actions": [
                { "action": "update:content:description_0", "data": { "headline": "Program 1.4", "text": "..." } },
                { "action": "update:content:image_0", "data": { "live": { "type": "schedule", "from": 1730000000000, "to": 1730001800000 } } },
                { "action": "update:content:event_0", "data": { "live": { "type": "schedule", "from": 1730000000000, "to": 1730001800000, "running": { "text": "..." } } } }
            ]
        }
    }
}
```

Three reusable patterns in that one item:

- **Focus → preview fan-out.** The `selection.action` is `"data"` (the multi-action, since `0.1.58`); its `data.actions[]` updates three separate preview items (`description_0`, `image_0`, `event_0`) at once, so moving the highlight refreshes the synopsis, poster and event strip below the grid. See [Selection Object](../experts-api/selection/selection-object.md).
- **Auto-refresh at the edge.** Only the *last* program in the visible window sets `over.action: "[invalidate:content|reload:content]"`, so the whole grid reloads exactly when the current window runs out.
- **Open details.** Selecting a program runs `panel:user:…?type=info&channel=N&program=M`; the service returns a small (also credentialed) content page with the synopsis and action buttons.

**Time headers via number expressions.** The slot labels are fixed timestamps formatted inline — `"{num:1730000000000:date:D, dd.mm.}"` and `"{num:1730000000000:time:hh:mm}"`. See [Number Inline Expressions](../experts-api/hidden-features/number-inline-expressions.md).

**Channel paging via POST → session → reload.** The up/down buttons do not navigate — they POST the new window to the service, which stores it in the session and answers `reload:content`:

```json
{
    "id": "channels_down_0",
    "type": "button",
    "enable": true,
    "layout": "14,7,1,1",
    "icon": "keyboard-arrow-down",
    "action": "[invalidate:content|execute:user:http://example.com/guide.php]",
    "data": { "channelOffset": 5 }
}
```

**Cross-link into the channels view.** The info panel's "watch" button jumps straight to the `live.php` channels content and starts the chosen channel using the focus separator (`>`): `"content:http://example.com/live.php>channel0>execute"` — load the content, focus item `channel0`, then execute it. See [Focus Separator](../experts-api/hidden-features/focus-separator.md).

> The service also returns a few non-MSX bookkeeping keys at the top level (`sessionId`, `sessionTimestamp`, `channelCount`, `channelOffset`, `startTimestamp`, `executionTime`); MSX ignores unknown top-level properties.

### Interaction & UX

- **Option shortcut** — [xp/hidden_feature_14.json](../examples/xp/hidden_feature_14.json): items inside an `options` panel get a `key` property so a single remote key triggers them directly — very common in practice for quick actions. See [Option Shortcut](../experts-api/hidden-features/option-shortcut.md).
- **Selection preview on focus** — [xp/selection_test_1.json](../examples/xp/selection_test_1.json) & [xp/selection_test_2.json](../examples/xp/selection_test_2.json): the `selection` object (since `0.1.110`) runs an action immediately when an item *receives focus* (auto-preview, load a backdrop, update another item) — it has no delay property of its own; test 2 layers a delay on top via the generic `delay:selection:{DELAY}:{ACTION}` internal action, and also adds an `underlay` and a `background`. See [Selection Object](../experts-api/selection/selection-object.md).
- **Image options (rotate images)** — [xp/hidden_feature_16.json](../examples/xp/hidden_feature_16.json): a simple image list that enables the image options panel via `properties` (`"image:options": true`, `"image:icon"`, `"image:action"`), letting the user rotate/adjust the shown image. See [Image Options](../experts-api/hidden-features/image-options.md).
- **Extended properties** — [xp/properties_test.json](../examples/xp/properties_test.json): a broad showcase of advanced item `properties` (player buttons, extensions, per-item overrides). The best single file to skim when you need "is there a property for…?". See [Extended Properties](../experts-api/special/extended-properties.md).
- **Input plugin (logins, search)** — [xp/plugin_test_13.json](../examples/xp/plugin_test_13.json): drives the input/interaction plugin (`content:request:interaction:…`, `execute:fetch:…`) for text entry — logins, search boxes, codes. Requires a matching server counterpart. See [Input Plugin](../experts-api/plugins/input-plugin.md).

### Plugins (media, immersive, platform, ads)

Plugins are HTML pages MSX loads to extend playback or interaction; JavaScript is optional. All are invoked from JSON via `video:plugin:{URL}` or the interaction actions — the JSON side is shown here, the plugin page itself is external.

- **Video plugins overview** — [xp/plugin_test_1.json](../examples/xp/plugin_test_1.json): one page wiring up many ready-made video plugins — `youtube.html`, `vimeo.html`, `twitch.html`, `dailymotion.html`, `facebook.html`, `soundcloud.html`, `wistia.html`, `html5.html`, and `template.html`. The go-to reference for "which plugin URL do I use for source X". See [Video/Audio Plugin](../experts-api/plugins/video-audio-plugin.md).
- **Interaction plugin** — [xp/plugin_test_2.json](../examples/xp/plugin_test_2.json): `interaction:load/reload/unload` and `content:request:interaction:…` to a background handler page (Template, Search, Settings, Observer). The JavaScript handler pattern is covered in the **deep dive below**. See [Interaction Plugin](../experts-api/plugins/interaction-plugin.md).
- **HTML5X plugin (build your own)** — [xp/plugin_test_4.json](../examples/xp/plugin_test_4.json): `video:plugin:…/html5x.html` with audio/subtitle tracks configured via `html5x:*` properties. A great reference when writing your **own** video plugin — even used as-is it is handy. The JavaScript player is covered in the **deep dive below**. See [HTML5X Plugin](../experts-api/plugins/html5x-plugin.md).
- **Panorama** — [xp/plugin_test_5.json](../examples/xp/plugin_test_5.json): `video:plugin:…/panorama.html` displays a wide (>16:9, e.g. 2:1) panorama *image* as a sliding pan — not a video, despite being loaded via the `video:plugin:` mechanism. See [Panorama Plugin](../experts-api/plugins/panorama-plugin.md).
- **Atmospheric backgrounds** — [xp/plugin_test_6_menu.json](../examples/xp/plugin_test_6_menu.json) + [xp/plugin_test_6_content.json](../examples/xp/plugin_test_6_content.json): a subtle full-screen looping video behind a transparent menu (a real crowd-pleaser / "wow" effect). The menu's `ready` action calls `execute:service:video:data:…/background.php`; the content uses `video:auto:…` with the `background.html` plugin. See [Background Plugin](../experts-api/plugins/background-plugin.md).
- **Backdrop plugin** — [xp/plugin_test_8.json](../examples/xp/plugin_test_8.json): another high-impact visual — a cross-fading backdrop behind content (`underlay`, `selection`, `delay:backdrop`). See [Backdrop Plugin](../experts-api/plugins/backdrop-plugin.md).
- **Android plugin** — [xp/plugin_test_9.json](../examples/xp/plugin_test_9.json): `video:plugin:…/android.html` — the native player path for Android/Fire TV devices. See [Android Plugin](../experts-api/plugins/android-plugin.md).
- **IMA ads** — [xp/plugin_test_11.json](../examples/xp/plugin_test_11.json): `video:plugin:…/ima.html` to insert Google IMA advertising into your content (uses `compress`, since `0.1.123`). Most of the ad setup is done server-side. See [IMA Plugin](../experts-api/plugins/ima-plugin.md).
- **MRSS + Ad plugin** — [xp/plugin_test_7.json](../examples/xp/plugin_test_7.json): loads an MRSS feed (via `reference`) together with the `ad.html` plugin. For richer MRSS examples see [MRSS Feeds](../extended-api/mrss-feeds.md). See also [Ad Plugin](../experts-api/plugins/ad-plugin.md).
- **Paging** — [xp/plugin_test_12.json](../examples/xp/plugin_test_12.json): a menu that demonstrates paging through large data sets. Needs a matching server endpoint. See [Paging Plugin](../experts-api/plugins/paging-plugin.md).
- **Tizen (Samsung) player + interaction** — [xp/tizen_test.json](../examples/xp/tizen_test.json): configures the Samsung native player via `tizen:*` extended properties (`tizen:buffer:size`, `tizen:load/ready/start`) and adds a button that loads the Tizen interaction plugin (`content:request:interaction:init@…/tizen.html`). Samsung Tizen 2016+ models, min MSX `0.1.128`. See [Tizen Player](../experts-api/special/tizen-player.md).
- **Benchmark / performance tests** — [Drawing Plugin](../experts-api/benchmark/drawing-plugin.md), [Particles Plugin](../experts-api/benchmark/particles-plugin.md), [Renderer Plugin](../experts-api/benchmark/renderer-plugin.md): three `video:plugin:…` pages built to stress-test a TV device's rendering performance/capabilities. None of them plays an actual video — Drawing and Particles render to a `<canvas>`, Renderer animates plain HTML/CSS elements instead.

A video/audio plugin doesn't have to contain an actual `<video>`/`<audio>` element at all — per [Video/Audio Plugin](../experts-api/plugins/video-audio-plugin.md), it is "nothing more than a simple HTML page that is loaded into an iframe," and "any HTML page can be loaded as a video/audio plugin." The benchmark tests above are a good real-world illustration of just how far that goes: none of them touches a real video element, yet each one loads and behaves exactly like any other `video:plugin:{URL}` item (same player controls, same extended properties, same lifecycle). The same freedom applies to real content, not just benchmarks — e.g. an animated slideshow/presentation driven entirely by an iframe.

**Whether a given plugin is worth loading via `video:plugin:{URL}` or `audio:plugin:{URL}` depends on whether its visual output is something you actually want to show.** Technically the two load the exact same plugin iframe — the only difference is that an `audio:plugin:{URL}` iframe is kept hidden, so all you see is the background (the same as a plain `audio:{URL}` item), while `video:plugin:{URL}` shows whatever the iframe renders. Which one fits depends on the specific plugin and use case, not a fixed rule — e.g. the [SoundCloud plugin](../experts-api/plugins/video-audio-plugin.md) (listed above) also renders a waveform visualization and cover artwork, so `video:plugin:` is the better fit to actually show that; conversely, a YouTube video that's really just music with a static thumbnail could just as reasonably be loaded via `audio:plugin:` instead, with your own custom `background` shown in its place. A dedicated audio-visualizer plugin is the clearest case for `video:plugin:`, but it isn't the only pattern where the choice matters.

### Deep dive — building an interaction plugin (`plugin_test_2`)

An interaction plugin is a headless HTML page MSX loads into a background iframe. It has **no UI of its own** — it only *handles events, data, and requests*, and in return hands MSX content to render or actions to run (see [Interaction Plugin](../experts-api/plugins/interaction-plugin.md)). `plugin_test_2.json` wires up four of them:

```
{ "title": "Template", "action": "interaction:load:http://msx.benzac.de/interaction/template.html" },
{ "title": "Settings Example", "action": "content:request:interaction:init@http://msx.benzac.de/interaction/settings.html" }
```

**The handler contract.** You register one handler object, implement the callbacks you need, then `setupHandler` + `init`:

```js
function MyHandler() {
    this.init = function() { /* ... */ };
    this.ready = function() { TVXInteractionPlugin.success("Ready."); };
    this.handleEvent = function(data) { /* app/video/slider/settings events */ };
    this.handleData = function(data) { /* data committed back from MSX */ };
    this.handleRequest = function(dataId, data, callback) { callback(/* content or data */); };
    this.onError = function(message, error) { /* ... */ };
}
TVXPluginTools.onReady(function() {
    TVXInteractionPlugin.setupHandler(new MyHandler());
    TVXInteractionPlugin.init();
});
```

See [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md) for the full method list, and [Plugin Events Reference](../experts-api/plugins/plugin-events-reference.md) for the `handleEvent` event types. The bare `template.html` handler just logs each callback; the **settings** handler shows the useful pattern below.

**1. The plugin serves the UI (`handleRequest`).** `content:request:interaction:init@…/settings.html` makes MSX load the plugin and call `handleRequest("init", …)`; whatever the handler passes to `callback(...)` becomes the rendered content — here a normal Content Root Object built in JavaScript (pages of `control` items):

```js
this.handleRequest = function(dataId, data, callback) {
    if (dataId == "init") {
        callback(settings);            // a full MSX content root (pages of controls)
    } else if (dataId == "special") {
        callback(specialControlPanel); // returned for panel:request:interaction:special
    } else {
        callback();
    }
};
```

`handleRequest` may answer asynchronously — just call `callback(result)` later (after a fetch, `setTimeout`, etc.).

**2. MSX commits user input back (`interaction:commit` → `handleData`).** Each control carries `action: "interaction:commit"` with a `data` payload; selecting it sends that payload to the plugin's `handleData`:

```json
{
    "id": "check_box_0-0",
    "type": "control",
    "label": "Check Box 0-0",
    "extensionIcon": "msx-white-soft:check-box-outline-blank",
    "action": "interaction:commit",
    "data": { "action": "settings:item:check_box_0-0" }
}
```

**3. The plugin updates its model and re-renders (`executeAction`).** `handleData` mutates the in-memory model (flip `checked`, swap `extensionIcon`) and then tells MSX to re-fetch:

```js
this.handleData = function(data) {
    var action = data.data.action;                         // "settings:item:check_box_0-0"
    if (action.indexOf("settings:item:") == 0) {
        updateSettingsItem(action.substr(14), data.data);  // flip state in the model
        TVXInteractionPlugin.executeAction("reload:content"); // re-render (or "reload:panel")
    }
};
```

That round-trip — **request → render → commit → update model → reload** — is how you build fully stateful, server-less UIs (settings screens, forms, multi-select panels, star ratings) entirely inside a plugin. Nested panels work the same way: a control can `panel:data` into a sub-panel whose items commit `[back|interaction:commit]`, or `panel:request:interaction:special` to have the handler serve the panel too. (`search.html` and `observer.html` are two more handlers wired up by the same JSON.)

**Immersive Mode — aligning a plugin that *does* show content.** Most interaction plugins are headless, but if yours renders visible content (the `template.html` handler shows a debug log), it should align with **Immersive Mode** — a display setting, default-on in recent MSX versions, that centers and scales the main frame — otherwise those elements can land outside the 16:9 frame. (It is a choice, not a rule: full-bleed visuals such as backgrounds should fill the whole screen anyway, while logos, text and boxes want to stay inside the frame. The [Backdrop plugin](../experts-api/plugins/backdrop-plugin.md) does exactly this: its atmospheric background is drawn fullscreen, while the backdrop image sits inside the content wrapper and so stays aligned to the 16:9 frame.) The shared `content-controller.js` (referenced by `template.html`, delegated to from the handler's `init`/`ready`/`handleEvent`) reads the current mode and repositions your wrapper:

```js
var setupWrapper = function() {
    if (immersiveMode == 1) {                     // center + scale to the main frame
        contentWrapper.css({
            left: "50%", top: "50%",
            width: TVXSettings.SCREEN_WIDTH + "px",
            height: TVXSettings.SCREEN_HEIGHT + "px",
            marginLeft: -Math.floor(TVXSettings.SCREEN_WIDTH / 2) + "px",
            marginTop:  -Math.floor(TVXSettings.SCREEN_HEIGHT / 2) + "px",
            transform: TVXSettings.ZOOM_FACTOR != 1 ? "scale(" + TVXSettings.ZOOM_FACTOR + ")" : ""
        });
    } else if (immersiveMode == 0) {              // reset
        contentWrapper.css({ left: "", top: "", width: "", height: "", marginLeft: "", marginTop: "", transform: "" });
    }
};
this.validate = function() {
    TVXInteractionPlugin.onValidatedSettings(function(data) {
        immersiveMode = TVXTools.strToNum(data.info.application.settings.immersiveMode, -1);
        setupWrapper();
    });
};
this.handleEvent = function(data) {
    if (data.event == "app:resize") {                     // zoom may have changed
        TVXInteractionPlugin.validateSettings(setupWrapper);
    } else if (data.event == "settings:immersive_mode") { // user toggled it
        immersiveMode = TVXTools.strToNum(data.value, -1);
        setupWrapper();
    }
};
```

Read the current mode via `onValidatedSettings`/`validateSettings` (`data.info.application.settings.immersiveMode`), size to `TVXSettings.SCREEN_WIDTH`/`SCREEN_HEIGHT`, scale by `TVXSettings.ZOOM_FACTOR`, and re-run on the `app:resize` and `settings:immersive_mode` events. See [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md) and [Plugin Events Reference](../experts-api/plugins/plugin-events-reference.md).

### Deep dive — building a video plugin (`plugin_test_4`, HTML5X)

A video plugin is an HTML page with a media element (here `<video id="player">`) that MSX loads via `video:plugin:{URL}`. You implement a **player object**; MSX calls its methods to drive playback and polls it for the progress bar. The anchor item plays a URL and configures subtitles through `html5x:*` item `properties`, wiring its content button to a player panel:

```json
{
    "playerLabel": "Sintel © copyright Blender Foundation",
    "action": "video:plugin:http://msx.benzac.de/plugins/html5x.html?url=http://msx.benzac.de/media/sintel/sintel.mp4",
    "properties": {
        "resume:position": "102",
        "button:content:icon": "settings",
        "button:content:action": "panel:request:player:options",
        "html5x:subtitle:en:English": "http://msx.benzac.de/media/sintel/en.vtt",
        "html5x:subtitle:de:Deutsch": "http://msx.benzac.de/media/sintel/de.vtt"
    }
}
```

> **Video sizing & fullscreen.** Since a plugin is just an HTML page, you can style it however you want; by convention these plugins size the media element to fill the whole frame (`video { width: 100%; height: 100%; }`). On a TV that frame is 16:9, but MSX also runs on mobile and desktop, where the frame matches the actual display — so the video fills the real screen for true fullscreen (source videos are not always 16:9).

**The player contract.** Register a player object; MSX calls its lifecycle, transport and state methods:

```js
function MyPlayer() {
    var player = null;
    this.init = function() { player = document.getElementById("player"); /* add media listeners */ };
    this.ready = function() {
        TVXVideoPlugin.startLoading();
        TVXVideoPlugin.requestData("info:base", function(data) {
            // resolve the source from the plugin URL params:
            setupVideoWithId(TVXServices.urlParams.get("id")) ||
            setupVideoWithUrl(TVXServices.urlParams.get("url"));
        });
    };
    this.play  = function() { player.play(); };
    this.pause = function() { player.pause(); };
    this.stop  = function() { player.pause(); };   // HTML5 has no real stop
    this.getDuration = function() { return player.duration; };
    this.getPosition = function() { return player.currentTime; };
    this.setPosition = function(p) { player.currentTime = p; };
    this.getUpdateData = function() {              // polled for the progress bar
        return { position: this.getPosition(), duration: this.getDuration(), speed: this.getSpeed() };
    };
    this.handleData = function(data) { handleMessage(data.message); };
    this.handleRequest = function(dataId, data, callback) { callback(createResponseData(dataId)); };
}
TVXPluginTools.onReady(function() {
    TVXVideoPlugin.setupPlayer(new MyPlayer());
    TVXVideoPlugin.init();
});
```

**Driving MSX from media events.** Map the `<video>` element's events onto MSX calls so the app UI mirrors real playback state:

```js
player.addEventListener("canplay", function() { TVXVideoPlugin.startPlayback(true); }); // accelerated start
player.addEventListener("playing", function() { TVXVideoPlugin.stopLoading(); TVXVideoPlugin.setState(TVXVideoState.PLAYING); });
player.addEventListener("pause",   function() { TVXVideoPlugin.setState(TVXVideoState.PAUSED); });
player.addEventListener("waiting", function() { TVXVideoPlugin.startLoading(); });
player.addEventListener("ended",   function() { TVXVideoPlugin.stopPlayback(); });
player.addEventListener("error",   function() { TVXVideoPlugin.error("Video error: ..."); });
```

`requestData(dataId, callback)` pulls data from MSX (e.g. `"video:info"` to read the item's `properties`); `requestInteractionResponse(id, callback)` resolves an `id`-based source through the interaction/resolve mechanism (so the same plugin supports `?id=…` as well as `?url=…`).

**The "extended" part — custom player panels (the reason to build your own).** This is the same round-trip as an interaction plugin, but on the *player* side. The item's content button runs `panel:request:player:options` → MSX calls the player's `handleRequest("options", …)`, which returns a Content Root Object built in JS (the audio/subtitle/fullscreen menu). Each panel item commits back with `player:commit:message:{MESSAGE}` → the player's `handleData` receives `data.message` and acts:

```json
{ "label": "English", "action": "player:commit:message:subtitle:0" }
```
```js
this.handleData = function(data) {
    var message = data.message;                   // "subtitle:0"
    if (message.indexOf("subtitle:") == 0) {
        TVXVideoPlugin.executeAction("cleanup");   // close the panel
        selectSubtitleTrack(TVXTools.strToNum(message.substr(9), -1), true, true);
    }
};
```

Subtitles come from the item `properties`: the plugin reads every `html5x:subtitle:{lang}:{Label}` entry, builds `<track>` elements, and remembers the chosen language via `TVXServices.storage` for next time. On top of that this plugin adds CORS handling (`html5x:cors` → `crossOrigin`/proxy), a related-content option (`html5x:content`), iOS/Mac fullscreen, live-stream duration handling (infinite `duration` → track the max position), and audio/subtitle language indicators shown via `setupExtensionLabel`.

> **Drawing visual overlays in the 16:9 frame.** A video plugin can align its own on-screen visuals with Immersive Mode the same way an interaction plugin does (via `TVXVideoPlugin.onValidatedSettings`/`validateSettings` and `TVXSettings`). This is rarely needed — most video plugins draw nothing of their own over the video — but the option exists if you render visual overlays (these are display-only; a plugin never receives key or mouse input).

See [Video/Audio Plugin](../experts-api/plugins/video-audio-plugin.md) for the player skeleton and [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md) for every `TVXVideoPlugin` method.

---

## Part B — Beautiful layouts

Once the data works, these show how to make it look like a polished TV UI. All live under `github/extended/` and **require MSX `0.1.112+`**, except [list3_section0.json](../examples/github/extended/list3_section0.json)–[list3_section3.json](../examples/github/extended/list3_section3.json) below, which require `0.1.144` (their tab-switching relies on `replace:content:`). They are filled with dummy images and built **statically** — in production you would generate them from a server or assemble them with a plugin, but the layout techniques are exactly the same.

They lean on the same toolbox: `template` + `items`, decorative `space` items, `offset` for pixel-nudging, `imageFiller: "cover"`, `imageOverlay`, `wrapperColor`, `enumerate` numbering, `id:`-prefixed `group` values for independent per-page enumeration counters, `teaser`/`control` item types, `selection` previews, `preload` for smooth paging, and `underlay`/`overlay` decoration pages.

- **Rich content lists** — [list0.json](../examples/github/extended/list0.json), [list1.json](../examples/github/extended/list1.json), [list2.json](../examples/github/extended/list2.json): vertical lists combining `space`-item page headers with `group: "id:{PAGE_ID}"` (invisible per `content-item-object.md`'s `id:` rule — it only gives each page's items their own separate enumeration counter, e.g. resetting to `(1/4)` on every page instead of continuing `(5/8)`, `(9/12)`, …, not a visible label), `enumerate`, `imageOverlay`, `teaser`/`control` items and `selection` — the best all-round layout references.
- **Tabbed sections (replace + flag)** — [list3_section0.json](../examples/github/extended/list3_section0.json) … [list3_section3.json](../examples/github/extended/list3_section3.json): a four-tab layout that swaps content in place via `flag: "tabs"` + `[invalidate:content|replace:content:tabs:{URL}]` (since `0.1.144` — a no-op on older clients) — no history entry per tab. See [Replace Action](../experts-api/hidden-features/replace-action.md).
- **List with footer & caption** — [list4.json](../examples/github/extended/list4.json): `template` + `items` with an `underlay` decoration page, a `footer`, and `captionUnderlay` (since `0.1.153`).
- **List with overlay & narrowed navigation** — [list5.json](../examples/github/extended/list5.json): `overlay` decoration, a `footer`, and `navigationSpan` to narrow the list scroll indicators so they stay centered under the narrower scrollable list column instead of the full content width, since an `overlay` preview panel occupies the other half of the screen.
- **Paged (horizontal) layouts** — [pages0.json](../examples/github/extended/pages0.json) (template + `underlay` + `selection`) and [pages1.json](../examples/github/extended/pages1.json) (`teaser` items across pages).
- **Nested sub-lists** — [sublist0.json](../examples/github/extended/sublist0.json), [sublist1.json](../examples/github/extended/sublist1.json), [sublist3.json](../examples/github/extended/sublist3.json): `template` + `items` sub-lists (rows within rows) for catalog-style browsing.

**Reference:** [Content Root Object](../main-api/content/content-root-object.md), [Content Item Object](../main-api/content/content-item-object.md), [Extended Properties](../experts-api/special/extended-properties.md)

### Deep dive — `layout` drives navigation, `offset` doesn't (`list0.json`)

MSX's directional-navigation engine (up/down/left/right) decides what to focus next **purely from each item's `layout`** (its grid position) — `offset` is invisible to it entirely; `offset` only nudges the rendered position/size, with zero effect on what gets focused next. That means you can deliberately make an item's logical grid footprint narrower or wider than its visual footprint (`layout` + a compensating `offset`) to steer navigation without changing how anything looks.

The general shape of the algorithm: while still on the current page, moving in a direction tries to preserve the perpendicular axis (X when moving vertically, Y when moving horizontally) as much as possible — staying "in lane" so gaps get skipped over rather than causing a diagonal jump — until the outermost edge of the page is reached in that direction. Once the edge is reached, MSX either wraps around or moves to the next/previous page, again trying to preserve X/Y alignment as much as possible.

[list0.json](../examples/github/extended/list0.json) exploits exactly this to make a "Show All" button reachable at the end of a row of four selectable items, without it looking like a fifth grid cell:

```json
{
    "layout": "9,1,2,3",
    "offset": "0,-0.5,1,0",
    "titleHeader": "{col:msx-white}Fourth Item"
},
{
    "type": "button",
    "layout": "11,1,1,1",
    "offset": "0,-1,0,-0.5",
    "icon": "more-horiz"
}
```

- The fourth selectable item's `layout` is one column narrower (`w: 2`, columns 9–10) than its siblings (`w: 3` each, e.g. `"6,1,3,3"`), but its `offset` width component (`+1`) adds that column back visually — so it renders at exactly the same size as the other three. **Logically**, though, its grid footprint stops at column 10.
- The button sits at `"layout": "11,1,1,1"` — the grid's outermost column (11 of 0–11) — on the same row (`y: 1`) as the selectable items.
- Because the fourth item's *logical* layout ends at column 10, pressing right from it lands exactly on column 11 — the button — instead of overflowing past the page edge (which would otherwise wrap to the menu or back to the first item).
- Because the button's `layout` `y` is still `1` (the same logical row as the selectable items), pressing up/down from a focused selectable item does **not** divert onto the button — up/down navigation looks past it for the next row/page, correctly moving to the next page while preserving X/Y, rather than landing on "Show All" when the user just wanted to move up or down (which would feel unintuitive).

The button's own `offset` (`"0,-1,0,-0.5"`) shifts and enlarges it purely visually (so it reads as its own tile, overlapping the row above) — none of that affects any of the navigation behavior above, only its `layout` does.

**What happens if you get this wrong:** if the fourth item instead used `"layout": "9,1,3,3"` + `"offset": "0,-0.5,0,0"` — full width, no compensating offset — it would render **visually identical** to the version above, but its logical grid footprint would now reach all the way to column 11, exactly overlapping the button's own `layout`. The button would still be visible on screen, but it would become **unreachable via remote/keyboard navigation entirely**: pressing right from the fourth item finds no further logical grid space to move into (the item's own layout already reaches the page edge there), so navigation overflows immediately instead of ever landing on the button. Getting the `layout`/`offset` split right isn't just a cosmetic nicety here — it's the only thing that makes the button reachable at all.

See [Content Item Object](../main-api/content/content-item-object.md) for `layout`/`offset`, and [JSON Building Guide → Actions](json-building-guide.md#step-8--actions) for the general focus-navigation note.

---

## See Also

- [Best Practices & Good to Know](best-practices.md) — practical recommendations and rendering quirks learned from building real pages
- [JSON Building Guide](json-building-guide.md) — step-by-step basics
- [AGENTS.md](../AGENTS.md) — mental model and gotchas
- [Examples Index](../examples/index.md) — full searchable catalog of all 101 examples
- [Actions Reference](actions-reference.md) · [Type Definitions](type-definitions.md) · [Versioning](versioning.md)
