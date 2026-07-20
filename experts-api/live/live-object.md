---
title: Live Object
category: Experts API - Live
summary: Property reference for the MSX live object that enables periodic content updates.
---

# Live Object

The live object can be used to periodically update content items (e.g. with live data, playback progress, etc.). It can have following states.

- `"init"`: The initial state. Possible next states: `"coming"`, `"running"`, or `"over"`.
- `"coming"`: The object is not live yet or it was live before and can become live again. Possible next states: `"running"` or `"over"`.
- `"running"`: The object is live. Possible next states: `"coming"` or `"over"`.
- `"over"`: The object is not live anymore. Possible next states: `"coming"` or `"running"`.

```json
{    
    "type": "schedule",
    "from": 0,
    "to": 0,   
    "duration": 0,
    "delay": 0,
    "source": "url",
    "coming": null,
    "running": null,
    "over": null,
    "execute": null,
    "preload": true,
    "color": "msx-glass",
    "title": "Item Title",
    "titleHeader": "Item Title Header",
    "titleFooter": "Item Title Footer",
    "label": "Item Label",
    "icon": "blank",     
    "headline": "Item Headline",
    "text": "Item Text",
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
    "extensionIcon": "blank",
    "extensionLabel": "Item Extension Label",    
    "action": null,
    "data": null    
}
```

Property syntax of live object.

| Property | Type | Default Value | Mandatory | Since Version | Description |
|---|---|---|---|---|---|
| `type` | `string` | `null` | No | **0.1.70** | The type of the live object. If this property is not set, the current live object is removed.<br><br>• `"schedule"`: The object is live for a specific schedule time, indicated by a start and end timestamp (i.e. the `from` and `to` properties). Possible states: `"init"`, `"coming"`, `"running"`, and `"over"`.<br>• `"lifetime"`: The object is live for a specific lifetime (starting from the creation timestamp), indicated by a duration and delay (i.e. the `duration` and `delay` properties). Possible states: `"init"`, `"coming"`, `"running"`, and `"over"`.<br>• `"airtime"`: The object is live for a specific airtime (running for the visible time), indicated by a duration (i.e. the `duration` property). Possible states: `"init"`, `"running"`, and `"over"`.<br>• `"playback"`: The object is live if the corresponding content item is currently being played. Possible states: `"init"`, `"running"`, and `"over"`. Before version **0.1.146**, this type can only be used if the corresponding content action is `video:{URL}` or `audio:{URL}`. Please note that in this case the corresponding content action URL is compared with the current player URL to check the live state. Since version **0.1.146**, the `source` property can be used to define which property should be used to check the live state.<br>• `"setup"`: The object is never live and has only the state `"init"`, but the live action (i.e. the `action` property) is executed if the corresponding content item becomes visible.<br><br>**Note: The live object is only evaluated if the corresponding content item is visible. If the corresponding content item is visible, the live object is evaluated every second, regardless of the current state. Since version 0.1.74, live objects of type `"playback"` can also have the state `"coming"` if the corresponding content item contains the extension property `resume:key`. Please also note that live objects are evaluated on an overlay/underlay page, even if the overlay/underlay page is hidden.** |
| `from` | `number` | `0` | No | **0.1.70** | The start timestamp of the live object in milliseconds. The value must be greater or equal than `0`, otherwise it is ignored.<br><br>**Note: To use this property, the `type` property must be set to `"schedule"`, otherwise this property is ignored.** |
| `to` | `number` | `0` | No | **0.1.70** | The end timestamp of the live object in milliseconds. The value must be greater or equal than `0` and the start timestamp, otherwise it is ignored.<br><br>**Note: To use this property, the `type` property must be set to `"schedule"`, otherwise this property is ignored.** |
| `duration` | `number` | `0` | No | **0.1.70** | The duration of the live object in milliseconds. The value must be greater or equal than `0`, otherwise it is ignored.<br><br>**Note: To use this property, the `type` property must be set to `"lifetime"` or `"airtime"`, otherwise this property is ignored.** |
| `delay` | `number` | `0` | No | **0.1.70** | The delay of the live object in milliseconds. The value must be greater or equal than `0`, otherwise it is ignored.<br><br>**Note: To use this property, the `type` property must be set to `"lifetime"`, otherwise this property is ignored.** |
| `source` | `string` | `"url"` | No | **0.1.146** | The source that defines which property should be used to check the playback live state.<br><br>• `"id"`: The corresponding content item ID is used to check the live state. If the corresponding content item does not contain an ID, the current live object is removed.<br>• `"url"`: The corresponding content action URL is used to check the live state. If the corresponding content action is not set to `video:{URL}` or `audio:{URL}`, the current live object is removed.<br>• `"key"`: The corresponding content item extension property `resume:key` is used to check the live state. If the corresponding content item does not contain the extension property `resume:key`, the current live object is removed.<br>• `"current"`: The live object will never be in the state `"coming"`, but will be in the state `"running"` as soon as the player is ready.<br>• `"none"`: The live object will never be in the state `"running"`, but will be in the state `"coming"` if the corresponding content item contains the extension property `resume:key` and it is resumable.<br><br>Since version **0.1.153**, you can also indicate a specific `"id"`, `"url"`, or `"key"` source.<br><br>• `"id:{ID}"`: The indicated content item ID is used to check the live state.<br>• `"url:{URL}"`: The indicated content action URL (i.e. `video:{URL}` or `audio:{URL}`) is used to check the live state.<br>• `"key:{KEY}"`: The indicated content `resume:key` is used to check the live state.<br>• `"resume:id:{ID}"`: The live object will never be in the state `"running"`, but will be in the state `"coming"` if the indicated content item ID is resumable.<br>• `"resume:url:{URL}"`: The live object will never be in the state `"running"`, but will be in the state `"coming"` if the indicated content action URL (i.e. `video:{URL}` or `audio:{URL}`) is resumable.<br>• `"resume:key:{KEY}"`: The live object will never be in the state `"running"`, but will be in the state `"coming"` if the indicated content `resume:key` is resumable.<br><br>**Note: To use this property, the `type` property must be set to `"playback"`, otherwise this property is ignored.** |
| `coming` | `object` | `null` | No | **0.1.70** | The state object that contains the live content properties (e.g. `title`, `titleHeader`, `titleFooter`, etc.) and the live action if the live object is in state `"coming"`. If this object does not contain live content properties, the live content properties from the live object are used.<br><br>**Note: To use this property, the `type` property must be set to `"schedule"`, otherwise this property is ignored. The live action will only be executed once, if the live object enters the state `"coming"`. Since version 0.1.74, live objects of type `"playback"` can also have this property.** |
| `running` | `object` | `null` | No | **0.1.70** | The state object that contains the live content properties (e.g. `title`, `titleHeader`, `titleFooter`, etc.) and the live action if the live object is in state `"running"`. If this object does not contain live content properties, the live content properties from the live object are used.<br><br>This object can also contain following sub-state objects to use separate live content properties for specific quartiles.<br><br>• `quartile1`: This sub-state object is used if the progress is in range of 0.0 to 0.25.<br>• `quartile2`: This sub-state object is used if the progress is in range of 0.25 to 0.5.<br>• `quartile3`: This sub-state object is used if the progress is in range of 0.5 to 0.75.<br>• `quartile4`: This sub-state object is used if the progress is in range of 0.75 to 1.0.<br><br>Since version **0.1.153**, this object can also contain following sub-state objects to use separate live content properties for specific states.<br><br>• `ready`: This sub-state object is used if the progress is exactly 0.0. If this object is not set, the `quartile1` object is used. It is typically used with live objects of type `"playback"`.<br>• `complete`: This sub-state object is used if the progress is exactly 1.0. If this object is not set, the `quartile4` object is used. It is typically used with live objects of type `"playback"`.<br><br>**Note: To use this property, the `type` property must be set to `"schedule"`, `"lifetime"`, `"airtime"`, or `"playback"`, otherwise this property is ignored. The live action will only be executed once, if the live object enters the state `"running"`. The sub-state objects do not support live actions. Since version 0.1.74, live objects of type `"playback"` can also contain the sub-state objects inside the `coming` property.** |
| `over` | `object` | `null` | No | **0.1.70** | The state object that contains the live content properties (e.g. `title`, `titleHeader`, `titleFooter`, etc.) and the live action if the live object is in state `"over"`. If this object does not contain live content properties, the live content properties from the live object are used.<br><br>**Note: To use this property, the `type` property must be set to `"schedule"`, `"lifetime"`, `"airtime"`, or `"playback"`, otherwise this property is ignored. The live action will only be executed once, if the live object enters the state `"over"`.** |
| `execute` | `object` | `null` | No | **0.1.74** | The execution object that contains the live action that is executed if the corresponding content action is set to `"live"` and the corresponding content item is executed.<br><br>**Note: To use this property, the `type` property must be set to `"schedule"`, `"lifetime"`, `"airtime"`, or `"setup"`, otherwise this property is ignored. If this property is set inside a state object (i.e. `coming`, `running`, or `over`), it is used (instead of this one) if the live object has the corresponding state.** |
| `preload` | `boolean` | `true` | No | **0.1.156** | Indicates if the live object should be preloaded when reloading/replacing the content and the live object is visible.<br><br>**Note: To use this property, the `type` property must be set to `"schedule"` or `"playback"`, otherwise this property is ignored.** |
| `color` | `string` | `null` | No | **0.1.70** | The live content property of the corresponding content item. |
| `title` | `string` | `null` | No | **0.1.70** | The live content property of the corresponding content item. This property supports [Live Inline Expressions](./live-inline-expressions.md).<br><br>**Note: Only if the `title` property has been set (to a non `null` value) in the origin data of the corresponding content item, it can be updated.** |
| `titleHeader` | `string` | `null` | No | **0.1.70** | The live content property of the corresponding content item. This property supports [Live Inline Expressions](./live-inline-expressions.md).<br><br>**Note: Only if the `titleHeader` property has been set (to a non `null` value) in the origin data of the corresponding content item, it can be updated.** |
| `titleFooter` | `string` | `null` | No | **0.1.70** | The live content property of the corresponding content item. This property supports [Live Inline Expressions](./live-inline-expressions.md).<br><br>**Note: Only if the `titleFooter` property has been set (to a non `null` value) in the origin data of the corresponding content item, it can be updated.** |
| `label` | `string` | `null` | No | **0.1.70** | The live content property of the corresponding content item. This property supports [Live Inline Expressions](./live-inline-expressions.md).<br><br>**Note: Only if the `label` property has been set (to a non `null` value) in the origin data of the corresponding content item, it can be updated.** |
| `icon` | `string` | `null` | No | **0.1.70** | The live content property of the corresponding content item.<br><br>**Note: Only if the `icon` property has been set (to a non `null` value) in the origin data of the corresponding content item, it can be updated.** |
| `headline` | `string` | `null` | No | **0.1.70** | The live content property of the corresponding content item. This property supports [Live Inline Expressions](./live-inline-expressions.md).<br><br>**Note: Only if the `headline` property has been set (to a non `null` value) in the origin data of the corresponding content item, it can be updated.** |
| `text` | `string`\|`array` | `null` | No | **0.1.70** | The live content property of the corresponding content item. This property supports [Live Inline Expressions](./live-inline-expressions.md).<br><br>**Note: Only if the `text` property has been set (to a non `null` value) in the origin data of the corresponding content item, it can be updated.** |
| `tag` | `string` | `null` | No | **0.1.70** | The live content property of the corresponding content item. This property supports [Live Inline Expressions](./live-inline-expressions.md).<br><br>**Note: Only if the `tag` property has been set (to a non `null` value) in the origin data of the corresponding content item, it can be updated.** |
| `tagColor` | `string` | `null` | No | **0.1.70** | The live content property of the corresponding content item. |
| `badge` | `string` | `null` | No | **0.1.70** | The live content property of the corresponding content item. This property supports [Live Inline Expressions](./live-inline-expressions.md).<br><br>**Note: Only if the `badge` property has been set (to a non `null` value) in the origin data of the corresponding content item, it can be updated.** |
| `badgeColor` | `string` | `null` | No | **0.1.70** | The live content property of the corresponding content item. |
| `stamp` | `string` | `null` | No | **0.1.146** | The live content property of the corresponding content item. This property supports [Live Inline Expressions](./live-inline-expressions.md).<br><br>**Note: Only if the `stamp` property has been set (to a non `null` value) in the origin data of the corresponding content item, it can be updated.** |
| `stampColor` | `string` | `null` | No | **0.1.146** | The live content property of the corresponding content item. |
| `progress` | `number` | `-1` | No | **0.1.70** | The live content property of the corresponding content item.<br><br>**Note: Only if the `progress` property has been set (to a non `null` value) in the origin data of the corresponding content item, it can be updated. By default, this property is automatically updated if the live object is in state `"running"`. If this property is set, it will not be updated automatically. Since version 0.1.74, this property is also automatically updated if the `type` property is set to `"playback"` and the corresponding content item contains the extension property `resume:key`. Since version 0.1.156, this property can be set to `-2` to explicitly enable auto-update (any other value is used as is).** |
| `progressColor` | `string` | `null` | No | **0.1.70** | The live content property of the corresponding content item. |
| `progressBackColor` | `string` | `null` | No | **0.1.156** | The live content property of the corresponding content item. |
| `wrapperColor` | `string` | `null` | No | **0.1.70** | The live content property of the corresponding content item. |
| `image` | `string` | `null` | No | **0.1.70** | The live content property of the corresponding content item.<br><br>**Note: Only if the `image` property has been set (to a non `null` value) in the origin data of the corresponding content item, it can be updated.** |
| `extensionIcon` | `string` | `null` | No | **0.1.70** | The live content property of the corresponding content item.<br><br>**Note: Only if the `extensionIcon` property has been set (to a non `null` value) in the origin data of the corresponding content item, it can be updated.** |
| `extensionLabel` | `string` | `null` | No | **0.1.70** | The live content property of the corresponding content item.<br><br>**Note: Only if the `extensionLabel` property has been set (to a non `null` value) in the origin data of the corresponding content item, it can be updated.** |
| `action` | `string` | `null` | No | **0.1.70** | The live action that is executed if the `type` property is set to `"setup"` and the corresponding content item becomes visible. The live action will also be executed if the `type` property is set to `"playback"`, the live object is in state `"running"`, and the corresponding content item is executed.<br><br>**Note: If this property is set inside a state object (i.e. `coming`, `running`, or `over`), it is executed if the live object enters the corresponding state.** |
| `data` | `object` | `null` | No | **0.1.70** | The additional data of the live action that is used if the action is executed. |

## See also

- [Live Inline Expressions](./live-inline-expressions.md)
- [Live Examples](./live-examples.md)
- [Cookbook → Deep dive — a minimal "resume playback" item (`live_test_2` pattern)](../../reference/cookbook.md#deep-dive--a-minimal-resume-playback-item-live_test_2-pattern) — why `resume:key` and a `type: "playback"` live object are normally used together, and how this pattern also works through a plugin player
- [Glossary → Live Object](../../reference/glossary.md#live-object)
