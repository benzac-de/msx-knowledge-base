---
title: Plugin Events Reference
category: Experts API - Plugin
summary: Complete reference for all event types delivered to handleEvent(data) in video/audio and interaction plugins.
---

# Plugin Events Reference

Both `MyPlayer.handleEvent(data)` (video/audio plugin) and `MyHandler.handleEvent(data)` (interaction plugin) receive event objects with a `data.event` string identifying the event type. This page documents all possible values and their associated payload properties.

**Note: Video events marked with \* are usually not handled by the player plugin, because the corresponding player function is also called automatically (e.g. `play()` → `"video:play"`).**

---

## App Events

| Event | Extra Properties | Description |
| --- | --- | --- |
| `app:suspend` | — | App is being suspended (e.g. TV standby). |
| `app:resume` | — | App has resumed from suspend. |
| `app:sleep` | — | App went to sleep (AFK timeout). |
| `app:awake` | — | App woke up from sleep. |
| `app:resize` | — | Screen/window size has changed. |
| `app:connect` | — | Network connection established. |
| `app:disconnect` | — | Network connection lost. |
| `app:time` | `data.offset`, `data.zoneOffset` | System time changed. `offset` contains the new time offset in milliseconds, `zoneOffset` the new time zone offset in milliseconds. |
| `app:result` | `data.id`, `data.code`, `data.extra` | Result from a `system:tvx:launch` action (Android/FireTV, v0.1.136+). See [Android Player](../special/android-player.md) for full payload details. |

### `app:result` payload structure

```json
{
    "event": "app:result",
    "id": "request_id",
    "code": -1,
    "extra": {
        "extra_string": "Value",
        "extra_int": 1,
        "extra_boolean": true
    }
}
```

| Property | Type | Description |
| --- | --- | --- |
| `id` | `string` | The custom request ID that was set in the launch action data. |
| `code` | `number` | The result code returned by the launched application. |
| `extra` | `object` | Extra data returned by the launched application. |

---

## Video Events

> **\*** These events fire alongside the matching player function call. Handle them only if you need to react to video state changes initiated from outside the plugin (e.g. from an interaction plugin or remote key).

| Event | Extra Properties | Description |
| --- | --- | --- |
| `video:load` \* | `data.info`, `data.data` | A new video/audio has been loaded. `info` contains the video info, `data` contains the video state before this event. |
| `video:play` \* | `data.data` | Playback started. `data` contains the video state before this event. |
| `video:pause` \* | `data.data` | Playback paused. `data` contains the video state before this event. |
| `video:stop` \* | `data.data` | Playback stopped. `data` contains the video state before this event. |
| `video:seek` \* | `data.position`, `data.data` | User seeked. `position` is the new position in seconds, `data` is the video state before this event. |
| `video:restart` \* | `data.data` | Playback restarted from beginning. `data` contains the video state before this event. |
| `video:speed` \* | `data.speed`, `data.data` | Playback speed changed. `speed` is the new value (0.125–8.0), `data` is the video state before this event. |
| `video:volume` \* | `data.volume`, `data.muted` | Volume changed. `volume` is the new level (0–100), `muted` is the new muted state. |

### `data.info` — Video info structure (`video:load`)

This is the same `info` object that is sent to a server via `execute:video:{URL}`. Full example from [Attached Data Examples](../special/attached-data-examples.md):

```json
{
    "id": null,
    "index": 0,
    "number": 1,
    "count": 3,
    "listIndex": 0,
    "listSize": 3,
    "type": "video",
    "url": "http://example.com/video1.mp4",
    "label": "Video 1",
    "background": null,
    "customLabel": null,
    "customBackground": null,
    "properties": null
}
```

| Property | Type | Description |
| --- | --- | --- |
| `id` | `string\|null` | Optional item ID. |
| `index` | `number` | Zero-based index in the current list. |
| `number` | `number` | One-based number in the current list. |
| `count` | `number` | Total items in the current list. |
| `listIndex` | `number` | Zero-based index across all lists. |
| `listSize` | `number` | Total items across all lists. |
| `type` | `string` | Media type (`"video"` or `"audio"`). |
| `url` | `string` | Media URL. |
| `label` | `string\|null` | Display label. |
| `background` | `string\|null` | Background image URL. |
| `customLabel` | `string\|null` | Custom label set at runtime. |
| `customBackground` | `string\|null` | Custom background set at runtime. |
| `properties` | `object\|null` | Additional custom properties. |

### `data.data` — Video state structure (all video events)

```json
{
    "state": 2,
    "position": 4,
    "duration": 118,
    "speed": 1,
    "ended": false
}
```

| Property | Type | Description |
| --- | --- | --- |
| `state` | `number` | Playback state: `TVXVideoState.STOPPED` (`1`), `TVXVideoState.PLAYING` (`2`), `TVXVideoState.PAUSED` (`3`). |
| `position` | `number` | Current position in seconds. |
| `duration` | `number` | Total duration in seconds. |
| `speed` | `number` | Current playback speed (0.125–8.0). |
| `ended` | `boolean` | Whether playback has reached the end. |

---

## Slider Events

| Event | Extra Properties | Description |
| --- | --- | --- |
| `slider:load` | `data.info` | A slider/slideshow has been loaded. `info` contains the slider info. |
| `slider:play` | — | Slideshow started. |
| `slider:pause` | — | Slideshow paused. |
| `slider:stop` | — | Slideshow stopped. |
| `slider:position` | `data.index`, `data.number`, `data.listIndex` | Active slide changed. `index` is zero-based, `number` is one-based, `listIndex` is the index across all slides. |
| `slider:rotation` | `data.rotation` | Image rotation changed. `rotation` is the new angle in degrees. |

### `data.info` — Slider info structure (`slider:load`)

```json
{
    "state": 3,
    "id": null,
    "index": 0,
    "number": 1,
    "count": 4,
    "listIndex": 0,
    "listSize": 4,
    "url": "http://example.com/image1.jpg",
    "label": "Image 1",
    "color": null,
    "filler": null,
    "rotation": 0,
    "customLabel": null,
    "customColor": null,
    "properties": null
}
```

| Property | Type | Description |
| --- | --- | --- |
| `state` | `number` | Playback state, same enum as video events: `TVXVideoState.STOPPED` (`1`), `TVXVideoState.PLAYING` (`2`), `TVXVideoState.PAUSED` (`3`). |
| `id` | `string\|null` | Optional item ID. |
| `index` | `number` | Zero-based index in the current list. |
| `number` | `number` | One-based number in the current list. |
| `count` | `number` | Total items in the current list. |
| `listIndex` | `number` | Zero-based index across all lists. |
| `listSize` | `number` | Total items across all lists. |
| `url` | `string` | Image URL. |
| `label` | `string\|null` | Corresponds to the content item's `imageLabel` — the image label displayed inside the slideshow. |
| `color` | `string\|null` | Corresponds to the content item's `imageColor` — the background color of the image (see [Colors](../../main-api/common/colors.md)). |
| `filler` | `string\|null` | Corresponds to the content item's `imageScreenFiller` — the filler type of the image (default `"fit"`). |
| `rotation` | `number` | Current image rotation in degrees (`0`–`360`), changeable via [Image Options](../hidden-features/image-options.md). |
| `customLabel` | `string\|null` | Custom label set at runtime. |
| `customColor` | `string\|null` | Custom color set at runtime. |
| `properties` | `object\|null` | Additional custom properties. |

---

## Settings Events

All settings events carry a single `data.value` property with the new setting value. The exception is `settings:menu_button` which carries two properties.

| Event | Extra Properties | Description |
| --- | --- | --- |
| `settings:animate` | `data.value` | Animation level changed. |
| `settings:transform` | `data.value` | Transform mode changed. |
| `settings:input` | `data.value` | Input mode changed. |
| `settings:remote` | `data.value` | Remote mode changed. |
| `settings:layout` | `data.value` | Layout setting changed. |
| `settings:scale` | `data.value` | Scale setting changed. |
| `settings:zoom` | `data.value` | Zoom setting changed. |
| `settings:validate_links` | `data.value` | Link validation setting changed. |
| `settings:random_playback` | `data.value` | Random playback setting changed. |
| `settings:slideshow_interval` | `data.value` | Slideshow interval changed (in milliseconds). |
| `settings:hover_effect` | `data.value` | Hover effect setting changed. |
| `settings:immersive_mode` | `data.value` | Immersive mode setting changed. |
| `settings:rounded_style` | `data.value` | Rounded style setting changed. |
| `settings:sleep_timeout` | `data.value` | Sleep timeout changed (in milliseconds). |
| `settings:eject_timeout` | `data.value` | Eject timeout changed (in milliseconds). |
| `settings:visual_execution` | `data.value` | Visual execution setting changed. |
| `settings:menu_button` | `data.action`, `data.keyCode` | Menu button assignment changed. `action` contains the button action, `keyCode` the associated key code. |

Current setting values are available in the `info.application.settings` and `info.framework.settings` objects. See [Attached Data Examples](../special/attached-data-examples.md) for the full structure.

---

## Custom Events

```
custom:{EVENT_ID}
```

Custom events are triggered by `TVXVideoPlugin.triggerEvent(eventId, data)` (from the player plugin) or `TVXInteractionPlugin.triggerEvent(eventId, data)` (from the interaction plugin). They allow the two plugins to communicate with each other.

| Property | Description |
| --- | --- |
| `data.event` | `"custom:{EVENT_ID}"` where `{EVENT_ID}` is the ID passed to `triggerEvent()`. |
| `data.data` | Optional. The data object passed as the second argument to `triggerEvent()`. |

**Example:**
```js
// In the video/audio plugin
TVXVideoPlugin.triggerEvent("myEvent", { value: 42 });

// In the interaction plugin's handleEvent
if (data.event === "custom:myEvent") {
    var value = data.data.value; // 42
}
```

---

## See also
- [Plugin API Reference](plugin-api-reference.md)
- [Video/Audio Plugin](video-audio-plugin.md)
- [Interaction Plugin](interaction-plugin.md)
- [Attached Data Examples](../special/attached-data-examples.md)
- [Android Player](../special/android-player.md)
