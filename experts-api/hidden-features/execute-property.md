---
title: Execute Property
category: Experts API - Hidden Features
summary: Explains the MSX execute property hidden feature for running actions on item load.
---

# Execute Property

It is possible to set an `execute` property (of type `boolean`) to a content item to automatically execute it on load. This feature is available since version **0.1.0**. Since version **0.1.120**, the execute property can also be set to a menu item to automatically enter the menu data on load. The internal `focus` and `execute` properties of the entered menu data are also evaluated.

**Note: To use this property, the `focus` property must be set to `true`, otherwise this property is ignored. This property has no effect if the content is loaded via a menu and the corresponding menu item has not set the `focus` and `execute` property to `true`.**

Please see following example.

## Example

### Screenshot

![Example Screenshot (Execute Property)](../../img/execute_property.png)

### Code

```json
{
    "type": "pages",
    "headline": "Videos",
    "template": {      
        "type": "separate",
        "layout": "0,0,2,4",
        "icon": "msx-white-soft:movie",
        "color": "msx-glass"
    },
    "items": [{
            "focus": true,
            "execute": true,
            "title": "Video 1",
            "action": "video:http://msx.benzac.de/media/video1.mp4"
        }, {
            "title": "Video 2",
            "action": "video:http://msx.benzac.de/media/video2.mp4"
        }, {
            "title": "Video 3",
            "action": "video:http://msx.benzac.de/media/video3.mp4"
        }]
}
```

### Demo

- [Launch via App](https://msx.benzac.de/?start=content:https://msx.benzac.de/info/xp/data/hidden_feature_3.json)
- [Launch via Demo Page](https://msx.benzac.de/info/?start=content:https://msx.benzac.de/info/xp/data/hidden_feature_3.json)

## See Also

- [Common Misconceptions → Hidden features](../../reference/common-misconceptions.md#hidden-features) — confirms `execute` auto-runs on load, not on selection/activation, and clarifies what it does on a menu item vs. a content item
