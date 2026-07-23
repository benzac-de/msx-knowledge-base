---
title: Image Options
category: Experts API - Hidden Features
summary: Explains the MSX image options hidden feature for rotating the current slideshow image via the options panel or remote control REWIND/FORWARD buttons.
---

# Image Options

It is possible to activate slideshow image options (e.g. to rotate the current slideshow image). The availability of image options for the current image is indicated in the slideshow labels on the right side. By default, slideshow image options are deactivated. This feature is available since version **0.1.145**.

**Note: If slideshow image options are activated, you can also use the remote control buttons `REWIND` and `FORWARD` to rotate the image to left and right.**

Please see following example.

## Example

### Screenshot

![Example Screenshot (Image Options)](../../img/image_options.png)

### Code

```json
{
    "type": "pages",
    "headline": "Image Options",
    "template": {
        "type": "default",
        "layout": "0,0,3,2",
        "color": "msx-glass",
        "imageFiller": "cover",
        "action": "image:context",
        "properties": {
            "image:options": true,
            "image:icon": "slider:options",
            "image:action": "slider:execute:slider:options:execute"
        }
    },
    "items": [{
            "titleFooter": "Image 1",
            "image": "http://msx.benzac.de/img/bg1.jpg",
            "imageLabel": "Image 1"
        }, {
            "titleFooter": "Image 2",
            "image": "http://msx.benzac.de/img/bg2.jpg",
            "imageLabel": "Image 2"
        }, {
            "titleFooter": "Image 3",
            "image": "http://msx.benzac.de/img/bg3.jpg",
            "imageLabel": "Image 3"
        }, {
            "titleFooter": "Image 4",
            "image": "http://msx.benzac.de/img/test.jpg",
            "imageLabel": "Image 4"
        }]
}
```

### Demo

- [Launch via App](https://msx.benzac.de/?start=content:https://msx.benzac.de/info/xp/data/hidden_feature_16.json)
- [Launch via Demo Page](https://msx.benzac.de/info/?start=content:https://msx.benzac.de/info/xp/data/hidden_feature_16.json)

## See Also

- [Cookbook → Interaction & UX](../../reference/cookbook.md#interaction--ux) — a real image list enabling this panel via `properties` (`image:options`, `image:icon`, `image:action`)
