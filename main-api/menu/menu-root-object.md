---
title: Menu Root Object
category: Main API - Menu
summary: Property reference for the MSX menu root object that configures a menu.
---

# Menu Root Object

```json
{ 
    "name": "Content Name",   
    "version": "1.0.0", 
    "reference": "http://link.to.menu", 
    "flag": "content_flag",  
    "reuse": true,
    "cache": true,
    "restore": true,
    "refocus": 0,
    "transparent": 0,
    "style": "default",
    "logo": "http://link.to.image",   
    "logoSize": "large",   
    "headline": "Menu Headline", 
    "background": "http://link.to.image",   
    "extension": "Content Extension", 
    "dictionary": "http://link.to.dictionary",
    "menu": [],
    "action": null,
    "data": null,
    "ready": null,
    "options": null    
}
```

Property syntax of menu root object.

| Property | Type | Default Value | Mandatory | Since Version | Description |
|---|---|---|---|---|---|
| `name` | `string` | `null` | No | 0.1.0 | The name of the content. This property is only used if the menu data is loaded at startup and can be used to set the content name. This value is displayed in the about panel of the Media Station X application. |
| `version` | `string` | `null` | No | 0.1.0 | The version of the content. This property is only used if the menu data is loaded at startup and can be used to add a content version. This value is displayed in the about panel of the Media Station X application. |
| `reference` | `string` | `null` | No | **0.1.134** | The reference of the menu data. This property is only used if the menu data is loaded at startup and can be used to load the actual data (i.e. non-startup related data) from another location. This allows you to keep the start menu data more dynamic (e.g. by reloading it on the fly).<br><br>**Note: Please note that most properties (e.g. `logo`, `headline`, `menu`, `options`, etc.) are ignored if this property is set, because the corresponding values are provided by the referenced data. Additionally, please note that it is also possible to indicate request actions instead of URLs (e.g. `"request:interaction:{DATA_ID}@{URL}"`).** |
| `flag` | `string` | `null` | No | **0.1.120** | A custom content flag that can be evaluated by plugins and/or servers.<br><br>Since version **0.1.144**, this flag can also be used to replace (and reload) the menu data at runtime. For more information, please see [Replace Action](../../experts-api/hidden-features/replace-action.md).<br><br>Since version **0.1.153**, this flag can also be used to refocus menu items by using the `refocus` property. |
| `reuse` | `boolean` | `true` | No | 0.1.0 | Indicates if the menu data can be reused. If this property is set to `false`, the menu data is recreated each time it is displayed. |
| `cache` | `boolean` | `true` | No | **0.1.58** | Indicates if the menu data is being cached. This property can be used to disable the cache feature. |
| `restore` | `boolean` | `true` | No | **0.1.120** | Indicates if the menu data is restorable. If this property is set to `false`, the menu data (and the related content data) is not restored when the application returns from a link action. |
| `refocus` | `number`\|`boolean` | `0` | No | **0.1.153** | Indicates if a menu item should be refocused. The `flag` property is used to cache and restore the focus value.<br><br>• `0`\|`false`: No menu item is refocused.<br>• `1`\|`true`: The cached focused menu item is refocused by using the item ID (and the item index as fallback).<br>• `2`: The cached focused menu item is refocused by using the item index only.<br><br>**Note: To use this property, the `flag` property must also be set, otherwise this property is ignored. If a menu item has a `focus` property set to `true`, the focus value is only cached but not restored.** |
| `transparent` | `number`\|`boolean` | `0` | No | **0.1.142** | Indicates if the corresponding content background is transparent. By default, the content background is set to a semi-transparent gray to darken the underlying video/image and make the content more visible.<br><br>• `0`\|`false`: The content background is not transparent.<br>• `1`\|`true`: The content background is transparent.<br>• `2`: The content background is only transparent if no video/audio is active or the active video/audio has set the extended property `"control:transparent"` to `"true"`. For more information, please see [Extended Properties](../../experts-api/special/extended-properties.md).<br><br>**Note: If the active menu item has its own `transparent` property, this property is ignored. If the corresponding content or content page has its own `transparent` property and becomes active, this property is also ignored.** |
| `style` | `string` | `"default"` | No | **0.1.142** | The style of the menu.<br><br>• `"default"`: The menu is displayed in default style (the menu background is set to a semi-transparent gray and the menu separator is visible).<br>• `"flat"`: The menu is displayed in flat style (the menu background and the menu separator are hidden).<br>• `"flat-separator"`: The menu is displayed in flat separator style (the menu background is hidden and the menu separator is visible).<br>• `"overlay"`: The menu is displayed in overlay style (the menu background is set to a dark overlay and the menu separator is hidden).<br>• `"overlay-separator"`: The menu is displayed in overlay separator style (the menu background is set to a dark overlay and the menu separator is visible). |
| `logo` | `string` | `null` | No | **0.1.60** | The logo image of the menu. This property can be used to display a logo instead of a headline. Please see property `logoSize` for the size of the area. |
| `logoSize` | `string` | `"large"` | No | **0.1.130** | The size of the logo area of the menu.<br><br>• `"small"`: The area of the logo is 416x48 (WxH) pixels at layout resolution 720p (624x72 at 1080p).<br>• `"large"`: The area of the logo is 416x96 (WxH) pixels at layout resolution 720p (624x144 at 1080p).<br><br>The logo is sized to fill the entire height (by keeping the ratio) and is positioned on the left side.<br><br>**Note: This property is ignored if no `logo` property is set.** |
| `headline` | `string` | `null` | No | 0.1.0 | The headline of the menu.<br><br>**Note: Since version 0.1.60, this property is ignored if a `logo` property is set.**<br><br>This property supports [Inline Expressions](../common/inline-expressions.md). |
| `background` | `string` | `null` | No | 0.1.0 | The background image that is used if no other background is set at lower levels. If the menu data is loaded at startup, this background image is used as default. |
| `extension` | `string` | `null` | No | **0.1.82** | The extension of each corresponding content screen. This property can be used to display a small label in the upper right corner of each corresponding content screen (e.g. to show the logged in user, the current date/time, etc.). This property has no effect if the corresponding content contains its own extension. This property supports [Inline Expressions](../common/inline-expressions.md) and [Live Inline Expressions](../../experts-api/live/live-inline-expressions.md). |
| `dictionary` | `string` | `null` | No | **0.1.120** | A URL to a dictionary file. This property is only used if the menu data is loaded at startup and can be used to setup a dictionary. A dictionary can be used to translate the application user interface. For more information, please see [Dictionary Structure](../../experts-api/special/dictionary-structure.md). |
| `menu` | `array` | `null` | **Yes** | 0.1.0 | An array of menu items. Please see [Menu Item Object](menu-item-object.md) for the data structure. This property is mandatory, because without it, no menu items can be displayed. |
| `action` | `string` | `null` | No | 0.1.0 | The start action that is executed if the menu data is loaded at startup. This property is part of the hidden features. For more information, please see [Start Action](../../experts-api/hidden-features/start-action.md). |
| `data` | `object` | `null` | No | 0.1.0 | The additional data of the start action that is used if the action is executed. This property is part of the hidden features. For more information, please see [Start Action](../../experts-api/hidden-features/start-action.md). |
| `ready` | `object` | `null` | No | **0.1.142** | A special ready object that can contain an `action` property (of type `string`) and an action-related `data` property (of type `object`). It can be used to execute an action when the menu is ready. Please see [Internal Actions](../../experts-api/special/internal-actions.md) for possible values. |
| `options` | `object` | `null` | No | **0.1.120** | A special content page object that is displayed in a content panel if a menu item is selected and the menu button is pressed. The availability of options for the current selected item is indicated in the lower right corner of the content screen. Please see [Content Page Object](../content/content-page-object.md) for the data structure.<br><br>Since version **0.1.130**, you can also set a [Content Root Object](../content/content-root-object.md) as options.<br><br>**Note: If the selected menu item has its own `options` property, this property is ignored.** |

## Related Hidden Features

- [Start Action](../../experts-api/hidden-features/start-action.md)
- [Focus Separator](../../experts-api/hidden-features/focus-separator.md)
- [Option Shortcut](../../experts-api/hidden-features/option-shortcut.md)
- [Replace Action](../../experts-api/hidden-features/replace-action.md)

## See Also

- [Menu Item Object](menu-item-object.md)
- [Menu Examples](menu-examples.md)
- [Common Misconceptions → Right property, wrong object](../../reference/common-misconceptions.md#right-property-wrong-object) — `logo`/`logoSize` exist only here, not on the Content Root Object
- [Best Practices & Good to Know → `transparent` and background images/videos](../../reference/best-practices.md#transparent-and-background-imagesvideos) — the priority chain (Content Page → Content Root → Menu Item → Menu Root) and overlay mechanics behind `transparent`/`background` here
- [Cookbook → Live data (EPG, progress, countdowns)](../../reference/cookbook.md#live-data-epg-progress-countdowns) — the live-clock pattern built on this object's `extension` property
- [Glossary → Cache](../../reference/glossary.md#cache), [→ Reuse](../../reference/glossary.md#reuse), and [→ Restore](../../reference/glossary.md#restore) — the full load/reuse mechanics behind these properties
