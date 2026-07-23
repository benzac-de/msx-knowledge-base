---
title: Start Object
category: Main API - Start
summary: Property reference for the MSX start object that configures the start parameter.
---

# Start Object

```json
{    
    "name": "Start Parameter Name",
    "version": "1.0.0",
    "parameter": "menu:http://link.to.menu",
    "welcome": "none",
    "launcher": {
        "icon": "blank",
        "image": "http://link.to.image",
        "color": "msx-black-soft"
    }
}
```

Property syntax of start object.

| Property | Type | Default Value | Mandatory | Since Version | Description |
|---|---|---|---|---|---|
| `name` | `string` | `null` | **Yes** | 0.1.0 | The name of the start parameter. This property is displayed at start parameter setup and should be used to show a short information about the start parameter (e.g. `"My Last Holiday Trip"`). This property is mandatory. |
| `version` | `string` | `null` | **Yes** | 0.1.0 | The version of the start parameter. This property is displayed at start parameter setup and should be used to show the current version of the start parameter (e.g. `"1.0.0"`). This property is mandatory. |
| `parameter` | `string` | `null` | **Yes** | 0.1.0 | The start parameter. This property specifies which menu or content is loaded at startup. It must start with `menu:` or `content:` to specify the JSON type. This property is mandatory, because without it, no start menu or content can be loaded.<br><br>Since version **0.1.65**, you can also set a start parameter that contains the string `{SERVER}` (e.g. `menu:http://{SERVER}/msx/menu.json`) to automatically replace it with the entered server.<br><br>Since version **0.1.97**, you can also set a start parameter that contains the string `{PREFIX}` (e.g. `menu:{PREFIX}link.to.menu` or `menu:{PREFIX}{SERVER}`) to automatically replace it with the used prefix (i.e. `http://` or `https://`).<br><br>Since version **0.1.97**, you can also set a start parameter that starts with `redirect:` (e.g. `redirect:http://link.to.start.object` or `redirect:{PREFIX}link.to.start.object` or `redirect:{PREFIX}{SERVER}/msx/start.php`) to load a start object from another location.<br><br>**Note: Please note that the auto-replacement feature only works for the start parameter file, for the other JSON files, you must always indicate full URLs (to avoid errors in cross-references). Additionally, please note that it is also possible to indicate request actions instead of URLs (e.g. `"menu:request:interaction:{DATA_ID}@{URL}"`).**<br><br>Please see [Menu Root Object](../menu/menu-root-object.md) and [Content Root Object](../../main-api/content/content-root-object.md) for the data structures. |
| `welcome` | `string` | `"none"` | No | **0.1.117** | The welcome type of the start parameter. If this property is set to `"start"` or `"content"`, the start parameter is also set as welcome pages in the settings (either as start action or content). If you want to manage various start parameters with a portal page (i.e. by using the `start` action), you can use this feature to keep the portal page available via the settings.<br><br>• `"none"`: Set no welcome pages (the current welcome pages are retained).<br>• `"start"`: Set welcome pages start action.<br>• `"content"`: Set welcome pages content. |
| `launcher` | `object` | `null` | No | **0.1.164** | It is possible to add a `launcher` object (that can contain an `icon`, `image`, and/or `color` property) to set up a launcher tile of the start parameter in the settings and setup completion panel.<br><br>• `icon`: The icon of the launcher tile. By default, `"play-circle-outline"` is used or `"blank"` if an `image` property is set. This icon (as decolored version) is also used in the settings for the welcome pages if the `welcome` property is set to `"content"`. Please see [Icons](../common/icons.md) for possible values.<br>• `image`: The image of the launcher tile. By default, `"none"` is used. The area of the image is 176x176 (WxH) pixels at layout resolution 720p (264x264 at 1080p). The image is sized to fill the entire width (by keeping the ratio) and is positioned in the center.<br>• `color`: The background color of the launcher tile. By default, `"msx-black-soft"` is used. Please see [Colors](../common/colors.md) for possible values.<br><br>This object is compatible with the `launcher` object defined by the **Launcher MSX** service. For more information, please see [Tips & Tricks — Launcher MSX](../../overview/tips-tricks.md#launcher-msx). |

## See Also

- [Setup Precondition](./setup-precondition.md)
- [Setup Start Parameter](./setup-start-parameter.md)
- [Common Misconceptions → Start object, placeholders & URLs](../../reference/common-misconceptions.md#start-object-placeholders--urls) — `version` is not the MSX app version, and `{SERVER}`/`{PREFIX}` only resolve here, nowhere else
- [Common Misconceptions → Custom & unknown properties](../../reference/common-misconceptions.md#custom--unknown-properties) — a worked example of a custom (non-official) field alongside real Start Object properties
