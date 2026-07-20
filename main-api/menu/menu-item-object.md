---
title: Menu Item Object
category: Main API - Menu
summary: Property reference for the MSX menu item object that defines a single menu entry.
---

# Menu Item Object

```json
{
    "id": "menu_item_id",
    "type": "default",
    "display": true,
    "enable": true,
    "focus": false,
    "execute": false,
    "transparent": 0,
    "icon": "blank",
    "image": "http://link.to.image",
    "label": "Item Label",
    "background": "http://link.to.image",
    "extensionIcon": "blank",
    "extensionLabel": "Extension Label",
    "lineColor": "msx-white",
    "data": null,
    "options": null
}
```

Property syntax of menu item object.

| Property | Type | Default Value | Mandatory | Since Version | Description |
|---|---|---|---|---|---|
| `id` | `string` | `null` | No | 0.1.0 | The ID of the menu item. This property can be used to identify the item. The ID must be unique for the menu data and only consist of letters, numbers, and the special characters `"_"`, `"-"`, `"."`. |
| `type` | `string` | `"default"` | No | 0.1.0 | The type of the menu item.<br><br>• `"default"`: Default item.<br>• `"separator"`: Separator item. This type can be used to group menu items by adding separator lines. It is also possible to add a `label` to a separator line. Since version **0.1.142**, the color of the separator line can be set with the `lineColor` property.<br>• `"settings"`: Application settings. This type is part of the hidden features. For more information, please see [Settings Menu Item](../../experts-api/hidden-features/settings-menu-item.md). |
| `display` | `boolean` | `true` | No | 0.1.0 | Indicates if the menu item is displayed. This property can be used to temporarily remove items. |
| `enable` | `boolean` | `true` | No | 0.1.0 | Indicates if the menu item is enabled. This property can be used to temporarily disable items. |
| `focus` | `boolean` | `false` | No | 0.1.0 | Indicates if the menu item is focused. This property can be used to set the focus to a specific item.<br><br>**Note: If this property is set to true for multiple items, only the first item will get the focus.** |
| `execute` | `boolean` | `false` | No | **0.1.120** | Indicates if the menu item is automatically executed on load. This property is part of the hidden features. For more information, please see [Execute Property](../../experts-api/hidden-features/execute-property.md). |
| `transparent` | `number`\|`boolean` | `0` | No | **0.1.142** | Indicates if the corresponding content background is transparent. By default, the content background is set to a semi-transparent gray to darken the underlying video/image and make the content more visible.<br><br>• `0`\|`false`: The content background is not transparent.<br>• `1`\|`true`: The content background is transparent.<br>• `2`: The content background is only transparent if no video/audio is active or the active video/audio has set the extended property `"control:transparent"` to `"true"`. For more information, please see [Extended Properties](../../experts-api/special/extended-properties.md).<br><br>**Note: If the corresponding content or content page has its own `transparent` property and becomes active, this property is ignored.** |
| `icon` | `string` | `null` | No | 0.1.0 | The icon of the menu item. Please see [Icons](../common/icons.md) for possible values.<br><br>**Note: Since version 0.1.130, this property is ignored if an `image` property is set.** |
| `image` | `string` | `null` | No | **0.1.130** | The image of the menu item. This property can be used to display an image instead of an icon. The area of the image is 32x48 (WxH) pixels at layout resolution 720p (48x72 at 1080p). The image is sized to fill the entire width (by keeping the ratio) and is positioned in the center. |
| `label` | `string` | `null` | No | 0.1.0 | The label of the menu or separator item. This property supports [Inline Expressions](../common/inline-expressions.md). |
| `background` | `string` | `null` | No | 0.1.0 | The background image that is used if no other background is set at lower levels. |
| `extensionIcon` | `string` | `null` | No | 0.1.0 | The extension icon. This property can be used to display an additional icon on the right side of the menu item. Please see [Icons](../common/icons.md) for possible values. |
| `extensionLabel` | `string` | `null` | No | 0.1.0 | The extension label. This property can be used to display an additional label on the right side of the menu item. This property is only used if no `extensionIcon` is set. This property supports [Inline Expressions](../common/inline-expressions.md). |
| `lineColor` | `string` | `null` | No | **0.1.142** | The color of the separator line. To use this property, the `type` property must be set to `"separator"`, otherwise this property is ignored. Please see [Colors](../common/colors.md) for possible values. |
| `data` | `string`\|`object` | `null` | No | 0.1.0 | The menu item data. This property is used to display the corresponding content. It can be indicated as URL (`string`) or directly as content root object (`object`). If this property is not set, a default content page with headline *"Content Not Available"* is displayed. Please see [Content Root Object](../content/content-root-object.md) for the data structure.<br><br>**Note: Please note that it is also possible to indicate request actions instead of URLs (e.g. `"request:interaction:{DATA_ID}@{URL}"`).** |
| `options` | `object` | `null` | No | **0.1.120** | A special content page object that is displayed in a content panel if the menu item is selected and the menu button is pressed. The availability of options for the current selected item is indicated in the lower right corner of the content screen. Please see [Content Page Object](../content/content-page-object.md) for the data structure.<br><br>Since version **0.1.130**, you can also set a [Content Root Object](../content/content-root-object.md) as options. |

## Related Hidden Features

- [Settings Menu Item](../../experts-api/hidden-features/settings-menu-item.md)
- [Execute Property](../../experts-api/hidden-features/execute-property.md)
- [Focus Separator](../../experts-api/hidden-features/focus-separator.md)
- [Option Shortcut](../../experts-api/hidden-features/option-shortcut.md)
- [Replace Action](../../experts-api/hidden-features/replace-action.md)

## See also

- [Menu Root Object](menu-root-object.md)
- [Menu Examples](menu-examples.md)
- [Common Misconceptions → Menu objects](../../reference/common-misconceptions.md#menu-objects) — the `data`-vs-`content:`-prefix error and the "menu items have no `action`" rule
- [Glossary → `data` Property](../../reference/glossary.md#data-property) — unset/`null` data (graceful "Content Not Available" fallback) vs. an invalid/malformed value (produces an error)
