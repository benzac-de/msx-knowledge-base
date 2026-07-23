---
title: Content Page Object
category: Main API - Content
summary: Property reference for the MSX content page object that configures a single content page.
---

# Content Page Object

```json
{   
    "display": true,
    "important": false,
    "wrap": false,
    "compress": true,
    "transparent": 0,
    "headline": "Page Headline", 
    "background": "http://link.to.image",
    "area": "0,0,12,6",
    "offset": "0,0,0,0",
    "position": "page:0",
    "template": null,
    "items": [],
    "action": null,
    "data": null,
    "options": null,
    "caption": "opt/menu",
    "captionUnderlay": -1,
    "navigationOffset": -1,
    "navigationSpan": -1
}
```

Property syntax of content page object.

| Property | Type | Default Value | Mandatory | Since Version | Description |
|---|---|---|---|---|---|
| `display` | `boolean` | `true` | No | 0.1.0 | Indicates if the content page is displayed. This property can be used to temporarily remove pages. |
| `important` | `boolean` | `false` | No | **0.1.110** | Indicates if the content page is important. If this property is set to `true`, a corresponding overlay/underlay page is hidden if the content page is active. If the content page is displayed as overlay/underlay page and this property is set to `true`, the page will never be hidden. |
| `wrap` | `boolean` | `false` | No | **0.1.102** | Indicates if the content page is wrapped. If this property is set to `true`, a corresponding menu can not be entered by using the navigation keys (i.e. left and right key). This property has no effect if the content is displayed in a panel, the content is not loaded via a menu, the content page is not the first or last one (if the content type is `"pages"`), or the content page is displayed as overlay/underlay page. |
| `compress` | `boolean` | `true` | No | **0.1.123** | Indicates if font size adjustments are performed when the content is compressed.<br><br>**Note: This property is ignored if the corresponding content root object has not set the `compress` property to `true`.**<br><br>This property is part of the hidden features. For more information, please see [Compress Property](../../experts-api/hidden-features/compress-property.md). |
| `transparent` | `number`\|`boolean` | `0` | No | **0.1.142** | Indicates if the content background is transparent for this page. By default, the content background is set to a semi-transparent gray to darken the underlying video/image and make the content more visible. This property has no effect if the content is displayed in a panel or the content page is displayed as overlay/underlay page.<br><br>• `0`\|`false`: The content background is not transparent.<br>• `1`\|`true`: The content background is transparent.<br>• `2`: The content background is only transparent if no video/audio is active or the active video/audio has set the extended property `"control:transparent"` to `"true"`. For more information, please see [Extended Properties](../../experts-api/special/extended-properties.md). |
| `headline` | `string` | `null` | No | 0.1.0 | The headline of the content page. This headline is attached as sub-headline to the content headline. If the content does not have a headline, this headline is used for that. This property has no effect if the content page is displayed as overlay/underlay page. This property supports [Inline Expressions](../common/inline-expressions.md). |
| `background` | `string` | `null` | No | 0.1.0 | The background image that is used if no other background is set at lower levels. This property has no effect if the content is displayed in a panel or the content page is displayed as overlay/underlay page. |
| `area` | `string` | `null` | No | **0.1.156** | The area used by insert pages in the format `"x,y,w,h"`. This property only has an effect if the content page is set as an insert page (i.e. it is set inside the `inserts` property of the corresponding content root object). It can be used to integrate created content items into the page and specifies the area in which the items can be positioned.<br><br>The `x` value can go from `0` to `11` (or from `0` to `7` for panels).<br>The `y` value can go from `0` to `5`.<br>The `w` value can go from `1` to `12` (or from `1` to `8` for panels).<br>The `h` value can go from `1` to `6`.<br><br>All values are absolute integers.<br><br>**Note: To use this property, the `position` property must also be set, otherwise this property is ignored. If a template object also specifies an area, the possible `x`, `y`, `w`, and `h` values are bounded to these area values. If the created content items cannot be integrated (e.g. there is no space), the content page will be inserted without them.** |
| `offset` | `string` | `null` | No | 0.1.0 | The offset of the content page in format `"x,y,w,h"`. This property can be used to add or subtract an offset.<br><br>**Note: This property has no effect if the content type is `"pages"`. If the content type is `"list"`, only the `y` and `h` values are used. This property is ignored if this page is an overlay/underlay page.**<br><br>The `x` value can go from `-11.0` to `11.0` (or from `-7.0` to `7.0` for panels).<br>The `y` value can go from `-5.0` to `5.0`.<br>The `w` value can go from `-11.0` to `11.0` (or from `-7.0` to `7.0` for panels).<br>The `h` value can go from `-5.0` to `5.0`.<br><br>All values are relative floating point numbers.<br><br>**Note: The offset values have been increased in version 0.1.110. In previous versions, each value can only go from `-1.0` to `1.0`.** |
| `position` | `string` | `null` | No | **0.1.156** | The desired position of an insert page in format `"page:{PAGE_INDEX}"`, `"offset:{INSERT_OFFSET}"`, or `"context:{CONTEXT_ID}"`. This property only has an effect if the content page is set as an insert page (i.e. it is set inside the `inserts` property of the corresponding content root object). If a page index is indicated, the content page is inserted once at a specific page index (e.g. `"page:0"` inserts the content page as first page, `"page:1"` inserts the content page as second page, etc.). If an insert offset is indicated, the content page is repeatedly inserted when the indicated insert offset (based on the last insertion) is reached (e.g. `"offset:2"` inserts the content page every third page). If a context ID is indicated, the content page is inserted at a specific context break (e.g. `"context:context_id"` inserts the content page if a templated content item has set the `break` property to `"context:context_id"`). It is also possible to set mulitple positions with the `|` separator (e.g. `"page:0|page:2|offset:3"`).<br><br>**Note: It is not possible to insert multiple content pages in a row (except those ones that integrate content items with the `area` property). To check whether and which page is inserted, the following procedure is used: Check context ID → Check page index → Check insert offset. If no templated content items are available, no content pages will be inserted at all.** |
| `template` | `object` | `null` | No | **0.1.156** | The template object used by insert pages to override specific integrated content item properties. Please see [Content Item Object](content-item-object.md) for the data structure. This property only has an effect if the content page is set as an insert page (i.e. it is set inside the `inserts` property of the corresponding content root object).<br><br>**Note: To use this property, the `position` and `area` property must also be set, otherwise this property is ignored. It is not possible to override the `layout` or `break` property. The special inline expression `{context:{PROPERTY}}` cannot be used inside this template object.** |
| `items` | `array` | `null` | **Yes** | 0.1.0 | An array of content items. Please see [Content Item Object](content-item-object.md) for the data structure. This property is mandatory, because without it, no content items can be displayed. |
| `action` | `string` | `null` | No | **0.1.112** | The content page action that is executed if the page becomes active. This property is part of the hidden features. For more information, please see [Page Action](../../experts-api/hidden-features/page-action.md). |
| `data` | `object` | `null` | No | **0.1.112** | The additional data of the content page action that is used if the action is executed. This property is part of the hidden features. For more information, please see [Page Action](../../experts-api/hidden-features/page-action.md). |
| `options` | `object` | `null` | No | **0.1.120** | A special page object that is displayed in a panel if a content item from this page is selected and the menu button is pressed. The availability of options for the current selected item is indicated in the lower right corner of the content screen. This property is ignored if the content is displayed in a panel. Please see Content Page Object for the data structure.<br><br>Since version **0.1.130**, you can also set a [Content Root Object](content-root-object.md) as options.<br><br>**Note: If the selected content item has its own `options` property, this property is ignored.** |
| `caption` | `string` | `"opt/menu"` | No | **0.1.130** | This property is only used if the content page is set as `options` property and can be used to set a custom options caption (indicated in the lower right corner of the content screen). This property supports [Inline Expressions](../common/inline-expressions.md). |
| `captionUnderlay` | `number` | `-1` | No | **0.1.153** | The underlay of the content caption. This property can be used to explicitly set or remove the underlay of the content caption. The underlay is used to darken the lower part of the screen to make the caption more visible. By default, the underlay is used if a content list has overflowing items (i.e. content items are under the caption). This property has no effect if the content page is displayed as overlay/underlay page.<br><br>• `-1`: Automatically set/remove underlay.<br>• `0`: Remove underlay.<br>• `1`: Set underlay. |
| `navigationOffset` | `number` | `-1` | No | **0.1.164** | The navigation offset of the list scroll indicators. This property, along with the `navigationSpan` property, allows you to align the scroll indicators with the content items, for example, when they are displayed in a specific area. The caption area is adjusted accordingly.<br><br>The value is a relative floating point number and can go from `0.0` to `12.0` (or from `0.0` to `8.0` for panels). Respectively, for compressed content, from `0.0` to `16.0` (or from `0.0` to `10.0` for panels). Invalid values are ignored. By default, the offset starts at `0.0`.<br><br>**Note: To use this property, the `type` property of the corresponding content root object must be set to `"list"` or the content must be displayed in a panel, otherwise this property is ignored.** |
| `navigationSpan` | `number` | `-1` | No | **0.1.164** | The navigation span of the list scroll indicators. This property, along with the `navigationOffset` property, allows you to align the scroll indicators with the content items, for example, when they are displayed in a specific area. The caption area is adjusted accordingly.<br><br>The value is a relative floating point number and can go from `0.0` to `12.0` (or from `0.0` to `8.0` for panels). Respectively, for compressed content, from `0.0` to `16.0` (or from `0.0` to `10.0` for panels). Invalid values are ignored. By default, the maximum available space is used for the span (depending on the context, this is `16.0`, `12.0`, `10.0` or `8.0`).<br><br>**Note: To use this property, the `type` property of the corresponding content root object must be set to `"list"` or the content must be displayed in a panel, otherwise this property is ignored.** |

## Related Hidden Features

- [Page Action](../../experts-api/hidden-features/page-action.md)
- [Compress Property](../../experts-api/hidden-features/compress-property.md)
- [Option Shortcut](../../experts-api/hidden-features/option-shortcut.md)
- [Advanced Templated Items](../../experts-api/hidden-features/advanced-templated-items.md)

## See Also

- [Content Root Object](content-root-object.md)
- [Content Item Object](content-item-object.md)
- [Content Examples](content-examples.md)
- [Content Guide](content-guide.md)
- [Common Misconceptions → Content & layout](../../reference/common-misconceptions.md#content--layout) — why a page of only `"space"` items doesn't render (except overlay/underlay pages)
- [Common Misconceptions → Right property, wrong object](../../reference/common-misconceptions.md#right-property-wrong-object) — which page/root-level structural properties belong here vs. on the Content Root only
