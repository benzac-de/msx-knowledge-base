---
title: Release Notes
category: Overview
summary: Release notes and version history for Media Station X.
---

# Release Notes

These release notes contain major API extensions, fundamental UI/UX changes, and important bug fixes.

## 0.1.166 (May 8, 2026)

### UI/UX changes

- **Fire TV devices:** Added direct exit behavior for the application during back navigation

### API extensions

- Updated [URL Parameters](../experts-api/special/url-parameters.md): Updated `exit` parameter

## 0.1.165 (May 7, 2026)

- Minor bug fixes (no significant extensions/changes)

## 0.1.164 (March 30, 2026)

### UI/UX changes

- Improved user experience when setting up start parameters, along with clearer guidance for responsible and legal use
- Updated disclaimer prompt shown when a start parameter is entered for the first time
- Immersive mode is now enabled by default (**Settings** → **Immersive Mode**)
- **iOS devices**: Improved support for different screen resolutions

### API extensions

- Updated [Start Object](../main-api/start/start-object.md): Added `launcher` property
- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `navigationOffset` property
- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `navigationSpan` property
- Updated [Content Page Object](../main-api/content/content-page-object.md): Added `navigationOffset` property
- Updated [Content Page Object](../main-api/content/content-page-object.md): Added `navigationSpan` property
- Updated [Dictionary Structure](../experts-api/special/dictionary-structure.md)

## 0.1.163 (Jul 28, 2025)

### Bug fixes

- **iOS devices**: Fixed issues with internal web history that caused plugins to load into the main frame or the back button to be displayed incorrectly after opening external links
- Fixed a bug that caused panel content to become invisible under certain conditions (e.g. when performing many panel navigation actions quickly)

### UI/UX changes

- Added a disclaimer prompt shown when a start parameter is entered for the first time

### API extensions

- Updated [Dictionary Structure](../experts-api/special/dictionary-structure.md)

## 0.1.162 (May 5, 2025)

- Minor bug fixes (no significant extensions/changes)

## 0.1.161 (Feb 11, 2025)

### UI/UX changes

- Added more icons (updated to the final Material Icons version)

### API extensions

- Updated [Icons](../main-api/common/icons.md)
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `progress:display` property

## 0.1.160 (Nov 6, 2024)

### UI/UX changes

- Added screen saver (**Settings** → **Screen Saver**) that allows you to setup sleep and eject timeouts
- Added visual execution (**Settings** → **Visual Effects** → **Visual Execution**) that visualizes an execution (when pressing the **OK** button) with a small animation
- Added cleaning action (**Settings** → **Application** → **Cleaning**) to clear the cache (if the platform supports it) and reload the application
- Updated an on-screen remote control type (**Settings** → **Remote Control** → **Navigation Frame**) to make it more usable (i.e. smaller areas for arrows)
- Updated context menu to be more dynamic (e.g. the player button is disabled when no video/audio is loaded and shows the player label when it is active)
- Added support for custom dictionaries (it is possible to set a custom dictionary with an action that is independent of the start parameter) (see [Dictionary Structure](../experts-api/special/dictionary-structure.md))
- Improved user experience on AR/VR headsets (e.g. added drag & drop support)
- Slightly improved selection engine

### API extensions

- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `preselect` property
- Updated [Requests](../main-api/common/requests.md): Added `{LANGUAGE}` keyword
- Updated [Internal Actions](../experts-api/special/internal-actions.md)
- Updated [URL Parameters](../experts-api/special/url-parameters.md): Added `pointer` parameter
- Updated [URL Parameters](../experts-api/special/url-parameters.md): Updated `dictionary` parameter
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md): Added `info.language` property
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md): Added `info.application.settings.sleepTimeout` property
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md): Added `info.application.settings.ejectTimeout` property
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md): Added `info.application.settings.visualExecution` property
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md): Added `info.framework.settings.pointer` property
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md): Added `info.dictionary.language` property
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md): Added `dictionary.language` property
- Updated [Dictionary Structure](../experts-api/special/dictionary-structure.md)
- Updated [Dictionary Inline Expressions](../experts-api/hidden-features/dictionary-inline-expressions.md)
- Updated [Live Inline Expressions](../experts-api/live/live-inline-expressions.md) (see [Live Inline Expressions — Live Formatter ID](../experts-api/live/live-inline-expressions.md#live-formatter-id))
- Updated [Number Inline Expressions](../experts-api/hidden-features/number-inline-expressions.md)
- Updated [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md)

## 0.1.157 (Apr 26, 2024)

### API extensions

- Updated [URL Parameters](../experts-api/special/url-parameters.md): Added `caption` parameter
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md): Added `info.framework.settings.caption` property
- Updated [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md)

## 0.1.156 (Apr 18, 2024)

### Bug fixes

- **iOS devices**: Fixed a bug with the new hybrid storage that caused the application to freeze on startup under certain conditions (only a gray screen was visible)

### UI/UX changes

- Added rounded style (**Settings** → **Rounded Style**) that gives the entire application a new look and feel by rounding most of the corners
- Extended functionality for templated content items (i.e. inserts and breaks) (see [Advanced Templated Items](../experts-api/hidden-features/advanced-templated-items.md))
- Added content separation to prevent overlapping content (see [Advanced Text & Image](../experts-api/hidden-features/advanced-text-image.md))
- Improved reload/replace action behavior by preloading visible live objects (this should avoid flashing progress bars when reloading/replacing content)

### API extensions

- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `inserts` property
- Updated [Content Page Object](../main-api/content/content-page-object.md): Added `area` property
- Updated [Content Page Object](../main-api/content/content-page-object.md): Added `position` property
- Updated [Content Page Object](../main-api/content/content-page-object.md): Added `template` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `round` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `break` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `separation` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `progressBackColor` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Updated `layout` property
- Updated [Internal Actions](../experts-api/special/internal-actions.md)
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `info:round` property
- Updated [URL Parameters](../experts-api/special/url-parameters.md): Updated `init` parameter
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md)
- Updated [Dictionary Structure](../experts-api/special/dictionary-structure.md)
- Updated [Live Object](../experts-api/live/live-object.md): Added `preload` property
- Updated [Live Object](../experts-api/live/live-object.md): Added `progressBackColor` property
- Updated [Live Object](../experts-api/live/live-object.md): Updated `progress` property
- Updated [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md)

## 0.1.155 (Mar 11, 2024)

### Bug fixes

- **Android devices**: Fixed a bug where the system back button on some devices would only show the application exit screen but not perform the actual back action
- **iOS devices**: Fixed storage issues on some devices by adding a hybrid storage (for developers, please see [Tips & Tricks — Storage on iOS devices](./tips-tricks.md#storage-on-ios-devices))

### UI/UX changes

- **Android devices:** Added long press feature of the system back button to restart the application from any state
- **iOS devices:** Added long press feature of the overlay back button to restart the application from a third-party context
- Added export/import function for resume keys (e.g. to transfer resume keys to another device) (see [Attached Data Examples](../experts-api/special/attached-data-examples.md))
- Improved reload/replace action behavior by preloading visible images (this should avoid flashing images when reloading/replacing content)

### API extensions

- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `decompress` property
- Updated [Internal Actions](../experts-api/special/internal-actions.md)
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md)
- Updated [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md)

## 0.1.154 (Jan 29, 2024)

### Bug fixes

- **Samsung TVs**: Fixed a bug in the [Tizen Player](../experts-api/special/tizen-player.md) that caused an endless busy indicator when a video/audio was aborted (i.e. stop/eject was executed) while the video/audio was still loading
- **Samsung TVs**: Fixed a subtitle bug in the [Tizen Player](../experts-api/special/tizen-player.md) that caused subtitles to not disappear on some TV devices

### UI/UX changes

- Added settings validation panel to automatically reset settings if some important settings (e.g. resolution, scaling, input, etc.) have been set incorrectly

### API extensions

- Updated [Internal Actions](../experts-api/special/internal-actions.md)
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `info:headline` property
- Updated [Dictionary Structure](../experts-api/special/dictionary-structure.md)
- Updated [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md)

## 0.1.153 (Jul 20, 2023)

### UI/UX changes

- **Samsung TVs**: Added subtitle style (see [Tizen Player](../experts-api/special/tizen-player.md))
- **Samsung TVs**: Menu button is not handled anymore to allow opening the native TV menu
- **Universal Windows Platform (UWP) devices**: Added support for F5 (reload application) and F11 (toggle fullscreen)
- Added new on-screen input type (**Settings** → **Input Type** → **Move & Execute**) that is similar to **Drag & Drop** but allows faster executions of content items
- Added support button in about panel
- Deactivated link validations for links that are opened in a separate window or in an external browser (i.e. by using the action `link:window:{URL}`)
- Increased visibility of the content caption (for lists with overflowing items) by adding an underlay that darkens the lower part of the screen
- Increased visibility of the busy indicator and the on-screen remote control buttons on light backgrounds by adding slight shadows
- Extended player headline space if no extension label is used
- Extended resume feature (beside the progress, the duration and a possible context are now also stored for resumable content) (see [Live Examples](../experts-api/live/live-examples.md))
- Added emergency combination to reset settings (see [Tips & Tricks — Emergency Combination](./tips-tricks.md#emergency-combination))

### API extensions

- Updated [Menu Root Object](../main-api/menu/menu-root-object.md): Added `refocus` property
- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `refocus` property
- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `captionUnderlay` property
- Updated [Content Page Object](../main-api/content/content-page-object.md): Added `captionUnderlay` property
- Updated [Internal Actions](../experts-api/special/internal-actions.md)
- Updated [URL Parameters](../experts-api/special/url-parameters.md): Updated `input` parameter
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `resume:context` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Updated `button:{BUTTON_ID}:focus` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Updated `button:{BUTTON_ID}:key` property (it is now dynamic)
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md): Added `video.resume.context` property
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md): Added `video.resume.duration` property
- Updated [Dictionary Structure](../experts-api/special/dictionary-structure.md)
- Updated [Tizen Player](../experts-api/special/tizen-player.md): Added `tizen:subtitle:style:color` property
- Updated [Tizen Player](../experts-api/special/tizen-player.md): Added `tizen:subtitle:style:size` property
- Updated [Tizen Player](../experts-api/special/tizen-player.md): Added `tizen:subtitle:style:type` property
- Updated [Live Object](../experts-api/live/live-object.md): Updated `source` property
- Updated [Live Object](../experts-api/live/live-object.md): Updated `running` property
- Updated [Live Examples](../experts-api/live/live-examples.md)
- Updated [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md)

## 0.1.151 (Nov 25, 2022)

### UI/UX changes

- Added immersive mode (**Settings** → **Immersive Mode**) to improve the user experience for devices that do not have a 16:9 screen ratio (e.g. mobile and desktop devices)

### API extensions

- Added [Keep Ratio Keyword](../experts-api/hidden-features/keep-ratio.md)
- Updated [Internal Actions](../experts-api/special/internal-actions.md)
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md): Added `info.application.settings.immersiveMode` property
- Updated [Dictionary Structure](../experts-api/special/dictionary-structure.md)
- Updated [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md)

## 0.1.148 (Sep 6, 2022)

### UI/UX changes

- **iOS devices**: Increased screen usage for iPhone X+ devices
- Improved user experience for devices that do not have a 16:9 screen ratio (e.g. mobile and desktop devices)
- Added shadow frame to separate the application screen from the background
- Added dimmer to darken the application background (i.e. setting the gray background to black) if a video is playing (and zoom or scale settings are used)
- Added new on-screen remote control type (**Settings** → **Remote Control** → **Corner Controls**) for tablets and phones
- Added new on-screen remote control type (**Settings** → **Remote Control** → **Complex**) for testing purposes

### API extensions

- Updated [URL Parameters](../experts-api/special/url-parameters.md): Updated `remote` parameter
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `control:dim` property
- Updated [Dictionary Structure](../experts-api/special/dictionary-structure.md)
- Updated [Key Property](../experts-api/hidden-features/key-property.md)

## 0.1.146 (Jun 27, 2022)

### UI/UX changes

- Added more functions to customize a content item (e.g. set stamps, center content properties vertically, etc.) (see [Advanced Text & Image](../experts-api/hidden-features/advanced-text-image.md))
- Added function to set the size of the player info image area
- Increased the player info text area (from 10 to 16 lines) and added function to set the player info overlay
- The display of badges has been improved by truncating too long values

### API extensions

- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `centration` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `stamp` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `stampColor` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `imageBoundary` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Updated `alignment` property
- Updated [Internal Actions](../experts-api/special/internal-actions.md)
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `info:overlay` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `info:size` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `progress:duration` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `progress:position` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `trigger:ending` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `trigger:-{SECONDS}` property
- Updated [Live Object](../experts-api/live/live-object.md): Added `source` property
- Updated [Live Object](../experts-api/live/live-object.md): Added `stamp` property
- Updated [Live Object](../experts-api/live/live-object.md): Added `stampColor` property
- Updated [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md)

## 0.1.145 (Feb 25, 2022)

### API extensions

- **Samsung TVs**: Improved subtitle support (see [Tizen Player](../experts-api/special/tizen-player.md))
- **Samsung TVs**: Added network connection handler
- Added slideshow image options to allow rotations of the current slideshow image (see [Image Options](../experts-api/hidden-features/image-options.md))
- Added more slideshow functions (e.g. to set label, color, and extension of the current slideshow image at runtime)
- Added captions to the dictionary (see [Dictionary Structure](../experts-api/special/dictionary-structure.md))
- Added [Image Options](../experts-api/hidden-features/image-options.md)
- Updated [Inline Expressions](../main-api/common/inline-expressions.md)
- Updated [Internal Actions](../experts-api/special/internal-actions.md)
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `control:retune` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `image:icon` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `image:options` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `image:rotation:trigger` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `image:rotation:value` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Updated `image:action` property (it is now dynamic)
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Updated `image:extension` property (it is now dynamic)
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Updated `control:action` property (it is now dynamic)
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md): Added `slider.info.rotation` property
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md): Added `slider.info.customLabel` property
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md): Added `slider.info.customColor` property
- Updated [Dictionary Structure](../experts-api/special/dictionary-structure.md)
- Updated [Tizen Player](../experts-api/special/tizen-player.md): Added `tizen:subtitle:hidden` property
- Updated [Tizen Player](../experts-api/special/tizen-player.md): Added `tizen:subtitle:type` property
- Updated [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md)
- Updated [Resolve Action](../experts-api/hidden-features/resolve-action.md)

## 0.1.144 (Jan 11, 2022)

### Bug fixes

- Performing stop/eject on the HTML5 player now unloads the source (instead of just pausing it)

### API extensions

- Added menu/content/panel replace actions to allow replacements of current data at runtime (see [Replace Action](../experts-api/hidden-features/replace-action.md))
- Added [Replace Action](../experts-api/hidden-features/replace-action.md)
- Updated [Internal Actions](../experts-api/special/internal-actions.md)
- Updated [Dictionary Structure](../experts-api/special/dictionary-structure.md)
- Updated [Focus Separator](../experts-api/hidden-features/focus-separator.md)

## 0.1.143 (Dec 13, 2021)

### Bug fixes

- **iOS devices**: Fixed storage issues within plugins (see [Tips & Tricks — Storage on iOS devices](./tips-tricks.md#storage-on-ios-devices))

### API extensions

- Updated [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md)

## 0.1.142 (Nov 17, 2021)

### UI/UX changes

- Added more functions to customize the menu (i.e. display the menu in different styles, set the menu content background to transparent, and set the line colors of menu item separators) (see [Menu Examples](../main-api/menu/menu-examples.md))

### API extensions

- Updated [Menu Root Object](../main-api/menu/menu-root-object.md): Added `ready` property
- Updated [Menu Root Object](../main-api/menu/menu-root-object.md): Added `style` property
- Updated [Menu Root Object](../main-api/menu/menu-root-object.md): Added `transparent` property
- Updated [Menu Item Object](../main-api/menu/menu-item-object.md): Added `transparent` property
- Updated [Menu Item Object](../main-api/menu/menu-item-object.md): Added `lineColor` property
- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `ready` property
- Updated [Content Root Object](../main-api/content/content-root-object.md): Updated `transparent` property
- Updated [Content Page Object](../main-api/content/content-page-object.md): Updated `transparent` property
- Updated [Requests](../main-api/common/requests.md): Added `{ID}` keyword
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `control:transparent` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `control:reuse` property
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md): Added `info.id` property
- Updated [Dictionary Structure](../experts-api/special/dictionary-structure.md)
- Updated [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md)

## 0.1.141 (Oct 7, 2021)

### API extensions

- **Samsung TVs**: Extended action to launch applications (it is now possible to add launch data to the `system:tizen:launch`/`system:tizen:launch:{APP_ID}` action)
- **Samsung TVs**: Improved subtitle support (see [Tizen Player](../experts-api/special/tizen-player.md))
- **LG TVs**: Extended action to launch applications (it is now possible to add launch data to the `system:lg:launch:{APP_ID}` action)
- Updated [Internal Actions](../experts-api/special/internal-actions.md)
- Updated [Tizen Player](../experts-api/special/tizen-player.md): Added `tizen:subtitle:delay` property
- Updated [Tizen Player](../experts-api/special/tizen-player.md): Added `tizen:subtitle:silent` property
- Updated [Tizen Player](../experts-api/special/tizen-player.md): Added `tizen:subtitle:url` property

## 0.1.140 (Aug 26, 2021)

### API extensions

- Extended video events for plugins (added `data.data` property for specific events that contains the active video data before the event occurred)
- Updated [Internal Actions](../experts-api/special/internal-actions.md)
- Updated [URL Parameters](../experts-api/special/url-parameters.md): Added `secure` parameter
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `control:action` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `control:return` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Updated `button:{BUTTON_ID}:action` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Updated `button:{BUTTON_ID}:icon` property
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md): Added `info.framework.secure` property
- Updated [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md)

## 0.1.138 (Jun 15, 2021)

### UI/UX changes

- Slightly speed up menu navigation

## 0.1.137 (May 26, 2021)

### UI/UX changes

- Improved player behavior (if the left/right key is pressed while the video/audio is in foreground, the player progress marker is focused; if the OK key is pressed while the video/audio is in foreground, the player default button is focused; by default, the last focused player button remains the focus)
- Updated application font (updated to the latest Roboto font version)
- Added more icons (updated to the latest Material Icons version)

### API extensions

- Updated [Icons](../main-api/common/icons.md)

## 0.1.136 (May 7, 2021)

### UI/UX changes

- Added new hover effect (**Settings** → **Hover Effect** → **Preselect**), which directly applies the selection when the cursor is over an item
- Added long press feature (for pointer and touch devices) to select an item without executing it (e.g. to access additional options)

### API extensions

- **Android/iOS devices**: Extended action to launch applications (it is now possible to add launch data to the `system:tvx:launch`/`system:tvx:launch:{APP_ID}` action and to handle the result)
- Added [Android Player](../experts-api/special/android-player.md)
- Added [NetCast Menu](../experts-api/special/netcast-menu.md)
- Updated [Internal Actions](../experts-api/special/internal-actions.md)
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Updated `label:duration` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Updated `label:position` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Updated `label:speed` property
- Updated [Dictionary Structure](../experts-api/special/dictionary-structure.md)

## 0.1.134 (Apr 16, 2021)

### UI/UX changes

- Improved navigation management
- Hide cursor on keyboard input
- Added hover effect for video/audio progress bar
- Added version suffix to make versions easier to identify
- The display of some translated labels has been improved by truncating too long values

### API extensions

- Updated [Menu Root Object](../main-api/menu/menu-root-object.md): Added `reference` property
- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `reference` property
- Updated [Internal Actions](../experts-api/special/internal-actions.md)
- Updated [URL Parameters](../experts-api/special/url-parameters.md): Added `welcome` parameter
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `trigger:active` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `trigger:inactive` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `trigger:player` property
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md): Added `info.framework.suffix` property
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md): Added `info.application.suffix` property
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md): Updated `info.content.state` property
- Updated [Dictionary Structure](../experts-api/special/dictionary-structure.md)
- Updated [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md)

## 0.1.132 (Mar 18, 2021)

### UI/UX changes

- Added shortcut feature for option items and player buttons

### API extensions

- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `shortcut` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `button:{BUTTON_ID}:key` property
- Added [Option Shortcut](../experts-api/hidden-features/option-shortcut.md)

## 0.1.131 (Mar 9, 2021)

### API extensions

- Updated [URL Parameters](../experts-api/special/url-parameters.md): Added `leave` parameter
- Updated [URL Parameters](../experts-api/special/url-parameters.md): Updated `playback` parameter
- Updated [Dictionary Structure](../experts-api/special/dictionary-structure.md)

## 0.1.130 (Mar 5, 2021)

### UI/UX changes

- **Universal Windows Platform (UWP) devices**: Improved user experience on Xbox consoles
- Added more functions to customize the player (i.e. show video/audio related info image and/or text and hide/override default player buttons) (see [Extended Properties](../experts-api/special/extended-properties.md))
- Added more functions to customize the menu (i.e. set custom images instead of icons and change logo size) (see [Menu Root Object](../main-api/menu/menu-root-object.md) and [Menu Item Object](../main-api/menu/menu-item-object.md))
- Slightly improved rendering performance

### API extensions

- Updated [Menu Root Object](../main-api/menu/menu-root-object.md): Added `logoSize` property
- Updated [Menu Item Object](../main-api/menu/menu-item-object.md): Added `image` property
- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `caption` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `caption` property
- Updated [Actions](../main-api/common/actions.md)
- Updated [Internal Actions](../experts-api/special/internal-actions.md)
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `button:{BUTTON_ID}:display` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `control:type` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `info:image` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `info:text` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `label:speed:{LABEL}` property
- Updated [Dictionary Structure](../experts-api/special/dictionary-structure.md)
- Updated [Selection Examples](../experts-api/selection/selection-examples.md)
- Updated [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md)
- Updated [Page Action](../experts-api/hidden-features/page-action.md)

## 0.1.128 (Feb 17, 2021)

### UI/UX changes

- **Samsung TVs**: Default player for Samsung TVs (2016+ models) is now the Tizen player to support 4k/8k video formats, buffer settings, and more (see [Tizen Player](../experts-api/special/tizen-player.md))
- AFK delay (time to hide the player, notifications, etc. when there are no interactions) has been slightly decreased
- The display of the start parameters has been improved by truncating too long values
- The application suspend/resume feature has been improved

### API extensions

- Added [Tizen Player](../experts-api/special/tizen-player.md)
- Updated [Requests](../main-api/common/requests.md): Added keyword support
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `truncation` property
- Updated [Internal Actions](../experts-api/special/internal-actions.md)
- Updated [URL Parameters](../experts-api/special/url-parameters.md): Added `suspend` parameter
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `video:state` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `video:position` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `video:duration` property
- Updated [Extended Properties](../experts-api/special/extended-properties.md): Added `video:speed` property
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md): Added `info.framework.suspend` property
- Updated [Dictionary Structure](../experts-api/special/dictionary-structure.md)
- Updated [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md)

## 0.1.127 (Jan 4, 2021)

### UI/UX changes

- **Samsung TVs**: Exit button is not handled anymore to use the native application exit behavior

## 0.1.125 (Nov 23, 2020)

### API extensions

- Updated [Internal Actions](../experts-api/special/internal-actions.md)
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md)

### Bug fixes

- **Universal Windows Platform (UWP) devices**: Updated application capabilities to allow private network connections

## 0.1.123 (Nov 13, 2020)

### API extensions

- Added [Compress Property](../experts-api/hidden-features/compress-property.md)
- Updated [Dictionary Inline Expressions](../experts-api/hidden-features/dictionary-inline-expressions.md)
- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `compress` property (see [Compress Property](../experts-api/hidden-features/compress-property.md))
- Updated [Content Page Object](../main-api/content/content-page-object.md): Added `compress` property (see [Compress Property](../experts-api/hidden-features/compress-property.md))
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `compress` property (see [Compress Property](../experts-api/hidden-features/compress-property.md))

## 0.1.120 (Oct 20, 2020)

### API extensions

- Added [Dictionary Structure](../experts-api/special/dictionary-structure.md)
- Added [Dictionary Inline Expressions](../experts-api/hidden-features/dictionary-inline-expressions.md)
- Updated [Execute Property](../experts-api/hidden-features/execute-property.md)
- Updated [Focus Separator](../experts-api/hidden-features/focus-separator.md)
- Updated [Menu Root Object](../main-api/menu/menu-root-object.md): Added `flag` property
- Updated [Menu Root Object](../main-api/menu/menu-root-object.md): Added `restore` property
- Updated [Menu Root Object](../main-api/menu/menu-root-object.md): Added `dictionary` property (see [Dictionary Structure](../experts-api/special/dictionary-structure.md))
- Updated [Menu Root Object](../main-api/menu/menu-root-object.md): Added `options` property
- Updated [Menu Item Object](../main-api/menu/menu-item-object.md): Added `execute` property (see [Execute Property](../experts-api/hidden-features/execute-property.md))
- Updated [Menu Item Object](../main-api/menu/menu-item-object.md): Added `options` property
- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `flag` property
- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `restore` property
- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `dictionary` property
- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `transparent` property
- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `dictionary` property (see [Dictionary Structure](../experts-api/special/dictionary-structure.md))
- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `options` property
- Updated [Content Page Object](../main-api/content/content-page-object.md): Added `transparent` property
- Updated [Content Page Object](../main-api/content/content-page-object.md): Added `options` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `options` property
- Updated [URL Parameters](../experts-api/special/url-parameters.md)
- Updated [Attached Data Examples](../experts-api/special/attached-data-examples.md)

## 0.1.119 (Sep 28, 2020)

### UI/UX changes

- **Android tablets/phones**: Enabled immersive mode (full screen experience)

## 0.1.118 (Sep 8, 2020)

### UI/UX changes

- Updated welcome pages (removed unnecessary example items)

## 0.1.117 (Sep 4, 2020)

### API extensions

- Updated [Start Object](../main-api/start/start-object.md): Added `welcome` property

## 0.1.116 (Sep 2, 2020)

### UI/UX changes

- **Android devices**: Added AndroidX support

## 0.1.114 (Jun 9, 2020)

- Minor bug fixes (no significant extensions/changes)

## 0.1.112 (May 19, 2020)

### API extensions

- Added [Page Action](../experts-api/hidden-features/page-action.md)
- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `underlay` property
- Updated [Content Page Object](../main-api/content/content-page-object.md): Added `action` property (see [Page Action](../experts-api/hidden-features/page-action.md))
- Updated [Content Page Object](../main-api/content/content-page-object.md): Added `data` property (see [Page Action](../experts-api/hidden-features/page-action.md))
- Updated [Actions](../main-api/common/actions.md): Updated extended actions (see [Actions — Extended Actions](../main-api/common/actions.md#extended-actions))

## 0.1.111 (Apr 29, 2020)

### API extensions

- Updated [Selection Object](../experts-api/selection/selection-object.md)
- Updated [Extended Properties](../experts-api/special/extended-properties.md)

## 0.1.110 (Mar 18, 2020)

### API extensions

- Added [Selection Object](../experts-api/selection/selection-object.md)
- Added [Large Offsets](../experts-api/hidden-features/large-offsets.md)
- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `overlay` property
- Updated [Content Page Object](../main-api/content/content-page-object.md): Added `important` property
- Updated [Content Page Object](../main-api/content/content-page-object.md): Updated `offset` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `area` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `selection` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Updated `offset` property
- Updated [Inline Expressions](../main-api/common/inline-expressions.md): Updated `{context:{PROPERTY}}` expression
- Updated [Actions](../main-api/common/actions.md): Updated extended actions (see [Actions — Extended Actions](../main-api/common/actions.md#extended-actions))

## 0.1.109 (Feb 21, 2020)

- Minor bug fixes (no significant extensions/changes)

## 0.1.107 (Jan 22, 2020)

- Minor bug fixes (no significant extensions/changes)

## 0.1.106 (Jan 17, 2020)

- Minor bug fixes (no significant extensions/changes)

## 0.1.103 (Jan 7, 2020)

### UI/UX changes

- Improved background mode
- Improved navigation engine

## 0.1.102 (Dec 5, 2019)

### API extensions

- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `wrap` property
- Updated [Content Page Object](../main-api/content/content-page-object.md): Added `wrap` property

## 0.1.100 (Nov 8, 2019)

### UI/UX changes

- Updated playback speed values

## 0.1.97 (Oct 17, 2019)

### API extensions

- Added [Key Property](../experts-api/hidden-features/key-property.md)
- Updated [Start Object](../main-api/start/start-object.md): Updated `parameter` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `key` property (see [Key Property](../experts-api/hidden-features/key-property.md))
- Updated [Content Item Object](../main-api/content/content-item-object.md): Updated `alignment` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Updated `imageOverlay` property

## 0.1.96 (Sep 24, 2019)

### UI/UX changes

- Improved video/audio error handling

## 0.1.95 (Sep 17, 2019)

### UI/UX changes

- Performance improvements for parsing and converting JSON data

## 0.1.92 (Sep 9, 2019)

### API extensions

- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `preload` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `group` property

## 0.1.91 (Aug 21, 2019)

### API extensions

- Added [Number Inline Expressions](../experts-api/hidden-features/number-inline-expressions.md)
- Added [Color Inline Expressions](../experts-api/hidden-features/color-inline-expressions.md)

## 0.1.90 (Aug 7, 2019)

### UI/UX changes

- Improved navigation and animation performance

## 0.1.88 (Jun 25, 2019)

### API extensions

- Updated [Setup Start Parameter](../main-api/start/setup-start-parameter.md)

### UI/UX changes

- Improved HTTPS support when setting up the start parameter

## 0.1.87 (Jun 11, 2019)

- Minor bug fixes (no significant extensions/changes)

## 0.1.85 (May 16, 2019)

- Minor bug fixes (no significant extensions/changes)

## 0.1.82 (May 3, 2019)

### API extensions

- Added [Interaction Plugin](../experts-api/plugins/interaction-plugin.md)
- Updated [Menu Root Object](../main-api/menu/menu-root-object.md): Added `extension` property
- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `extension` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `alignment` property

## 0.1.80 (Apr 16, 2019)

### UI/UX changes

- Improved image slider

## 0.1.78 (Mar 11, 2019)

- Minor bug fixes (no significant extensions/changes)

## 0.1.74 (Feb 28, 2019)

### API extensions

- Added [Hide Images](../experts-api/hidden-features/hide-images.md)
- Updated [Video/Audio Plugin](../experts-api/plugins/video-audio-plugin.md)
- Updated [Live Inline Expressions](../experts-api/live/live-inline-expressions.md) (see [Live Inline Expressions — Live Duration Text](../experts-api/live/live-inline-expressions.md#live-duration-text))

## 0.1.70 (Feb 5, 2019)

### API extensions

- Added [Live Object](../experts-api/live/live-object.md)
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `progress` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `progressColor` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `wrapperColor` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `live` property (see [Live Object](../experts-api/live/live-object.md))

## 0.1.68 (Jan 9, 2019)

- Minor bug fixes (no significant extensions/changes)

## 0.1.66 (Nov 29, 2018)

### Bug fixes

- **Android devices**: Updated network configuration to allow insecure connections (i.e. via `http://`)

## 0.1.65 (Nov 22, 2018)

### API extensions

- Updated [Start Object](../main-api/start/start-object.md): Updated `parameter` property

## 0.1.64 (Nov 6, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.60 (Oct 24, 2018)

### API extensions

- Updated [Menu Root Object](../main-api/menu/menu-root-object.md): Added `logo` property

### UI/UX changes

- Improved media player

## 0.1.58 (Sep 26, 2018)

### API extensions

- Added [Extended Properties](../experts-api/special/extended-properties.md)
- Updated [Menu Root Object](../main-api/menu/menu-root-object.md): Added `cache` property
- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `cache` property
- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `important` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `imagePreload` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `properties` property (see [Extended Properties](../experts-api/special/extended-properties.md))
- Updated [Actions](../main-api/common/actions.md): Added extended actions (see [Actions — Extended Actions](../main-api/common/actions.md#extended-actions))

## 0.1.54 (Aug 29, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.53 (Aug 24, 2018)

### API extensions

- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `header` property
- Updated [Content Root Object](../main-api/content/content-root-object.md): Added `footer` property

### UI/UX changes

- Improved error handling

## 0.1.51 (Aug 3, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.48 (Jun 29, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.46 (Jun 22, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.45 (Jun 18, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.44 (Jun 6, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.43 (May 24, 2018)

### UI/UX changes

- Added link to examples on welcome pages

## 0.1.41 (May 3, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.40 (Apr 20, 2018)

### API extensions

- Added [Video/Audio Plugin](../experts-api/plugins/video-audio-plugin.md)
- Added [YouTube, Vimeo & Co.](../extended-api/youtube-vimeo-co.md)
- Updated [Actions](../main-api/common/actions.md): Added `video:plugin:{URL}` action (see [YouTube, Vimeo & Co.](../extended-api/youtube-vimeo-co.md))
- Updated [Actions](../main-api/common/actions.md): Added `audio:plugin:{URL}` action (see [YouTube, Vimeo & Co.](../extended-api/youtube-vimeo-co.md))

## 0.1.36 (Mar 23, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.34 (Mar 2, 2018)

### UI/UX changes

- Improved slideshow performance

## 0.1.33 (Feb 28, 2018)

### UI/UX changes

- Improved slideshow performance

## 0.1.30 (Feb 21, 2018)

### API extensions

- Updated [Actions](../main-api/common/actions.md): Added `start` action (see [Actions — Basic Actions](../main-api/common/actions.md#basic-actions))
- Updated [Actions](../main-api/common/actions.md): Added HbbTV actions (see [Actions — HbbTV Actions](../main-api/common/actions.md#hbbtv-actions))
- Updated [Actions](../main-api/common/actions.md): Added server actions (see [Actions — Server Actions](../main-api/common/actions.md#server-actions))

## 0.1.28 (Feb 13, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.27 (Feb 7, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.26 (Feb 2, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.25 (Feb 1, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.24 (Jan 31, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.20 (Jan 22, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.14 (Jan 16, 2018)

### UI/UX changes

- Updated image slider

## 0.1.13 (Jan 15, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.12 (Jan 12, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.11 (Jan 11, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.9 (Jan 10, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.7 (Jan 8, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.6 (Jan 6, 2018)

### API extensions

- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `imageColor` property
- Updated [Content Item Object](../main-api/content/content-item-object.md): Added `imageScreenFiller` property

## 0.1.5 (Jan 5, 2018)

- Minor bug fixes (no significant extensions/changes)

## 0.1.0 (Dec 18, 2017)

- Initial release

## See Also

- [Versioning → Version Baseline: 0.1.0](../reference/versioning.md#version-baseline-010) — curated timeline of feature-introduction milestones distilled from this full changelog
