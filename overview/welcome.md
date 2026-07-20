---
title: Welcome to Media Station X - Wiki
category: Overview
summary: Welcome page and API taxonomy overview for Media Station X.
---

# Welcome to Media Station X - Wiki

**Welcome to Media Station X - Wiki**

These wiki pages contain all information about the various JSON structures with examples and screenshots as well as descriptions and source code examples of the different plugin possibilities. In other words, everything you want to know about the Media Station X API can be found here.

For more information about the Media Station X project, please visit: [https://msx.benzac.de/info/](https://msx.benzac.de/info/).

**Note: The experts API are designed for experts who have well experiences with the main API. If you are new to Media Station X, please familiarize yourself with the main API before using the experts one.**

## Main API

### Start API

The start object is used to setup the start parameter. The start parameter specifies which menu or content is loaded at startup.

- [Start Object](../main-api/start/start-object.md)
- [Setup Precondition](../main-api/start/setup-precondition.md)
- [Setup Start Parameter](../main-api/start/setup-start-parameter.md)

### Menu API

Menu objects are used to group different content objects. They are displayed as lists (vertical direction) on the left side of the screen.

- [Menu Root Object](../main-api/menu/menu-root-object.md)
- [Menu Item Object](../main-api/menu/menu-item-object.md)
- [Menu Examples](../main-api/menu/menu-examples.md)

### Content API

Content objects are used to display content items on a grid. They can be displayed as pages (horizontal direction) or lists (vertical direction).

- [Content Root Object](../main-api/content/content-root-object.md)
- [Content Page Object](../main-api/content/content-page-object.md)
- [Content Item Object](../main-api/content/content-item-object.md)
- [Content Examples](../main-api/content/content-examples.md)
- [Content Guide](../main-api/content/content-guide.md)

### Common API

The common API is used in menu and content objects. It consists of requests, responses, colors, icons, inline expressions, and actions.

- [Requests](../main-api/common/requests.md)
- [Responses](../main-api/common/responses.md)
- [Colors](../main-api/common/colors.md)
- [Icons](../main-api/common/icons.md)
- [Inline Expressions](../main-api/common/inline-expressions.md)
- [Actions](../main-api/common/actions.md)

### Extended API

The extended API describes the integration of YouTube, Vimeo & Co., the conversion of M3U/PLS files, and the loading of MRSS feeds.

- [YouTube, Vimeo & Co.](../extended-api/youtube-vimeo-co.md)
- [M3U/PLS Files](../extended-api/m3u-pls-files.md)
- [MRSS Feeds](../extended-api/mrss-feeds.md)

## Experts API

### Special API

The special API can be used for special use cases. It consists of internal actions, URL parameters, extended properties, attached data examples, the dictionary structure, the Tizen player, the NetCast menu, and the Android player.

- [Internal Actions](../experts-api/special/internal-actions.md)
- [URL Parameters](../experts-api/special/url-parameters.md)
- [Extended Properties](../experts-api/special/extended-properties.md)
- [Attached Data Examples](../experts-api/special/attached-data-examples.md)
- [Dictionary Structure](../experts-api/special/dictionary-structure.md)
- [Tizen Player](../experts-api/special/tizen-player.md)
- [NetCast Menu](../experts-api/special/netcast-menu.md)
- [Android Player](../experts-api/special/android-player.md)

### Live API

The live API can be used to periodically update content items (e.g. with live data, playback progress, etc.). It also describes the live inline expressions that are also used in other properties (e.g. in the `extension` property).

- [Live Object](../experts-api/live/live-object.md)
- [Live Inline Expressions](../experts-api/live/live-inline-expressions.md)
- [Live Examples](../experts-api/live/live-examples.md)

### Selection API

The selection API can be used to specify the behavior of a content item if it is selected (i.e. if it has the focus).

- [Selection Object](../experts-api/selection/selection-object.md)
- [Selection Examples](../experts-api/selection/selection-examples.md)

### Plugin API

The plugin API can be used to create your own video/audio or interaction plugin.

- [Video/Audio Plugin](../experts-api/plugins/video-audio-plugin.md)
- [Interaction Plugin](../experts-api/plugins/interaction-plugin.md)
- [Image Plugin](../experts-api/plugins/image-plugin.md)
- [HTML5X Plugin](../experts-api/plugins/html5x-plugin.md)
- [Panorama Plugin](../experts-api/plugins/panorama-plugin.md)
- [Background Plugin](../experts-api/plugins/background-plugin.md)
- [Backdrop Plugin](../experts-api/plugins/backdrop-plugin.md)
- [Ad Plugin](../experts-api/plugins/ad-plugin.md)
- [Android Plugin](../experts-api/plugins/android-plugin.md)
- [Play Plugin](../experts-api/plugins/play-plugin.md)
- [IMA Plugin](../experts-api/plugins/ima-plugin.md)
- [Paging Plugin](../experts-api/plugins/paging-plugin.md)
- [Input Plugin](../experts-api/plugins/input-plugin.md)
- [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md)

### Hidden Features

Hidden features are used in menu and content objects and can be used for special use cases. They are called "hidden" because they were not intended to be official features (to keep the menu and content API clear and compact).

- [Start Action](../experts-api/hidden-features/start-action.md)
- [Settings Menu Item](../experts-api/hidden-features/settings-menu-item.md)
- [Execute Property](../experts-api/hidden-features/execute-property.md)
- [Focus Separator](../experts-api/hidden-features/focus-separator.md)
- [Hide Images](../experts-api/hidden-features/hide-images.md)
- [Number Inline Expressions](../experts-api/hidden-features/number-inline-expressions.md)
- [Color Inline Expressions](../experts-api/hidden-features/color-inline-expressions.md)
- [Key Property](../experts-api/hidden-features/key-property.md)
- [Large Offsets](../experts-api/hidden-features/large-offsets.md)
- [Page Action](../experts-api/hidden-features/page-action.md)
- [Dictionary Inline Expressions](../experts-api/hidden-features/dictionary-inline-expressions.md)
- [Compress Property](../experts-api/hidden-features/compress-property.md)
- [Resolve Action](../experts-api/hidden-features/resolve-action.md)
- [Option Shortcut](../experts-api/hidden-features/option-shortcut.md)
- [Replace Action](../experts-api/hidden-features/replace-action.md)
- [Image Options](../experts-api/hidden-features/image-options.md)
- [Advanced Text & Image](../experts-api/hidden-features/advanced-text-image.md)
- [Keep Ratio Keyword](../experts-api/hidden-features/keep-ratio.md)
- [Advanced Templated Items](../experts-api/hidden-features/advanced-templated-items.md)

### Benchmark Tests

Benchmark tests have been developed to check the performance and capabilities of a TV device.

- [Renderer Plugin](../experts-api/benchmark/renderer-plugin.md)
- [Drawing Plugin](../experts-api/benchmark/drawing-plugin.md)
- [Particles Plugin](../experts-api/benchmark/particles-plugin.md)

## Related Links

- [Media Station X - App](https://msx.benzac.de/)
- [Media Station X - Info](https://msx.benzac.de/info/)
- [Media Station X - Icons](https://msx.benzac.de/info/icons.html)
- [Media Station X - Actions](https://msx.benzac.de/info/xp/actions.html)
- [Media Station X - M3U](https://msx.benzac.de/info/m3u.html)
- [Media Station X - MRSS](https://msx.benzac.de/info/mrss.html)
- [YouTube Channel](https://www.youtube.com/@benzac-de)
- [Facebook Group](https://www.facebook.com/benzac.de)
- [GitHub Projects](https://github.com/benzac-de)
- [Support](https://msx.benzac.de/support/)
