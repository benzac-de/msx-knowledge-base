---
title: Attached Data Examples
category: Experts API - Special
summary: Examples demonstrating the use of attached data for data transmission to servers or plugins.
---

# Attached Data Examples

These are examples of the attached data that is sent/committed to the server/plugin for specific execute/commit actions. They are only valid for version **0.1.160+**. Please ensure that you are using the correct version, because the syntax in previous versions can be different. For more information about the execute/commit actions, please see [Internal Actions](./internal-actions.md).

**Note: Please note that no action-related `data` property is used for all examples. Therefore, the sent/committed `data` property is always `null`. Since version 0.1.150, the properties `server`, `parameter`, `menuId`, `contentId`, `panelId`, and `interaction` (from the `info.content` data) will be indicated as `"protected"` if the start parameter contains the keyword `msx-protected` at any position (e.g. `"menu:http://example.com/msx/access.php?token=0123456789#msx-protected"` → `"protected"`). This feature protects the values from being sent/committed to a server/plugin (for the user, these values are still visible).**

## Examples of Attached Data

### `execute:{URL}` / `player:commit` / `interaction:commit`

**Action Example:**

`execute:{URL}`<br>
`player:commit`<br>
`interaction:commit`

**Data Example:**

```json
{
    "data": null
}
```

---

### `execute:code:{URL}` / `player:commit:code` / `interaction:commit:code`

**Action Example:**

`execute:code:{URL}`<br>
`player:commit:code`<br>
`interaction:commit:code`

**Data Example:**

```json
{
    "code": "1234",
    "data": null
}
```

---

### `execute:video:{URL}` / `player:commit:video` / `interaction:commit:video`

**Action Example:**

`execute:video:{URL}`<br>
`player:commit:video`<br>
`interaction:commit:video`

**Data Example:**

```json
{
    "video": {
        "info": {
            "id": null,
            "index": 0,
            "number": 1,
            "count": 3,
            "listIndex": 0,
            "listSize": 3,
            "type": "video",
            "url": "http://msx.benzac.de/media/video1.mp4",
            "label": "Video 1",
            "background": null,
            "customLabel": null,
            "customBackground": null,
            "properties": null
        },
        "data": {
            "state": 2,
            "position": 4,
            "duration": 118,
            "speed": 1,
            "ended": false
        },
        "resume": {
            "key": null,
            "context": null,
            "count": 10,
            "resuming": false,
            "position": null,
            "progress": -1,
            "duration": -1
        },
        "volume": {
            "type": "player",
            "level": 100,
            "muted": false
        },
        "scene": "foreground"
    },
    "data": null
}
```

---

### `execute:resume:{URL}` / `player:commit:resume` / `interaction:commit:resume`

**Action Example:**

`execute:resume:{URL}`<br>
`player:commit:resume`<br>
`interaction:commit:resume`

**Data Example:**

```json
{
    "resume": {
        "size": 20,
        "properties": {
            "-21337696": "0.88",
            "511673863": "0.64384|146",
            "-816678121": "0.0625|16|1008992261",
            "-816678120": "1|112|1008992261",
            "1167505037": "1",
            [...]
        }
    },
    "data": null
}
```

---

### `execute:slider:{URL}` / `player:commit:slider` / `interaction:commit:slider`

**Action Example:**

`execute:slider:{URL}`<br>
`player:commit:slider`<br>
`interaction:commit:slider`

**Data Example:**

```json
{
    "slider": {
        "state": 3,
        "id": null,
        "index": 0,
        "number": 1,
        "count": 4,
        "listIndex": 0,
        "listSize": 4,
        "url": "http://msx.benzac.de/img/bg1.jpg",
        "label": "Image 1",
        "color": null,
        "filler": null,
        "rotation": 0,
        "customLabel": null,
        "customColor": null,
        "properties": null
    },
    "data": null
}
```

---

### `execute:info:extended:{URL}` / `player:commit:info:extended` / `interaction:commit:info:extended`

**Action Example:**

`execute:info:extended:{URL}`<br>
`player:commit:info:extended`<br>
`interaction:commit:info:extended`

**Data Example:**

```json
{
    "info": {
        "host": "local",
        "secure": false,
        "client": null,
        "platform": "android",
        "id": "Vv58jUgVvufU8pVrq7XCQ5DnrEBwmhfy",
        "player": "html5/plugin",
        "language": "en",
        "userAgent": "Mozilla/5.0 ...",
        "application": {
            "name": "Media Station X",
            "version": "0.1.107",
            "suffix": null,
            "settings": {
                "validateLinks": 1,
                "randomPlayback": 0,
                "slideshowInterval": 10000,
                "hoverEffect": 1,
                "immersiveMode": 0,
                "roundedStyle": 0,
                "sleepTimeout": 300000,
                "ejectTimeout": 14400000,
                "visualExecution": 1,
                "menuButton": {
                    "action": -1,
                    "keyCode": -1
                }
            }
        },
        "framework": {
            "name": "TVX Framework",
            "version": "0.1.14",
            "suffix": null,
            "settings": {
                "animate": 2,
                "transform": 1,
                "input": 3,
                "remote": 1,
                "layout": "1",
                "scale": "auto",
                "zoom": null,
                "center": 1,
                "background": 1,
                "leave": 0,
                "exit": 1,
                "back": 1,
                "volume": 1,
                "busy": 1,
                "speed": 3,
                "playback": 1,
                "fullscreen": 1,
                "suspend": 2,
                "secure": 0,
                "caption": 2,
                "pointer": 4
            }
        },
        "content": {
            "name": "Google Drive MSX",
            "version": "1.0.14",
            "flag": null,
            "server": "gd.msx.benzac.de",
            "secure": false,
            "parameter": "menu:request:interaction:init@http://gd.msx.benzac.de/interaction",
            "state": {
                "start": true,
                "restored": false,                
                "menuId": null,
                "menuFlag": null,
                "menuFocus": "index:0",
                "menuIndex": 0,
                "menuSize": 3,
                "contentId": "request:interaction:gd:welcome",
                "contentFlag": null,
                "contentFocus": null,
                "contentIndex": -1,
                "contentSize": -1,
                "contentVisible": true,
                "panelId": null,
                "panelFlag": null,
                "panelFocus": null,
                "panelIndex": -1,
                "panelSize": -1,
                "panelVisible": false,
                "videoVisible": false,
                "videoActive": false,
                "playerVisible": false,
                "slideshowVisible": false,
                "volumeVisible": false,
                "logVisible": false
            },
            "interaction": "http://gd.msx.benzac.de/interaction"            
        },
        "dictionary": {
            "custom": false,
            "url": "http://msx.benzac.de/dic/en.json",
            "name": "English",
            "version": "1.0.0",
            "language": "en",
            "size": 277
        },
        "screen": {
            "width": 1280,
            "height": 720,
            "factor": 1,
            "zoomFactor": 1
        },
        "time": {
            "timestamp": 1580465719024,
            "now": 1580465718178,
            "zone": 3600000,
            "offset": 0,
            "zoneOffset": 0
        },
        "urlParams": {},
        "system": {
            "name": "Device - Android 8.0.0",
            "ipAddress": "192.168.0.10",
            "manufacturer": null,
            "deviceId": "0000000000000000"
        }
    },
    "data": null
}
```

---

### `player:commit:message:test` / `interaction:commit:message:test`

**Action Example:**

`player:commit:message:test`<br>
`interaction:commit:message:test`

**Data Example:**

```json
{
    "message": "test",
    "data": null
}
```

---

### `player:commit:string:transform:test` / `interaction:commit:string:transform:test`

**Action Example:**

`player:commit:string:transform:test`<br>
`interaction:commit:string:transform:test`

**Data Example:**

```json
{
    "string": "pzr.",
    "data": null
}
```

---

### `player:commit:string:normalize:pzr.` / `interaction:commit:string:normalize:pzr.`

**Action Example:**

`player:commit:string:normalize:pzr.`<br>
`interaction:commit:string:normalize:pzr.`

**Data Example:**

```json
{
    "string": "test",
    "data": null
}
```

---

### `player:commit:response:http://msx.benzac.de/services/echo.php` / `interaction:commit:response:http://msx.benzac.de/services/echo.php`

**Action Example:**

`player:commit:response:http://msx.benzac.de/services/echo.php`<br>
`interaction:commit:response:http://msx.benzac.de/services/echo.php`

**Data Example:**

```json
{
    "response": {
        "action": "info:Hello, I am a simple echo service."
    },
    "error": null,
    "data": null
}
```

---

### `player:commit:dictionary` / `interaction:commit:dictionary`

**Action Example:**

`player:commit:dictionary`<br>
`interaction:commit:dictionary`

**Data Example:**

```json
{
    "dictionary": {
        "name": "English",
        "version": "1.0.0",
        "language": "en",
        "size": 277,
        "properties": {
            "format:date": "mm/dd/yy",
            "format:day": "D mm/dd",
            "format:full_day": "DD, MM d, yyyy",
            "format:long_date": "MM d, yyyy",
            "format:long_day": "D mm/dd/yyyy",
            "format:long_time": "h:mm:ss/ampm",
            "format:separator": ", ",
            "format:time": "h:mm/ampm",
            [...]
        }
    },
    "data": null
}
```

---

### `notification:test`

**Action Example:**

`notification:test`

**Data Example:**

```json
{
    "notification": "test",
    "data": null
}
```
