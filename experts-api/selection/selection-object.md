---
title: Selection Object
category: Experts API - Selection
summary: Property reference for the MSX selection object that configures focused-item behavior.
---

# Selection Object

The selection object can be used to specify the behavior of a content item if it is selected (i.e. if it has the focus).

```json
{
    "important": false,
    "headline": "Selection Headline",
    "background": "http://link.to.image",    
    "action": null,
    "data": null    
}
```

Property syntax of selection object.

| Property | Type | Default Value | Mandatory | Since Version | Description |
|---|---|---|---|---|---|
| `important` | `boolean` | `false` | No | **0.1.110** | Indicates if the selection is important. This property only has an effect if the application is controlled with mouse or touch. If this property is set to `true`, the corresponding content item action will only be executed if the item has the focus.<br><br>**Note: Since version 0.1.136, the item also has the focus when the cursor is over it and the hover effect is set to preselect (Settings → Hover Effect → Preselect).** |
| `headline` | `string` | `null` | No | **0.1.111** | The headline of the content that is used if the corresponding content item has the focus. This headline is attached as sub-headline to the content headline. If the content does not have a headline, this headline is used for that. |
| `background` | `string` | `null` | No | **0.1.110** | The background image that is used if the corresponding content item has the focus. This property has no effect if the content is displayed in a panel. |
| `action` | `string` | `null` | No | **0.1.110** | The selection action that is executed if the corresponding content item becomes the focus. Please see [Internal Actions](../special/internal-actions.md) for possible values. |
| `data` | `object` | `null` | No | **0.1.110** | The additional data of the selection action that is used if the action is executed. Please see [Internal Actions](../special/internal-actions.md) for possible values. |

## See also

- [Selection Examples](./selection-examples.md)
- [Common Misconceptions → Right property, wrong object](../../reference/common-misconceptions.md#right-property-wrong-object) — confirms these are the only five properties (no `text`, `image`, `title`, `delay`, or `preview`)
- [In-App Settings Reference → Hover Effect](../../reference/settings-reference.md#hover-effect--hovereffect-settingshover_effect) — how `Preselect` (since `0.1.136`) makes hover also count as focus for the `important` property above
