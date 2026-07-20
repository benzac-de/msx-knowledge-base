---
title: In-App Settings Reference
category: Reference
summary: Consolidated reference for MSX's native, end-user-facing Settings screen — every panel, its exact menu path, allowed values and default, how to reach or set it from JSON, and how a plugin reads it at runtime.
---

# In-App Settings Reference

Media Station X has its own native **Settings** screen — a scene inside the app itself, separate from the JSON content a page author builds. It draws on:

- [Internal Actions](../experts-api/special/internal-actions.md) (`settings:` Actions section) — the list of Settings panels and their value enums, each reachable as a JSON action.
- [URL Parameters](../experts-api/special/url-parameters.md) — mainly the same underlying [TVX Framework](../overview/about.md#technology-stack) settings, but as launch-time overrides.
- [Release Notes](../overview/release-notes.md) — since-versions and menu paths for the settings called out by name when added.
- [Attached Data Examples](../experts-api/special/attached-data-examples.md) / [Type Definitions](type-definitions.md) (`MSXAttachedApplicationSettings`, `MSXAttachedFrameworkSettings`) — the read-only structural view of current values.

---

## 1. How this screen relates to JSON building

Although the Settings screen itself isn't JSON, it is fully reachable **from** JSON and from plugins — and it is itself built out of ordinary MSX content items:

- **It's built using MSX's own object model.** The Settings scene's two pages are literal Content Page Objects, made of items with a `label`, an `icon`, and an `action` — the same object shapes documented in [Content Item Object](../main-api/content/content-item-object.md). Some items are conditionally shown/enabled (e.g. an item can stay hidden until relevant, or compute its own `enable`/`badge`/`label` at render time).
- **Entry point in a menu:** setting a menu item's `type` to `"settings"` embeds the entry point for this whole screen into your own menu (since `0.1.0`). See [Settings Menu Item](../experts-api/hidden-features/settings-menu-item.md).
- **Open it from an action:** `settings` (toggles the whole Settings scene) and `settings:{PANEL}` (opens one specific panel, e.g. `settings:rounded_style`) are internal actions (`0.1.160+`). See [Internal Actions → settings: Actions](../experts-api/special/internal-actions.md#settings-actions).
- **Reachable anytime — except when you leave the MSX context.** Pressing the menu key (physical remote or on-screen button, see the **Menu Button** setting in [Application Settings](#3-application-settings)) runs the `menu` internal action, which opens the **Context Menu** — a panel with (typically) five entries: **Home** (`home` — loads the configured start page), **Player** (`player` — opens the player if something is playing in the background; since `0.1.160` this entry also shows what's currently playing, or appears grayed out if nothing is loaded), **Volume** (`volume`), **Settings** (`settings`), and **Exit** (`exit`). Selecting **Settings** there opens the Settings screen. This whole chain is available at any point while you're inside MSX, but that stops being true the moment JSON opens an external page in-app via `link:{URL}` (e.g. an HTML5 game or a third-party site) — you're no longer in the MSX context, so Settings (and the rest of the app) isn't reachable until you get back. Getting back depends entirely on that external page implementing its own exit behavior; many TV apps/games don't, leaving the user stuck with no way back short of restarting the app (not possible on every platform) or power-cycling the TV. This is exactly why **Validate Links** exists (see [Application Settings](#3-application-settings)) and why [Tips & Tricks — External HTML5 Games/Apps](../overview/tips-tricks.md#external-html5-gamesapps) recommends the loaded page implement its own exit button. On Android and iOS specifically, holding the back button for a few seconds forces a hard restart of MSX back to a known state (added `0.1.155`) — see [Release Notes](../overview/release-notes.md).
- **Set a value directly from an action:** most panels also accept `settings:{PANEL}:{VALUE}` to set the value without showing the panel UI at all (e.g. `settings:rounded_style:1`) — see the tables below for each panel's accepted values.
- **Read the current values:** a plugin reads the live values via `info.application.settings.*` / `info.framework.settings.*` (see [Attached Data Examples](../experts-api/special/attached-data-examples.md), `execute:info:extended:{URL}`) or `onValidatedSettings()` / `validateSettings()` called on its own **video/interaction plugin instance** (`TVXVideoPlugin`/`TVXInteractionPlugin` — see [Plugin API Reference](../experts-api/plugins/plugin-api-reference.md)), and is notified of live changes via the `settings:*` events in `handleEvent` (see [Plugin Events Reference](../experts-api/plugins/plugin-events-reference.md)). `TVXPluginTools` has its own, same-named `validateSettings()` (plus `invalidateSettings()`), but that pair is what `TVXVideoPlugin`/`TVXInteractionPlugin` use *internally* to actually populate the settings data — a plugin author calls `onValidatedSettings()`/`validateSettings()` on the video/interaction plugin instance, not on `TVXPluginTools` directly. The [Cookbook's Immersive Mode deep dive](cookbook.md#deep-dive--building-an-interaction-plugin-plugin_test_2) is a worked example of exactly this read/react pattern for `immersiveMode`.
- **Reset if misconfigured:** since `0.1.154`, a settings-validation panel (`settings:validate`) guards exactly six settings — **Transform, Layout, Scale, Zoom, Remote, Input** — because a bad change to any of them could make the whole UI unusable: it shows a live 30-second countdown with a **Confirm** button and a **Reset** button that is focused by default; if the countdown reaches zero without confirmation, the countdown's over action resets the settings automatically — so an unresponsive/misconfigured screen defaults to safety rather than to keeping the change. Independently, since `0.1.153`, the user can force a reset at any time with the **Emergency Combination** — enter `911` or `↑↑↓↓←→←→` followed by `OK`. See [Tips & Tricks — Emergency Combination](../overview/tips-tricks.md#emergency-combination).

---

## 2. Settings screen map

The Settings scene is two content pages. This is the actual navigation tree, reconstructed from the scene's own item list (labels and `action` strings) — not every entry sets a value; several just navigate to a sub-panel or another scene.

**Page 1**

| Item | Action | Notes |
|---|---|---|
| **Start Parameter** | `settings:start` | Only enabled if the start parameter is changeable. |
| **About** | `settings:about` | Not the [About](../overview/about.md) project page — an app/content info panel, detailed [below](#5-other-settings-scene-entries-and-safety-mechanisms). |
| **Volume** | `volume` | Only enabled if the platform's `volume` setting is not `0`/Off; otherwise shows a "TV Control" badge (volume is handled by the TV/device instead). |
| **Application** | `dialog:application` | An application-actions panel (Reload/Restart/Cleaning always shown; Fullscreen/Leave/Exit shown only if the corresponding Framework Setting allows it), detailed [below](#5-other-settings-scene-entries-and-safety-mechanisms). |
| **Visual Effects** | `settings:visual_effects` | Groups **Animations** (`settings:animate`), **Transformations** (`settings:transform`), **Visual Execution** (`settings:visual_execution`). |
| **Resolution** | `settings:resolution` | Groups **Layout** (`settings:layout`), **Scale Factor** (`settings:scale`), **Zoom Factor** (`settings:zoom`). |
| **Screen Saver** | `settings:screen_saver` | Groups **Sleep Timeout** (`settings:sleep_timeout`), **Eject Timeout** (`settings:eject_timeout`). |
| **Validate Links** | `settings:validate_links` | |
| **Random Playback** | `settings:random_playback` | |
| **Slideshow Interval** | `settings:slideshow_interval` | |

**Page 2**

| Item | Action | Notes |
|---|---|---|
| **Welcome Pages** | dynamic (`menu:data:start:welcome`, or the stored `welcome`/start-action parameter) | Shows/edits what loads when no start parameter is set. |
| **Content Guide** | `menu:data:guide:menu` | Same menu as [examples/guide/menu.json](../examples/guide/menu.json), just with real `http://` links instead of the internal `*:data:{ID}` addressing. |
| **Log** | `log` | |
| **Reset Settings** | `settings:reset` | |
| **Input Type** | `settings:input` | Only enabled if the `input` setting is changeable. |
| **Remote Control** | `settings:remote` | Only enabled if the `remote` setting is changeable. |
| **Hover Effect** | `settings:hover_effect` | |
| **Menu Button** | `settings:menu_button` | |
| **Rounded Style** | `settings:rounded_style` | |
| **Immersive Mode** | `settings:immersive_mode` | Hidden by default — shown only when relevant (e.g. on devices without a 16:9 screen ratio, per its `0.1.151` release-note description). |

`settings:visual_effects`, `settings:resolution`, and `settings:screen_saver` are **grouping panels**: the top-level Settings scene shows one combined entry, which opens a sub-panel where each underlying field is set individually via its own `settings:{FIELD}:{VALUE}` action (documented in [Application Settings](#3-application-settings) and [Framework Settings](#4-framework-settings--the-two-tiers) below).

**A note on `menu:data:guide:menu`, `menu:data:start:welcome`, and similar actions:** these `{prefix}:data:{ID}` strings call an **internal link** — an undocumented, internal-only addressing scheme, not a documented action pattern for JSON authors to use. Instead of fetching over HTTP, they resolve to JSON bundled inside the app itself, mainly for the app's built-in Guide (the in-app help menu) and the Welcome Pages entry to cross-link their own bundled pages. These internal loads do show up in the app's own **Log** (see the Log entry in the Page 2 table above) — e.g. as a message like "Request data for entry '{TYPE}' with ID '{ID}'" — but that's a debugging trace, not a documented feature. Do not use this `*:data:{ID}` syntax in your own JSON — it has no documented meaning outside the app's own bundled content.

---

## 3. Application Settings

These ten fields make up `MSXAttachedApplicationSettings`. All ten are Settings-screen-only (no launch-time URL parameter equivalent).

### Immersive Mode — `immersiveMode` (`settings:immersive_mode`)
- **Values:** `0` Off · `1` On
- **Default:** **On** (`1`, per the app's own settings object) — matches "on by default" since `0.1.164`
- **Since:** added `0.1.151`; **on by default** since `0.1.164`
- **Platform availability:** only shown on mobile and desktop devices; TV devices always have a fixed 16:9 picture, so this entry doesn't exist there. Android/Fire TV is a special case — one MSX app build covers Android mobile, Android TV, and Fire TV, so that build shows both Immersive Mode **and** Scale Factor (unlike other TV platforms, which have neither).
- **What it does:** "The immersive mode can be used for devices that do not have a 16:9 screen ratio (e.g. mobile and desktop devices). If it is enabled, the background and video will be stretched to fill the entire screen. Please note that some plugins may not support this mode."

### Rounded Style — `roundedStyle` (`settings:rounded_style`)
- **Values:** `0` Off · `1` On
- **Default:** **Off**
- **Since:** `0.1.156`
- **What it does:** "The rounded style gives the entire application a new look and feel by rounding most of the corners."

### Hover Effect — `hoverEffect` (`settings:hover_effect`)
- **Values:** `0` Off · `1` Plain · `2` Preselect
- **Default:** **Plain** (`1`)
- **Since:** `0.1.0` (Off/Plain); the **Preselect** value added `0.1.136`
- **What it does:** "The type of the hover effect. If you control the application with a pointer device, this effect helps you to select items. You can ignore this setting if you control the application with a standard remote control or with touch."
- **Plain vs. Preselect:** `Plain` is a purely visual highlight on hover — nothing is executed. `Preselect` goes further: hovering directly applies the item's [Selection Object](../experts-api/selection/selection-object.md) as if it were focused — `background`/`headline` and the selection `action` all fire, not just a highlight. Preselect is arguably the more impressive effect, but can feel chaotic when a lot is happening on screen, which is why `Plain` is the default.

### Screen Saver — `sleepTimeout` / `ejectTimeout` (`settings:screen_saver` → `settings:sleep_timeout`, `settings:eject_timeout`)
- **Since:** `0.1.160`
- **What it does (umbrella):** "The application-internal screen saver settings. Please note that these settings do not affect the screen saver settings of the device."
- **Sleep Timeout** — dims to a clock screen after this much idle time (no effect while video/audio/slideshow is active). Default: `300000` (**5 min**). Values: `0` Off · `60000` (1 min) · `300000` (5 min) · `600000` (10 min) · `900000` (15 min); a value below `60000` (1 min, the app's fixed minimum) is treated as `0`/Off. Displayed as "`{minutes}` min".
- **Eject Timeout** — automatically ejects the active video/audio/slideshow after this much idle time (no effect if nothing is active). Default: `14400000` (**4 hr**). Values: `0` Off · `7200000` (2 hr) · `14400000` (4 hr) · `28800000` (8 hr) · `43200000` (12 hr); a value below `3600000` (1 hr, the app's fixed minimum) is treated as `0`/Off. Displayed as "`{hours}` hr". Ejecting is preceded by a 30-second **Auto Eject** confirmation dialog ("Please confirm that the current video/audio/slideshow should remain active, otherwise it is automatically ejected in `30` seconds.").

### Visual Execution — `visualExecution` (`settings:visual_execution`)
- **Values:** `0` Off · `1` On
- **Default:** **On**
- **Since:** `0.1.160`
- **What it does:** "Indicates whether an execution (when pressing the OK button) should be visualized with a small animation. Please note that Animations must also be enabled for this setting to have an effect."

### Validate Links — `validateLinks` (`settings:validate_links`)
- **Values:** `0` Off · `1` On (labeled Yes/No in the UI)
- **Default:** **On**
- **Since:** `0.1.0`
- **What it does:** "Indicates whether links should be checked with a short validation panel. Opening links can freeze the application, because not every link is suitable for each platform. Additionally, it may happen that it is not possible to return to the current page. Therefore, it is recommended to validate links, before they are opened." See also [Tips & Tricks — External HTML5 Games/Apps](../overview/tips-tricks.md).

### Random Playback — `randomPlayback` (`settings:random_playback`)
- **Values:** `0` Off · `1` On (labeled Yes/No in the UI)
- **Default:** **Off**
- **Since:** `0.1.0`
- **What it does:** "Indicates whether playlist items are played in random order."

### Slideshow Interval — `slideshowInterval` (`settings:slideshow_interval`)
- **Values:** `1000` Very Fast · `5000` Fast · `10000` Normal · `20000` Slow · `40000` Very Slow (milliseconds); displayed as "`{seconds}` sec" for any other numeric value
- **Default:** **Normal** (`10000` ms)
- **Since:** `0.1.0`
- **What it does:** "The time until the next image is changed in the slideshow."

### Menu Button — `menuButton` (`settings:menu_button`)
- **Shape:** `{ action: number, keyCode: number }` — binds one physical remote key to an internal action. `action` is one of the [`TVXAction`](type-definitions.md#tvxaction) constants (`LEFT`, `RIGHT`, `UP`, `DOWN`, `EXECUTE`, `EXIT`, `BACK`, `MENU`, `GUIDE`, `OPTIONS`, `INFO`, `CLEAR`, `CHANNEL_LIST`, `SETTINGS`, `SEARCH`, `PLAY`, `PAUSE`, `STOP`, and more — see the full list); `keyCode` is the platform-specific physical key code for the bound remote key.
- **Default:** **None** — the app's own settings object hardcodes `action: -1` (Unknown) and `keyCode: -1` (None); no custom binding until the user assigns one. By default, the menu key and the blue button already open this function; the Menu Button setting lets you assign one *additional* key.
- **Since:** `0.1.0`
- **What it does:** "The menu button is used to quickly access all major controls or additional options. By default, the menu button and the blue button are mapped to this function. Additionally, you can define an extra button here. Press OK to reset the entry."
- **Why it matters:** MSX's baseline control scheme only needs D-Pad + OK + Back — a menu key isn't strictly required for simple pages. But it's the fast path to the Context Menu (Home/Player/Volume/Settings/Exit, see [above](#1-how-this-screen-relates-to-json-building)) on more complex media structures, and becomes mandatory as soon as a Content Item's `options` property is used: an item's `options` panel only opens when that item is selected **and** the menu button is pressed.

---

## 4. Framework Settings — the two tiers

These 21 fields make up `MSXAttachedFrameworkSettings`. Only 7 of the 21 are also exposed in the Settings screen; the rest are launch/start-parameter only.

**Where these values come from.** All 21 are platform-preconfigured: the TVX Framework sets a default for every one of them once per platform, before the app is ever used (e.g. the `exit` function is wired up differently on Samsung vs. Android vs. HbbTV). MSX then copies exactly the 7 fields below (`animate`, `transform`, `input`, `remote`, and the `layout`/`scale`/`zoom` trio) into its own Application-level settings, specifically because it makes sense to let the user retune them afterward; that copy is what the Settings screen exposes in [4a](#4a-runtime-adjustable-and-launch-configurable-7-fields) below. The other 14 ([4b](#4b-launchstart-parameter-only-14-fields) below) are platform-preconfigured the same way, but deliberately left unchangeable in the running app, because the platform itself dictates them (e.g. the `exit` and `volume` functions).

**URL parameters lock these values.** Setting a URL parameter is a TVX Framework mechanism, not an MSX one — it pre-configures a value for a specific deployment (e.g. to hand a platform a ready-made URL) rather than letting the app pick it at runtime. A value set this way always wins (highest priority over anything stored internally) and, once set, can no longer be changed from within the running app — including for the 7 fields that would otherwise be user-adjustable via the Settings screen. This locking mechanism also covers two further MSX-specific values that aren't part of `MSXAttachedFrameworkSettings` at all: the **Start Parameter** (`start`) and the **Dictionary Parameter** (`dictionary`). A few further URL parameters (e.g. `init`, `platform`, `t`) configure one-off launch behavior and don't correspond to any setting at all. See [URL Parameters](../experts-api/special/url-parameters.md).

**The Welcome Parameter (`welcome`) is the one exception — a fallback, not a lock.** Setting `welcome` overrides MSX's own built-in default Welcome Pages (the app's out-of-the-box greeting/intro screens with sample screenshots, otherwise reachable via **Settings → Welcome Pages**), but unlike the values above it does **not** unconditionally override a configured start parameter:
- If no start parameter is configured at all (neither via URL parameter nor via the in-app Setup Start Parameter UI), the `welcome` parameter's data is used *as* the effective start parameter.
- If a start parameter **is** configured but its own JSON has no `welcome` property, the `welcome` parameter's data is not discarded — it stays available under **Settings → Welcome Pages**, just no longer the active boot target, since the configured start parameter takes priority for booting.
- If a start parameter **is** configured *and* its own JSON also sets a `welcome` property (since `0.1.117`, see [Start Object](../main-api/start/start-object.md)), the URL-set `welcome` parameter is fully ignored — until a **Start Parameter Reset** is performed (a reset of the app's start-related data only, distinct from the general MSX Settings reset, see [`settings:reset`](#5-other-settings-scene-entries-and-safety-mechanisms)).

**The two mechanisms don't cover the same ground.** URL parameters mainly target the raw TVX Settings — all 21 Framework Settings, plus the 3 MSX-only launch params above. Internal `settings:*` actions, on the other hand, can only ever set **MSX Settings** — the 10 [Application Settings](#3-application-settings) and the 7 copied-from-TVX fields in [4a](#4a-runtime-adjustable-and-launch-configurable-7-fields) — never the other 14 raw TVX Settings in [4b](#4b-launchstart-parameter-only-14-fields) directly, because those were never copied into the MSX settings object in the first place. That's the structural reason those 14 have no `settings:{FIELD}:{VALUE}` action at all, not just a design choice to hide a panel.

### 4a. Runtime-adjustable AND launch-configurable (7 fields)

Both a `settings:{PANEL}:{VALUE}` action and a same-named URL parameter exist. Internally, the app tracks a change flag for six of these seven — Transform, Layout, Scale, Zoom, Input, Remote — matching exactly the six whose description below states a reload is required, and exactly the six guarded by the settings-validation panel described in [How this screen relates to JSON building](#1-how-this-screen-relates-to-json-building) above: a bad value for any of them can make the whole UI unusable, so a restart forces a confirm-or-revert check. Animations, whose description carries no such note, has no change flag and isn't validated.

**Visual Effects** (`settings:visual_effects` groups the three below) — "The settings for visual effects. Depending on the platform, changing these values may increase or decrease the performance."

- **Animations** — `animate` (`settings:animate`), since `0.1.0`. Values: `0` Off · `1` JS · `2` CSS. "The type of animations. Depending on the platform, changing this value may increase or decrease the performance. It is recommended to keep the preset value."
- **Transformations** — `transform` (`settings:transform`), since `0.1.0`. Values: `0` Off · `1` 2D · `2` 3D. "The type of transformations. Depending on the platform, changing this value may increase or decrease the performance. A reload of the application is required after this value has been changed. It is recommended to keep the preset value."
- **Visual Execution** — see [Application Settings](#3-application-settings) (`visualExecution` is an Application Setting, but its panel lives under this same Visual Effects group).

**Why both fields have multiple non-Off options, not just an on/off switch.** These trace back to MSX's earliest Smart TV days. Many TVs (especially budget models — some still today) simply couldn't run animations at acceptable performance, which is why `Off` exists and still matters. Beyond that, on some TV platforms JS-driven animations proved more reliable, and sometimes even faster, than CSS transitions — hence the JS/CSS choice rather than one fixed implementation. Transformations followed the same pattern: older TVs often couldn't do CSS transforms at all (`Off`), and among those that could, some manufacturers' devices ran 3D transforms faster than 2D while others were the reverse — a browser-engine rendering-path difference, per a TV WebKit engineer consulted at the time, though the exact cause varies by platform. MSX preconfigures the right values per platform automatically (typically everything off on older devices, `CSS` animations plus `2D` or `3D` transforms on newer ones) — this is the same platform-preconfiguration mechanism described [above](#4-framework-settings--the-two-tiers).

**Resolution** (`settings:resolution` groups the three below) — "The resolution settings of the user interface. Please note that these settings do not affect the video resolution (e.g. if the layout resolution is set to 720p, 1080p or higher resolution videos can still be watched). It is recommended to keep the preset values."

- **Layout** — `layout` (`settings:layout`), since `0.1.0`. "The resolution of the layout. Depending on the platform, changing this value may increase or decrease the performance. A reload of the application is required. It is recommended to keep the preset value." The Settings-screen panel offers exactly **5** selectable resolution factors (factor → label): `0.5` → 360p · `0.75` → 540p · `1` → 720p · `1.5` → 1080p · `3` → 2160p — **fixed sizes only**. Six further factors — `0.25` (180p), `0.33` (240p), `0.66` (480p), `0.8` (576p), `0.9` (648p), and `6` (4320p) — are equally valid `layout` values, but are **not** offered as options in the panel; all six are left out of the picker deliberately, since showing every factor as a selectable entry would turn the single-screen resolution choice into a multi-page list, and in practice the omitted factors rarely matter. They're still settable at runtime, in two different forms depending on the mechanism: via the `layout` [URL Parameter](../experts-api/special/url-parameters.md)/start parameter, which accepts the **numeric** factor, the named `{N}p` form (e.g. `layout=0.25` or `layout=180p`), and `auto`/`detect`; or via the `settings:layout:{VALUE}` internal action, which accepts **only the numeric factor** (`settings:layout:0.25`) — neither the named `180p`-style form nor `auto`/`detect` are valid there. `auto`/`detect` specifically are **not** offered in the Settings screen at all, by either mechanism — they can only be set once, initially, via the URL/start parameter. `layout` also has its own `fix:` prefix (e.g. `fix:detect`, `fix:1`) — undocumented on the URL Parameters page (redundant there, since any URL-parameter value is already fixed), but used internally by a platform's own preconfiguration to lock the resolved value so the Settings-screen entry can't change it. Most TV platforms preconfigure `layout` this way (often `fix:detect` — detect the screen resolution once, then lock it), precisely because changing it later makes no sense on a fixed-scaling TV screen. This is effectively an expert-only setting: changing it can make the whole picture render too large or too small if Scale or Zoom Factor is off, which is exactly why it's one of the six settings guarded by the restart-validation panel above. It is not meant to be changed by the user under normal circumstances, though for very specific TV models an adjustment can make sense. The resolved value (whichever way it was set) directly determines [`MSXAttachedScreenInfo`](type-definitions.md#msxattachedscreeninfo)'s `width`, `height`, and `factor` (its `zoomFactor` comes from Zoom Factor below, not from Layout).
- **Scale Factor** — `scale` (`settings:scale`), since `0.1.0`. "The scale factor of the layout. By default, this is detected automatically. Depending on the platform, the auto-detection may not work correctly. If the layout does not fit into the screen, you can set this factor to a fixed value. A reload of the application is required." Values: `default` No scaling · `auto` Auto-detect (always treated as fixed) · `device` Scale to device width (labeled "Not Fixed" unless prefixed `fix:`) · `{NUMBER}` a specific factor (labeled "Not Fixed" unless prefixed `fix:{NUMBER}`, which is labeled "Fixed"). "Fixed" here means the viewport's `user-scalable` is set to `no` (the standard HTML viewport-meta mechanism), disabling the user's own pinch/manual zoom; "Not Fixed" leaves the viewport scalable by the user.
- **Zoom Factor** — `zoom` (`settings:zoom`), since `0.1.0`. "The zoom factor of the layout. By default, the resolution of the layout is set via the Layout settings and no additional zooming is performed. If the layout does not fit into the screen, you can set this factor to Auto. A reload of the application is required." Values: `default` No zooming · `auto` Auto-detect · `{NUMBER}` a specific zoom factor. This is the source of [`MSXAttachedScreenInfo`](type-definitions.md#msxattachedscreeninfo)'s `zoomFactor`.

In practice, a given platform rarely shows both Scale Factor and Zoom Factor at once — usually only one of them appears, or neither: mobile devices typically expose **Scale Factor** (a viewport setting), desktop devices typically expose **Zoom Factor** (a CSS zoom setting), and TV devices typically expose **neither**, since they run at a fixed 720p/1080p resolution.

**Input Type vs. Remote Control — what's the difference?** Input Type governs how mouse/touch gestures act **directly on the UI itself** (clicking, swiping, dragging items to navigate). Remote Control governs a separate **on-screen virtual remote overlay** (D-pad/Back/Menu/Eject/Player buttons drawn on top of the UI) — historically the predecessor mechanism, for devices/situations with no direct mouse/touch interaction with the UI at all. Now that Input Type covers direct interaction well, the virtual remote is mostly needed only for a few special functions (Back, Menu) that direct gestures don't cover — which is why the smallest Remote Control option is the default on most platforms.

**Input Type** — `input` (`settings:input`), since `0.1.0`. "The type of the on-screen input. If you control the application with a standard remote control, you can ignore this setting. A reload of the application is required." Values: `0` Off · `1` Remote Only · `2` Click & Swipe · `3` Drag & Drop · `4` Move & Execute (added `0.1.153`).
- **Click & Swipe:** click elements directly, and swipe left/right/up/down to change pages — no drag feel, but a popular choice for TVs that have a pointer device.
- **Drag & Drop:** feels native, like on mobile or desktop — lists can be smoothly dragged/panned with the mouse or a hand gesture.
- **Move & Execute:** like Drag & Drop, but with one difference: clicking an element that isn't fully active on the current page yet (e.g. already peeking in at the edge, or in the menu while its content is shown on the right but not yet active) still executes it — the standard transition happens, and the element is executed immediately afterward (e.g. the video starts). With Drag & Drop or Click & Swipe, that same click only performs the transition; a second click is then needed to execute the element. This immediate-execution behavior isn't the default because it can be confusing.

**Remote Control** — `remote` (`settings:remote`), since `0.1.0`. "The type of the on-screen remote control. If you control the application with a standard remote control, you can ignore this setting. A reload of the application is required." Values: `0` Off · `1` Minimalistic · `2` Navigation Frame (updated `0.1.160`) · `3` Corner Controls (added `0.1.148`) · `4` Complex (added `0.1.148`).
- **Minimalistic** (default on most platforms): just Back and Menu — the two special functions direct Input Type gestures can't cover.
- **Navigation Frame:** an on-screen D-pad with OK and Back, plus Menu and Eject (quick player eject) filling out the remaining corners.
- **Corner Controls:** like Navigation Frame but without the D-pad/OK — four corner buttons: Back, Menu, Eject, and Player (quick access to the player). Default on mobile, since the screen corners are usually free (so these buttons rarely cover the UI) while still adding the useful Eject/Player shortcuts that Minimalistic lacks.
- **Complex:** an expert-only setting mirroring a complete remote control — every [`TVXAction`](type-definitions.md#tvxaction) constant is available here. Rarely used in practice.

### 4b. Launch/start-parameter only (14 fields)

These exist only as [URL Parameters](../experts-api/special/url-parameters.md) (which the app's own start-parameter setup UI also writes to); no `settings:{PANEL}` internal action exists for any of them, so a JSON action string cannot toggle them at runtime.

| Field | What it controls |
|---|---|
| `back` | Back-button function: off, or use web history |
| `background` | Screen background: transparent or opaque |
| `busy` | Busy indicator: off, delayed, or instant |
| `caption` | Options/menu caption visibility in the lower-right corner (added `0.1.157`) — a global on/off toggle only; it does not set the indicator's text. The displayed text itself is a separate, per-content mechanism: the JSON `caption`/`captionUnderlay` properties on the Content Root/Page Object (see [Glossary: Options](glossary.md#options)). This Framework Setting can hide that indicator entirely, independent of what text those properties would otherwise show. |
| `center` | Whether the UI is aligned top-left or centered |
| `exit` | Exit-button behavior (9 variants, incl. "direct execution during back navigation") |
| `fullscreen` | Fullscreen availability: off, always on, or toggleable |
| `leave` | Whether a "leave" function (web-history back) is shown, for apps that opened MSX from elsewhere |
| `playback` | What happens to video/audio when the app sleeps: stop, pause-only, pause/play, or keep playing |
| `pointer` | Which pointer input is handled: off, mouse, touch, merged, switch, or combi (added `0.1.160`) |
| `secure` | `http://`→`https://` upgrade behavior for JSON/plugins/media |
| `speed` | Whether the playback-speed button is enabled, for video/audio/both |
| `suspend` | How app suspend/resume is detected: off, visibility handler, or system handler |
| `volume` | Volume control: off, on, or mute-only |

---

## 5. Other Settings-scene entries and safety mechanisms

Not values to configure, but part of the same scene:

- `settings:about` — **not** the same as [About](../overview/about.md) (that page is the developer's project bio/tech-stack/release-history write-up). This is an app/content info panel showing, as a key/value list: Application (MSX name + version), Framework (TVX Framework name + version), Copyright, Contact, Web, Content Info (the loaded content/service's own name + version), Content Server, Host Server, Dictionary, Client, Platform, ID, Player, Current Time, and User Agent — a snapshot of values that mostly stay fixed for the whole session, not a continuously-updating display. Two buttons below it: **Extended Information** opens a further key/value sub-panel (additional data plus a URL Parameters row) with its own **Platform Options** button (`system:options`); **Support** opens a donation panel — a message from the developer explaining MSX is free/registration-less and asking for a small donation if you find it useful, a QR code, and a "Donate" button pointing to `https://msx.benzac.de/support/`.
- `settings:start` — the start-parameter setup panel. See [Setup Start Parameter](../main-api/start/setup-start-parameter.md).
- `dialog:application` — a panel of application-lifecycle action buttons: **Fullscreen** (`fullscreen`, toggles fullscreen; only shown if the `fullscreen` Framework Setting is `2`/Toggleable), **Reload** (`reload`, "Reload Current Page"), **Restart** (`restart`, "Start With First Page"), **Cleaning** (`cleaning`, "Clear Cache & Reload"), **Leave** (`leave`, "Back To Previous Page"; only shown if the `leave` Framework Setting is `1`/On), and **Exit** (`exit`; only shown if the `exit` Framework Setting is non-`0`). See [4b](#4b-launchstart-parameter-only-14-fields) for what those three gating Framework Settings mean.
- `volume` — opens the Volume overlay, when the platform allows the app to control it (per the `volume` Framework Setting in [4b](#4b-launchstart-parameter-only-14-fields)). Not a toggle: on most platforms it's a 0–100 in-app volume level. The exception is iOS, where only mute/unmute is possible — matching the `volume` setting's `2`/"Mute only" state (visible, but toggle-only), as opposed to `1`/"On" (default on most other platforms, full range) or `0`/"Off" (hidden entirely).
- `log` — opens the debug log: a simple list of the last 30 messages, no scrolling. Pressing **OK** while the log is open shows a panel titled "Log" with a single option, **Clear**; confirming clears it, closing the panel without confirming leaves it untouched.
- `menu:data:guide:menu` — opens the Content Guide. [examples/guide/menu.json](../examples/guide/menu.json) is essentially this same menu, just rewritten with real `http://` links in place of the internal `*:data:{ID}` addressing (see the note in [Settings screen map](#2-settings-screen-map)) — a good concrete before/after of that syntax.
- `settings:reset` — a Yes/No confirmation ("Do you want to reset settings and reload the application?"); **Yes** resets settings and reloads, **No** goes back.
- `settings:validate` — the countdown-confirmation panel shown after a risky settings change; **Confirm** keeps the change, **Reset** (focused by default) or letting the countdown expire both reset settings (see [How this screen relates to JSON building](#1-how-this-screen-relates-to-json-building)).

---

## 6. What is not covered by any native setting

The sources describe no global, app-level setting for **language selection**, **subtitles**, **parental controls**, **buffer/cache size**, **video quality**, or similar. Subtitle language and similar preferences are instead handled per-plugin as extended item `properties` (e.g. `html5x:subtitle:{lang}:{Label}`, see [HTML5X Plugin](../experts-api/plugins/html5x-plugin.md)) or per-platform (e.g. `tizen:subtitle:style:*`, see [Tizen Player](../experts-api/special/tizen-player.md)) — not as a native Settings-screen entry. Do not assume such a global setting exists; if a question needs one, say it is not documented rather than inventing a panel.

**Why these aren't native settings.** MSX is deliberately generic — a single white-label shell used for wildly different kinds of content and services. A one-size-fits-all setting for something as use-case-specific as parental controls, buffer/cache size, or video quality wouldn't fit most services at all, so none of these were built into the native Settings screen. Language selection is a special case of the same reasoning: in the end, a JSON author's content must either support multiple languages via [Dictionary Inline Expressions](../experts-api/hidden-features/dictionary-inline-expressions.md)/[Dictionary Structure](../experts-api/special/dictionary-structure.md), or simply be written directly in one target language — there's no third option a generic app-level language switch could meaningfully drive. That's also why the entire language feature was deliberately externalized into the Dictionary mechanism instead of a native setting: MSX gives JSON authors several ways to build multi-language content (dictionary lookups, server-side language detection/selection, `dictionary:{URL}` at runtime, etc.), but leaves the actual design to them.

**The practical takeaway:** for exactly these use-case-specific needs, build your own settings **inside your own JSON/service** instead of expecting MSX to provide one. Two proven patterns:
- A custom Settings-style content page backed by your own server, storing choices via `execute:{URL}` and reading them back into the served JSON.
- A fully server-less, stateful settings UI built with an **interaction plugin**, following the request → render → commit → update-model → reload round trip — see the [Cookbook's interaction plugin deep dive](cookbook.md#deep-dive--building-an-interaction-plugin-plugin_test_2) (`plugin_test_2`), which walks through exactly this pattern for a settings screen (checkboxes committed via `interaction:commit`, handled in `handleData`, re-rendered with `reload:content`/`reload:panel`).

---

## See Also

- [Internal Actions](../experts-api/special/internal-actions.md) — full `settings:` action tables
- [URL Parameters](../experts-api/special/url-parameters.md) — full launch-time enums
- [Release Notes](../overview/release-notes.md) — since-versions and menu-path names
- [Attached Data Examples](../experts-api/special/attached-data-examples.md) · [Type Definitions](type-definitions.md) — `MSXAttachedApplicationSettings` / `MSXAttachedFrameworkSettings` structures
- [Plugin Events Reference](../experts-api/plugins/plugin-events-reference.md) — `settings:*` change events
- [Settings Menu Item](../experts-api/hidden-features/settings-menu-item.md) — embedding the entry point in your own menu
- [Common Misconceptions](common-misconceptions.md) · [AGENTS.md](../AGENTS.md)
