---
title: Content Item Object
category: Main API - Content
summary: Property reference for the MSX content item object that defines a single content entry.
---

# Content Item Object

```json
{   
    "id": "content_item_id",    
    "type": "default",
    "key": "red",
    "layout": "0,0,1,1",
    "area": "0,0,12,6",
    "offset": "0,0,0,0",
    "display": true,
    "enable": true,
    "focus": false,
    "execute": false,    
    "enumerate": false,
    "compress": true,
    "decompress": false,
    "shortcut": true,
    "round": true,
    "break": "line",
    "group": "Item Group",
    "color": "msx-glass",
    "title": "Item Title",
    "titleHeader": "Item Title Header",
    "titleFooter": "Item Title Footer",
    "label": "Item Label",
    "icon": "blank",
    "iconSize": "medium",
    "headline": "Item Headline",
    "text": "Item Text",   
    "alignment": "left",
    "truncation": "headline",
    "centration": "text",
    "separation": -1,
    "tag": "Item Tag",
    "tagColor": "msx-red",
    "badge": "Item Badge",
    "badgeColor": "msx-green",
    "stamp": "Item Stamp",
    "stampColor": "msx-yellow",
    "progress": -1,
    "progressColor": "msx-blue",
    "progressBackColor": "msx-black",
    "wrapperColor": "msx-gray",
    "image": "http://link.to.image",
    "imageFiller": "default",
    "imageWidth": -1,
    "imageHeight": -1,
    "imageOverlay": -1,
    "imagePreload": false,
    "imageLabel": "Image Label",
    "imageColor": "msx-black",
    "imageScreenFiller": "fit",      
    "imageBoundary": false, 
    "playerLabel": "Player Label",
    "background": "http://link.to.image",    
    "extensionIcon": "blank",
    "extensionLabel": "Extension Label",
    "action": null,
    "data": null,
    "properties": null,
    "live": null,
    "selection": null,
    "options": null
}
```

Property syntax of content item object.

| Property | Type | Default Value | Mandatory | Since Version | Description |
|---|---|---|---|---|---|
| `id` | `string` | `null` | No | 0.1.0 | The ID of the content item. This property can be used to identify the item. The ID must be unique for the content data and only consist of letters, numbers, and the special characters `"_"`, `"-"`, `"."`. |
| `type` | `string` | `"default"` | No | 0.1.0 | The type of the content item.<br><br>• `"default"`: Default item.<br>• `"teaser"`: Teaser items are similar to `"default"` items, but they have a larger title.<br>• `"button"`: Button items have a frame and a transparent background color. The `icon` and `label` properties are typically used with this type.<br>• `"separate"`: Separate items have separated titles.<br>• `"space"`: Space items are not selectable and can be used to add background or foreground items.<br>• `"control"`: Control items are similar to `"button"` items and mainly designed for internal purposes (e.g. they are used in context menus and settings). The `icon` and `label` properties are typically used with this type. Additionally, the `extensionLabel` and `extensionIcon` properties are available for this type.<br><br>**Note: Please note that a content page must have at least one selectable item in order to be displayed (e.g. a content page that only contains items of type `"space"`, cannot be displayed). This does not apply to a content page that is displayed as overlay/underlay page, because such kind of page can only contain items of type `"space"`.** |
| `key` | `string` | `null` | No | **0.1.97** | The key that is used to assign a keyboard key (or a remote control button) to the content item action. This property is part of the hidden features. For more information, please see [Key Property](../../experts-api/hidden-features/key-property.md). |
| `layout` | `string` | `null` | **Only for the template object and non-templated items** | 0.1.0 | The layout of the content item in format `"x,y,w,h"`. This property is used to position the item on the layout grid.<br><br>**Note: The grid size is 12x6 (or 8x6 for panels). It is not possible to register multiple items on the same position, however, it is possible to overlap items (e.g. to add background or foreground items of type `"space"`). If this property is set to the template object, only the `w` and `h` values are used. This property has no effect if it is set to a templated item. Since version 0.1.156, it is possible to register multiple items of type `"space"` on the same position, because they are now excluded from the selection engine (e.g. you can register a button and multiple space items on the same position).**<br><br>The `x` value can go from `0` to `11` (or from `0` to `7` for panels).<br>The `y` value can go from `0` to `5`.<br>The `w` value can go from `1` to `12` (or from `1` to `8` for panels).<br>The `h` value can go from `1` to `6`.<br><br>All values are absolute integers. Please use the `offset` property to position items between the grid. |
| `area` | `string` | `null` | No | **0.1.110** | The area used for created content items in the format `"x,y,w,h"`. This property can only be used inside the template object and is used to specify the area in which the created items can be positioned. It is typically used with an overlay/underlay page to keep a specific area clear of content.<br><br>The `x` value can go from `0` to `11` (or from `0` to `7` for panels).<br>The `y` value can go from `0` to `5`.<br>The `w` value can go from `1` to `12` (or from `1` to `8` for panels).<br>The `h` value can go from `1` to `6`.<br><br>All values are absolute integers.<br><br>**Note: If this property is set, the possible `w` and `h` values of the `layout` property are changed accordingly.** |
| `offset` | `string` | `null` | No | 0.1.0 | The offset of the content item in format `"x,y,w,h"`. This property can be used to add or subtract an offset.<br><br>The `x` value can go from `-11.0` to `11.0` (or from `-7.0` to `7.0` for panels).<br>The `y` value can go from `-5.0` to `5.0`.<br>The `w` value can go from `-11.0` to `11.0` (or from `-7.0` to `7.0` for panels).<br>The `h` value can go from `-5.0` to `5.0`.<br><br>All values are relative floating point numbers.<br><br>**Note: The offset values have been increased in version 0.1.110. In previous versions, each value can only go from `-1.0` to `1.0`.** |
| `display` | `boolean` | `true` | No | 0.1.0 | Indicates if the content item is displayed. This property can be used to temporarily remove items. |
| `enable` | `boolean` | `true` | No | 0.1.0 | Indicates if the content item is enabled. This property can be used to temporarily disable items. |
| `focus` | `boolean` | `false` | No | 0.1.0 | Indicates if the content item is focused. This property can be used to set the focus to a specific item.<br><br>**Note: If this property is set to `true` for multiple items, only the first item will get the focus. This property has no effect if the content is loaded via a menu. Since version 0.1.120, this property is evaluated if the content is loaded via a menu and the corresponding menu item has set the `focus` and `execute` property to `true`. For more information, please see [Execute Property](../../experts-api/hidden-features/execute-property.md).** |
| `execute` | `boolean` | `false` | No | 0.1.0 | Indicates if the content item is automatically executed on load. This property is part of the hidden features. For more information, please see [Execute Property](../../experts-api/hidden-features/execute-property.md). |
| `enumerate` | `boolean` | `true`\|`false` | No | 0.1.0 | Indicates if the content item is enumerated. Enumerated items can be executed in a row (e.g. to start a playlist or a slideshow). The default value is `true` if the item is automatically created (via the template object), otherwise `false`. |
| `compress` | `boolean` | `true` | No | **0.1.123** | Indicates if font size adjustments are performed when the content is compressed.<br><br>**Note: This property is ignored if the corresponding content root object has not set the `compress` property to `true`.**<br><br>This property is part of the hidden features. For more information, please see [Compress Property](../../experts-api/hidden-features/compress-property.md). |
| `decompress` | `boolean` | `false` | No | **0.1.155** | This property can only be used inside the template object and is used to decompress compressed content items. This means that the standard layout grid size (12x6 or 8x6 for panels) is used. This allows you to use compressed header/footer/insert/overlay/underlay pages with uncompressed content items. Technically, the content items are mapped from the standard layout grid size to the compressed layout grid size (16x8 or 10x8 for panels) by making layout and offset adjustments.<br><br>**Note: This property is ignored if the corresponding content root object has not set the `compress` property to `true`.** |
| `shortcut` | `boolean` | `true` | No | **0.1.132** | Indicates if the content item can be used as shortcut if it is used inside the `options` property and the `key` property is set.<br><br>This property is part of the hidden features. For more information, please see [Option Shortcut](../../experts-api/hidden-features/option-shortcut.md). |
| `round` | `number`\|`boolean` | `true` | No | **0.1.156** | Indicates if the content item should have rounded corners if the rounded style is used. By default, most of the content item corners are rounded for this style.<br><br>• `0`\|`false`: The content item corners are not rounded if the rounded style is used (only special corners like from badges and stamps are rounded).<br>• `1`\|`true`: Most of the content item corners are rounded if the rounded style is used.<br>• `2`: This value is only relevant for items of type `"button"` or `"control"` and indicates that image corners should not be rounded if the rounded style is used (e.g. when using transparent logos that use some corners).<br>• `3`: This value is only relevant for items of type `"default"` or `"space"` and indicates that the progress bar should be rounded if the `progress` property is used, the `imageBoundary` property is set to `true`, and an `imageHeight` property is set. Otherwise, such progress bars are integrated into the content item unrounded. |
| `break` | `string` | `null` | No | **0.1.156** | The break type of the content item if it is automatically created (i.e. via the template object). By default, no breaks are enforced (i.e. only if a line/page is full, a new one is started).<br><br>• `"line"`: The item will be displayed on a new line (or on a new page if a new line is not possible).<br>• `"page"`: The item will be displayed on a new page.<br>• `"context"`: The item will be displayed on a new page and a page offset is added (if no insert page has been inserted) to indicate a context break.<br>• `"context:{CONTEXT_ID}"`: The item will be displayed on a new page and an insert page with the indicated context ID (e.g. `"context:context_id"`) is inserted beforehand. If there is no insert page for the indicated context ID, this type behaves like `"context"`.<br><br>**Note: This property only has an effect if it is set to a templated item.** |
| `group` | `string` | `null` | No | **0.1.92** | The group label of the content item if it is enumerated. This label is shown in the content headline after the enumeration info. If the group label starts with the string `"id:"` (e.g. `"id:group_id"`), the label is only used as ID and does not appear in the content headline.<br><br>**Note: Each group has its own enumeration counter. Therefore, if a group item is executed, only the related group items are executed in a row. To use this property, the `enumerate` property must be set to `true`, otherwise this property is ignored.**<br><br>This property supports [Inline Expressions](../common/inline-expressions.md). |
| `color` | `string` | `null` | No | 0.1.0 | The background color of the content item. Please see [Colors](../common/colors.md) for possible values.<br><br>**Note: This property is ignored for content items of type `"button"` or `"control"`.** |
| `title` | `string` | `null` | No | 0.1.0 | The title of the content item that is positioned in the lower left corner.<br><br>**Note: Since version 0.1.82, you can change the alignment of this content with the `alignment` property.**<br><br>This property supports [Inline Expressions](../common/inline-expressions.md). |
| `titleHeader` | `string` | `null` | No | 0.1.0 | The title header of the content item that is positioned in the lower left corner.<br><br>**Note: Since version 0.1.82, you can change the alignment of this content with the `alignment` property.**<br><br>This property supports [Inline Expressions](../common/inline-expressions.md). |
| `titleFooter` | `string` | `null` | No | 0.1.0 | The title footer of the content item that is positioned in the lower left corner.<br><br>**Note: Since version 0.1.82, you can change the alignment of this content with the `alignment` property.**<br><br>This property supports [Inline Expressions](../common/inline-expressions.md). |
| `label` | `string` | `null` | No | 0.1.0 | The label of the content item that is positioned in the bottom center or on the left side if the item type is `"control"`. This property is typically used for items of type `"button"` or `"control"`. This property supports [Inline Expressions](../common/inline-expressions.md). |
| `icon` | `string` | `null` | No | 0.1.0 | The icon of the content item that is positioned in the center or on the left side if the item type is `"control"`. This property is typically used for items of type `"button"` or `"control"`. Please see [Icons](../common/icons.md) for possible values. |
| `iconSize` | `string` | `"medium"` | No | 0.1.0 | The size of the content icon.<br><br>• `"small"`: Small size.<br>• `"medium"`: Medium size.<br>• `"large"`: Large size.<br>• `"extra-large"`: Extra-large size.<br><br>**Note: This property is ignored for content items of type `"control"`.** |
| `headline` | `string` | `null` | No | 0.1.0 | The headline of the content item that is positioned in the upper left corner.<br><br>**Note: Since version 0.1.82, you can change the alignment of this content with the `alignment` property.**<br><br>This property supports [Inline Expressions](../common/inline-expressions.md). |
| `text` | `string`\|`array` | `null` | No | 0.1.0 | The text of the content item that is positioned in the upper left corner. By default, the text is indicated as single `string`. Additionally, longer texts can be split into an `array` of strings.<br><br>**Note: Since version 0.1.82, you can change the alignment of this content with the `alignment` property.**<br><br>This property supports [Inline Expressions](../common/inline-expressions.md). |
| `alignment` | `string` | `"left"` | No | **0.1.82** | The content alignment of the properties `title`, `titleHeader`, `titleFooter`, `headline`, and `text`. By default, the content of these properties are aligned on the left side.<br><br>• `"left"`: The content is aligned on the left side.<br>• `"center"`: The content is aligned in the center.<br>• `"right"`: The content is aligned on the right side.<br>• `"justify"`: The content is aligned to fill the entire size.<br><br>Since version **0.1.97**, you can also set separate alignments for the title (i.e. `title`, `titleHeader`, and `titleFooter`) and text (i.e. `headline` and `text`) properties.<br><br>• `"title-left"`: The title properties are aligned on the left side.<br>• `"title-center"`: The title properties are aligned in the center.<br>• `"title-right"`: The title properties are aligned on the right side.<br>• `"title-justify"`: The title properties are aligned to fill the entire size.<br>• `"text-left"`: The text properties are aligned on the left side.<br>• `"text-center"`: The text properties are aligned in the center.<br>• `"text-right"`: The text properties are aligned on the right side.<br>• `"text-justify"`: The text properties are aligned to fill the entire size.<br><br>Since version **0.1.146**, you can also set the alignment of the badge and the stamp.<br><br>• `"badge-left"`: The badge is aligned on the left side.<br>• `"badge-center"`: The badge is aligned in the center.<br>• `"badge-right"`: The badge is aligned on the right side.<br>• `"stamp-left"`: The stamp is aligned on the left side.<br>• `"stamp-center"`: The stamp is aligned in the center.<br>• `"stamp-right"`: The stamp is aligned on the right side.<br><br>Since version **0.1.156**, you can also assign the title and text properties to specific areas if content separation is used.<br><br>• `"separation-left"`: The title and text properties are assigned to the left area.<br>• `"separation-title-left"`: The title properties are assigned to the left area.<br>• `"separation-text-left"`: The text properties are assigned to the left area.<br>• `"separation-right"`: The title and text properties are assigned to the right area.<br>• `"separation-title-right"`: The title properties are assigned to the right area.<br>• `"separation-text-right"`: The text properties are assigned to the right area.<br><br>If you want to set multiple alignments, please use the separator `|` (e.g. `"title-left|stamp-right"`). |
| `truncation` | `string` | `null` | No | **0.1.128** | Indicates which content text properties (i.e. `title`, `titleHeader`, `titleFooter`, `headline`, and `text`) should be truncated. By default, these properties are not truncated. If you want to indicate multiple properties, please use the separator `|` (e.g. `"titleHeader|titleFooter"`). |
| `centration` | `string` | `null` | No | **0.1.146** | Indicates which of the following content properties should be centered vertically.<br><br>• `title` (this also includes the `titleHeader` and `titleFooter` properties)<br>• `text` (this also includes the `headline` property)<br>• `label`<br>• `badge`<br>• `stamp`<br><br>By default, these properties are either top or bottom aligned. If you want to indicate multiple properties, please use the separator `|` (e.g. `"badge|stamp"`). Typically, this property is useful when the layout height (i.e. the `h` value of the `layout` property) is set to `1`.<br><br>**Note: Please note that properties like `icon` or `extensionLabel` are always centered vertically and are therefore not available for this property. For items of type `"space"`, an `image` or a `color` property must also be set, otherwise this property is ignored.** |
| `separation` | `number` | `-1` | No | **0.1.156** | The content separation value to separate label (i.e. `label` and `extensionLabel`), title (i.e. `title`, `titleHeader`, and `titleFooter`), and text (i.e. `headline` and `text`) properties into a left and right area. This property can be used to prevent overlapping content. The value is a relative floating point number and can go from `0.0` (0% for the left and 100% for the right area) to `1.0` (100% for the left and 0% for the right area). If this property is set to `-1`, no content separation is performed. By default, all properties (except `extensionLabel`) are assigned to the left area. This can be changed with the `alignment` property.<br><br>**Note: For items of type `"space"`, an `image` or a `color` property must also be set, otherwise this property is ignored. Only for items of type `"control"`, the `label` and `extensionLabel` properties are used for content separations. For items of type `"separate"`, the `title`, `titleHeader`, and `titleFooter` properties are excluded from content separations.** |
| `tag` | `string` | `null` | No | 0.1.0 | The tag of the content item that is positioned in the upper right corner. This property supports [Inline Expressions](../common/inline-expressions.md).<br><br>**Note: For items of type `"space"`, an `image` or a `color` property must also be set, otherwise this property is ignored.** |
| `tagColor` | `string` | `null` | No | 0.1.0 | The background color of the content tag. Please see [Colors](../common/colors.md) for possible values. |
| `badge` | `string` | `null` | No | 0.1.0 | The badge of the content item that is positioned in the upper left corner. This property supports [Inline Expressions](../common/inline-expressions.md).<br><br>**Note: For items of type `"space"`, an `image` or a `color` property must also be set, otherwise this property is ignored. Since version 0.1.146, you can change the alignment of the badge with the `alignment` property.** |
| `badgeColor` | `string` | `null` | No | 0.1.0 | The background color of the content badge. Please see [Colors](../common/colors.md) for possible values. |
| `stamp` | `string` | `null` | No | **0.1.146** | The stamp of the content item that is positioned in the lower right corner. This property supports [Inline Expressions](../common/inline-expressions.md).<br><br>**Note: For items of type `"space"`, an `image` or a `color` property must also be set, otherwise this property is ignored. You can change the alignment of the stamp with the `alignment` property.** |
| `stampColor` | `string` | `null` | No | **0.1.146** | The background color of the content stamp. Please see [Colors](../common/colors.md) for possible values. |
| `progress` | `number` | `-1` | No | **0.1.70** | The progress of the content item that is shown as bar at the bottom. The value is a relative floating point number and can go from `0.0` to `1.0`. If this property is set to `-1` (or another negative value), the progress bar is not visible, but can be updated. If this property is not set at all, the progress bar is not visible and can not be updated. |
| `progressColor` | `string` | `null` | No | **0.1.70** | The color of the progress bar. Please see [Colors](../common/colors.md) for possible values. |
| `progressBackColor` | `string` | `null` | No | **0.1.156** | The background color of the progress bar. Please see [Colors](../common/colors.md) for possible values. |
| `wrapperColor` | `string` | `null` | No | **0.1.70** | The color of the content wrapper (e.g. the color of the image space within the content item). To use this property, the content must be wrapped (e.g. the `imageWidth` or `imageHeight` property is set), otherwise this property is ignored. Please see [Colors](../common/colors.md) for possible values. |
| `image` | `string` | `null` | No | 0.1.0 | The image of the content item. The properties `imageFiller`, `imageWidth`, and `imageHeight` can be used to style and position the image. |
| `imageFiller` | `string` | `"default"` | No | 0.1.0 | The filler type of the content image.<br><br>• `"default"`: The image is stretched to fill the entire size.<br>• `"width"`\|`"width-top"`: The image is sized to fill the entire width (by keeping the ratio) and is positioned at the top.<br>• `"width-center"`: The image is sized to fill the entire width (by keeping the ratio) and is positioned in the center.<br>• `"width-bottom"`: The image is sized to fill the entire width (by keeping the ratio) and is positioned at the bottom.<br>• `"height"`\|`"height-left"`: The image is sized to fill the entire height (by keeping the ratio) and is positioned on the left side.<br>• `"height-center"`: The image is sized to fill the entire height (by keeping the ratio) and is positioned in the center.<br>• `"height-right"`: The image is sized to fill the entire height (by keeping the ratio) and is positioned on the right side.<br>• `"fit"`: The image is sized (by keeping the ratio) to fit into the size and is positioned in the center.<br>• `"cover"`: The image is sized (by keeping the ratio) to cover the entire size and is positioned in the center.<br>• `"smart"`: If the ratio of the image and the size is the same, `"cover"` is used, otherwise `"fit"` is used. |
| `imageWidth` | `number` | `-1` | No | 0.1.0 | The width of the content image. This property can be used to reduce the width of the image and to position the text on the right side of it. The value is a relative floating point number and can go from `0.0` to `12.0` (or from `0.0` to `8.0` for panels). Additionally, the value must be less or equal than the item width, otherwise it is ignored. |
| `imageHeight` | `number` | `-1` | No | 0.1.0 | The height of the content image. This property can be used to reduce the height of the image. The value is a relative floating point number and can go from `0.0` to `6.0`. Additionally, the value must be less or equal than the item height, otherwise it is ignored. |
| `imageOverlay` | `number` | `-1` | No | 0.1.0 | The overlay of the content image. This property can be used to explicitly set or remove the overlay of the image. The overlay is used to darken the lower part of the image to make the titles (i.e. `title`, `titleHeader`, and `titleFooter`) more visible. By default, the overlay is used if an image has titles on top of it.<br><br>• `-1`: Automatically set/remove overlay.<br>• `0`: Remove overlay.<br>• `1`: Set overlay.<br><br>Since version **0.1.97**, you can also set an inverted overlay to darken the upper part of the image to make the text (i.e. `headline` and `text`) more visible.<br><br>• `2`: Set inverted overlay and automatically set/remove default overlay.<br>• `3`: Set inverted overlay and remove default overlay.<br>• `4`: Set inverted and default overlay. |
| `imagePreload` | `boolean` | `false` | No | **0.1.58** | Indicates if the content image is preloaded to make it directly visible. By default, images are loaded on the fly.<br><br>**Note: For preloaded images, the `imageFiller` property can not be set to `"fit"`, `"cover"`, or `"smart"`. Since version 0.1.155, this property can be set to `false` to disable the auto image preload feature for reload/replace actions.** |
| `imageLabel` | `string` | `null` | No | 0.1.0 | The label of the content image that is used inside the slideshow. This property is only used if the content action is `image:{URL}` or `image:context`. This property supports [Inline Expressions](../common/inline-expressions.md). |
| `imageColor` | `string` | `null` | No | **0.1.6** | The background color of the content image that is used inside the slideshow. This property is only used if the content action is `image:{URL}` or `image:context`. Please see [Colors](../common/colors.md) for possible values. |
| `imageScreenFiller` | `string` | `"fit"` | No | **0.1.6** | The filler type of the content image that is used inside the slideshow. This property is only used if the content action is `image:{URL}` or `image:context`. Please see property `imageFiller` for possible values. |
| `imageBoundary` | `boolean` | `false` | No | **0.1.146** | Indicates if specific content properties (i.e. `tag`, `badge`, `stamp`, `progress`, and `imageOverlay`) are bound to the image area. By default, these properties are bound to the area of the entire content item. To use this property, the content must be wrapped (e.g. the `imageWidth` or `imageHeight` property is set), otherwise this property is ignored.<br><br>**Note: Please note that the named content properties are already bound to the image area for items of type `"separate"`.** |
| `playerLabel` | `string` | `null` | No | 0.1.0 | The label of the content video/audio that is used inside the player. This property is only used if the content action is `video:{URL}` or `audio:{URL}`. This property supports [Inline Expressions](../common/inline-expressions.md). |
| `background` | `string` | `null` | No | 0.1.0 | The background of the content audio that is used if it is currently being played. This property is only used if the content action is <code>audio:{URL}</code>. |
| `extensionIcon` | `string` | `null` | No | 0.1.0 | The extension icon. This property is only available for content items of type `"control"` and can be used to display an additional icon on the right side of the control item. Please see [Icons](../common/icons.md) for possible values. |
| `extensionLabel` | `string` | `null` | No | 0.1.0 | The extension label. This property is only available for content items of type `"control"` and can be used to display an additional label on the right side of the control item. This property is only used if no `extensionIcon` is set. This property supports [Inline Expressions](../common/inline-expressions.md). |
| `action` | `string` | `null` | No | 0.1.0 | The action of the content item that is performed if the item is executed. If this property is not set and the item is executed, an info message with *"No action available."* is displayed. Please see [Actions](../common/actions.md) for possible values. |
| `data` | `object` | `null` | No | 0.1.0 | The additional data of the content item that is used if the item is executed. Please see [Actions](../common/actions.md) for possible values. |
| `properties` | `object` | `null` | No | **0.1.58** | Extended properties for special use cases. Each property is a key-value pair of type `string`. Please see [Extended Properties](../../experts-api/special/extended-properties.md) for possible values. |
| `live` | `object` | `null` | No | **0.1.70** | A special live object to periodically update content items (e.g. with live data, playback progress, etc.). Please see [Live Object](../../experts-api/live/live-object.md) for the data structure. |
| `selection` | `object` | `null` | No | **0.1.110** | A special selection object to specify the behavior of the content item if it is selected (i.e. if it has the focus). Please see [Selection Object](../../experts-api/selection/selection-object.md) for the data structure. |
| `options` | `object` | `null` | No | **0.1.120** | A special page object that is displayed in a panel if the content item is selected and the menu button is pressed. The availability of options for the current selected item is indicated in the lower right corner of the content screen. This property is ignored if the content is displayed in a panel. Please see [Content Page Object](content-page-object.md) for the data structure.<br><br>Since version **0.1.130**, you can also set a [Content Root Object](content-root-object.md) as options. |

## Related Hidden Features

- [Execute Property](../../experts-api/hidden-features/execute-property.md)
- [Focus Separator](../../experts-api/hidden-features/focus-separator.md)
- [Hide Images](../../experts-api/hidden-features/hide-images.md)
- [Number Inline Expressions](../../experts-api/hidden-features/number-inline-expressions.md)
- [Color Inline Expressions](../../experts-api/hidden-features/color-inline-expressions.md)
- [Key Property](../../experts-api/hidden-features/key-property.md)
- [Large Offsets](../../experts-api/hidden-features/large-offsets.md)
- [Dictionary Inline Expressions](../../experts-api/hidden-features/dictionary-inline-expressions.md)
- [Compress Property](../../experts-api/hidden-features/compress-property.md)
- [Resolve Action](../../experts-api/hidden-features/resolve-action.md)
- [Option Shortcut](../../experts-api/hidden-features/option-shortcut.md)
- [Advanced Text & Image](../../experts-api/hidden-features/advanced-text-image.md)

## See Also

- [Content Root Object](content-root-object.md)
- [Content Page Object](content-page-object.md)
- [Content Examples](content-examples.md)
- [Content Guide](content-guide.md)
- [Best Practices & Good to Know → Item `type: "separate"` reserves less image/focus space than its `layout` height suggests](../../reference/best-practices.md#item-type-separate-reserves-less-imagefocus-space-than-its-layout-height-suggests) — also covers `image`+`icon` combining and the `image` boundary on `"control"`/`"button"` items
- [Best Practices & Good to Know → Sizing `image` sources for the grid vs. for full-screen](../../reference/best-practices.md#sizing-image-sources-for-the-grid-vs-for-full-screen)
- [Best Practices & Good to Know → Image sizing, overlay, and boundaries within an item](../../reference/best-practices.md#image-sizing-overlay-and-boundaries-within-an-item-imagewidth-imageheight-imageoverlay-imageboundary) — `imageWidth`/`imageHeight`/`imageOverlay`/`imageBoundary` worked through in detail
- [Best Practices & Good to Know → Don't combine `image` + `icon` on the same item](../../reference/best-practices.md#dont-combine-image--icon-on-the-same-item--contrast-risk-imageoverlay-cant-fix) — the contrast risk `imageOverlay` can't fix
- [Best Practices & Good to Know → Choosing `"teaser"` over `"default"`](../../reference/best-practices.md#choosing-teaser-over-default--reserve-it-for-large-items) — reserve it for large items
- [Best Practices & Good to Know → Give `"button"` items enough height when combining `icon` + `label`](../../reference/best-practices.md#give-button-items-enough-height-when-combining-icon--label) — exact height/`iconSize` combinations that fit
- [Best Practices & Good to Know → Give `"control"` items a `layout` height of `1`](../../reference/best-practices.md#give-control-items-a-layout-height-of-1)
- [Best Practices & Good to Know → Set `separation` on a `"control"` item combining `label` with `extensionIcon`/`extensionLabel`](../../reference/best-practices.md#set-separation-on-a-control-item-combining-label-with-extensioniconextensionlabel)
- [Best Practices & Good to Know → `tag`/`stamp`/`badge`](../../reference/best-practices.md#tagstampbadge--short-single-line-text-and-default-colors-that-already-fit) — short single-line text, and default colors that already fit
- [Best Practices & Good to Know → `"space"` items needed a layout/offset workaround before `0.1.156`](../../reference/best-practices.md#space-items-needed-a-layoutoffset-workaround-to-share-a-position-before-01156)
- [Cookbook → Deep dive — `layout` drives navigation, `offset` doesn't](../../reference/cookbook.md#deep-dive--layout-drives-navigation-offset-doesnt-list0json) — a real example steering focus without changing how anything looks
- [Common Misconceptions → Content & layout](../../reference/common-misconceptions.md#content--layout)
