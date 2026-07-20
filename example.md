---
title: Example Chat — Building a YouTube MSX Page with Resume, Live Progress, and a Background
category: Example
summary: "Verbatim chat transcript showing how an LLM uses the MSX Knowledge Base (llms.txt + AGENTS.md as index) to build a YouTube Content Root Object step by step — video:plugin: actions, the resume:key extended property, a live playback progress footer, a background image with the msx-keep-ratio keyword — and then how to get the result hosted and running on a real TV, including generating a base64 web-preview link."
---

# Example Chat — Building a YouTube MSX Page with Resume, Live Progress, and a Background

This is a verbatim export of a chat session that used the MSX Knowledge Base (`llms.txt` + `AGENTS.md` as index) to build up an MSX JSON page step by step. User messages are shown as block quotes; assistant replies follow directly below each one, unedited.

---

> Read `llms.txt` and `AGENTS.md` in this repository, then use them as the index and usage guide for the MSX Knowledge Base. Read further files from this repository on demand to answer my questions about Media Station X.

I've loaded `llms.txt` (the file index) and `AGENTS.md` (the usage guide) for the MSX Knowledge Base. I now have the mental model, JSON hierarchy, action syntax, inline expressions, colors, versioning rules, and the file index to pull from on demand.

What would you like to know about Media Station X?

---

> Create an MSX JSON from the following YouTube links:
> - https://www.youtube.com/watch?v=x_qR3KEsBHU
> - https://www.youtube.com/watch?v=e-l7Wy70nbE
> - https://www.youtube.com/watch?v=xIWYN7oOavw

Based on the [YouTube, Vimeo & Co.](docs/extended-api/youtube-vimeo-co.md) page, YouTube videos use the `video:plugin:{URL}` action pointing to the YouTube plugin with the video ID (`0.1.40+`). Here's a Content Root Object with all three videos:

```json
{
    "type": "list",
    "headline": "YouTube Videos",
    "template": {
        "type": "separate",
        "layout": "0,0,4,4",
        "icon": "msx-white-soft:movie",
        "color": "msx-glass"
    },
    "items": [
        {
            "badge": "{txt:msx-white:YouTube}",
            "badgeColor": "#ff0000",
            "title": "Video 1",
            "image": "https://img.youtube.com/vi/x_qR3KEsBHU/hqdefault.jpg",
            "playerLabel": "YouTube - Video 1",
            "action": "video:plugin:http://msx.benzac.de/plugins/youtube.html?id=x_qR3KEsBHU"
        },
        {
            "badge": "{txt:msx-white:YouTube}",
            "badgeColor": "#ff0000",
            "title": "Video 2",
            "image": "https://img.youtube.com/vi/e-l7Wy70nbE/hqdefault.jpg",
            "playerLabel": "YouTube - Video 2",
            "action": "video:plugin:http://msx.benzac.de/plugins/youtube.html?id=e-l7Wy70nbE"
        },
        {
            "badge": "{txt:msx-white:YouTube}",
            "badgeColor": "#ff0000",
            "title": "Video 3",
            "image": "https://img.youtube.com/vi/xIWYN7oOavw/hqdefault.jpg",
            "playerLabel": "YouTube - Video 3",
            "action": "video:plugin:http://msx.benzac.de/plugins/youtube.html?id=xIWYN7oOavw"
        }
    ]
}
```

Notes:
- This is a **Content Root Object** (`type: "list"`, templated layout) — serve it directly via `content:{URL}` from a menu item's `data`, or wrap it in a Start Object's `parameter: "content:{URL}"`.
- The `image` values point to YouTube's public thumbnail CDN — freely-authored content, not part of the MSX API surface, so feel free to swap in your own titles/thumbnails.
- Minimum MSX app version: **0.1.40** (for `video:plugin:`).

You can test this instantly by base64-encoding the JSON into `https://msx.benzac.de/?start=content:json:{BASE64}` — want me to generate that link?

---

> Can I extend the list so that MSX remembers the playback position of each video?

The extended property that does this is `resume:key` (`properties` bag on the Content Item Object, since `0.1.111`) — see [Extended Properties](docs/experts-api/special/extended-properties.md#L57) and the [Cookbook resume-playback deep dive](docs/reference/cookbook.md#L95-144). Setting `"resume:key": "url"` makes MSX hash the item's own video URL and use it to store/restore the playback position automatically — no extra scaffolding (like a `live` object) is required just for resuming; that's only needed if you also want a live progress readout on the tile itself.

Here's the extended list:

```json
{
    "type": "list",
    "headline": "YouTube Videos",
    "template": {
        "type": "separate",
        "layout": "0,0,4,4",
        "icon": "msx-white-soft:movie",
        "color": "msx-glass"
    },
    "items": [
        {
            "badge": "{txt:msx-white:YouTube}",
            "badgeColor": "#ff0000",
            "title": "Video 1",
            "image": "https://img.youtube.com/vi/x_qR3KEsBHU/hqdefault.jpg",
            "playerLabel": "YouTube - Video 1",
            "action": "video:plugin:http://msx.benzac.de/plugins/youtube.html?id=x_qR3KEsBHU",
            "properties": {
                "resume:key": "url"
            }
        },
        {
            "badge": "{txt:msx-white:YouTube}",
            "badgeColor": "#ff0000",
            "title": "Video 2",
            "image": "https://img.youtube.com/vi/e-l7Wy70nbE/hqdefault.jpg",
            "playerLabel": "YouTube - Video 2",
            "action": "video:plugin:http://msx.benzac.de/plugins/youtube.html?id=e-l7Wy70nbE",
            "properties": {
                "resume:key": "url"
            }
        },
        {
            "badge": "{txt:msx-white:YouTube}",
            "badgeColor": "#ff0000",
            "title": "Video 3",
            "image": "https://img.youtube.com/vi/xIWYN7oOavw/hqdefault.jpg",
            "playerLabel": "YouTube - Video 3",
            "action": "video:plugin:http://msx.benzac.de/plugins/youtube.html?id=xIWYN7oOavw",
            "properties": {
                "resume:key": "url"
            }
        }
    ]
}
```

Notes:
- `"resume:key": "url"` works here because each item's action URL is unique (the `id=` query param differs per video) — the KB confirms this pattern also works through a plugin player like `video:plugin:{URL}`, not just plain `video:{URL}`, as long as the plugin reports position/duration back to MSX (which the YouTube plugin does).
- Minimum MSX app version for `resume:key`: **0.1.111** (raises the floor from `0.1.40` for the base list).
- If you'd also like a "now playing" progress indicator on the tile (e.g. showing elapsed time before resuming), that needs the fuller `live` object pattern from the cookbook deep dive — let me know if you want that added too.

---

> Yes, please add it.

The full pattern applies at the **template** level (since these items share the same `live`/`resume` behavior, keyed per-item by their own action URL) rather than repeating it on each item:

```json
{
    "type": "list",
    "headline": "YouTube Videos",
    "template": {
        "type": "separate",
        "layout": "0,0,4,4",
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
    },
    "items": [
        {
            "badge": "{txt:msx-white:YouTube}",
            "badgeColor": "#ff0000",
            "title": "Video 1",
            "image": "https://img.youtube.com/vi/x_qR3KEsBHU/hqdefault.jpg",
            "playerLabel": "YouTube - Video 1",
            "action": "video:plugin:http://msx.benzac.de/plugins/youtube.html?id=x_qR3KEsBHU"
        },
        {
            "badge": "{txt:msx-white:YouTube}",
            "badgeColor": "#ff0000",
            "title": "Video 2",
            "image": "https://img.youtube.com/vi/e-l7Wy70nbE/hqdefault.jpg",
            "playerLabel": "YouTube - Video 2",
            "action": "video:plugin:http://msx.benzac.de/plugins/youtube.html?id=e-l7Wy70nbE"
        },
        {
            "badge": "{txt:msx-white:YouTube}",
            "badgeColor": "#ff0000",
            "title": "Video 3",
            "image": "https://img.youtube.com/vi/xIWYN7oOavw/hqdefault.jpg",
            "playerLabel": "YouTube - Video 3",
            "action": "video:plugin:http://msx.benzac.de/plugins/youtube.html?id=xIWYN7oOavw"
        }
    ]
}
```

What this adds, per the [Cookbook resume-playback deep dive](docs/reference/cookbook.md#L95-144):

- **`resume:key: "url"`** — the actual resume mechanism; keyed by each item's own action URL (unique per video here), saved on a 1-second cycle.
- **`live.type: "playback"`** (`0.1.70+`) — binds the item's live state to the player instead of a schedule. Combined with `resume:key`, it can enter state `"coming"` (idle-but-resumable) as well as `"running"`/`"over"` (`0.1.74+`).
- **`progress: -1`** — keeps the progress bar hidden until there's a saved/live position, then auto-fills — both at rest (`"coming"`, from the saved position) and live (`"running"`, from actual playback), with no manual wiring needed.
- **`titleFooter: ""` + `live.titleFooter: ""` + `live.running.titleFooter: "{progress:time:hh:mm:ss}"`** — required non-null base value so the `live` object is allowed to update it, staying blank until a video is actively playing, then showing a live `hh:mm:ss` readout.
- **`live.action: "player:show"`** — while `"running"`, re-selecting the tile reopens the existing player instead of restarting/re-resolving the video from the top.

Net effect: each tile now visually shows watch progress at rest and live position while playing, in addition to silently resuming. Minimum MSX app version stays **0.1.111** (the tightest requirement in this set — `live.type: "playback"` itself is `0.1.70`, its `"coming"` state via `resume:key` is `0.1.74`).

---

> Could you also add a cool background from the web?

Good — the `msx-keep-ratio` keyword (`0.1.150+`) is exactly what I'll use so a cinematic background doesn't get stretched. Adding it at the Content Root level:

Confirmed `background` is a top-level Content Root Object property. Here's the final JSON with a background added:

```json
{
    "type": "list",
    "headline": "YouTube Videos",
    "background": "https://picsum.photos/seed/msx_youtube_videos/1920/1080#msx-keep-ratio",
    "template": {
        "type": "separate",
        "layout": "0,0,4,4",
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
    },
    "items": [
        {
            "badge": "{txt:msx-white:YouTube}",
            "badgeColor": "#ff0000",
            "title": "Video 1",
            "image": "https://img.youtube.com/vi/x_qR3KEsBHU/hqdefault.jpg",
            "playerLabel": "YouTube - Video 1",
            "action": "video:plugin:http://msx.benzac.de/plugins/youtube.html?id=x_qR3KEsBHU"
        },
        {
            "badge": "{txt:msx-white:YouTube}",
            "badgeColor": "#ff0000",
            "title": "Video 2",
            "image": "https://img.youtube.com/vi/e-l7Wy70nbE/hqdefault.jpg",
            "playerLabel": "YouTube - Video 2",
            "action": "video:plugin:http://msx.benzac.de/plugins/youtube.html?id=e-l7Wy70nbE"
        },
        {
            "badge": "{txt:msx-white:YouTube}",
            "badgeColor": "#ff0000",
            "title": "Video 3",
            "image": "https://img.youtube.com/vi/xIWYN7oOavw/hqdefault.jpg",
            "playerLabel": "YouTube - Video 3",
            "action": "video:plugin:http://msx.benzac.de/plugins/youtube.html?id=xIWYN7oOavw"
        }
    ]
}
```

Notes on the background:

- **`background`** (Content Root Object, `0.1.0`) — used here since it's the highest-priority level in the `background`/`transparent` priority chain (Content Page → Content Root → Menu Item → Menu Root) that we're actually setting.
- Source: **picsum.photos**, a random-image service — the same source already used in this KB's own [`list4.json`](docs/examples/github/extended/list4.json) example, so it's a proven-working choice, not an invented one.
- **`#msx-keep-ratio`** (hidden feature, `0.1.150+`) appended to the URL prevents the image from being stretched to fill the screen — it scales to fit instead, preserving its aspect ratio.
- I deliberately left **`transparent` unset**. Per [Best Practices → `transparent` and background images/videos](docs/reference/best-practices.md#L32-48): a random picsum image isn't guaranteed dark enough for MSX's default white text to stay legible without the dimming overlay, and the unset default (`transparent: 0`) is the one setting that's always safe regardless of image brightness. If you'd like a specific, confirmed-dark image instead so the overlay can be removed for a punchier look, let me know and I can adjust.

Minimum MSX app version for this page is now **0.1.150** (raised by `msx-keep-ratio`, up from `0.1.111` for the resume/live block).

---

> How do I get this JSON onto my TV now?

Two things need to happen: your JSON needs to be **hosted somewhere with CORS enabled**, and the MSX app on your TV needs to be **pointed at it** via its start parameter. Here's the full path from where you are now.

## 1. Wrap it in a Start Object

What you have is a **Content Root Object**, not a Start Object — MSX's start parameter needs to point at it via `content:{URL}`. Create a second file, `start.json`:

```json
{
    "name": "My Example Content",
    "version": "1.0.0",
    "parameter": "content:http://{SERVER}/msx/youtube_videos.json"
}
```

`{SERVER}` is a placeholder — since `0.1.65` MSX auto-replaces it with whatever server address you enter in the app (only in `start.json`; every other file needs a full URL, which your `youtube_videos.json` already has for its own resources).

## 2. Host both files with CORS enabled

Any static HTTP(S) host with CORS headers works — this isn't an MSX-specific requirement, just ordinary web hosting (GitHub Pages, a cloud function, nginx, etc. all qualify). The KB documents one concrete recipe as a worked example ([Quick Start Guide](docs/overview/quick-start-guide.md)):

- Install Node.js + the `http-server` package.
- Put `start.json` and `youtube_videos.json` in a folder, e.g. `c:\http-server\msx\`.
- Run `http-server --cors` from the parent folder — it prints an IP/port like `192.168.0.10:8080`.

## 3. Point the TV app at it

On the TV, in the Media Station X app: **Settings → Start Parameter → Setup**, then enter the server address (e.g. `192.168.0.10:8080`). The TV and your hosting machine need to be on the same network. See [Setup Start Parameter](docs/main-api/start/setup-start-parameter.md).

## Optional: preview before committing to TV setup

You can sanity-check the JSON instantly in the **web version** first, without hosting anything, by base64-encoding it into a URL:

```
https://msx.benzac.de/?start=content:json:{BASE64_ENCODED_JSON}
```

I can generate that link for you right now if you want to eyeball the layout/background/resume behavior in a browser before setting up real hosting — just say so.

**If setup fails:** "status: 0" almost always means CORS isn't enabled or there's a certificate issue — see [Tips & Tricks → Common Errors](docs/overview/tips-tricks.md#common-errors).

---

> Please give me the preview link.

Here's your ready-to-click preview link (1,634 characters — well under browser URL limits):

```
https://msx.benzac.de/?start=content:json:eyJ0eXBlIjoibGlzdCIsImhlYWRsaW5lIjoiWW91VHViZSBWaWRlb3MiLCJiYWNrZ3JvdW5kIjoiaHR0cHM6Ly9waWNzdW0ucGhvdG9zL3NlZWQvbXN4X3lvdXR1YmVfdmlkZW9zLzE5MjAvMTA4MCNtc3gta2VlcC1yYXRpbyIsInRlbXBsYXRlIjp7InR5cGUiOiJzZXBhcmF0ZSIsImxheW91dCI6IjAsMCw0LDQiLCJpY29uIjoibXN4LXdoaXRlLXNvZnQ6bW92aWUiLCJjb2xvciI6Im1zeC1nbGFzcyIsInRpdGxlRm9vdGVyIjoiIiwicHJvZ3Jlc3MiOi0xLCJsaXZlIjp7InR5cGUiOiJwbGF5YmFjayIsInRpdGxlRm9vdGVyIjoiIiwicnVubmluZyI6eyJ0aXRsZUZvb3RlciI6Intwcm9ncmVzczp0aW1lOmhoOm1tOnNzfSJ9LCJhY3Rpb24iOiJwbGF5ZXI6c2hvdyJ9LCJwcm9wZXJ0aWVzIjp7InJlc3VtZTprZXkiOiJ1cmwifX0sIml0ZW1zIjpbeyJiYWRnZSI6Int0eHQ6bXN4LXdoaXRlOllvdVR1YmV9IiwiYmFkZ2VDb2xvciI6IiNmZjAwMDAiLCJ0aXRsZSI6IlZpZGVvIDEiLCJpbWFnZSI6Imh0dHBzOi8vaW1nLnlvdXR1YmUuY29tL3ZpL3hfcVIzS0VzQkhVL2hxZGVmYXVsdC5qcGciLCJwbGF5ZXJMYWJlbCI6IllvdVR1YmUgLSBWaWRlbyAxIiwiYWN0aW9uIjoidmlkZW86cGx1Z2luOmh0dHA6Ly9tc3guYmVuemFjLmRlL3BsdWdpbnMveW91dHViZS5odG1sP2lkPXhfcVIzS0VzQkhVIn0seyJiYWRnZSI6Int0eHQ6bXN4LXdoaXRlOllvdVR1YmV9IiwiYmFkZ2VDb2xvciI6IiNmZjAwMDAiLCJ0aXRsZSI6IlZpZGVvIDIiLCJpbWFnZSI6Imh0dHBzOi8vaW1nLnlvdXR1YmUuY29tL3ZpL2UtbDdXeTcwbmJFL2hxZGVmYXVsdC5qcGciLCJwbGF5ZXJMYWJlbCI6IllvdVR1YmUgLSBWaWRlbyAyIiwiYWN0aW9uIjoidmlkZW86cGx1Z2luOmh0dHA6Ly9tc3guYmVuemFjLmRlL3BsdWdpbnMveW91dHViZS5odG1sP2lkPWUtbDdXeTcwbmJFIn0seyJiYWRnZSI6Int0eHQ6bXN4LXdoaXRlOllvdVR1YmV9IiwiYmFkZ2VDb2xvciI6IiNmZjAwMDAiLCJ0aXRsZSI6IlZpZGVvIDMiLCJpbWFnZSI6Imh0dHBzOi8vaW1nLnlvdXR1YmUuY29tL3ZpL3hJV1lON29PYXZ3L2hxZGVmYXVsdC5qcGciLCJwbGF5ZXJMYWJlbCI6IllvdVR1YmUgLSBWaWRlbyAzIiwiYWN0aW9uIjoidmlkZW86cGx1Z2luOmh0dHA6Ly9tc3guYmVuemFjLmRlL3BsdWdpbnMveW91dHViZS5odG1sP2lkPXhJV1lON29PYXZ3In1dfQ%3D%3D
```

This loads your JSON directly as a **Content Root Object** in the web version of MSX (`content:json:{BASE64}`, per [JSON Building Guide → Testing Your JSON](docs/reference/json-building-guide.md#L589-620)) — no hosting needed. You should see the picsum background, the three YouTube tiles with their thumbnails, and the resume/progress behavior working once you play and back out of a video.

Note this is for **preview only** — the base64 payload isn't a substitute for real hosting, so once you're happy with it, follow the hosting + Start Parameter setup steps from before to get it onto the actual TV app.
