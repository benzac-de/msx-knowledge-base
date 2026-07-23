---
title: Internal Actions
category: Experts API - Special
summary: Reference for all internal MSX actions, valid for version 0.1.160+, including main actions and internal-only actions.
---

# Internal Actions

These actions are used internally, but can also be used to create more complex and interactive media pages. Unlike the main actions, they have no version control and are only valid for version **0.1.160+**. Therefore, please check your version if you encounter any errors/warnings, because the syntax in previous versions can be different.

**Note: The main actions ([Actions](../../main-api/common/actions.md)) are also described here (in less detail) to provide a better overview of all available actions.**

If you would like to search for an action, please have a look at this link: [https://msx.benzac.de/info/xp/actions.html](https://msx.benzac.de/info/xp/actions.html).

## See Also

- [Actions Reference](../../reference/actions-reference.md) — consolidated Main + Internal actions, including cases where "Main Action: No" still carries a tighter since-version than the `0.1.160+` blanket (`execute:service:...` since `0.1.90`, the entire broadcast/HbbTV family since `0.1.30`, the `content:json:`/`menu:json:`/`panel:json:`/`playlist:json:`/`slideshow:json:` base64-embedding variants since `0.1.0`, `system:tvx:launch`/`system:tvx:launch:{APP_ID}` and `system:netcast:menu` since `0.1.136`, `request:interaction:{DATA_ID}@{URL}` combinations since `0.1.82`) — see that page for the complete, authoritative list
- [Actions Reference → Actions That Mirror a "Dynamic" Extended Property](../../reference/actions-reference.md#actions-that-mirror-a-dynamic-extended-property) — the largest exception group: the full `player:*`/`slider:*`/`resume:position`/`trigger:{KEY}:{ACTION}` cross-reference table, each action sharing its property's own since-version (`0.1.58`–`0.1.156`, not blanket `0.1.160+`)
- [Actions Reference → Plugin-Commit Actions (`player:commit`, `interaction:commit:response:...`) and Tizen Player](../../reference/actions-reference.md#plugin-commit-actions-playercommit-interactioncommitresponse-and-tizen-player) — `player:commit`/`player:commit:message:{MESSAGE}` (built for video/audio plugins generally) are `0.1.74`; `interaction:commit:response:{REQUEST_ACTION}`/`interaction:commit:response:request:player:{DATA_ID}` (reused by [Tizen Player](./tizen-player.md)) are `0.1.128`; neither is blanket `0.1.160+`
- [Actions Reference → Request Actions](../../reference/actions-reference.md#request-actions-requestinteraction-is-0182-requestplayer-remains-01160) — the `request:interaction:{DATA_ID}@{URL}` sub-variant seen throughout the tables below (e.g. `content:request:interaction:...`, `*:resolve:request:interaction:...`) is `0.1.82`, not blanket `0.1.160+`, except when combined with a later-floored prefix (`auto:`, Resolve); the sibling `request:player:{DATA_ID}` sub-variant remains blanket `0.1.160+`
- [Common Misconceptions → Actions](../../reference/common-misconceptions.md#actions) and [→ Server actions](../../reference/common-misconceptions.md#server-actions-execute)
- [Versioning → Forward Compatibility](../../reference/versioning.md#forward-compatibility) — why an unrecognized internal action on an older client surfaces as a warning, not a hard error
- [Glossary → Invalidate](../../reference/glossary.md#invalidate) and [→ Release](../../reference/glossary.md#release) — the distinction between `invalidate:*` (visual only) and `release:*` (actually clears cache)
- [Best Practices & Good to Know → The player UI shows for several seconds by default](../../reference/best-practices.md#the-player-ui-shows-for-several-seconds-by-default-when-starting-videoaudio--several-mechanisms-shorten-or-suppress-it) — how `auto:{URL}`/`auto:` (internal-only, `0.1.160+`) fits alongside `control:load`/`trigger:load`/`trigger:start` to suppress or shorten the player UI

**Column guide:**
- **Main Action = Yes**: This action is also an official Main Action (documented in [Actions](../../main-api/common/actions.md)) and is version-stable.
- **Main Action = No**: This is an internal-only action. In most cases it requires version **0.1.160+** with no finer version control — but a substantial number of exceptions have their own tighter since-version despite being flagged "No" here: a few individual families (e.g. `execute:service:...` since `0.1.90`, the broadcast/HbbTV family since `0.1.30`, the `:json:` base64-embedding variants since `0.1.0`, `system:tvx:launch`/`system:netcast:menu` since `0.1.136`), the `request:interaction:{DATA_ID}@{URL}` sub-variant of most URL-accepting actions (`0.1.82`, not blanket `0.1.160+`, except when floored higher by a combined prefix like `auto:` or Resolve — its sibling `request:player:{DATA_ID}` sub-variant remains blanket `0.1.160+`), plus — the largest group — every action that is the runtime-settable form of a "Dynamic" [Extended Property](../../experts-api/special/extended-properties.md) (mostly `player:...`, plus a few `slider:...`, `resume:position`, and `trigger:{KEY}:{ACTION}`), each sharing that property's own since-version (`0.1.58`–`0.1.156`), plus `player:commit`/`player:commit:message:{MESSAGE}` (`0.1.74`) and `interaction:commit:response:{REQUEST_ACTION}`/`interaction:commit:response:request:player:{DATA_ID}` (`0.1.128`) — built for video/audio plugins generally, and reused (not invented) by [Tizen Player](./tizen-player.md); see [Actions Reference → Actions That Mirror a "Dynamic" Extended Property](../../reference/actions-reference.md#actions-that-mirror-a-dynamic-extended-property), [→ Plugin-Commit Actions (`player:commit`, `interaction:commit:response:...`) and Tizen Player](../../reference/actions-reference.md#plugin-commit-actions-playercommit-interactioncommitresponse-and-tizen-player), and [→ Request Actions](../../reference/actions-reference.md#request-actions-requestinteraction-is-0182-requestplayer-remains-01160) for the complete, precise cross-references.

## audio: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `audio:{URL}`<br> `audio:http://msx.benzac.de/media/audio1.mp3`<br> | Optional | **Yes** | Plays an audio. |
| `audio:auto:{URL}`<br> `audio:auto:http://msx.benzac.de/media/audio1.mp3`<br> | Optional | No | Plays an audio in auto mode. |
| `audio:auto:broadcast:{BC_ACTION}`<br> `audio:auto:broadcast:current`<br> `audio:auto:broadcast:next`<br> `audio:auto:broadcast:prev`<br> `audio:auto:broadcast:release`<br> `audio:auto:broadcast:name:{CHANNEL_NAME}`<br> `audio:auto:broadcast:triplet:{ONID}.{TSID}.{SID}`<br> | Optional | No | Executes a platform-specific audio broadcast action in auto mode. |
| `audio:auto:plugin:{URL}`<br> `audio:auto:plugin:http://msx.benzac.de/plugins/template.html`<br> | Optional | No | Plays a plugin audio in auto mode. |
| `audio:auto:resolve:{REQUEST_ACTION}`<br> `audio:auto:resolve:http://msx.benzac.de/services/resolve.php?type=audio`<br> `audio:auto:resolve:user:{URL}`<br> `audio:auto:resolve:request:interaction:{DATA_ID}`<br> `audio:auto:resolve:request:interaction:{DATA_ID}@{URL}`<br> | Optional | No | Plays an audio by resolving it with a URL in auto mode (the response data must contain a `url` property). |
| `audio:broadcast:{BC_ACTION}`<br> `audio:broadcast:current`<br> `audio:broadcast:next`<br> `audio:broadcast:prev`<br> `audio:broadcast:release`<br> `audio:broadcast:name:{CHANNEL_NAME}`<br> `audio:broadcast:triplet:{ONID}.{TSID}.{SID}`<br> | Optional | No | Executes a platform-specific audio broadcast action. |
| `audio:plugin:{URL}`<br> `audio:plugin:http://msx.benzac.de/plugins/template.html`<br> | Optional | **Yes** | Plays a plugin audio. |
| `audio:resolve:{REQUEST_ACTION}`<br> `audio:resolve:http://msx.benzac.de/services/resolve.php?type=audio`<br> `audio:resolve:user:{URL}`<br> `audio:resolve:request:interaction:{DATA_ID}`<br> `audio:resolve:request:interaction:{DATA_ID}@{URL}`<br> | Optional | No | Plays an audio by resolving it with a URL (the response data must contain a `url` property). |

## awake: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `awake` | None | No | Awakes the screen when the screen saver is active and starts a new sleep or eject timeout. |

## back: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `back` | None | No | Goes back in history. |

## background: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `background` | None | No | Shows the background scene (if a video is active, the video scene becomes visible). |
| `background:{URL}`<br> `background:http://msx.benzac.de/img/bg1.jpg`<br> `background:none`<br> `background:default`<br> | None | No | Loads a background (temporarily). The background is reset as soon as a new selection is performed. |
| `background:resolve:{REQUEST_ACTION}`<br> `background:resolve:http://msx.benzac.de/services/resolve.php?type=image`<br> `background:resolve:user:{URL}`<br> `background:resolve:request:interaction:{DATA_ID}`<br> `background:resolve:request:interaction:{DATA_ID}@{URL}`<br> | None | No | Loads a background (temporarily) by resolving it with a URL (the response data must contain a `url` property). The background is reset as soon as a new selection is performed. |

## busy: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `busy:start:{BUSY_ID}`<br> `busy:start:loading`<br> | None | No | Starts a busy action. A busy action can be pending for maximum 30 seconds (after this time, it is automatically stopped). |
| `busy:stop:{BUSY_ID}`<br> `busy:stop:loading`<br> | None | No | Stops a busy action. |

## cleaning: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `cleaning` | None | No | Clears the cache (if the platform supports it) and reloads the application. |

## cleanup: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `cleanup` | None | No | Closes all overlays. |

## close: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `close`<br> `close`<br> | None | No | Closes visible content and/or panel. |
| `close:{CONTENT_FLAG}`<br> `close:content_flag`<br> | None | No | Closes a visible content and/or panel that has the specific flag. |

## content: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `content:{URL}`<br> `content:http://msx.benzac.de/info/data/guide/videos.json`<br> | None | **Yes** | Loads a content page. |
| `content:data` | **Required** | **Yes** | Shows a content page. |
| `content:json:{BASE64_ENCODED_JSON}` | None | No | Shows a content page by indicating the data inline as base64-encoded JSON. |
| `content:request:interaction:{DATA_ID}`<br> `content:request:interaction:custom_data_id`<br> | None | No | Loads a content page from the interaction plugin. |
| `content:request:interaction:{DATA_ID}@{URL}`<br> `content:request:interaction:custom_data_id@http://msx.benzac.de/interaction/template.html`<br> | None | No | Loads a content page from the interaction plugin (with ensuring that the interaction plugin is loaded). |
| `content:request:player:{DATA_ID}`<br> `content:request:player:custom_data_id`<br> | None | No | Loads a content page from the player (handled by the video/audio plugin). |
| `content:user:{URL}`<br> `content:user:http://msx.benzac.de/info/data/guide/videos.json`<br> | None | **Yes** | Loads a content page in user mode. |

## data: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `data` | **Required** | **Yes** | Executes multiple actions in a row. |

## delay: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `delay:{ACTION_ID}:{DELAY}:{ACTION}`<br> `delay:custom_action_id:1:info:Delayed action executed (after 1 second).`<br> `delay:custom_action_id:2:info:Delayed action executed (after 2 seconds).`<br> `delay:custom_action_id:30:info:Delayed action executed (after 30 seconds).`<br> | Optional | No | Executes an action after a specific delay (indicated in seconds). The maximum delay is 30 seconds.<br><br>**Note: There can only be one pending delay action per ID. If a new delay action is started for an existing ID, the previous action is automatically canceled.** |
| `delay:{ACTION_ID}:cancel`<br> `delay:custom_action_id:cancel`<br> | None | No | Cancels a pending delay action. |
| `delay:{ACTION_ID}:execute`<br> `delay:custom_action_id:execute`<br> | None | No | Executes (and stops) a pending delay action. |
| `delay:{ACTION_ID}:restart`<br> `delay:custom_action_id:restart`<br> | None | No | Restarts a pending delay action. |

## dialog: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `dialog:{DIALOG_ID}`<br> `dialog:reload`<br> `dialog:restart`<br> `dialog:cleaning`<br> `dialog:leave`<br> `dialog:exit`<br> `dialog:application`<br> `dialog:action_not_available`<br> `dialog:action_not_available_for_platform`<br> `dialog:video_not_available`<br> `dialog:audio_not_available`<br> `dialog:content_not_available`<br> `dialog:feature_not_available`<br> `dialog:none`<br> | None | No | Shows a dialog. The dialog `none` is a special dialog and will close an active notification. |

## dictionary: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `dictionary:{URL}`<br> `dictionary:http://msx.benzac.de/dic/en.json`<br> `dictionary:none`<br> | None | No | Sets up a custom dictionary. If the URL is set to `none`, a custom dictionary is reset. If a custom dictionary is set, a possible dictionary from the startup data is ignored (unless it is set via the `start` URL parameter). |

## eject: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `eject` | None | No | Shows the auto eject panel if a video/audio/slideshow is active. |

## enter: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `enter:{CODE}`<br> `enter:1234`<br> | **Required** | No | If the entered code is correct, the indicated action is executed (the data must contain an `action` property and can optionally contain an action-related `data` property). |

## error: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `error:{MESSAGE}`<br> `error:This is an error message.`<br> | None | **Yes** | Shows an error message. |

## event: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `event:{EVENT_ID}`<br> `event:custom_event_id`<br> | Optional | No | Triggers a custom event (that can be handled by the interaction and/or video/audio plugin). |

## execute: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `execute:{URL}`<br> `execute:http://msx.benzac.de/services/echo.php`<br> | Optional | **Yes** | Executes an action on server side. |
| `execute:accurate:{URL}`<br> `execute:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | **Yes** | Executes an action on server side with accurate headers. |
| `execute:code:{URL}`<br> `execute:code:http://msx.benzac.de/services/echo.php`<br> | Optional | **Yes** | Executes an action on server side with an entered code. |
| `execute:code:accurate:{URL}`<br> `execute:code:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | **Yes** | Executes an action on server side with an entered code with accurate headers. |
| `execute:code:request:interaction:{DATA_ID}`<br> `execute:code:request:interaction:custom_data_id`<br> | Optional | No | Executes an action on the interaction plugin with an entered code. |
| `execute:code:request:interaction:{DATA_ID}@{URL}`<br> `execute:code:request:interaction:custom_data_id@http://msx.benzac.de/interaction/template.html`<br> | Optional | No | Executes an action on the interaction plugin with an entered code (with ensuring that the interaction plugin is loaded). |
| `execute:code:request:player:{DATA_ID}`<br> `execute:code:request:player:custom_data_id`<br> | Optional | No | Executes an action on the player with an entered code (handled by the video/audio plugin). |
| `execute:code:user:{URL}`<br> `execute:code:user:http://msx.benzac.de/services/echo.php`<br> | Optional | **Yes** | Executes an action on server side with an entered code in user mode. |
| `execute:code:user:accurate:{URL}`<br> `execute:code:user:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | **Yes** | Executes an action on server side with an entered code in user mode with accurate headers. |
| `execute:fetch:{URL}`<br> `execute:fetch:http://msx.benzac.de/services/echo.php`<br> | None | No | Executes an action by simply fetching it from the server. |
| `execute:fetch:accurate:{URL}`<br> `execute:fetch:accurate:http://msx.benzac.de/services/echo.php`<br> | None | No | Executes an action by simply fetching it from the server with accurate headers. |
| `execute:fetch:user:{URL}`<br> `execute:fetch:user:http://msx.benzac.de/services/echo.php`<br> | None | No | Executes an action by simply fetching it from the server in user mode. |
| `execute:fetch:user:accurate:{URL}`<br> `execute:fetch:user:accurate:http://msx.benzac.de/services/echo.php`<br> | None | No | Executes an action by simply fetching it from the server in user mode with accurate headers. |
| `execute:info:{URL}`<br> `execute:info:http://msx.benzac.de/services/echo.php`<br> `execute:info:extended:http://msx.benzac.de/services/echo.php`<br> `execute:info:base:http://msx.benzac.de/services/echo.php`<br> `execute:info:application:http://msx.benzac.de/services/echo.php`<br> `execute:info:framework:http://msx.benzac.de/services/echo.php`<br> `execute:info:content:http://msx.benzac.de/services/echo.php`<br> `execute:info:dictionary:http://msx.benzac.de/services/echo.php`<br> `execute:info:screen:http://msx.benzac.de/services/echo.php`<br> `execute:info:time:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes an action on server side with attached system info. It is also possible to attach the extended system info (including URL parameters and system data) or only a specific system info. |
| `execute:info:accurate:{URL}`<br> `execute:info:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes an action on server side with attached system info with accurate headers. It is also possible to attach the extended or only a specific system info. Please see action `execute:info:{URL}` for possible examples. |
| `execute:info:request:interaction:{DATA_ID}`<br> `execute:info:request:interaction:custom_data_id`<br> | Optional | No | Executes an action on the interaction plugin with attached system info. It is also possible to attach the extended or only a specific system info. Please see action `execute:info:{URL}` for possible examples. |
| `execute:info:request:interaction:{DATA_ID}@{URL}`<br> `execute:info:request:interaction:custom_data_id@http://msx.benzac.de/interaction/template.html`<br> | Optional | No | Executes an action on the interaction plugin with attached system info (with ensuring that the interaction plugin is loaded). It is also possible to attach the extended or only a specific system info. Please see action `execute:info:{URL}` for possible examples. |
| `execute:info:request:player:{DATA_ID}`<br> `execute:info:request:player:custom_data_id`<br> | Optional | No | Executes an action on the player with attached system info (handled by the video/audio plugin). It is also possible to attach the extended or only a specific system info. Please see action `execute:info:{URL}` for possible examples. |
| `execute:info:silent:{REQUEST_ACTION}`<br> `execute:info:silent:http://msx.benzac.de/services/echo.php`<br> `execute:info:silent:user:{URL}`<br> `execute:info:silent:accurate:{URL}`<br> `execute:info:silent:user:accurate:{URL}`<br> `execute:info:silent:request:player:{DATA_ID}`<br> `execute:info:silent:request:interaction:{DATA_ID}`<br> `execute:info:silent:request:interaction:{DATA_ID}@{URL}`<br> | Optional | No | Executes an action with attached system info in silent mode (by not showing a busy indicator and error messages). It is also possible to attach the extended or only a specific system info. Please see action `execute:info:{URL}` for possible examples. |
| `execute:info:user:{URL}`<br> `execute:info:user:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes an action on server side with attached system info in user mode. It is also possible to attach the extended or only a specific system info. Please see action `execute:info:{URL}` for possible examples. |
| `execute:info:user:accurate:{URL}`<br> `execute:info:user:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes an action on server side with attached system info in user mode with accurate headers. It is also possible to attach the extended or only a specific system info. Please see action `execute:info:{URL}` for possible examples. |
| `execute:request:interaction:{DATA_ID}`<br> `execute:request:interaction:custom_data_id`<br> | Optional | No | Executes an action on the interaction plugin. |
| `execute:request:interaction:{DATA_ID}@{URL}`<br> `execute:request:interaction:custom_data_id@http://msx.benzac.de/interaction/template.html`<br> | Optional | No | Executes an action on the interaction plugin (with ensuring that the interaction plugin is loaded). |
| `execute:request:player:{DATA_ID}`<br> `execute:request:player:custom_data_id`<br> | Optional | No | Executes an action on the player (handled by the video/audio plugin). |
| `execute:response:{REQUEST_ACTION}`<br> `execute:response:http://msx.benzac.de/services/echo.php`<br> `execute:response:user:{URL}`<br> `execute:response:accurate:{URL}`<br> `execute:response:user:accurate:{URL}`<br> `execute:response:request:player:{DATA_ID}`<br> `execute:response:request:interaction:{DATA_ID}`<br> `execute:response:request:interaction:{DATA_ID}@{URL}`<br> `execute:response:silent:{URL}`<br> `execute:response:silent:user:{URL}`<br>                                     `execute:response:silent:accurate:{URL}`<br> `execute:response:silent:user:accurate:{URL}`<br> `execute:response:silent:request:player:{DATA_ID}`<br> `execute:response:silent:request:interaction:{DATA_ID}`<br> `execute:response:silent:request:interaction:{DATA_ID}@{URL}`<br> | **Required** | No | Executes an action with attached response from a request action (the data must contain a `requestAction` property and can optionally contain a request-related `requestData` property). |
| `execute:resume:{URL}`<br> `execute:resume:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes an action on server side with attached resume keys. |
| `execute:resume:accurate:{URL}`<br> `execute:resume:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes an action on server side with attached resume keys with accurate headers. |
| `execute:resume:request:interaction:{DATA_ID}`<br> `execute:resume:request:interaction:custom_data_id`<br> | Optional | No | Executes an action on the interaction plugin with attached resume keys. |
| `execute:resume:request:interaction:{DATA_ID}@{URL}`<br> `execute:resume:request:interaction:custom_data_id@http://msx.benzac.de/interaction/template.html`<br> | Optional | No | Executes an action on the interaction plugin with attached resume keys (with ensuring that the interaction plugin is loaded). |
| `execute:resume:request:player:{DATA_ID}`<br> `execute:resume:request:player:custom_data_id`<br> | Optional | No | Executes an action on the player with attached resume keys (handled by the video/audio plugin). |
| `execute:resume:silent:{REQUEST_ACTION}`<br> `execute:resume:silent:http://msx.benzac.de/services/echo.php`<br> `execute:resume:silent:user:{URL}`<br> `execute:resume:silent:accurate:{URL}`<br> `execute:resume:silent:user:accurate:{URL}`<br> `execute:resume:silent:request:player:{DATA_ID}`<br> `execute:resume:silent:request:interaction:{DATA_ID}`<br> `execute:resume:silent:request:interaction:{DATA_ID}@{URL}`<br> | Optional | No | Executes an action with attached resume keys in silent mode (by not showing a busy indicator and error messages). |
| `execute:resume:user:{URL}`<br> `execute:resume:user:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes an action on server side with attached resume keys in user mode. |
| `execute:resume:user:accurate:{URL}`<br> `execute:resume:user:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes an action on server side with attached resume keys in user mode with accurate headers. |
| `execute:service:{URL}`<br> `execute:service:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes a service action on server side. |
| `execute:service:accurate:{URL}`<br> `execute:service:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes a service action on server side with accurate headers. |
| `execute:service:fetch:{URL}`<br> `execute:service:fetch:http://msx.benzac.de/services/echo.php`<br> | None | No | Executes a service action by simply fetching it from the server. |
| `execute:service:fetch:accurate:{URL}`<br> `execute:service:fetch:accurate:http://msx.benzac.de/services/echo.php`<br> | None | No | Executes a service action by simply fetching it from the server with accurate headers. |
| `execute:service:fetch:user:{URL}`<br> `execute:service:fetch:user:http://msx.benzac.de/services/echo.php`<br> | None | No | Executes a service action by simply fetching it from the server in user mode. |
| `execute:service:fetch:user:accurate:{URL}`<br> `execute:service:fetch:user:accurate:http://msx.benzac.de/services/echo.php`<br> | None | No | Executes a service action by simply fetching it from the server in user mode with accurate headers. |
| `execute:service:info:{URL}`<br> `execute:service:info:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes a service action on server side with attached system info. It is also possible to attach the extended or only a specific system info. Please see action `execute:info:{URL}` for possible examples. |
| `execute:service:info:accurate:{URL}`<br> `execute:service:info:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes a service action on server side with attached system info with accurate headers. It is also possible to attach the extended or only a specific system info. Please see action `execute:info:{URL}` for possible examples. |
| `execute:service:info:request:interaction:{DATA_ID}`<br> `execute:service:info:request:interaction:custom_data_id`<br> | Optional | No | Executes a service action on the interaction plugin with attached system info. It is also possible to attach the extended or only a specific system info. Please see action `execute:info:{URL}` for possible examples. |
| `execute:service:info:request:interaction:{DATA_ID}@{URL}`<br> `execute:service:info:request:interaction:custom_data_id@http://msx.benzac.de/interaction/template.html`<br> | Optional | No | Executes a service action on the interaction plugin with attached system info (with ensuring that the interaction plugin is loaded). It is also possible to attach the extended or only a specific system info. Please see action `execute:info:{URL}` for possible examples. |
| `execute:service:info:request:player:{DATA_ID}`<br> `execute:service:info:request:player:custom_data_id`<br> | Optional | No | Executes a service action on the player with attached system info (handled by the video/audio plugin). It is also possible to attach the extended or only a specific system info. Please see action `execute:info:{URL}` for possible examples. |
| `execute:service:info:silent:{REQUEST_ACTION}`<br> `execute:service:info:silent:http://msx.benzac.de/services/echo.php`<br> `execute:service:info:silent:user:{URL}`<br> `execute:service:info:silent:accurate:{URL}`<br> `execute:service:info:silent:user:accurate:{URL}`<br> `execute:service:info:silent:request:player:{DATA_ID}`<br> `execute:service:info:silent:request:interaction:{DATA_ID}`<br> `execute:service:info:silent:request:interaction:{DATA_ID}@{URL}`<br> | Optional | No | Executes a service action with attached system info in silent mode (by not showing a busy indicator and error messages). It is also possible to attach the extended or only a specific system info.  Please see action `execute:info:{URL}` for possible examples. |
| `execute:service:info:user:{URL}`<br> `execute:service:info:user:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes a service action on server side with attached system info in user mode. It is also possible to attach the extended or only a specific system info. Please see action `execute:info:{URL}` for possible examples. |
| `execute:service:info:user:accurate:{URL}`<br> `execute:service:info:user:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes a service action on server side with attached system info in user mode with accurate headers. It is also possible to attach the extended or only a specific system info. Please see action `execute:info:{URL}` for possible examples. |
| `execute:service:request:interaction:{DATA_ID}`<br> `execute:service:request:interaction:custom_data_id`<br> | Optional | No | Executes a service action on the interaction plugin. |
| `execute:service:request:interaction:{DATA_ID}@{URL}`<br> `execute:service:request:interaction:custom_data_id@http://msx.benzac.de/interaction/template.html`<br> | Optional | No | Executes a service action on the interaction plugin (with ensuring that the interaction plugin is loaded). |
| `execute:service:request:player:{DATA_ID}`<br> `execute:service:request:player:custom_data_id`<br> | Optional | No | Executes a service action on the player (handled by the video/audio plugin). |
| `execute:service:response:{REQUEST_ACTION}`<br> `execute:service:response:http://msx.benzac.de/services/echo.php`<br> `execute:service:response:user:{URL}`<br> `execute:service:response:accurate:{URL}`<br> `execute:service:response:user:accurate:{URL}`<br> `execute:service:response:request:player:{DATA_ID}`<br> `execute:service:response:request:interaction:{DATA_ID}`<br> `execute:service:response:request:interaction:{DATA_ID}@{URL}`<br> `execute:service:response:silent:{URL}`<br> `execute:service:response:silent:user:{URL}`<br> `execute:service:response:silent:accurate:{URL}`<br> `execute:service:response:silent:user:accurate:{URL}`<br> `execute:service:response:silent:request:player:{DATA_ID}`<br> `execute:service:response:silent:request:interaction:{DATA_ID}`<br> `execute:service:response:silent:request:interaction:{DATA_ID}@{URL}`<br> | **Required** | No | Executes a service action with attached response from a request action (the data must contain a `requestAction` property and can optionally contain a request-related `requestData` property). |
| `execute:service:resume:{URL}`<br> `execute:service:resume:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes a service action on server side with attached resume keys. |
| `execute:service:resume:accurate:{URL}`<br> `execute:service:resume:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes a service action on server side with attached resume keys with accurate headers. |
| `execute:service:resume:request:interaction:{DATA_ID}`<br> `execute:service:resume:request:interaction:custom_data_id`<br> | Optional | No | Executes a service action on the interaction plugin with attached resume keys. |
| `execute:service:resume:request:interaction:{DATA_ID}@{URL}`<br> `execute:service:resume:request:interaction:custom_data_id@http://msx.benzac.de/interaction/template.html`<br> | Optional | No | Executes a service action on the interaction plugin with attached resume keys (with ensuring that the interaction plugin is loaded). |
| `execute:service:resume:request:player:{DATA_ID}`<br> `execute:service:resume:request:player:custom_data_id`<br> | Optional | No | Executes a service action on the player with attached resume keys (handled by the video/audio plugin). |
| `execute:service:resume:silent:{REQUEST_ACTION}`<br> `execute:service:resume:silent:http://msx.benzac.de/services/echo.php`<br> `execute:service:resume:silent:user:{URL}`<br> `execute:service:resume:silent:accurate:{URL}`<br> `execute:service:resume:silent:user:accurate:{URL}`<br> `execute:service:resume:silent:request:player:{DATA_ID}`<br> `execute:service:resume:silent:request:interaction:{DATA_ID}`<br> `execute:service:resume:silent:request:interaction:{DATA_ID}@{URL}`<br> | Optional | No | Executes a service action with attached resume keys in silent mode (by not showing a busy indicator and error messages). |
| `execute:service:resume:user:{URL}`<br> `execute:service:resume:user:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes a service action on server side with attached resume keys in user mode. |
| `execute:service:resume:user:accurate:{URL}`<br> `execute:service:resume:user:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes a service action on server side with attached resume keys in user mode with accurate headers. |
| `execute:service:silent:{REQUEST_ACTION}`<br> `execute:service:silent:http://msx.benzac.de/services/echo.php`<br>                                    `execute:service:silent:user:{URL}`<br> `execute:service:silent:accurate:{URL}`<br> `execute:service:silent:user:accurate:{URL}`<br> `execute:service:silent:fetch:{URL}`<br> `execute:service:silent:fetch:user:{URL}`<br> `execute:service:silent:fetch:accurate:{URL}`<br> `execute:service:silent:fetch:user:accurate:{URL}`<br> `execute:service:silent:request:player:{DATA_ID}`<br> `execute:service:silent:request:interaction:{DATA_ID}`<br> `execute:service:silent:request:interaction:{DATA_ID}@{URL}`<br> | Optional | No | Executes a service action in silent mode (by not showing a busy indicator and error messages). |
| `execute:service:slider:{URL}`<br> `execute:service:slider:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes a service action on server side with attached slider info. |
| `execute:service:slider:accurate:{URL}`<br> `execute:service:slider:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes a service action on server side with attached slider info with accurate headers. |
| `execute:service:slider:request:interaction:{DATA_ID}`<br> `execute:service:slider:request:interaction:custom_data_id`<br> | Optional | No | Executes a service action on the interaction plugin with attached slider info. |
| `execute:service:slider:request:interaction:{DATA_ID}@{URL}`<br> `execute:service:slider:request:interaction:custom_data_id@http://msx.benzac.de/interaction/template.html`<br> | Optional | No | Executes a service action on the interaction plugin with attached slider info (with ensuring that the interaction plugin is loaded). |
| `execute:service:slider:request:player:{DATA_ID}`<br> `execute:service:slider:request:player:custom_data_id`<br> | Optional | No | Executes a service action on the player with attached slider info (handled by the video/audio plugin). |
| `execute:service:slider:silent:{REQUEST_ACTION}`<br> `execute:service:slider:silent:http://msx.benzac.de/services/echo.php`<br>                                   `execute:service:slider:silent:user:{URL}`<br> `execute:service:slider:silent:accurate:{URL}`<br> `execute:service:slider:silent:user:accurate:{URL}`<br>     `execute:service:slider:silent:request:player:{DATA_ID}`<br> `execute:service:slider:silent:request:interaction:{DATA_ID}`<br> `execute:service:slider:silent:request:interaction:{DATA_ID}@{URL}`<br> | Optional | No | Executes a service action with attached slider info in silent mode (by not showing a busy indicator and error messages). |
| `execute:service:slider:user:{URL}`<br> `execute:service:slider:user:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes a service action on server side with attached slider info in user mode. |
| `execute:service:slider:user:accurate:{URL}`<br> `execute:service:slider:user:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes a service action on server side with attached slider info in user mode with accurate headers. |
| `execute:service:user:{URL}`<br> `execute:service:user:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes a service action on server side in user mode. |
| `execute:service:user:accurate:{URL}`<br> `execute:service:user:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes a service action on server side in user mode with accurate headers. |
| `execute:service:video:{URL}`<br> `execute:service:video:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes a service action on server side with attached video info. It is also possible to attach only a specific video info. Please see action `execute:video:{URL}` for possible examples. |
| `execute:service:video:accurate:{URL}`<br> `execute:service:video:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes a service action on server side with attached video info with accurate headers. It is also possible to attach only a specific video info. Please see action `execute:video:{URL}` for possible examples. |
| `execute:service:video:request:interaction:{DATA_ID}`<br> `execute:service:video:request:interaction:custom_data_id`<br> | Optional | No | Executes a service action on the interaction plugin with attached video info. It is also possible to attach only a specific video info. Please see action `execute:video:{URL}` for possible examples. |
| `execute:service:video:request:interaction:{DATA_ID}@{URL}`<br> `execute:service:video:request:interaction:custom_data_id@http://msx.benzac.de/interaction/template.html`<br> | Optional | No | Executes a service action on the interaction plugin with attached video info (with ensuring that the interaction plugin is loaded). It is also possible to attach only a specific video info. Please see action `execute:video:{URL}` for possible examples. |
| `execute:service:video:request:player:{DATA_ID}`<br> `execute:service:video:request:player:custom_data_id`<br> | Optional | No | Executes a service action on the player with attached video info (handled by the video/audio plugin). It is also possible to attach only a specific video info. Please see action `execute:video:{URL}` for possible examples. |
| `execute:service:video:silent:{REQUEST_ACTION}`<br> `execute:service:video:silent:http://msx.benzac.de/services/echo.php`<br>                                    `execute:service:video:silent:user:{URL}`<br> `execute:service:video:silent:accurate:{URL}`<br> `execute:service:video:silent:user:accurate:{URL}`<br> `execute:service:video:silent:request:player:{DATA_ID}`<br> `execute:service:video:silent:request:interaction:{DATA_ID}`<br> `execute:service:video:silent:request:interaction:{DATA_ID}@{URL}`<br> | Optional | No | Executes a service action with attached video info in silent mode (by not showing a busy indicator and error messages). It is also possible to attach only a specific video info. Please see action `execute:video:{URL}` for possible examples. |
| `execute:service:video:user:{URL}`<br> `execute:service:video:user:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes a service action on server side with attached video info in user mode. It is also possible to attach only a specific video info. Please see action `execute:video:{URL}` for possible examples. |
| `execute:service:video:user:accurate:{URL}`<br> `execute:service:video:user:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes a service action on server side with attached video info in user mode with accurate headers. It is also possible to attach only a specific video info. Please see action `execute:video:{URL}` for possible examples. |
| `execute:silent:{REQUEST_ACTION}`<br> `execute:silent:http://msx.benzac.de/services/echo.php`<br>                                                    `execute:silent:user:{URL}`<br> `execute:silent:accurate:{URL}`<br> `execute:silent:user:accurate:{URL}`<br> `execute:silent:fetch:{URL}`<br> `execute:silent:fetch:user:{URL}`<br> `execute:silent:fetch:accurate:{URL}`<br>                                     `execute:silent:fetch:user:accurate:{URL}`<br> `execute:silent:request:player:{DATA_ID}`<br>          `execute:silent:request:interaction:{DATA_ID}`<br> `execute:silent:request:interaction:{DATA_ID}@{URL}`<br> | Optional | No | Executes an action in silent mode (by not showing a busy indicator and error messages). |
| `execute:slider:{URL}`<br> `execute:slider:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes an action on server side with attached slider info. |
| `execute:slider:accurate:{URL}`<br> `execute:slider:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes an action on server side with attached slider info with accurate headers. |
| `execute:slider:request:interaction:{DATA_ID}`<br> `execute:slider:request:interaction:custom_data_id`<br> | Optional | No | Executes an action on the interaction plugin with attached slider info. |
| `execute:slider:request:interaction:{DATA_ID}@{URL}`<br> `execute:slider:request:interaction:custom_data_id@http://msx.benzac.de/interaction/template.html`<br> | Optional | No | Executes an action on the interaction plugin with attached slider info (with ensuring that the interaction plugin is loaded). |
| `execute:slider:request:player:{DATA_ID}`<br> `execute:slider:request:player:custom_data_id`<br> | Optional | No | Executes an action on the player with attached slider info (handled by the video/audio plugin). |
| `execute:slider:silent:{REQUEST_ACTION}`<br> `execute:slider:silent:http://msx.benzac.de/services/echo.php`<br>                                    `execute:slider:silent:user:{URL}`<br> `execute:slider:silent:accurate:{URL}`<br> `execute:slider:silent:user:accurate:{URL}`<br> `execute:slider:silent:request:player:{DATA_ID}`<br> `execute:slider:silent:request:interaction:{DATA_ID}`<br> `execute:slider:silent:request:interaction:{DATA_ID}@{URL}`<br> | Optional | No | Executes an action with attached slider info in silent mode (by not showing a busy indicator and error messages). |
| `execute:slider:user:{URL}`<br> `execute:slider:user:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes an action on server side with attached slider info in user mode. |
| `execute:slider:user:accurate:{URL}`<br> `execute:slider:user:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes an action on server side with attached slider info in user mode with accurate headers. |
| `execute:user:{URL}`<br> `execute:user:http://msx.benzac.de/services/echo.php`<br> | Optional | **Yes** | Executes an action on server side in user mode. |
| `execute:user:accurate:{URL}`<br> `execute:user:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | **Yes** | Executes an action on server side in user mode with accurate headers. |
| `execute:video:{URL}`<br> `execute:video:http://msx.benzac.de/services/echo.php`<br> `execute:video:info:http://msx.benzac.de/services/echo.php`<br> `execute:video:data:http://msx.benzac.de/services/echo.php`<br> `execute:video:resume:http://msx.benzac.de/services/echo.php`<br> `execute:video:volume:http://msx.benzac.de/services/echo.php`<br> `execute:video:scene:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes an action on server side with attached video info. It is also possible to attach only a specific video info. |
| `execute:video:accurate:{URL}`<br> `execute:video:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes an action on server side with attached video info with accurate headers. It is also possible to attach only a specific video info. Please see action `execute:video:{URL}` for possible examples. |
| `execute:video:request:interaction:{DATA_ID}`<br> `execute:video:request:interaction:custom_data_id`<br> | Optional | No | Executes an action on the interaction plugin with attached video info. It is also possible to attach only a specific video info. Please see action `execute:video:{URL}` for possible examples. |
| `execute:video:request:interaction:{DATA_ID}@{URL}`<br> `execute:video:request:interaction:custom_data_id@http://msx.benzac.de/interaction/template.html`<br> | Optional | No | Executes an action on the interaction plugin with attached video info (with ensuring that the interaction plugin is loaded). It is also possible to attach only a specific video info. Please see action `execute:video:{URL}` for possible examples. |
| `execute:video:request:player:{DATA_ID}`<br> `execute:video:request:player:custom_data_id`<br> | Optional | No | Executes an action on the player with attached video info (handled by the video/audio plugin). It is also possible to attach only a specific video info. Please see action `execute:video:{URL}` for possible examples. |
| `execute:video:silent:{REQUEST_ACTION}`<br> `execute:video:silent:http://msx.benzac.de/services/echo.php`<br>                                   `execute:video:silent:accurate:{URL}`<br> `execute:video:silent:user:{URL}`<br> `execute:video:silent:user:accurate:{URL}`<br> `execute:video:silent:request:player:{DATA_ID}`<br> `execute:video:silent:request:interaction:{DATA_ID}`<br> `execute:video:silent:request:interaction:{DATA_ID}@{URL}`<br> | Optional | No | Executes an action with attached video info in silent mode (by not showing a busy indicator and error messages). It is also possible to attach only a specific video info. Please see action `execute:video:{URL}` for possible examples. |
| `execute:video:user:{URL}`<br> `execute:video:user:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes an action on server side with attached video info in user mode. It is also possible to attach only a specific video info. Please see action `execute:video:{URL}` for possible examples. |
| `execute:video:user:accurate:{URL}`<br> `execute:video:user:accurate:http://msx.benzac.de/services/echo.php`<br> | Optional | No | Executes an action on server side with attached video info in user mode with accurate headers. It is also possible to attach only a specific video info. Please see action `execute:video:{URL}` for possible examples. |

## exit: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `exit` | None | No | Exits the application by using the platform-specific function. |

## focus: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `focus:{ITEM_ID}`<br> `focus:content_item_id`<br> | None | **Yes** | Focuses an item with the indicated ID in the current context. |
| `focus:clear:{CONTENT_FLAG}`<br> `focus:clear:content_flag`<br> | None | No | Clears the focus cache for a menu, content, and/or panel that has the specific flag. |
| `focus:control:{CONTROL}`<br> `focus:control:menu`<br> `focus:control:content`<br> | None | No | Focuses a control in the current context. |
| `focus:execute:{ITEM_ID}`<br> `focus:execute:content_item_id`<br> | None | No | Focuses and executes an item with the indicated ID in the current context. |
| `focus:execute:index:{ITEM_INDEX}`<br> `focus:execute:index:0`<br> | None | No | Focuses and executes an item with the indicated index in the current context. |
| `focus:index:{ITEM_INDEX}`<br> `focus:index:0`<br> | None | **Yes** | Focuses an item with the indicated index in the current context. |

## fullscreen: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `fullscreen` | None | No | Toggles the fullscreen. |

## history: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `history:back` | None | No | Goes back in the web history. |
| `history:clear` | None | No | Clears the web history. |
| `history:start` | None | No | Goes to the web history start. |
| `history:validate` | None | No | Validates the web history. |

## home: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `home` | None | No | Shows the main scene (or the context menu if the main scene is already active). |

## image: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `image:{URL}`<br> `image:http://msx.benzac.de/img/bg1.jpg`<br> | Optional | **Yes** | Shows an image. |
| `image:context` | **Required** | **Yes** | Shows an image by using the current context. |
| `image:resolve:{REQUEST_ACTION}`<br> `image:resolve:http://msx.benzac.de/services/resolve.php?type=image`<br> `image:resolve:user:{URL}`<br> `image:resolve:request:interaction:{DATA_ID}`<br> `image:resolve:request:interaction:{DATA_ID}@{URL}`<br> | Optional | No | Shows an image by resolving it with a URL (the response data must contain a `url` property). |

## info: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `info:{MESSAGE}`<br> `info:This is an info message.`<br> | None | **Yes** | Shows an info message. |

## interaction: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `interaction:busy:start:{BUSY_ID}`<br> `interaction:busy:start:loading`<br> | None | No | Starts an interaction-related busy action. This busy action is automatically stopped if the interaction plugin is unloaded. A busy action can be pending for maximum 30 seconds (after this time, it is automatically stopped). |
| `interaction:busy:stop:{BUSY_ID}`<br> `interaction:busy:stop:loading`<br> | None | No | Stops an interaction-related busy action. |
| `interaction:commit` | **Required** | No | Commits data to the interaction plugin. |
| `interaction:commit:code` | Optional | No | Commits a code to the interaction plugin. |
| `interaction:commit:dictionary` | Optional | No | Commits the dictionary to the interaction plugin. |
| `interaction:commit:info` | Optional | No | Commits the system info to the interaction plugin. |
| `interaction:commit:info:{DATA_TYPE}`<br> `interaction:commit:info:extended`<br> `interaction:commit:info:base`<br> `interaction:commit:info:application`<br> `interaction:commit:info:framework`<br> `interaction:commit:info:content`<br> `interaction:commit:info:dictionary`<br> `interaction:commit:info:screen`<br> `interaction:commit:info:time`<br> | Optional | No | Commits the extended system info (including URL parameters and system data) or a specific system info to the interaction plugin. |
| `interaction:commit:message:{MESSAGE}`<br> `interaction:commit:message:This is an interaction message.`<br> | Optional | No | Commits a message to the interaction plugin. |
| `interaction:commit:response:{REQUEST_ACTION}`<br> `interaction:commit:response:http://msx.benzac.de/services/echo.php`<br> `interaction:commit:response:user:{URL}`<br> `interaction:commit:response:accurate:{URL}`<br> `interaction:commit:response:user:accurate:{URL}`<br> `interaction:commit:response:request:player:{DATA_ID}`<br> `interaction:commit:response:silent:{URL}`<br> `interaction:commit:response:silent:user:{URL}`<br> `interaction:commit:response:silent:accurate:{URL}`<br> `interaction:commit:response:silent:user:accurate:{URL}`<br> `interaction:commit:response:silent:request:player:{DATA_ID}`<br> | Optional | No | Commits a response to the interaction plugin. |
| `interaction:commit:slider` | Optional | No | Commits the slider info to the interaction plugin. |
| `interaction:commit:string:{TRANSFORM_TYPE}:{STRING}`<br> `interaction:commit:string:transform:Media Station X`<br> `interaction:commit:string:normalize:EW6l] 801JCU; )`<br> | Optional | No | Commits a transformed/normalized string to the interaction plugin. |
| `interaction:commit:video` | Optional | No | Commits the video info to the interaction plugin. |
| `interaction:commit:video:{DATA_TYPE}`<br> `interaction:commit:video:info`<br> `interaction:commit:video:data`<br> `interaction:commit:video:resume`<br> `interaction:commit:video:volume`<br> `interaction:commit:video:scene`<br> | Optional | No | Commits a specific video info to the interaction plugin. |
| `interaction:load:{URL}`<br> `interaction:load:http://msx.benzac.de/interaction/template.html`<br> `interaction:load:none`<br> | Optional | No | Loads the interaction plugin (and optionally commits data to it).  If the interaction plugin is already loaded and action-related data is provided, this action behaves like `interaction:commit`. |
| `interaction:reload` | None | No | Reloads the interaction plugin. |
| `interaction:unload` | None | No | Unloads the interaction plugin. |

## invalidate: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `invalidate:content` | None | No | Invalidates the current content (only if the content data is reloadable). |
| `invalidate:content:flag:{CONTENT_FLAG}`<br> `invalidate:content:flag:content_flag`<br> | None | No | Invalidates the current content if it has the specific flag. |
| `invalidate:content:{ITEM_ID}`<br> `invalidate:content:content_item_id`<br> | None | No | Invalidates a content item. |
| `invalidate:content:overlay:{ITEM_ID}`<br> `invalidate:content:overlay:content_item_id`<br> | None | No | Invalidates a content overlay item. |
| `invalidate:content:underlay:{ITEM_ID}`<br> `invalidate:content:underlay:content_item_id`<br> | None | No | Invalidates a content underlay item. |
| `invalidate:menu` | None | No | Invalidates the current menu (only if the menu data is reloadable). |
| `invalidate:menu:flag:{CONTENT_FLAG}`<br> `invalidate:menu:flag:content_flag`<br> | None | No | Invalidates the current menu if it has the specific flag. |
| `invalidate:menu:{ITEM_ID}`<br> `invalidate:menu:menu_item_id`<br> | None | No | Invalidates a menu item. |
| `invalidate:panel` | None | No | Invalidates the current panel (only if the panel data is reloadable). |
| `invalidate:panel:flag:{CONTENT_FLAG}`<br> `invalidate:panel:flag:content_flag`<br> | None | No | Invalidates the current panel if it has the specific flag. |
| `invalidate:panel:{ITEM_ID}`<br> `invalidate:panel:content_item_id`<br> | None | No | Invalidates a panel item. |
| `invalidate:panel:overlay:{ITEM_ID}`<br> `invalidate:panel:overlay:content_item_id`<br> | None | No | Invalidates a panel overlay item. |
| `invalidate:panel:underlay:{ITEM_ID}`<br> `invalidate:panel:underlay:content_item_id`<br> | None | No | Invalidates a panel underlay item. |

## key: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `key:{REMOTE_KEY}`<br> `key:left`<br> `key:right`<br> `key:up`<br> `key:down`<br> `key:execute`<br> `key:back`<br> `key:red`<br> `key:green`<br> `key:yellow`<br> `key:blue`<br> `key:channel_up`<br> `key:channel_down`<br> `key:play`<br> `key:pause`<br> `key:stop`<br> | None | No | Triggers a remote key. |
| `key:code:{REMOTE_KEY_CODE}`<br> `key:code:37`<br> `key:code:39`<br> `key:code:38`<br> `key:code:40`<br> `key:code:13`<br> `key:code:8`<br> | None | No | Triggers a remote key by using the remote key code. |

## lazy: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `lazy:{ACTION}`<br> `lazy:info:Lazy action executed.`<br> | Optional | No | Executes an action when all running animations are completed.  If no animations are currently running, the action is executed directly. |

## leave: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `leave` | None | No | Leaves the application by using the web history back function. |

## link: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `link:{URL}`<br> `link:http://msx.benzac.de/info/`<br> | None | **Yes** | Opens a link. |
| `link:replace:{URL}`<br> `link:replace:http://msx.benzac.de/info/`<br> | None | No | Opens a link by replacing the current URL. |
| `link:validate:{URL}`<br> `link:validate:http://msx.benzac.de/info/`<br> `link:validate:window:http://msx.benzac.de/info/`<br> | None | No | Opens a link by enforcing validation. |
| `link:window:{URL}`<br> `link:window:http://msx.benzac.de/info/`<br> | None | **Yes** | Opens a link in a window. |

## live: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `live` | None | No | Executes the live action. |

## log: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `log` | None | No | Toggles the log scene. |
| `log:clear` | None | No | Clears the log. |

## logger: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `logger:debug:{MESSAGE}`<br> `logger:debug:This is a debug message.`<br> | None | No | Logs a debug message. |
| `logger:error:{MESSAGE}`<br> `logger:error:This is an error message.`<br> | Optional | No | Logs (and shows) an error message. |
| `logger:info:{MESSAGE}`<br> `logger:info:This is an info message.`<br> | Optional | No | Logs (and shows) an info message. |
| `logger:success:{MESSAGE}`<br> `logger:success:This is a success message.`<br> | Optional | No | Logs (and shows) a success message. |
| `logger:warn:{MESSAGE}`<br> `logger:warn:This is a warning message.`<br> | Optional | No | Logs (and shows) a warning message. |

## main: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `main` | None | No | Shows the main scene (if the main scene is not already active). |

## menu: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `menu` | None | No | Toggles the context menu. |
| `menu:{URL}`<br> `menu:http://msx.benzac.de/info/data/guide/actions.json`<br> | None | **Yes** | Loads a menu. |
| `menu:data` | **Required** | **Yes** | Shows a menu. |
| `menu:json:{BASE64_ENCODED_JSON}` | None | No | Shows a menu by indicating the data inline as base64-encoded JSON. |
| `menu:request:interaction:{DATA_ID}`<br> `menu:request:interaction:custom_data_id`<br> | None | No | Loads a menu from the interaction plugin. |
| `menu:request:interaction:{DATA_ID}@{URL}`<br> `menu:request:interaction:custom_data_id@http://msx.benzac.de/interaction/template.html`<br> | None | No | Loads a menu from the interaction plugin (with ensuring that the interaction plugin is loaded). |
| `menu:request:player:{DATA_ID}`<br> `menu:request:player:custom_data_id`<br> | None | No | Loads a menu from the player (handled by the video/audio plugin). |
| `menu:user:{URL}`<br> `menu:user:http://msx.benzac.de/info/data/guide/actions.json` | None | **Yes** | Loads a menu in user mode. |

## notification: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `notification:{MESSAGE}`<br> `notification:This is a notification message.` | Optional | No | Commits a notification message to the video/audio and interaction plugin. |

## options: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `options` | None | No | Toggles the content options. |

## panel: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `panel:{URL}`<br> `panel:http://msx.benzac.de/info/data/guide/panel.json`<br> | None | **Yes** | Loads a panel. |
| `panel:data` | **Required** | **Yes** | Shows a panel. |
| `panel:json:{BASE64_ENCODED_JSON}` | None | No | Shows a panel by indicating the data inline as base64-encoded JSON. |
| `panel:request:interaction:{DATA_ID}`<br> `panel:request:interaction:custom_data_id`<br> | None | No | Loads a panel from the interaction plugin. |
| `panel:request:interaction:{DATA_ID}@{URL}`<br> `panel:request:interaction:custom_data_id@http://msx.benzac.de/interaction/template.html`<br> | None | No | Loads a panel from the interaction plugin (with ensuring that the interaction plugin is loaded). |
| `panel:request:player:{DATA_ID}`<br> `panel:request:player:custom_data_id`<br> | None | No | Loads a panel from the player (handled by the video/audio plugin). |
| `panel:user:{URL}`<br> `panel:user:http://msx.benzac.de/info/data/guide/panel.json`<br> | None | **Yes** | Loads a panel in user mode. |

## playback: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `playback` | None | No | Shows video/audio playback (closes overlaying content if a video/audio is active). |

## player: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `player` | None | No | Toggles the player scene. |
| `player:action` | None | No | Shows the player action. |
| `player:auto:{PLAYER_ACTION}`<br> `player:auto:next`<br> `player:auto:prev`<br> `player:auto:play`<br> `player:auto:pause`<br> `player:auto:play_pause`<br> `player:auto:forward`<br> `player:auto:rewind`<br> `player:auto:restart`<br> `player:auto:goto:first`<br> `player:auto:goto:last`<br> | Optional | No | Executes a player action in auto mode. |
| `player:background:{URL}`<br> `player:background:http://msx.benzac.de/img/bg1.jpg`<br> `player:background:none`<br> `player:background:default`<br> | None | No | Sets up an audio background (overrides the `background` property of the corresponding content item with a custom value). |
| `player:busy:start:{BUSY_ID}`<br> `player:busy:start:loading`<br> | None | No | Starts a player-related busy action. This busy action is automatically stopped if the player is stopped. A busy action can be pending for maximum 30 seconds (after this time, it is automatically stopped). |
| `player:busy:stop:{BUSY_ID}`<br> `player:busy:stop:loading`<br> | None | No | Stops a player-related busy action. |
| `player:button:{BUTTON_ID}:disable`<br> `player:button:content:disable`<br> `player:button:restart:disable`<br> `player:button:prev:disable`<br> `player:button:rewind:disable`<br> `player:button:play_pause:disable`<br> `player:button:forward:disable`<br> `player:button:next:disable`<br> `player:button:speed:disable`<br> | None | No | Disables a player button (all buttons except the eject button are supported). |
| `player:button:{BUTTON_ID}:enable`<br> `player:button:content:enable`<br> `player:button:restart:enable`<br> `player:button:prev:enable`<br> `player:button:rewind:enable`<br> `player:button:play_pause:enable`<br> `player:button:forward:enable`<br> `player:button:next:enable`<br> `player:button:speed:enable`<br> | None | No | Enables a player button (all buttons except the eject button are supported). |
| `player:button:{BUTTON_ID}:execute`<br>                                     `player:button:content:execute`<br> `player:button:restart:execute`<br> `player:button:prev:execute`<br> `player:button:rewind:execute`<br> `player:button:play_pause:execute`<br> `player:button:forward:execute`<br> `player:button:next:execute`<br> `player:button:speed:execute`<br> `player:button:eject:execute`<br> | None | No | Executes a player button. |
| `player:button:{BUTTON_ID}:focus`<br> `player:button:content:focus`<br> `player:button:restart:focus`<br> `player:button:prev:focus`<br> `player:button:rewind:focus`<br> `player:button:play_pause:focus`<br> `player:button:forward:focus`<br> `player:button:next:focus`<br> `player:button:speed:focus`<br> `player:button:eject:focus`<br> `player:button:none:focus`<br> `player:button:default:focus`<br> `player:button:invalid:focus`<br> | None | No | Focuses a player button. Additionally, the focus button for player execution events will be set. If the button `none` is used, no button is focused and the focus button for player execution events will be set `none`. If the button `default` is used, the default button is focused and the focus button for player execution events will also be set to default. If the button `invalid` (or any other invalid ID) is used, no button is focused, but the focus button for player execution events will be set to default. |
| `player:button:{BUTTON_ID}:reset`<br> `player:button:content:reset`<br> `player:button:restart:reset`<br> `player:button:prev:reset`<br> `player:button:rewind:reset`<br> `player:button:play_pause:reset`<br> `player:button:forward::reset`<br> `player:button:next:reset`<br> `player:button:speed:reset`<br> | None | No | Resets a player button (all buttons except the eject button are supported). |
| `player:button:{BUTTON_ID}:setup`<br> `player:button:content:setup`<br> `player:button:restart:setup`<br> `player:button:prev:setup`<br> `player:button:rewind:setup`<br> `player:button:play_pause:setup`<br> `player:button:forward:setup`<br> `player:button:next:setup`<br> `player:button:speed:setup`<br> | Optional | No | Sets up a player button (all buttons except the eject button are supported).  The data can contain a `key` property (of type `string`), an `icon` property (of type `string`), an `action` property (of type `string`), and an `enable` property (of type `boolean`). If some properties are not set, the default values are used.<br><br>**Note: If the `icon` property is set, the `action` property must also be set (and vice versa), otherwise the default values are used for both properties.** |
| `player:commit` | **Required** | No | Commits data to the video/audio plugin. |
| `player:commit:code` | Optional | No | Commits a code to the video/audio plugin. |
| `player:commit:dictionary` | Optional | No | Commits the dictionary to the video/audio plugin. |
| `player:commit:info` | Optional | No | Commits the system info to the video/audio plugin. |
| `player:commit:info:{DATA_TYPE}`<br> `player:commit:info:extended`<br> `player:commit:info:base`<br> `player:commit:info:application`<br> `player:commit:info:framework`<br> `player:commit:info:content`<br> `player:commit:info:dictionary`<br> `player:commit:info:screen`<br> `player:commit:info:time`<br> | Optional | No | Commits the extended system info (including URL parameters and system data) or a specific system info to the video/audio plugin. |
| `player:commit:message:{MESSAGE}`<br> `player:commit:message:This is a player message.`<br> | Optional | No | Commits a message to the video/audio plugin. |
| `player:commit:response:{REQUEST_ACTION}`<br> `player:commit:response:http://msx.benzac.de/services/echo.php`<br> `player:commit:response:user:{URL}`<br> `player:commit:response:accurate:{URL}`<br> `player:commit:response:user:accurate:{URL}`<br> `player:commit:response:request:interaction:{DATA_ID}`<br> `player:commit:response:request:interaction:{DATA_ID}@{URL}`<br>  `player:commit:response:silent:{URL}`<br> `player:commit:response:silent:user:{URL}`<br> `player:commit:response:silent:accurate:{URL}`<br> `player:commit:response:silent:user:accurate:{URL}`<br>                                   `player:commit:response:silent:request:interaction:{DATA_ID}`<br> `player:commit:response:silent:request:interaction:{DATA_ID}@{URL}`<br> | Optional | No | Commits a response to the video/audio plugin. |
| `player:commit:slider` | Optional | No | Commits the slider info to the video/audio plugin. |
| `player:commit:string:{TRANSFORM_TYPE}:{STRING}`<br> `player:commit:string:transform:Media Station X`<br> `player:commit:string:normalize:EW6l] 801JCU; )`<br> | Optional | No | Commits a transformed/normalized string to the video/audio plugin. |
| `player:commit:video` | Optional | No | Commits the video info to the video/audio plugin. |
| `player:commit:video:{DATA_TYPE}`<br> `player:commit:video:info`<br> `player:commit:video:data`<br> `player:commit:video:resume`<br> `player:commit:video:volume`<br> `player:commit:video:scene`<br> | Optional | No | Commits a specific video info to the video/audio plugin. |
| `player:content` | None | No | Shows the related content page (default action for the player button in the lower left corner). |
| `player:content:{REQUEST_ACTION}`<br> `player:content:http://msx.benzac.de/info/data/guide/videos.json`<br> `player:content:user:{URL}`<br> `player:content:request:interaction:{DATA_ID}`<br> `player:content:request:interaction:{DATA_ID}@{URL}`<br> | None | No | Loads a content page with the player back function. |
| `player:content:data` | **Required** | No | Shows a content page with the player back function. |
| `player:content:json:{BASE64_ENCODED_JSON}` | None | No | Shows a content page with the player back function by indicating the data inline as base64-encoded JSON. |
| `player:control:action:{ACTION}`<br> `player:control:action:info:Custom player control action executed.`<br> `player:control:action:default`<br> | None | No | Sets up a custom player control action (replacement for the action that is executed if the OK key is pressed while the video/audio is in foreground).<br><br>**Note: For control actions, it is not possible to provide an action-related `data` property. If you want to execute a control data action, please use the `execute:fetch:{URL}` action, alternatively.** |
| `player:default` | None | No | Executes the following default player action. Shows the player with applied remote key `execute`. |
| `player:eject` | None | No | Stops/Ejects the video/audio. |
| `player:execute_pause:{ACTION}`<br> `player:execute_pause:info:Video is paused.`<br> | Optional | No | Executes an action if the player is paused. |
| `player:execute_play:{ACTION}`<br> `player:execute_play:info:Video is playing.`<br> | Optional | No | Executes an action if the player is playing. |
| `player:forward` | None | No | Jumps forward in the playback. |
| `player:goto:{ITEM_ID}`<br> `player:goto:content_item_id`<br> | None | No | Plays the item with the indicated ID. |
| `player:goto:current` | None | No | Replays the current item (by retuning it). |
| `player:goto:first` | None | No | Plays the first item. |
| `player:goto:index:{ITEM_INDEX}`<br> `player:goto:index:0`<br> | None | No | Plays the item with the indicated index. |
| `player:goto:last` | None | No | Plays the last item. |
| `player:goto:next` | None | No | Plays the next item. |
| `player:goto:number:{ITEM_NUMBER}`<br> `player:goto:number:1`<br> | None | No | Plays the item with the indicated number. |
| `player:goto:prev` | None | No | Plays the previous item. |
| `player:goto:random` | None | No | Plays a random item. |
| `player:goto:swap` | None | No | Plays the swap item. |
| `player:hide` | None | No | Hides the player. |
| `player:hide_pause` | None | No | Hides the player if it is paused. |
| `player:hide_play` | None | No | Hides the player if it is playing. |
| `player:info:headline:{HEADLINE}`<br> `player:info:headline:Additional video/audio information headline`<br> `player:info:headline:default`<br> | None | No | Sets up a player info headline (only available for extended players). |
| `player:info:image:{URL}`<br> `player:info:image:http://msx.benzac.de/img/icon.png`<br> `player:info:image:default`<br> | None | No | Sets up a player info image (only available for extended players). |
| `player:info:overlay:{OVERLAY}`<br> `player:info:overlay:full`<br> `player:info:overlay:default`<br> | None | No | Sets up the player info overlay (only available for extended players). |
| `player:info:round:disable`<br> | None | No | Disables rounded corners of the info image if the rounded style is used (only available for extended players). |
| `player:info:round:enable`<br> | None | No | Enables rounded corners of the info image if the rounded style is used (only available for extended players). |
| `player:info:size:{SIZE}`<br> `player:info:size:small`<br> `player:info:size:medium`<br> `player:info:size:large`<br> `player:info:size:extra-large`<br> `player:info:size:default`<br> | None | No | Sets up the size of the player info image area (only available for extended players). |
| `player:info:text:{TEXT}`<br> `player:info:text:Additional video/audio information text.`<br> `player:info:text:default`<br> | None | No | Sets up a player info text (only available for extended players). |
| `player:invalidate` | None | No | Invalidates the player (hides the player if it is not extended). |
| `player:label:content:{LABEL}`<br> `player:label:content:Custom player content label`<br> `player:label:content:default`<br> | None | No | Sets up the player content label (overrides the `playerLabel` property of the corresponding content item with a custom value). |
| `player:label:duration:{LABEL}`<br> `player:label:duration:Custom player duration label`<br> `player:label:duration:Custom player duration label ({VALUE})`<br> `player:label:duration:default`<br> | None | No | Sets up the player duration label (overrides the default duration value of the video/audio player with a custom value). If the custom value contains the `{VALUE}` keyword, it is replaced with the default value. |
| `player:label:extension:{LABEL}`<br> `player:label:extension:{ico:live-tv}`<br> `player:label:extension:default`<br> | None | No | Sets up an additional player extension label. |
| `player:label:position:{LABEL}`<br> `player:label:position:Custom player position label`<br> `player:label:position:Custom player position label ({VALUE})`<br> `player:label:position:default`<br> | None | No | Sets up the player position label (overrides the default position value of the video/audio player with a custom value). If the custom value contains the `{VALUE}` keyword, it is replaced with the default value. |
| `player:label:speed:{LABEL}`<br> `player:label:speed:Custom player speed label`<br> `player:label:speed:Custom player speed label ({VALUE})`<br> `player:label:speed:default`<br> | None | No | Sets up the player speed label (overrides the default speed value of the video/audio player with a custom value). If the custom value contains the `{VALUE}` keyword, it is replaced with the default value. |
| `player:menu:{REQUEST_ACTION}`<br> `player:menu:http://msx.benzac.de/info/data/guide/actions.json`<br> `player:menu:user:{URL}`<br> `player:menu:request:interaction:{DATA_ID}`<br> `player:menu:request:interaction:{DATA_ID}@{URL}`<br> | None | No | Loads a menu with the player back function. |
| `player:menu:data` | **Required** | No | Shows a menu with the player back function. |
| `player:menu:json:{BASE64_ENCODED_JSON}` | None | No | Shows a menu with the player back function by indicating the data inline as base64-encoded JSON. |
| `player:next` | None | No | Plays the next related item. |
| `player:pause` | None | No | Pauses the video/audio. |
| `player:play` | None | No | Plays the video/audio. |
| `player:play_pause` | None | No | Toggles the video/audio playback. |
| `player:prev` | None | No | Plays the previous related item. |
| `player:progress:color:{COLOR}`<br> `player:progress:color:msx-red`<br> `player:progress:color:default`<br> | None | No | Sets up the player progress color. |
| `player:progress:duration:{PROGRESS_DURATION}`<br> `player:progress:duration:60`<br> `player:progress:duration:-1`<br> | None | No | Sets up the player progress duration in seconds (overrides the default duration value of the video/audio player with a custom value). The duration `-1` is default and uses the value of the video/audio player.<br><br>**Note: Please note that this duration is only used in the player controls (it is not used for trigger and resume functions, nor for live playback objects).** |
| `player:progress:marker:disable` | None | No | Disables the player progress marker. |
| `player:progress:marker:enable` | None | No | Enables the player progress marker. |
| `player:progress:marker:invalidate` | None | No | Invalidates the player progress marker (marker will be unfocused). |
| `player:progress:position:{PROGRESS_POSITION}`<br> `player:progress:position:0`<br> `player:progress:position:-1`<br> | None | No | Sets up the player progress position (overrides the default position value of the video/audio player with a custom value). The position `-1` is default and uses the value of the video/audio player.<br><br>**Note: Please note that this position is only used in the player controls (it is not used for trigger and resume functions, nor for live playback objects).** |
| `player:progress:type:{PROGRESS_TYPE}`<br> `player:progress:type:position`<br> `player:progress:type:position:{POSITION} {ico:image}`<br> `player:progress:type:number`<br> `player:progress:type:number:{NUMBER} {ico:image}`<br> `player:progress:type:percentage`<br> `player:progress:type:time:{LIVE_DURATION_FORMAT}`<br> `player:progress:type:text:{LIVE_DURATION_TEXT}`<br> `player:progress:type:reverse:position`<br> `player:progress:type:reverse:position:{POSITION} {ico:image}`<br> `player:progress:type:reverse:number`<br> `player:progress:type:reverse:number:{NUMBER} {ico:image}`<br> `player:progress:type:reverse:percentage`<br> `player:progress:type:reverse:time:{LIVE_DURATION_FORMAT}`<br> `player:progress:type:reverse:text:{LIVE_DURATION_TEXT}`<br> `player:progress:type:difference:position`<br> `player:progress:type:difference:position:{POSITION} {ico:image}`<br> `player:progress:type:difference:number`<br> `player:progress:type:difference:number:{NUMBER} {ico:image}`<br> `player:progress:type:difference:percentage`<br> `player:progress:type:difference:time:{LIVE_DURATION_FORMAT}`<br> `player:progress:type:difference:text:{LIVE_DURATION_TEXT}`<br> `player:progress:type:fix:Fix progress label`<br> `player:progress:type:default`<br> | None | No | Sets up the player progress type (`default` is like `time:hh:mm:ss`). For the type that starts with (or contains) `position:`, the `{POSITION}` part is automatically replaced with the corresponding position value. For the type that starts with (or contains) `number:`, the `{NUMBER}` part is automatically replaced with the corresponding number value (which is the incremented position value). |
| `player:refresh` | None | No | Refreshes all player values. |
| `player:reset` | None | No | Resets custom player values that have been set at runtime. |
| `player:restart` | None | No | Restarts the video/audio. |
| `player:rewind` | None | No | Jumps rewind in the playback. |
| `player:seek:{VALUE}`<br> `player:seek:10`<br> `player:seek:+10`<br> `player:seek:-10`<br> `player:seek:10%`<br> `player:seek:+10%`<br> `player:seek:-10%`<br> | None | No | Executes a seek action. |
| `player:show` | None | No | Shows the player. |
| `player:show:{REMOTE_KEY}`<br>                                     `player:show:left`<br> `player:show:right`<br> `player:show:up`<br> `player:show:down`<br> `player:show:execute`<br> | None | No | Shows the player with applied remote key. |
| `player:show:code:{REMOTE_KEY_CODE}`<br> `player:show:code:37`<br> `player:show:code:39`<br> `player:show:code:38`<br> `player:show:code:40`<br> `player:show:code:13`<br> | None | No | Shows the player with applied remote key code. |
| `player:show_pause` | None | No | Shows the player if it is paused. |
| `player:show_pause:{REMOTE_KEY}`<br> `player:show_pause:left`<br> `player:show_pause:right`<br> `player:show_pause:up`<br> `player:show_pause:down`<br> `player:show_pause:execute`<br> | None | No | Shows the player if it is paused with applied remote key. |
| `player:show_pause:code:{REMOTE_KEY_CODE}`<br> `player:show_pause:code:37`<br> `player:show_pause:code:39`<br> `player:show_pause:code:38`<br> `player:show_pause:code:40`<br> `player:show_pause:code:13`<br> | None | No | Shows the player if it is paused with applied remote key code. |
| `player:show_play` | None | No | Shows the player if it is playing. |
| `player:show_play:{REMOTE_KEY}`<br> `player:show_play:left`<br> `player:show_play:right`<br> `player:show_play:up`<br> `player:show_play:down`<br> `player:show_play:execute`<br> | None | No | Shows the player if it is playing with applied remote key. |
| `player:show_play:code:{REMOTE_KEY_CODE}`<br> `player:show_play:code:37`<br> `player:show_play:code:39`<br> `player:show_play:code:38`<br> `player:show_play:code:40`<br> `player:show_play:code:13`<br> | None | No | Shows the player if it is playing with applied remote key code. |
| `player:speed` | None | No | Shows the playback speed panel. |
| `player:speed:dec` | None | No | Decreases the playback speed. |
| `player:speed:inc` | None | No | Increases the playback speed. |
| `player:speed:set:{VALUE}`<br> `player:speed:set:0.125`<br> `player:speed:set:0.25`<br> `player:speed:set:0.5`<br> `player:speed:set:1`<br> `player:speed:set:2`<br> `player:speed:set:4`<br> `player:speed:set:8`<br> | None | No | Sets the playback speed. |
| `player:stop` | None | No | Stops/Ejects the video/audio. |
| `player:sync` | None | No | Synchronizes the video/audio with the corresponding content item (i.e. the corresponding content item is selected). This action is automatically executed when a new video/audio is played. |
| `player:ticking:restart` | None | No | Restarts the player ticking (the next tick will be `0t`). |
| `player:video:clear` | None | No | Clears all custom video values. |
| `player:video:duration:{VIDEO_DURATION}`<br> `player:video:duration:60`<br> | None | No | Sets up a custom video duration in seconds.  If a custom duration is set, only that duration is displayed (and used for trigger and resume functions, as well as live playback objects) during playback.  It can be used for live data or ads to disable/override the progress feature. The duration `-1` re-enables the display feature. |
| `player:video:position:{VIDEO_POSITION}`<br> `player:video:position:0`<br> | None | No | Sets up a custom video position in seconds.  If a custom position is set, only that position is displayed (and used for trigger and resume functions, as well as live playback objects) during playback and it is not possible to seek to another position (instead a `custom:video:seek` event is triggered).  It can be used for live data or ads to disable/override the progress feature. The position `-1` re-enables the display and seek feature. The position `-2` only re-enables the display feature (it is still not possible to seek to another position). |
| `player:video:speed:{VIDEO_SPEED}`<br> `player:video:speed:1`<br> | None | No | Sets up a custom video speed.  If a custom speed is set, only that speed is displayed during playback and it is not possible to change it (instead a `custom:video:speed` event is triggered).  It can be used for live data or ads to disable/override the speed feature. The speed `-1` re-enables the display and change feature. The speed `-2` only re-enables the display feature (it is still not possible to change it).<br><br>**Note: The actual video speed is not changed. For example, if the custom video speed is set to x0.5, the actual video may still be playing at normal speed.** |
| `player:video:state:{VIDEO_STATE}`<br> `player:video:state:2`<br> | None | No | Sets up a custom video state.  If a custom state is set, only that state is displayed during playback and it is not possible to change it (instead a `custom:video:play` or `custom:video:pause` event is triggered).  It can be used for live data or ads to disable/override the play/pause feature. - `-2`: Read-only (only re-enables the display feature) - `-1`: Clear (re-enables the display and change feature) - `1`: Stopped (not supported) - `2`: Playing - `3`: Paused<br><br>**Note: The actual video state is not changed. For example, if the custom video state is paused, the actual video may still be in the playing state.** |

## playlist: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `playlist:{URL}`<br> `playlist:http://msx.benzac.de/info/data/guide/videos.json`<br> | None | **Yes** | Loads a playlist. |
| `playlist:auto:{URL}`<br> `playlist:auto:http://msx.benzac.de/info/data/guide/videos.json`<br> | None | No | Loads a playlist in auto mode. |
| `playlist:auto:data` | **Required** | No | Plays a playlist in auto mode. |
| `playlist:auto:json:{BASE64_ENCODED_JSON}` | None | No | Plays a playlist in auto mode by indicating the data inline as base64-encoded JSON. |
| `playlist:auto:request:interaction:{DATA_ID}`<br> `playlist:auto:request:interaction:custom_data_id`<br> | None | No | Loads a playlist from the interaction plugin in auto mode. |
| `playlist:auto:request:interaction:{DATA_ID}@{URL}`<br> `playlist:auto:request:interaction:custom_data_id@http://msx.benzac.de/interaction/template.html`<br> | None | No | Loads a playlist from the interaction plugin in auto mode (with ensuring that the interaction plugin is loaded). |
| `playlist:auto:request:player:{DATA_ID}`<br> `playlist:auto:request:player:custom_data_id`<br> | None | No | Loads a playlist from the player in auto mode (handled by the video/audio plugin). |
| `playlist:auto:user:{URL}`<br> `playlist:auto:user:http://msx.benzac.de/info/data/guide/videos.json`<br> | None | No | Loads a playlist in auto and user mode. |
| `playlist:data` | **Required** | **Yes** | Plays a playlist. |
| `playlist:json:{BASE64_ENCODED_JSON}` | None | No | Plays a playlist by indicating the data inline as base64-encoded JSON. |
| `playlist:request:interaction:{DATA_ID}`<br> `playlist:request:interaction:custom_data_id`<br> | None | No | Loads a playlist from the interaction plugin. |
| `playlist:request:interaction:{DATA_ID}@{URL}`<br> `playlist:request:interaction:custom_data_id@http://msx.benzac.de/interaction/template.html`<br> | None | No | Loads a playlist from the interaction plugin (with ensuring that the interaction plugin is loaded). |
| `playlist:request:player:{DATA_ID}`<br> `playlist:request:player:custom_data_id`<br> | None | No | Loads a playlist from the player (handled by the video/audio plugin). |
| `playlist:user:{URL}`<br> `playlist:user:http://msx.benzac.de/info/data/guide/videos.json`<br> | None | **Yes** | Loads a playlist in user mode. |

## quiet: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `quiet` | None | No | Stops a running video/audio. |

## release: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `release:content` | None | No | Releases the current content data (clears caches and removes references).<br><br>**Note: Please note that once the data has been released, it cannot be reloaded using the `reload` action. However, the `replace` action can still be used.** |
| `release:menu` | None | No | Releases the current menu data (clears caches and removes references).<br><br>**Note: Please note that once the data has been released, it cannot be reloaded using the `reload` action. However, the `replace` action can still be used.** |
| `release:panel` | None | No | Releases the current panel data (clears caches and removes references).<br><br>**Note: Please note that once the data has been released, it cannot be reloaded using the `reload` action. However, the `replace` action can still be used.** |

## reload: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `reload` | None | **Yes** | Reloads the application. |
| `reload:content` | None | **Yes** | Reloads the current content data. |
| `reload:menu` | None | **Yes** | Reloads the current menu data. |
| `reload:panel` | None | **Yes** | Reloads the current panel data. |

## reopen: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `reopen:{PARAMS}`<br> `reopen:key1=value1&key2=value2`<br> | None | No | Reopens the application link with new URL parameters. |

## replace: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `replace:content:{CONTENT_FLAG}:{REQUEST_ACTION}`<br>                                    `replace:content:content_flag:http://msx.benzac.de/info/data/guide/videos.json`<br> `replace:content:content_flag:user:{URL}`<br> `replace:content:content_flag:request:interaction:{DATA_ID}`<br> `replace:content:content_flag:request:interaction:{DATA_ID}@{URL}`<br> `replace:content:content_flag:json:{BASE64_ENCODED_JSON}`<br> | None | No | Replaces (and reloads) the current content data.<br><br>**Note: The current content data `flag` property must match the indicated flag, otherwise the content data will not be replaced.** |
| `replace:menu:{CONTENT_FLAG}:{REQUEST_ACTION}`<br> `replace:menu:content_flag:http://msx.benzac.de/info/data/guide/menu.json`<br> `replace:menu:content_flag:user:{URL}`<br> `replace:menu:content_flag:request:interaction:{DATA_ID}`<br> `replace:menu:content_flag:request:interaction:{DATA_ID}@{URL}`<br> `replace:menu:content_flag:json:{BASE64_ENCODED_JSON}`<br> | None | No | Replaces (and reloads) the current menu data.<br><br>**Note: The current menu data `flag` property must match the indicated flag, otherwise the menu data will not be replaced.** |
| `replace:panel:{CONTENT_FLAG}:{REQUEST_ACTION}`<br> `replace:panel:content_flag:http://msx.benzac.de/info/data/guide/panel.json`<br> `replace:panel:content_flag:user:{URL}`<br> `replace:panel:content_flag:request:interaction:{DATA_ID}`<br> `replace:panel:content_flag:request:interaction:{DATA_ID}@{URL}`<br> `replace:panel:content_flag:json:{BASE64_ENCODED_JSON}`<br> | None | No | Replaces (and reloads) the current panel data.<br><br>**Note: The current panel data `flag` property must match the indicated flag, otherwise the panel data will not be replaced.** |

## response: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `response:{REQUEST_ID}`<br> `response:rq10_1541593729708`<br> | Optional | No | Handles a response for a pending request. |

## restart: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `restart` | None | No | Restarts the application. |

## resume: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `resume:cancel` | None | No | Cancels an ongoing resume process. |
| `resume:clear` | None | No | Clears all resume keys. |
| `resume:clear:{RESUME_KEY}`<br> `resume:clear:content_item_id`<br> `resume:clear:http://msx.benzac.de/media/video1.mp4`<br> `resume:clear:custom_resume_key`<br> | None | No | Clears a specific resume key. |
| `resume:clear:context:{RESUME_CONTEXT}`<br> `resume:clear:context:custom_resume_context`<br> | None | No | Clears all resume keys for a specific context. |
| `resume:clear:context:hash:{RESUME_CONTEXT_HASH_CODE}`<br> `resume:clear:context:hash:105224747`<br> | None | No | Clears all resume keys for a specific context by using the context hash code. |
| `resume:clear:current` | None | No | Clears the current resume key. |
| `resume:clear:hash:{RESUME_KEY_HASH_CODE}`<br> `resume:clear:hash:511673863`<br> | None | No | Clears a specific resume key by using the resume key hash code. |
| `resume:import` | **Required** | No | Imports resume keys (the data must contain a `properties` property that should contain the resume keys as key-value pairs of type `string` in the format `"{RESUME_KEY_HASH_CODE}": "{RESUME_VALUE}"`). |
| `resume:position:{RESUME_POSITION}`<br> `resume:position:10`<br> `resume:position:10%`<br> `resume:position:none`<br> | None | No | Sets up a fix resume position in seconds or percentage to start the current content from there. If the resume position is set to `none`, no resuming is performed. This action must be called after a `video:{URL}`, `audio:{URL}`, or `playlist:{URL}` action in order to take effect. |

## selection: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `selection` | None | No | Executes the selection action. |

## settings: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `settings` | None | No | Toggles the settings scene. |
| `settings:about` | None | No | Shows the about panel. |
| `settings:animate` | None | No | Shows the animation settings panel. |
| `settings:animate:{VALUE}`<br> `settings:animate:0`<br> `settings:animate:1`<br> `settings:animate:2`<br> | None | No | Sets the animation settings. - `0`: Off - `1`: JS - `2`: CSS |
| `settings:eject_timeout` | None | No | Shows the eject timeout settings panel. |
| `settings:eject_timeout:{VALUE}`<br> `settings:eject_timeout:0`<br> `settings:eject_timeout:7200000`<br> `settings:eject_timeout:14400000`<br> `settings:eject_timeout:28800000`<br> `settings:eject_timeout:43200000`<br> | None | No | Sets the eject timeout settings (in milliseconds). Value must be greater than or equal to `3600000` (1 hr), otherwise it is interpreted as `0` (off). |
| `settings:hover_effect` | None | No | Shows the hover effect settings panel. |
| `settings:hover_effect:{VALUE}`<br> `settings:hover_effect:0`<br> `settings:hover_effect:1`<br> `settings:hover_effect:2`<br> | None | No | Sets the hover effect settings. - `0`: Off - `1`: Plain - `2`: Preselect |
| `settings:immersive_mode` | None | No | Shows the immersive mode settings panel. |
| `settings:immersive_mode:{VALUE}`<br> `settings:immersive_mode:0`<br> `settings:immersive_mode:1`<br> | None | No | Sets the immersive mode settings. - `0`: Off - `1`: On |
| `settings:input` | None | No | Shows the input settings panel. |
| `settings:input:{VALUE}`<br> `settings:input:0`<br> `settings:input:1`<br> `settings:input:2`<br> `settings:input:3`<br> `settings:input:4`<br> | None | No | Sets the input settings. - `0`: Off - `1`: Remote Only - `2`: Click & Swipe - `3`: Drag & Drop - `4`: Move & Execute |
| `settings:layout` | None | No | Shows the layout settings panel. |
| `settings:layout:{VALUE}`<br> `settings:layout:0.5`<br> `settings:layout:0.75`<br> `settings:layout:1`<br> `settings:layout:1.5`<br> `settings:layout:3`<br> | None | No | Sets the layout settings. - `0.5`: 360p - `0.75`: 540p - `1`: 720p - `1.5`: 1080p - `3`: 2160p |
| `settings:menu_button` | None | No | Shows the menu button settings panel. |
| `settings:random_playback` | None | No | Shows the random playback settings panel. |
| `settings:random_playback:{VALUE}`<br> `settings:random_playback:0`<br> `settings:random_playback:1`<br> | None | No | Sets the random playback settings. - `0`: Off - `1`: On |
| `settings:remote` | None | No | Shows the remote settings panel. |
| `settings:remote:{VALUE}`<br> `settings:remote:0`<br> `settings:remote:1`<br> `settings:remote:2`<br> `settings:remote:3`<br> `settings:remote:4`<br> | None | No | Sets the remote settings. - `0`: Off - `1`: Minimalistic - `2`: Navigation Frame - `3`: Corner Controls - `4`: Complex |
| `settings:reset` | None | No | Shows the reset settings panel. |
| `settings:resolution` | None | No | Shows the resolution settings panel. |
| `settings:rounded_style` | None | No | Shows the rounded style settings panel. |
| `settings:rounded_style:{VALUE}`<br> `settings:rounded_style:0`<br> `settings:rounded_style:1`<br> | None | No | Sets the rounded style settings. - `0`: Off - `1`: On |
| `settings:scale` | None | No | Shows the scale settings panel. |
| `settings:scale:{VALUE}`<br> `settings:scale:default`<br> `settings:scale:auto`<br> `settings:scale:device`<br> `settings:scale:0.75`<br> `settings:scale:fix:0.75`<br> | None | No | Sets the scale settings (viewport settings). - `default`: No scaling - `auto`: Auto detect (with resize adjustment) - `device`: Set scale factor to device width (not fixed) - `{NUMBER}`: Set scale factor to specific number (not fixed) - `fix:{NUMBER}`: Set scale factor to specific number (fixed) |
| `settings:screen_saver` | None | No | Shows the screen saver settings panel. |
| `settings:sleep_timeout` | None | No | Shows the sleep timeout settings panel. |
| `settings:sleep_timeout:{VALUE}`<br> `settings:sleep_timeout:0`<br> `settings:sleep_timeout:60000`<br> `settings:sleep_timeout:300000`<br> `settings:sleep_timeout:600000`<br> `settings:sleep_timeout:900000`<br> | None | No | Sets the sleep timeout settings (in milliseconds). Value must be greater than or equal to `60000` (1 min), otherwise it is interpreted as `0` (off). |
| `settings:slideshow_interval` | None | No | Shows the slideshow interval settings panel. |
| `settings:slideshow_interval:{VALUE}`<br> `settings:slideshow_interval:1000`<br> `settings:slideshow_interval:5000`<br> `settings:slideshow_interval:10000`<br> `settings:slideshow_interval:20000`<br> `settings:slideshow_interval:40000`<br> | None | No | Sets the slideshow interval settings (in milliseconds). - `1000`: Very Fast - `5000`: Fast - `10000`: Normal - `20000`: Slow - `40000`: Very Slow |
| `settings:start` | None | No | Shows the start parameter panel. |
| `settings:transform` | None | No | Shows the transformation settings panel. |
| `settings:transform:{VALUE}`<br> `settings:transform:0`<br> `settings:transform:1`<br> `settings:transform:2`<br> | None | No | Sets the transformation settings. - `0`: Off - `1`: 2D - `2`: 3D |
| `settings:validate` | None | No | Shows the validate settings panel. |
| `settings:validate_links` | None | No | Shows the validate links settings panel. |
| `settings:validate_links:{VALUE}`<br> `settings:validate_links:0`<br> `settings:validate_links:1`<br> | None | No | Sets the validate links settings. - `0`: Off - `1`: On |
| `settings:visual_effects` | None | No | Shows the visual effects settings panel. |
| `settings:visual_execution` | None | No | Shows the visual execution settings panel. |
| `settings:visual_execution:{VALUE}`<br> `settings:visual_execution:0`<br> `settings:visual_execution:1`<br> | None | No | Sets the visual execution settings. - `0`: Off - `1`: On |
| `settings:zoom` | None | No | Shows the zoom settings panel. |
| `settings:zoom:{VALUE}`<br> `settings:zoom:default`<br> `settings:zoom:auto`<br> `settings:zoom:0.75`<br> | None | No | Sets the zoom settings. - `default`: No zooming - `auto`: Auto detect (with resize adjustment) - `{NUMBER}`: Set zoom factor to specific number |

## sleep: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `sleep` | None | No | Forces the screen saver to be active (even if a video/audio/slideshow is active). |

## slider: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `slider:action:{ACTION}`<br> `slider:action:info:Custom image action executed.`<br> `slider:action:default`<br> | None | No | Sets up a custom slideshow image action of the current image (replacement for the default slideshow image action).<br><br>**Note: For image actions, it is not possible to provide an action-related `data` property. If you want to execute an image data action, please use the `execute:fetch:{URL}` action, alternatively.** |
| `slider:break` | None | No | Pauses the slideshow if it is playing. |
| `slider:color:{COLOR}`<br> `slider:color:msx-blue`<br> `slider:color:default`<br> | None | No | Sets up the slideshow image color of the current image (overrides the `imageColor` property of the corresponding content item with a custom value). |
| `slider:continue` | None | No | Plays the slideshow if it is not playing. |
| `slider:default` | None | No | Executes the following default slideshow action. Shows the slideshow labels or toggles the slideshow playback (or shows a warning if no action is available). |
| `slider:eject` | None | No | Stops/Ejects the slideshow. |
| `slider:execute:{ACTION}`<br> `slider:execute:info:Slideshow action executed.`<br> | Optional | No | Shows the slideshow labels or executes the indicated slideshow action (if labels are already visible or not present). |
| `slider:extension:{LABEL}`<br> `slider:extension:Custom image extension label`<br> `slider:extension:default`<br> | None | No | Sets up the slideshow image extension label of the current image. |
| `slider:goto:{ITEM_ID}`<br> `slider:goto:content_item_id`<br> | None | No | Goes to the item with the indicated ID. |
| `slider:goto:first` | None | No | Goes to the first item. |
| `slider:goto:index:{ITEM_INDEX}`<br> `slider:goto:index:0`<br> | None | No | Goes to the item with the indicated index. |
| `slider:goto:last` | None | No | Goes to the last item. |
| `slider:goto:next` | None | No | Goes to the next item. |
| `slider:goto:number:{ITEM_NUMBER}`<br> `slider:goto:number:1`<br> | None | No | Goes to the item with the indicated number. |
| `slider:goto:prev` | None | No | Goes to the previous item. |
| `slider:icon:{ICON}`<br> `slider:icon:info`<br> `slider:icon:default`<br> | None | No | Sets up a custom slideshow image icon of the current image.<br><br>**Note: A custom slideshow image action must be available for the icon to be visible.** |
| `slider:label:{LABEL}`<br> `slider:label:Custom image label`<br> `slider:label:default`<br> | None | No | Sets up the slideshow image label of the current image (overrides the `imageLabel` property of the corresponding content item with a custom value).<br><br>**Note: Only if the `imageLabel` property has been set (to a non `null` value) in the corresponding content item, it can be updated.** |
| `slider:labels:hide` | None | No | Hides the slideshow labels. |
| `slider:labels:play_pause` | None | No | Shows the slideshow labels or toggles the slideshow playback. |
| `slider:labels:show` | None | No | Shows the slideshow labels. |
| `slider:labels:toggle` | None | No | Toggles the slideshow labels. |
| `slider:next` | None | No | Goes to the next item without showing the labels. |
| `slider:options` | None | No | Toggles the slideshow image options. |
| `slider:options:execute` | None | No | Executes the selected slideshow image option item. |
| `slider:options:select:{ITEM_ID}`<br> `slider:options:select:slideshow`<br> `slider:options:select:right_rotation`<br> `slider:options:select:left_rotation`<br> `slider:options:select:full_rotation`<br> `slider:options:select:reset_rotation`<br> | None | No | Selects a slideshow image option item.<br><br>**Note: This action does not focus the item, it just notifies the slideshow which item is selected.** |
| `slider:pause` | None | No | Pauses the slideshow. |
| `slider:play` | None | No | Plays the slideshow. |
| `slider:play_pause` | None | No | Toggles the slideshow playback. |
| `slider:prev` | None | No | Goes to the previous item without showing the labels. |
| `slider:reset` | None | No | Resets custom slideshow image values of the current image that have been set at runtime. |
| `slider:restart` | None | No | Restarts the slideshow. |
| `slider:rotate:{ROTATION_VALUE}`<br> `slider:rotate:right`<br> `slider:rotate:left`<br> `slider:rotate:full`<br> `slider:rotate:full-right`<br> `slider:rotate:full-left`<br> `slider:rotate:reset`<br> `slider:rotate:-270`<br> `slider:rotate:-180`<br> `slider:rotate:-90`<br> `slider:rotate:0`<br> `slider:rotate:90`<br> `slider:rotate:180`<br> `slider:rotate:270`<br> | None | No | Rotates the current slideshow image.<br><br>**Note: If the rotation value is a number (which must be divisible by 90), it is applied as an absolute rotation in degrees.** |
| `slider:stop` | None | No | Stops/Ejects the slideshow. |
| `slider:sync` | None | No | Synchronizes the current slideshow image with the corresponding content item (i.e. the corresponding content item is selected in the background). This action is automatically executed when the slideshow is stopped/ejected. |

## slideshow: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `slideshow:{URL}`<br> `slideshow:http://msx.benzac.de/info/data/guide/images.json`<br> | None | **Yes** | Loads a slideshow. |
| `slideshow:data` | **Required** | **Yes** | Shows a slideshow. |
| `slideshow:json:{BASE64_ENCODED_JSON}` | None | No | Shows a slideshow by indicating the data inline as base64-encoded JSON. |
| `slideshow:request:interaction:{DATA_ID}`<br> `slideshow:request:interaction:custom_data_id`<br> | None | No | Loads a slideshow from the interaction plugin. |
| `slideshow:request:interaction:{DATA_ID}@{URL}`<br> `slideshow:request:interaction:custom_data_id@http://msx.benzac.de/interaction/template.html`<br> | None | No | Loads a slideshow from the interaction plugin (with ensuring that the interaction plugin is loaded). |
| `slideshow:request:player:{DATA_ID}`<br> `slideshow:request:player:custom_data_id`<br> | None | No | Loads a slideshow from the player (handled by the video/audio plugin). |
| `slideshow:user:{URL}`<br> `slideshow:user:http://msx.benzac.de/info/data/guide/images.json`<br> | None | **Yes** | Loads a slideshow in user mode. |

## start: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `start` | **Required** | **Yes** | Sets up a start parameter. |

## still: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `still` | None | No | Stops a running slideshow. |

## success: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `success:{MESSAGE}`<br> `success:This is a success message.`<br> | None | **Yes** | Shows a success message. |

## system: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `system:hbbtv:{SYSTEM_ACTION}`<br> `system:hbbtv:show`<br> `system:hbbtv:hide`<br> `system:hbbtv:launch:{APP_ID}`<br> `system:hbbtv:foreground`<br> `system:hbbtv:background`<br> `system:hbbtv:transient`<br> `system:hbbtv:update`<br> | None | No | Executes an HbbTV-related action. |
| `system:lg:{SYSTEM_ACTION}`<br> `system:lg:launch:{APP_ID}`<br> | Optional | No | Executes an LG-related action. |
| `system:netcast:{SYSTEM_ACTION}`<br> `system:netcast:menu`<br> `system:netcast:ratio`<br> | None | No | Executes a Netcast-related action. |
| `system:options` | None | No | Shows the system options. |
| `system:samsung:{SYSTEM_ACTION}`<br> `system:samsung:launch:{APP_ID}`<br> | None | No | Executes a Samsung-related action. |
| `system:tizen:{SYSTEM_ACTION}`<br> `system:tizen:launch`<br> `system:tizen:launch:{APP_ID}`<br> `system:tizen:keys:mapped`<br> `system:tizen:keys:unmapped`<br> | Optional | No | Executes a Tizen-related action. |
| `system:tvx:{SYSTEM_ACTION}`<br> `system:tvx:about`<br> `system:tvx:settings`<br> `system:tvx:restart`<br> `system:tvx:exit`<br> `system:tvx:log`<br> `system:tvx:log:clear`<br> `system:tvx:system_log`<br> `system:tvx:system_log:clear`<br> `system:tvx:options`<br> `system:tvx:launch`<br> `system:tvx:launch:{APP_ID}`<br> | Optional | No | Executes a TVX-related action (Android-/iOS-related action). |
| `system:uwp:{SYSTEM_ACTION}`<br> `system:uwp:launch:{APP_ID}`<br> | None | No | Executes a UWP-related action (Universal Windows Platform action). |

## test: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `test` | None | No | Toggles the test scene. |

## time: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `time:clear` | None | No | Clears the time and zone offset. |
| `time:init` | **Required** | No | Initializes the time and/or zone offset (the data must contain a `time` and/or `timeZone` property). |
| `time:set` | **Required** | No | Sets a fix time and/or zone offset (the data must contain an `offset` and/or `zoneOffset` property). |

## trigger: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `trigger:{TRIGGER_KEY}:{ACTION}`<br> `trigger:load:info:Video loaded.`<br> `trigger:ready:info:Video ready.`<br> `trigger:start:info:Video started.`<br> `trigger:ending:info:Video is ending (video is playing and 10 seconds or less are remaining).`<br> `trigger:end:info:Video ended.`<br> `trigger:complete:info:Video completed.`<br> `trigger:10:info:10 seconds of video passed.`<br> `trigger:10%:info:10% of video passed.`<br> `trigger:10t:info:10 ticks of video passed.`<br> `trigger:-10:info:Last 10 seconds of video passed.`<br> `trigger:20:shot:info:20 seconds of video passed (shot).`<br> `trigger:20%:shot:info:20% of video passed (shot).`<br> `trigger:20t:shot:info:20 ticks of video passed (shot).`<br> `trigger:-20:shot:info:Last 20 seconds of video passed (shot).`<br> `trigger:quartile1:info:First quartile of video entered.`<br> `trigger:quartile2:info:Second quartile of video entered.`<br> `trigger:quartile3:info:Third quartile of video entered.`<br> `trigger:quartile4:info:Fourth quartile of video entered.`<br> `trigger:first_quartile:info:First quartile of video reached.`<br> `trigger:midpoint:info:Midpoint of video reached.`<br> `trigger:third_quartile:info:Third quartile of video reached.`<br> `trigger:play:info:Video is playing.`<br> `trigger:pause:info:Video paused.`<br> `trigger:stop:info:Video stopped.`<br> `trigger:active:info:Video player is visible or video moved to foreground.`<br> `trigger:inactive:info:Video moved to background.`<br> `trigger:player:info:Video player is visible.`<br> `trigger:foreground:info:Video moved to foreground.`<br> `trigger:background:info:Video moved to background.`<br> `trigger:back:info:Video back executed.`<br> `trigger:back:none`<br> | None | No | Sets up a video/audio trigger for the current content. If the action `none` is indicated, the trigger is removed again. This action must be called after a `video:{URL}`, `audio:{URL}`, or `playlist:{URL}` action in order to take effect.<br><br>**Note: Triggers that are set at runtime are executed after the default triggers and do not overwrite them. For trigger actions, it is not possible to provide an action-related `data` property. If you want to execute a trigger data action, please use the `execute:fetch:{URL}` action, alternatively.** |

## update: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `update:content:{ITEM_ID}`<br> `update:content:content_item_id`<br> | **Required** | **Yes** | Updates a content item. |
| `update:content:overlay:{ITEM_ID}`<br> `update:content:overlay:content_item_id`<br> | **Required** | **Yes** | Updates a content overlay item. |
| `update:content:underlay:{ITEM_ID}`<br> `update:content:underlay:content_item_id`<br> | **Required** | **Yes** | Updates a content underlay item. |
| `update:menu:{ITEM_ID}`<br> `update:menu:menu_item_id`<br> | **Required** | **Yes** | Updates a menu item. |
| `update:panel:{ITEM_ID}`<br> `update:panel:content_item_id`<br> | **Required** | **Yes** | Updates a panel item. |
| `update:panel:overlay:{ITEM_ID}`<br> `update:panel:overlay:content_item_id`<br> | **Required** | **Yes** | Updates a panel overlay item. |
| `update:panel:underlay:{ITEM_ID}`<br> `update:panel:underlay:content_item_id`<br> | **Required** | **Yes** | Updates a panel underlay item. |

## video: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `video:{URL}`<br> `video:http://msx.benzac.de/media/video1.mp4`<br> | Optional | **Yes** | Plays a video. |
| `video:auto:{URL}`<br> `video:auto:http://msx.benzac.de/media/video1.mp4`<br> | Optional | No | Plays a video in auto mode. |
| `video:auto:broadcast:{BC_ACTION}`<br> `video:auto:broadcast:current`<br> `video:auto:broadcast:next`<br> `video:auto:broadcast:prev`<br> `video:auto:broadcast:release`<br> `video:auto:broadcast:name:{CHANNEL_NAME}`<br> `video:auto:broadcast:triplet:{ONID}.{TSID}.{SID}`<br> | Optional | No | Executes a platform-specific video broadcast action in auto mode. |
| `video:auto:plugin:{URL}`<br> `video:auto:plugin:http://msx.benzac.de/plugins/template.html`<br> | Optional | No | Plays a plugin video in auto mode. |
| `video:auto:resolve:{REQUEST_ACTION}`<br> `video:auto:resolve:http://msx.benzac.de/services/resolve.php?type=video`<br> `video:auto:resolve:user:{URL}`<br> `video:auto:resolve:request:interaction:{DATA_ID}`<br> `video:auto:resolve:request:interaction:{DATA_ID}@{URL}`<br> | Optional | No | Plays a video by resolving it with a URL in auto mode (the response data must contain a `url` property). |
| `video:broadcast:{BC_ACTION}`<br> `video:broadcast:current`<br> `video:broadcast:next`<br> `video:broadcast:prev`<br> `video:broadcast:release`<br> `video:broadcast:name:{CHANNEL_NAME}`<br> `video:broadcast:triplet:{ONID}.{TSID}.{SID}`<br> | Optional | No | Executes a platform-specific video broadcast action. |
| `video:plugin:{URL}`<br> `video:plugin:http://msx.benzac.de/plugins/template.html`<br> | Optional | **Yes** | Plays a plugin video. |
| `video:resolve:{REQUEST_ACTION}`<br> `video:resolve:http://msx.benzac.de/services/resolve.php?type=video`<br> `video:resolve:user:{URL}`<br> `video:resolve:request:interaction:{DATA_ID}`<br> `video:resolve:request:interaction:{DATA_ID}@{URL}`<br> | Optional | No | Plays a video by resolving it with a URL (the response data must contain a `url` property). |

## volume: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `volume` | None | No | Toggles the volume scene. |
| `volume:action` | None | No | Shows the volume action. |
| `volume:dec` | None | No | Decreases the volume. |
| `volume:hide` | None | No | Hides the volume scene. |
| `volume:inc` | None | No | Increases the volume. |
| `volume:mute` | None | No | Mutes the volume. |
| `volume:set:{VALUE}`<br> `volume:set:100`<br> `volume:set:50`<br> `volume:set:0`<br> | None | No | Sets the volume. |
| `volume:show` | None | No | Shows the volume scene. |
| `volume:toggle_show` | None | No | Toggles the volume or shows the volume scene. |
| `volume:unmute` | None | No | Unmutes the volume. |
| `volume:toggle` | None | No | Toggles the volume. |

## warn: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `warn:{MESSAGE}`<br> `warn:This is a warning message.`<br> | None | **Yes** | Shows a warning message. |

## other: Actions

| Syntax & Example | Data | Main Action | Description |
|---|---|---|---|
| `[{ACTION}\|{ACTION}\|...]`<br> `[reload:content\|info:Action executed.]`<br> | Optional | **Yes** | Executes multiple actions in a row. |
