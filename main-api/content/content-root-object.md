---
title: Content Root Object
category: Main API - Content
summary: Property reference for the MSX Content Root Object, the top-level container for a content screen configured either via manually-positioned pages or an auto-positioned template + items collection.
---

# Content Root Object

```json
{  
    "name": "Content Name",
    "version": "1.0.0", 
    "reference": "http://link.to.content",
    "flag": "content_flag",
    "reuse": true,
    "cache": true,    
    "restore": true,
    "important": false,  
    "wrap": false,
    "compress": false,
    "preselect": false,
    "refocus": 0,
    "transparent": 0,
    "type": "pages",
    "preload": "none",
    "headline": "Content Headline", 
    "background": "http://link.to.image",
    "extension": "Content Extension",
    "dictionary": "http://link.to.dictionary",
    "template": null,
    "items": [],    
    "pages": [],
    "header": null,
    "footer": null,  
    "inserts": [],
    "overlay": null,  
    "underlay": null,
    "action": null,
    "data": null,
    "ready": null,
    "options": null,
    "caption": "opt/menu",
    "captionUnderlay": -1,
    "navigationOffset": -1,
    "navigationSpan": -1
}
```

Property syntax of content root object.

| Property | Type | Default Value | Mandatory | Since Version | Description |
|---|---|---|---|---|---|
| `name` | `string` | `null` | No | 0.1.0 | The name of the content. This property is only used if the content data is loaded at startup and can be used to set the content name. This value is displayed in the about panel of the Media Station X application. |
| `version` | `string` | `null` | No | 0.1.0 | The version of the content. This property is only used if the content data is loaded at startup and can be used to add a content version. This value is displayed in the about panel of the Media Station X application. |
| `reference` | `string` | `null` | No | **0.1.134** | The reference of the content data. This property is only used if the content data is loaded at startup and can be used to load the actual data (i.e. non-startup related data) from another location. This allows you to keep the start content data more dynamic (e.g. by reloading it on the fly).<br><br>**Note: Please note that most properties (e.g. `headline`, `items`, `pages`, `options`, etc.) are ignored if this property is set, because the corresponding values are provided by the referenced data. Additionally, please note that it is also possible to indicate request actions instead of URLs (e.g. `"request:interaction:{DATA_ID}@{URL}"`).** |
| `flag` | `string` | `null` | No | **0.1.120** | A custom content flag that can be evaluated by plugins and/or servers.<br><br>Since version **0.1.144**, this flag can also be used to replace (and reload) the content data at runtime. For more information, please see [Replace Action](../../experts-api/hidden-features/replace-action.md).<br><br>Since version **0.1.153**, this flag can also be used to refocus content items by using the `refocus` property. |
| `reuse` | `boolean` | `true` | No | 0.1.0 | Indicates if the content data can be reused. If this property is set to `false`, the content data is recreated each time it is displayed. |
| `cache` | `boolean` | `true` | No | **0.1.58** | Indicates if the content data is being cached. This property can be used to disable the cache feature. |
| `restore` | `boolean` | `true` | No | **0.1.120** | Indicates if the content data is restorable. If this property is set to `false`, the content data is not restored when the application returns from a link action. This property has no effect if the content is displayed in a panel.<br><br>**Note: If the content is loaded via a menu and the menu data has set the `restore` property to `false`, this property is ignored.** |
| `important` | `boolean` | `false` | No | **0.1.58** | Indicates if the content data is important. This property can be used to enforce that the content headline is displayed. This property has no effect if the content is displayed in a panel or the content is not loaded via a menu.<br><br>**Note: The content headline is not displayed if the content is loaded via a menu, because in that case the menu item label is used as headline.** |
| `wrap` | `boolean` | `false` | No | **0.1.102** | Indicates if the content data is wrapped. If this property is set to `true`, a corresponding menu can not be entered by using the navigation keys (i.e. left and right key). This property has no effect if the content is displayed in a panel or the content is not loaded via a menu.<br><br>**Note: If the active content page has its own `wrap` property, this property is ignored.** |
| `compress` | `boolean` | `false` | No | **0.1.123** | Indicates if the content is compressed by 25%. This property is part of the hidden features. For more information, please see [Compress Property](../../experts-api/hidden-features/compress-property.md). |
| `preselect` | `boolean` | `false` | No | **0.1.160** | Indicates if content pages and items should be preselected by executing the corresponding page and seletion actions before a content scroll animation. By default, these actions are executed after a content scroll animation. |
| `refocus` | `number`\|`boolean` | `0` | No | **0.1.153** | Indicates if a content item should be refocused. The `flag` property is used to cache and restore the focus value.<br><br>• `0`\|`false`: No content item is refocused.<br>• `1`\|`true`: The cached focused content item is refocused by using the item ID (and the item index as fallback).<br>• `2`: The cached focused content item is refocused by using the item index only.<br><br>**Note: To use this property, the `flag` property must also be set, otherwise this property is ignored. If a content item has a `focus` property set to `true`, the focus value is only cached but not restored.** |
| `transparent` | `number`\|`boolean` | `0` | No | **0.1.142** | Indicates if the content background is transparent. By default, the content background is set to a semi-transparent gray to darken the underlying video/image and make the content more visible. This property has no effect if the content is displayed in a panel.<br><br>• `0`\|`false`: The content background is not transparent.<br>• `1`\|`true`: The content background is transparent.<br>• `2`: The content background is only transparent if no video/audio is active or the active video/audio has set the extended property `"control:transparent"` to `"true"`. For more information, please see [Extended Properties](../../experts-api/special/extended-properties.md).<br><br>**Note: If the active content page has its own `transparent` property, this property is ignored.** |
| `type` | `string` | `"pages"` | No | 0.1.0 | The type of the content items.<br><br>• `"pages"`: The content items are displayed as pages in horizontal direction.<br>• `"list"`: The content items are displayed as list in vertical direction.<br><br>**Note: If the `"list"` type is used, content pages are stacked (i.e. vertical gaps between content pages are removed). This property has no effect if the content is displayed in a panel, because panels always use the `"list"` type.** |
| `preload` | `string` | `"none"` | No | **0.1.92** | The preload mode of the content pages (available for type `"pages"` and `"list"`). This property can be used to preload the next and/or previous visible page (e.g. to preload images, to pre-perform live updates, etc.). By default, this feature is disabled.<br><br>**Note: Please use this feature wisely, because preloading pages can decrease the performance.**<br><br>• `"none"`: Preload no pages.<br>• `"next"`: Preload the next visible page.<br>• `"prev"`: Preload the previous visible page.<br>• `"full"`: Preload the next and previous visible page. |
| `headline` | `string` | `null` | No | 0.1.0 | The headline of the content.<br><br>**Note: This property has no effect if the content is loaded via a menu, because in that case the menu item label is used as headline. Since version 0.1.58, you can set the property `important` to `true` to enforce that this property is used.**<br><br>This property supports [Inline Expressions](../common/inline-expressions.md). |
| `background` | `string` | `null` | No | 0.1.0 | The background image that is used if no other background is set at lower levels. If the content data is loaded at startup, this background image is used as default. |
| `extension` | `string` | `null` | No | **0.1.82** | The extension of the content screen. This property can be used to display a small label in the upper right corner of the content screen (e.g. to show the logged in user, the current date/time, etc.). This property supports [Inline Expressions](../common/inline-expressions.md) and [Live Inline Expressions](../../experts-api/live/live-inline-expressions.md). |
| `dictionary` | `string` | `null` | No | **0.1.120** | A URL to a dictionary file. This property is only used if the content data is loaded at startup and can be used to setup a dictionary. A dictionary can be used to translate the application user interface. For more information, please see [Dictionary Structure](../../experts-api/special/dictionary-structure.md). |
| `template` | `object` | `null` | **Only for templated items** | 0.1.0 | The template object to automatically create and position content items. Please see [Content Item Object](content-item-object.md) for the data structure. This property is mandatory if the content items should be automatically created and positioned.<br><br>**Note: Templated content items have all the same size and can be used to quickly create lists without having to deal with grid positions. To use this property, the `items` property must also be set, otherwise this property is ignored.** |
| `items` | `array` | `null` | **Only for templated items** | 0.1.0 | An array of content items. Please see [Content Item Object](content-item-object.md) for the data structure. This property is mandatory if the content items should be automatically created and positioned.<br><br>**Note: Templated content items only have to specify the relevant properties. The other properties are copied from the `template` object. To use this property, the `template` property must also be set, otherwise this property is ignored.** |
| `pages` | `array` | `null` | **Only for non-templated items** | 0.1.0 | An array of content pages. Please see [Content Page Object](content-page-object.md) for the data structure. This property is mandatory if the content items should be manually created and positioned.<br><br>**Note: This property is ignored if a `template` and `items` property is set.** |
| `header` | `object` | `null` | No | **0.1.53** | An individual header page for templated content items. Please see [Content Page Object](content-page-object.md) for the data structure.<br><br>**Note: To use this property, the `template` and `items` property must also be set, otherwise this property is ignored.** |
| `footer` | `object` | `null` | No | **0.1.53** | An individual footer page for templated content items. Please see [Content Page Object](content-page-object.md) for the data structure.<br><br>**Note: To use this property, the `template` and `items` property must also be set, otherwise this property is ignored.** |
| `inserts` | `array` | `null` | No | **0.1.156** | An array of individual insert pages for templated content items. Please see [Content Page Object](content-page-object.md) for the data structure and the `position` property available there for more information.<br><br>**Note: To use this property, the `template` and `items` property must also be set, otherwise this property is ignored.** |
| `overlay` | `object` | `null` | No | **0.1.110** | An independent overlay page that is displayed over each content page. Please see [Content Page Object](content-page-object.md) for the data structure.<br><br>**Note: For the overlay page, only items of type `"space"` are allowed. It is possible to hide the overlay page if the active content page has set the `important` property to `true`.** |
| `underlay` | `object` | `null` | No | **0.1.112** | An independent underlay page that is displayed under each content page. Please see [Content Page Object](content-page-object.md) for the data structure.<br><br>**Note: For the underlay page, only items of type `"space"` are allowed. It is possible to hide the underlay page if the active content page has set the `important` property to `true`.** |
| `action` | `string` | `null` | No | 0.1.0 | The start action that is executed if the content data is loaded at startup. This property is part of the hidden features. For more information, please see [Start Action](../../experts-api/hidden-features/start-action.md). |
| `data` | `object` | `null` | No | 0.1.0 | The additional data of the start action that is used if the action is executed. This property is part of the hidden features. For more information, please see [Start Action](../../experts-api/hidden-features/start-action.md). |
| `ready` | `object` | `null` | No | **0.1.142** | A special ready object that can contain an `action` property (of type `string`) and an action-related `data` property (of type `object`). It can be used to execute an action when the content is ready. Please see [Internal Actions](../../experts-api/special/internal-actions.md) for possible values. |
| `options` | `object` | `null` | No | **0.1.120** | A special page object that is displayed in a panel if a content item is selected and the menu button is pressed. The availability of options for the current selected item is indicated in the lower right corner of the content screen. This property is ignored if the content is displayed in a panel. Please see [Content Page Object](content-page-object.md) for the data structure.<br><br>Since version **0.1.130**, you can also set a Content Root Object as options.<br><br>**Note: If the active content page or the selected content item has its own `options` property, this property is ignored.** |
| `caption` | `string` | `"opt/menu"` | No | **0.1.130** | This property is only used if the content data is set as `options` property and can be used to set a custom options caption (indicated in the lower right corner of the content screen). This property supports [Inline Expressions](../common/inline-expressions.md). |
| `captionUnderlay` | `number` | `-1` | No | **0.1.153** | The underlay of the content caption. This property can be used to explicitly set or remove the underlay of the content caption. The underlay is used to darken the lower part of the screen to make the caption more visible. By default, the underlay is used if a content list has overflowing items (i.e. content items are under the caption).<br><br>• `-1`: Automatically set/remove underlay.<br>• `0`: Remove underlay.<br>• `1`: Set underlay.<br><br>**Note: If the active content page has set its own `captionUnderlay` property to `0` or `1`, this property is ignored.** |
| `navigationOffset` | `number` | `-1` | No | **0.1.164** | The navigation offset of the list scroll indicators. This property, along with the `navigationSpan` property, allows you to align the scroll indicators with the content items, for example, when they are displayed in a specific area. The caption area is adjusted accordingly.<br><br>The value is a relative floating point number and can go from `0.0` to `12.0` (or from `0.0` to `8.0` for panels). Respectively, for compressed content, from `0.0` to `16.0` (or from `0.0` to `10.0` for panels). Invalid values are ignored. By default, the offset starts at `0.0`.<br><br>**Note: To use this property, the `type` property must be set to `"list"` or the content must be displayed in a panel, otherwise this property is ignored. If the active content page has its own `navigationOffset` or `navigationSpan` property, this property is also ignored.** |
| `navigationSpan` | `number` | `-1` | No | **0.1.164** | The navigation span of the list scroll indicators. This property, along with the `navigationOffset` property, allows you to align the scroll indicators with the content items, for example, when they are displayed in a specific area. The caption area is adjusted accordingly.<br><br>The value is a relative floating point number and can go from `0.0` to `12.0` (or from `0.0` to `8.0` for panels). Respectively, for compressed content, from `0.0` to `16.0` (or from `0.0` to `10.0` for panels). Invalid values are ignored. By default, the maximum available space is used for the span (depending on the context, this is `16.0`, `12.0`, `10.0` or `8.0`).<br><br>**Note: To use this property, the `type` property must be set to `"list"` or the content must be displayed in a panel, otherwise this property is ignored. If the active content page has its own `navigationOffset` or `navigationSpan` property, this property is also ignored.** |

## Related Hidden Features

- [Start Action](../../experts-api/hidden-features/start-action.md)
- [Focus Separator](../../experts-api/hidden-features/focus-separator.md)
- [Compress Property](../../experts-api/hidden-features/compress-property.md)
- [Option Shortcut](../../experts-api/hidden-features/option-shortcut.md)
- [Replace Action](../../experts-api/hidden-features/replace-action.md)

## See also

- [Content Page Object](content-page-object.md)
- [Content Item Object](content-item-object.md)
- [Content Examples](content-examples.md)
- [Content Guide](content-guide.md)
- [Best Practices & Good to Know → `transparent` and background images/videos](../../reference/best-practices.md#transparent-and-background-imagesvideos) — the exact two-state overlay mechanics behind the `transparent` values above
- [Best Practices & Good to Know → Video/audio keeps playing in the background after `back`](../../reference/best-practices.md#videoaudio-keeps-playing-in-the-background-after-back-by-default) — why a running background video interacts with `transparent` the way it does
- [Glossary → Cache](../../reference/glossary.md#cache), [→ Reuse](../../reference/glossary.md#reuse), [→ Restore](../../reference/glossary.md#restore), and [→ Grid](../../reference/glossary.md#grid) — the full load/reuse mechanics and exact compress-grid math behind these properties
