---
title: Plugin API Reference
category: Experts API - Plugin
summary: Complete API reference for MSX plugins, covering classical, Classic Module, and TypeScript Module access forms.
---

# Plugin API Reference

This API reference is intended to give you an overview of which classes are exposed by the JavaScript plugin file. Please note that they are not yet fully documented. The main classes are `TVXVideoPlugin` and `TVXInteractionPlugin`. Basically, you only need these classes to create a plugin.

**Note: If you would like to load the JavaScript file as module (either as classic or TypeScript module), you have to use the module version. Please see [Usage (Classic Module)](#usage-classic-module) or [Usage (TypeScript Module)](#usage-typescript-module) for more information.**

## API Access Forms

The same API is available under three access patterns.

| Access Form | Context | Key Classes |
|-------------|---------|-------------|
| Classical (ES5/global) | Loaded via `<script src="tvx-plugin.min.js">` | `TVXVideoPlugin`, `TVXInteractionPlugin` |
| Classic Module (AMD/CommonJS) | `var tvx = require('tvx-plugin-module')` | `tvx.VideoPlugin`, `tvx.InteractionPlugin` |
| TypeScript Module | `import * as tvx from 'tvx-plugin-module'` | `tvx.TVXVideoPlugin` (interface), `tvx.VideoPlugin` (instance) |

Equivalences: `TVXVideoPlugin` ≙ `tvx.VideoPlugin` (Classic Module) ≙ `tvx.VideoPlugin` (TypeScript Module instance). Similarly: `TVXInteractionPlugin` ≙ `tvx.InteractionPlugin`.

**ES5 recommendation:** For TV browser compatibility, it is recommended to use ES5 (the classical global form) when writing plugins that must run on TV platforms, as TV browsers often have limited ES module support.

## Reference

JavaScript plugin file: [http://msx.benzac.de/js/tvx-plugin.min.js](http://msx.benzac.de/js/tvx-plugin.min.js)

```js
//******************************************************************************
//TVX Plugin v0.0.79 - API Reference
//(c) 2026 Benjamin Zachey
//******************************************************************************

//******************************************************************************
//TVXSettings (static class)
//******************************************************************************
TVXSettings.NAME;
TVXSettings.SHORTCUT;
TVXSettings.VERSION;
TVXSettings.SUFFIX;
TVXSettings.DUMMY_DATE;
TVXSettings.TIME_OFFSET;
TVXSettings.TIME_ZONE_OFFSET;
TVXSettings.ANIMATION_DURATION;
TVXSettings.ANIMATION_EASE;
TVXSettings.AFK_DELAY;
TVXSettings.SCREEN_WIDTH;
TVXSettings.SCREEN_HEIGHT;
TVXSettings.SCREEN_FACTOR;
TVXSettings.ZOOM_FACTOR;
TVXSettings.PLATFORM;
TVXSettings.ID;
TVXSettings.ANIMATE;
TVXSettings.TRANSFORM;
TVXSettings.INPUT;
TVXSettings.REMOTE;
TVXSettings.LAYOUT;
TVXSettings.SCALE;
TVXSettings.ZOOM;
TVXSettings.CENTER;
TVXSettings.BACKGROUND;
TVXSettings.LEAVE;
TVXSettings.EXIT;
TVXSettings.BACK;
TVXSettings.VOLUME;
TVXSettings.BUSY;
TVXSettings.SPEED;
TVXSettings.PLAYBACK;
TVXSettings.FULLSCREEN;
TVXSettings.SUSPEND;
TVXSettings.SECURE;
TVXSettings.CAPTION;
TVXSettings.POINTER;
TVXSettings.APP;
//******************************************************************************

//******************************************************************************
//TVXStyles (static class)
//******************************************************************************
TVXStyles.COMMON;
TVXStyles.LOGGER;
TVXStyles.RENDERER;
TVXStyles.REMOTE;
TVXStyles.APP;
//******************************************************************************

//******************************************************************************
//TVXTools (static class)
//******************************************************************************
TVXTools.createVersion(version);
TVXTools.checkVersion(currentVersion, minVersion);
TVXTools.createKey(length, type);
TVXTools.isFullStr(obj);
TVXTools.isBool(obj);
TVXTools.isNum(obj);
TVXTools.isArray(obj);
TVXTools.isEmpty(obj);
TVXTools.isValid(obj, hasValue);
TVXTools.isId(id);
TVXTools.isJson(json);
TVXTools.isHttpUrl(url);
TVXTools.strValue(obj);
TVXTools.strFullCheck(str, defaultValue);
TVXTools.strTrim(str);
TVXTools.strClear(str);
TVXTools.strFlatten(str);
TVXTools.strTruncate(str, length);
TVXTools.strShuffle(str);
TVXTools.strReplace(str, find, replace);
TVXTools.strReplaceMap(str, map);
TVXTools.strToBool(str, defaultValue);
TVXTools.strToNum(str, defaultValue);
TVXTools.strToAction(str);
TVXTools.strToJsonStr(str);
TVXTools.strToUrlStr(str);
TVXTools.strContainsToken(str, token, start, end);
TVXTools.strCountToken(str, token, start, end);
TVXTools.numToStr(num, thousandsSeparator, decimalSeparator, decimalDigits, decimalTrimming);
TVXTools.htmlTrim(str);
TVXTools.htmlEscape(str);
TVXTools.htmlUnescape(str);
TVXTools.htmlAttrEscape(str);
TVXTools.htmlAttrUnescape(str);
TVXTools.htmlTextEscape(str);
TVXTools.htmlCharacter(str);
TVXTools.base64Encode(str);
TVXTools.base64EncodeUrl(str);
TVXTools.base64EncodeId(str);
TVXTools.base64Decode(str);
TVXTools.base64DecodeUrl(str);
TVXTools.base64DecodeId(str);
TVXTools.utf8Encode(str);
TVXTools.utf8Decode(str);
TVXTools.createHash(str, reverse);
TVXTools.serialize(obj, name);
TVXTools.deserialize(json);
TVXTools.isSecureContext();
TVXTools.secureUrl(url);
TVXTools.getPrefixUrl(suffix);
TVXTools.getHostUrl(suffix);
TVXTools.getPathUrl(suffix);
TVXTools.getAbsoluteUrl(url);
TVXTools.getRootPath(subPath, trim);
TVXTools.exprEscape(text, exprStart, exprEnd, textCallback, exprCallback, completeCallback, state);
//******************************************************************************

//******************************************************************************
//TVXDateTools (static class)
//******************************************************************************
TVXDateTools.applyDictionary(dictionary);
TVXDateTools.getTimestamp();
TVXDateTools.getNow();
TVXDateTools.getFormatSeparator();
TVXDateTools.getFormattedDateStr(date, format, y, m, d);
TVXDateTools.getDayStr(date, y, m, d);
TVXDateTools.getDayLongStr(date, y, m, d);
TVXDateTools.getDayFullStr(date, y, m, d);
TVXDateTools.getDateStr(date, y, m, d);
TVXDateTools.getDateLongStr(date, y, m, d);
TVXDateTools.getFormattedTimeStr(date, format, h, m, s);
TVXDateTools.getTimeStr(date, h, m, s);
TVXDateTools.getTimeLongStr(date, h, m, s);
TVXDateTools.getRecordingStr(date);
TVXDateTools.getFormattedDurationStr(timeInMs, format);
TVXDateTools.getDurationStr(timeInMs, values);
TVXDateTools.getVideoStr(timeInMs, digits);
TVXDateTools.getFormattedNumberStr(number, format);
//******************************************************************************

//******************************************************************************
//TVXDateFormatter (static class)
//******************************************************************************
TVXDateFormatter.toTimeStr(date);
TVXDateFormatter.toTimeLongStr(date);
TVXDateFormatter.toTimeDayStr(date);
TVXDateFormatter.toTimeDayLongStr(date);
TVXDateFormatter.toDayStr(date);
TVXDateFormatter.toDayLongStr(date);
TVXDateFormatter.toDayFullStr(date);
TVXDateFormatter.toDayTimeStr(date);
TVXDateFormatter.toDayTimeLongStr(date);
TVXDateFormatter.toDayTimeFullStr(date);
TVXDateFormatter.toDateStr(date);
TVXDateFormatter.toDateLongStr(date);
TVXDateFormatter.toDateTimeStr(date);
TVXDateFormatter.toDateTimeLongStr(date);
//******************************************************************************

//******************************************************************************
//TVXPropertyTools (static class)
//******************************************************************************
TVXPropertyTools.foreach(data, callback);
TVXPropertyTools.getValue(data, key, defaultValue);
TVXPropertyTools.get(data, key, defaultValue);
TVXPropertyTools.getFullStr(data, key, defaultValue);
TVXPropertyTools.getNum(data, key, defaultValue);
TVXPropertyTools.getBool(data, key, defaultValue);
TVXPropertyTools.has(data, key, hasValue);
TVXPropertyTools.put(data, key, value);
TVXPropertyTools.remove(data, key);
TVXPropertyTools.clear(data);
TVXPropertyTools.count(data);
TVXPropertyTools.extend(data, extension);
//******************************************************************************

/******************************************************************************/
//TVXVideoState (static class)
/******************************************************************************/
TVXVideoState.STOPPED;
TVXVideoState.PLAYING;
TVXVideoState.PAUSED;
/******************************************************************************/

/******************************************************************************/
//TVXAction (static class)
/******************************************************************************/
TVXAction.LEFT;
TVXAction.RIGHT;
TVXAction.UP;
TVXAction.DOWN;
TVXAction.EXECUTE;
TVXAction.EXIT;
TVXAction.BACK;
TVXAction.MENU;
TVXAction.GUIDE;
TVXAction.OPTIONS;
TVXAction.INFO;
TVXAction.CLEAR;
TVXAction.CHANNEL_LIST;
TVXAction.SETTINGS;
TVXAction.SEARCH;
TVXAction.PLAY;
TVXAction.PAUSE;
TVXAction.STOP;
TVXAction.NEXT_TRACK;
TVXAction.PREVIOUS_TRACK;
TVXAction.TOGGLE_PLAY_PAUSE;
TVXAction.FORWARD;
TVXAction.REWIND;
TVXAction.RECORD;
TVXAction.RESTART;
TVXAction.MUTE;
TVXAction.UNMUTE;
TVXAction.TOGGLE_MUTE;
TVXAction.CHANNEL_UP;
TVXAction.CHANNEL_DOWN;
TVXAction.VOLUME_UP;
TVXAction.VOLUME_DOWN;
TVXAction.RED;
TVXAction.GREEN;
TVXAction.YELLOW;
TVXAction.BLUE;
TVXAction.KEY_0;
TVXAction.KEY_1;
TVXAction.KEY_2;
TVXAction.KEY_3;
TVXAction.KEY_4;
TVXAction.KEY_5;
TVXAction.KEY_6;
TVXAction.KEY_7;
TVXAction.KEY_8;
TVXAction.KEY_9;
TVXAction.POWER;
TVXAction.SYSTEM;
TVXAction.CURSOR_ON;
TVXAction.CURSOR_OFF;
TVXAction.SCROLL_UP;
TVXAction.SCROLL_DOWN;
TVXAction.SWIPE_LEFT;
TVXAction.SWIPE_RIGHT;
TVXAction.SWIPE_UP;
TVXAction.SWIPE_DOWN;
TVXAction.SLEEP;
TVXAction.WAKE_UP;
TVXAction.CONNECTION_UP;
TVXAction.CONNECTION_DOWN;
TVXAction.DEBUG;
TVXAction.UNKNOWN;
TVXAction.actionToStr(action);
TVXAction.strToAction(str);
TVXAction.isNavigationAction(action);
TVXAction.isBaseAction(action);
TVXAction.isVideoAction(action);
TVXAction.isChannelAction(action);
TVXAction.isVolumeAction(action);
TVXAction.isNumberAction(action);
TVXAction.isColorAction(action);
TVXAction.isSystemAction(action);
/******************************************************************************/

/******************************************************************************/
//TVXCookies (default class)
/******************************************************************************/
var cookies = new TVXCookies();
cookies.set(name, value, expires);
cookies.get(name, defaultValue);
cookies.getFullStr(name, defaultValue);
cookies.getNum(name, defaultValue);
cookies.getBool(name, defaultValue);
cookies.has(name, hasValue);
cookies.foreach(callback);
cookies.remove(name);
cookies.clear();
/******************************************************************************/

/******************************************************************************/
//TVXStorage (default class)
/******************************************************************************/
var storage = new TVXStorage();
storage.set(name, value, expires);
storage.get(name, defaultValue);
storage.getFullStr(name, defaultValue);
storage.getNum(name, defaultValue);
storage.getBool(name, defaultValue);
storage.has(name, hasValue);
storage.foreach(callback);
storage.remove(name);
storage.clear();
storage.flush();
storage.getType();
storage.setType(type);
storage.isReady();
storage.onReady(handler);
/******************************************************************************/

/******************************************************************************/
//TVXUrlParams (default class)
/******************************************************************************/
var urlParams = new TVXUrlParams(url);
urlParams.set(name, value);
urlParams.get(name, defaultValue);
urlParams.getFullStr(name, defaultValue);
urlParams.getNum(name, defaultValue);
urlParams.getBool(name, defaultValue);
urlParams.has(name, hasValue);
urlParams.foreach(callback);
urlParams.remove(name);
urlParams.clear();
urlParams.create(url);
urlParams.build(encoded, separator);
/******************************************************************************/

/******************************************************************************/
//TVXOptions (default class)
/******************************************************************************/
var options = new TVXOptions(options, defaultOptions);
options.getValue(name);
options.get(name);
options.getFullStr(name);
options.getNum(name);
options.getBool(name);
options.has(name, hasValue);
/******************************************************************************/

/******************************************************************************/
//TVXObservers (default class)
/******************************************************************************/
var observers = new TVXObservers();
observers.hasObserver(name);
observers.hasObservers();
observers.addObserver(name, handler);
observers.removeObserver(name);
observers.onEvent(name, handler);
observers.notifyObserver(name, data);
observers.notifyObservers(data);
observers.clear();
/******************************************************************************/

/******************************************************************************/
//TVXEventObservers (default class)
/******************************************************************************/
var eventObservers = new TVXEventObservers();
eventObservers.hasObserver(eventName, handlerName);
eventObservers.hasObservers(eventName);
eventObservers.addObserver(eventName, handlerName, handler);
eventObservers.removeObserver(eventName, handlerName);
eventObservers.onEvent(eventName, handlerName, handler);
eventObservers.notifyObserver(eventName, handlerName, data);
eventObservers.notifyObservers(eventName, data);
eventObservers.clear(eventName);
/******************************************************************************/

/******************************************************************************/
//TVXQueue (default class)
/******************************************************************************/
var queue = new TVXQueue();
queue.delegate;
queue.execute();
queue.process();
queue.reset();
queue.push(action, delay);
queue.isBusy();
/******************************************************************************/

/******************************************************************************/
//TVXWorker (default class)
/******************************************************************************/
var worker = new TVXWorker(size, delay);
worker.size;
worker.delay;
worker.work();
worker.process();
worker.reset();
worker.execute(action);
worker.isBusy();
/******************************************************************************/

/******************************************************************************/
//TVXDelay (default class)
/******************************************************************************/
var delay = new TVXDelay(delay);
delay.delay;
delay.start(action);
delay.restart();
delay.stop();
delay.finish();
delay.isBusy();
/******************************************************************************/

/******************************************************************************/
//TVXClick (default class)
/******************************************************************************/
var click = new TVXClick();
click.click(clicked);
/******************************************************************************/

/******************************************************************************/
//TVXAjax (default class)
/******************************************************************************/
var ajax = new TVXAjax();
ajax.get(url, callback, options);
ajax.post(url, data, callback, options);
ajax.put(url, data, callback, options);
ajax.del(url, callback, options);
/******************************************************************************/

/******************************************************************************/
//TVXLogLevel (static class)
/******************************************************************************/
TVXLogLevel.OFF;
TVXLogLevel.ERROR;
TVXLogLevel.WARN;
TVXLogLevel.INFO;
TVXLogLevel.DEBUG;
/******************************************************************************/

/******************************************************************************/
//TVXLogger (default class)
/******************************************************************************/
var logger = new TVXLogger();
logger.level;
logger.maxLines;
logger.maxDebugLength;
logger.maxInfoLength;
logger.maxWarnLength;
logger.maxErrorLength;
logger.registerControl(control, print);
logger.unregisterControl(control);
logger.print();
logger.clear();
logger.log(level, message);
logger.debug(message);
logger.info(message);
logger.warn(message);
logger.error(message);
/******************************************************************************/

/******************************************************************************/
//TVXDictionary (default class)
/******************************************************************************/
var dictionary = new TVXDictionary();
dictionary.onReady(name, handler);
dictionary.init(data);
dictionary.getName();
dictionary.getVersion();
dictionary.getLanguage();
dictionary.getSize();
dictionary.isInitialized();
dictionary.getValueForKey(key, defaultValue);
dictionary.getValueForExpr(expr);
dictionary.getData();
/******************************************************************************/

/******************************************************************************/
//TVXClock (default class)
/******************************************************************************/
var clock = new TVXClock();
clock.delay;
clock.now;
clock.format;
clock.isRunning();
clock.hasHook(name);
clock.hasHooks();
clock.hasControl();
clock.clearHooks();
clock.addHook(name, hook);
clock.removeHook(name);
clock.onTick(name, hook);
clock.registerControl(control);
clock.unregisterControl(control);
clock.update();
clock.validate();
clock.process();
clock.start();
clock.stop();
/******************************************************************************/

/******************************************************************************/
//TVXDataLoader (default class)
/******************************************************************************/
var dataLoader = new TVXDataLoader();
dataLoader.load(url, cacheId, callback, options);
dataLoader.clearCache(cacheId);
dataLoader.clear();
/******************************************************************************/

/******************************************************************************/
//TVXServices (static class)
/******************************************************************************/
TVXServices.logger;//instance of TVXLogger
TVXServices.cookies;//instance of TVXCookies
TVXServices.storage;//instance of TVXStorage
TVXServices.urlParams;//instance of TVXUrlParams
TVXServices.ajax;//instance of TVXAjax
TVXServices.loader;//instance of TVXDataLoader
/******************************************************************************/

/******************************************************************************/
//TVXDataService (default class)
/******************************************************************************/
var dataService = new TVXDataService();
dataService.onReady(name, handler);
dataService.onError(name, handler);
dataService.onCompleted(name, handler);
dataService.foreachEntry(callback);
dataService.foreachError(callback);
dataService.getData(id);
dataService.getEntry(id);
dataService.setEntry(id, entry);
dataService.getError(id);
dataService.setError(id, error);
dataService.shouldStoreData(id);
dataService.createData(id, resp);
dataService.putData(url, data, callback, options);
dataService.postData(url, data, callback, options);
dataService.deleteData(url, callback, options);
dataService.loadData(id, url, callback, options);
dataService.clearData(id);
dataService.clear();
/******************************************************************************/

/******************************************************************************/
//TVXBlobService (default class)
/******************************************************************************/
var blobService = new TVXBlobService();
blobService.onReady(name, handler);
blobService.onError(name, handler);
blobService.onCompleted(name, handler);
blobService.foreachEntry(callback);
blobService.foreachError(callback);
blobService.getBlob(id);
blobService.getUrl(id);
blobService.getEntry(id);
blobService.setEntry(id, entry);
blobService.getError(id);
blobService.setError(id, error);
blobService.executeBlob(id, url, data, callback, options);
blobService.loadBlob(id, url, callback, options);
blobService.clearBlob(id);
blobService.clear();
/******************************************************************************/

/******************************************************************************/
//TVXRequestService (default class)
/******************************************************************************/
var requestService = new TVXRequestService(timeout);
requestService.timeout;
requestService.startRequest(callback);
requestService.getRequestsCount();
requestService.isRequestPending(id);
requestService.handleData(id, data);
requestService.handleError(id, message, type);
/******************************************************************************/

/******************************************************************************/
//TVXBusyService (default class)
/******************************************************************************/
var busyService = new TVXBusyService();
busyService.isBusy();
busyService.start(clear);
busyService.stop(clear);
busyService.onReady(handler);
/******************************************************************************/

/******************************************************************************/
//TVXPluginTools (static class)
/******************************************************************************/
TVXPluginTools.createCanvas(canvasClass, width, height);
TVXPluginTools.createIFrame(frameClass, src);
TVXPluginTools.areSettingsValidated();
TVXPluginTools.invalidateSettings();
TVXPluginTools.validateSettings(data);
TVXPluginTools.onValidatedSettings(callback);
TVXPluginTools.handleSettingsEvent(data);
TVXPluginTools.getFrameworkInfo(data);
TVXPluginTools.getApplicationInfo(data);
TVXPluginTools.getContentInfo(data);
TVXPluginTools.checkFramework(data, minVersion, requiredName);
TVXPluginTools.checkApplication(data, minVersion, requiredName);
TVXPluginTools.checkContent(data, minVersion, requiredName);
TVXPluginTools.isSameContentState(state1, state2);
TVXPluginTools.createChangedContentState(currentState, newState);
TVXPluginTools.isReady();
TVXPluginTools.onReady(handler);
TVXPluginTools.startInitService();
TVXPluginTools.stopInitService();
/******************************************************************************/

/******************************************************************************/
//TVXVideoPlugin (static class)
//This class provides the interface for a video/audio plugin.
/******************************************************************************/
TVXVideoPlugin.setupPlayer(player);//Sets up the player object (see MyPlayer class).
TVXVideoPlugin.setSeekDelay(delay);//Sets the seek delay (in milliseconds).
TVXVideoPlugin.getSeekDelay();//Gets the seek delay (in milliseconds).
TVXVideoPlugin.setLoadingDelay(delay);//Sets the loading delay (in milliseconds).
TVXVideoPlugin.getLoadingDelay();//Gets the loading delay (in milliseconds).
TVXVideoPlugin.isFullscreenEnabled(element);//Indicates if the fullscreen mode is enabled.
TVXVideoPlugin.isFullscreenActive(element);//Indicates if the fullscreen mode is active.
TVXVideoPlugin.requestFullscreen(element);//Requests the fullscreen mode for an element (returns true on success).
TVXVideoPlugin.exitFullscreen(element);//Exits the fullscreen mode (returns true on success).
TVXVideoPlugin.setState(state, commit);//Sets the state (see TVXVideoState class).
TVXVideoPlugin.getState();//Gets the state (see TVXVideoState class).
TVXVideoPlugin.applyState();//Calls the play/pause/stop function on the player object.
TVXVideoPlugin.setPosition(position, commit);//Sets the position (in seconds) and stops the seek process.
TVXVideoPlugin.getPosition();//Gets the position (in seconds).
TVXVideoPlugin.applyPosition();//Calls the setPosition function on the player object.
TVXVideoPlugin.setDuration(duration, commit);//Sets the duration (in seconds).
TVXVideoPlugin.getDuration();//Gets the duration (in seconds).
TVXVideoPlugin.setSpeed(speed, commit);//Sets the speed (0.125 .. 8.0).
TVXVideoPlugin.getSpeed();//Gets the speed (0.125 .. 8.0).
TVXVideoPlugin.applySpeed();//Calls the setSpeed function on the player object.
TVXVideoPlugin.setEnded(ended, commit);//Sets if the player has ended.
TVXVideoPlugin.hasEnded();//Indicates if the player has ended.
TVXVideoPlugin.setVolume(volume, commit);//Sets the volume (0 .. 100).
TVXVideoPlugin.getVolume();//Gets the volume (0 .. 100).
TVXVideoPlugin.setMuted(muted, commit);//Sets if the player is muted.
TVXVideoPlugin.isMuted();//Indicates if the player is muted.
TVXVideoPlugin.applyVolume();//Calls the setVolume and setMuted function on the player object.
TVXVideoPlugin.getWidth();//Gets the player width (in pixels).
TVXVideoPlugin.getHeight();//Gets the player height (in pixels).
TVXVideoPlugin.applySize();//Calls the setSize function on the player object.
TVXVideoPlugin.executeAction(action, data);//Executes any action.
TVXVideoPlugin.debug(message, log);//Logs a debug message.
TVXVideoPlugin.success(message, log, show);//Logs (and shows) a success message.
TVXVideoPlugin.info(message, log, show);//Logs (and shows) an info message.
TVXVideoPlugin.warn(message, log, show);//Logs (and shows) a warning message.
TVXVideoPlugin.error(message, log, show);//Logs (and shows) an error message.
TVXVideoPlugin.showMenu(data);//Shows (or loads) a menu (the data parameter can be a JSON or URL).
TVXVideoPlugin.showContent(data);//Shows (or loads) a content page (the data parameter can be a JSON or URL).
TVXVideoPlugin.showPanel(data);//Shows (or loads) a panel (the data parameter can be a JSON or URL).
TVXVideoPlugin.showPlayer(key);//Shows the player.
TVXVideoPlugin.showAction();//Shows the player action.
TVXVideoPlugin.hidePlayer();//Hides the player.
TVXVideoPlugin.setupContentLabel(label);//Sets up the player content label.
TVXVideoPlugin.setupExtensionLabel(label);//Sets up an additional player extension label.
TVXVideoPlugin.setupPositionLabel(label);//Sets up the player position label.
TVXVideoPlugin.setupDurationLabel(label);//Sets up the player duration label.
TVXVideoPlugin.setupSpeedLabel(label);//Sets up the player speed label.
TVXVideoPlugin.setupInfoHeadline(headline);//Sets up a player info headline (only available for extended players).
TVXVideoPlugin.setupInfoText(text);//Sets up a player info text (only available for extended players).
TVXVideoPlugin.setupInfoImage(image);//Sets up a player info image (only available for extended players).
TVXVideoPlugin.setupInfoOverlay(overlay);//Sets up the player info overlay (only available for extended players).
TVXVideoPlugin.setupInfoSize(size);//Sets up the size of the player info image area (only available for extended players).
TVXVideoPlugin.enableInfoRound();//Enables rounded corners of the info image if the rounded style is used (only available for extended players).
TVXVideoPlugin.disableInfoRound();//Disables rounded corners of the info image if the rounded style is used (only available for extended players).
TVXVideoPlugin.setupControlAction(action);//Sets up a custom player control action (replacement for the action that is executed if the OK key is pressed while the video/audio is in foreground).
TVXVideoPlugin.setupButton(id, data);//Sets up a player button (all buttons except the eject button are supported).
TVXVideoPlugin.enableButton(id);//Enables a player button (all buttons except the eject button are supported).
TVXVideoPlugin.disableButton(id);//Disables a player button (all buttons except the eject button are supported).
TVXVideoPlugin.resetButton(id);//Resets a player button (all buttons except the eject button are supported).
TVXVideoPlugin.focusButton(id);//Focuses a player button.
TVXVideoPlugin.setupProgressPosition(position);//Sets up the player progress position.
TVXVideoPlugin.setupProgressDuration(duration);//Sets up the player progress duration.
TVXVideoPlugin.setupProgressColor(color);//Sets up the player progress color.
TVXVideoPlugin.setupProgressType(type);//Sets up the player progress type.
TVXVideoPlugin.enableProgressMarker();//Enables the player progress marker.
TVXVideoPlugin.disableProgressMarker();//Disables the player progress marker.
TVXVideoPlugin.invalidateProgressMarker();//Invalidates the player progress marker (marker will be unfocused).
TVXVideoPlugin.refreshPlayer();//Refreshes all player values.
TVXVideoPlugin.resetPlayer();//Resets custom player values that have been set at runtime.
TVXVideoPlugin.setupBackground(background);//Sets up an audio background.
TVXVideoPlugin.setupTrigger(key, action, shot);//Sets up a video/audio trigger.
TVXVideoPlugin.clearTrigger(key);//Clears a video/audio trigger.
TVXVideoPlugin.cancelResume();//Cancels an ongoing resume process.
TVXVideoPlugin.requestData(dataId, callback, data);//Requests any data (e.g. "info", "video", "code", etc.).
TVXVideoPlugin.requestInteractionResponse(dataId, callback, data);//Requests a response from the interaction plugin.
TVXVideoPlugin.validateSettings(callback);//Validates (or revalidates) the settings (see TVXSettings class).
TVXVideoPlugin.onValidatedSettings(callback);//Validates the settings if they are not validated (see TVXSettings class).
TVXVideoPlugin.triggerEvent(eventId, data);//Triggers a custom event (that can be handled by the interaction plugin).
TVXVideoPlugin.setupSteam(baseSteam);//Sets up a local steam.
TVXVideoPlugin.createSteam(scope);//Creates a steam.
TVXVideoPlugin.resolveToken(token, scope);//Resolves a token.
TVXVideoPlugin.transformString(string, scope);//Transforms a string.
TVXVideoPlugin.normalizeString(string, scope);//Normalizes a string.
TVXVideoPlugin.transformStringAsync(string, scope, callback);//Transforms a string asynchronously.
TVXVideoPlugin.normalizeStringAsync(string, scope, callback);//Normalizes a string asynchronously.
TVXVideoPlugin.transformUrl(url);//Transforms a URL.
TVXVideoPlugin.createHashKey(string, scope);//Creates a hash key.
TVXVideoPlugin.clearDeviceId();//Clears the device ID (if it was created).
TVXVideoPlugin.getDeviceId(data);//Returns (or creates) the device ID.
TVXVideoPlugin.requestDeviceId(callback);//Requests the device ID.
TVXVideoPlugin.hasContentObservers();//Indicates if content observers exist.
TVXVideoPlugin.addContentObserver(name, handler);//Adds a content observer.
TVXVideoPlugin.removeContentObserver(name);//Removes a content observer.
TVXVideoPlugin.clearContentObservers();//Removes all content observers.
TVXVideoPlugin.init();//Initializes the player.
TVXVideoPlugin.commit();//Commits all player values.
TVXVideoPlugin.isInitialized();//Indicates if the player is initialized.
TVXVideoPlugin.isReady();//Indicates if the player is ready.
TVXVideoPlugin.startPlayback(accelerate);//Starts the playback (calls the play and periodically the getUpdateData function on the player object).
TVXVideoPlugin.stopPlayback();//Stops the playback.
TVXVideoPlugin.cancelPlayback();//Cancels the playback.
TVXVideoPlugin.startLoading(restart);//Starts a loading process (shows a busy indicator after the loading delay).
TVXVideoPlugin.stopLoading();//Stops a loading process.
TVXVideoPlugin.executeHandler(handler, callback);//Executes a handler function and handles all errors that occur.
/******************************************************************************/

/******************************************************************************/
//MyPlayer (example class)
//Note: This is an example class (not included in the plugin script) showing the structure of a player object.
/******************************************************************************/
function MyPlayer() {
    this.init = function() {
        //Init player
    };
    this.ready = function() {
        //Player is ready        
    };
    this.play = function() {
        //Play
    };
    this.pause = function() {
        //Pause
    };
    this.stop = function() {
        //Stop
    };
    this.getDuration = function() {
        //Get duration in seconds
        return 60;
    };
    this.getPosition = function() {
        //Get position in seconds
        return 0;
    };
    this.setPosition = function(position) {
        //Set position in seconds        
    };
    this.setVolume = function(volume) {
        //Set volume (0 .. 100)
    };
    this.getVolume = function() {
        //Get volume (0 .. 100)
        return 100;
    };
    this.setMuted = function(muted) {
        //Set muted
    };
    this.isMuted = function() {
        //Get muted
        return false;
    };
    this.getSpeed = function() {
        //Get speed (0.125 .. 8.0)
        return 1;
    };
    this.setSpeed = function(speed) {
        //Set speed (0.125 .. 8.0)
    };
    this.setSize = function(width, height) {
        //Set window size
    };
    this.getUpdateData = function() {
        //Get update data (this will be called each second)
        return {
            position: this.getPosition(),
            duration: this.getDuration(),
            speed: this.getSpeed()
        };
    };
    this.handleEvent = function(data) {
        //Handle event
        //The data.event property can contain following values:
        //- "app:suspend"
        //- "app:resume"
        //- "app:sleep"
        //- "app:awake"
        //- "app:resize"
        //- "app:connect"
        //- "app:disconnect"       
        //- "app:time" (data.offset and data.zoneOffset properties contain the new time and zone offset)
        //- "app:result" (data.id property contains the request ID, data.code property contains the result code, and data.extra property contains the extra data)
        //- "video:load"* (data.info property contains the loaded video info and data.data property contains the active video data before this event occurred)
        //- "video:play"* (data.data property contains the active video data before this event occurred)
        //- "video:pause"* (data.data property contains the active video data before this event occurred)
        //- "video:stop"* (data.data property contains the active video data before this event occurred)
        //- "video:seek"* (data.position property contains the seeked position in seconds and data.data property contains the active video data before this event occurred)
        //- "video:restart"* (data.data property contains the active video data before this event occurred)
        //- "video:speed"* (data.speed property contains the new speed value and data.data property contains the active video data before this event occurred)
        //- "video:volume"* (data.volume and data.muted properties contain the new volume level and muted state)
        //- "slider:load" (data.info property contains the loaded slider info)
        //- "slider:play"
        //- "slider:pause"
        //- "slider:stop"
        //- "slider:position" (data.index, data.number, and data.listIndex properties contain the new item index, number, and list index)
        //- "slider:rotation" (data.rotation property contains the new rotation value in degrees)
        //- "settings:animate" (data.value property contains the new settings value)
        //- "settings:transform" (data.value property contains the new settings value)
        //- "settings:input" (data.value property contains the new settings value)
        //- "settings:remote" (data.value property contains the new settings value)
        //- "settings:layout" (data.value property contains the new settings value)
        //- "settings:scale" (data.value property contains the new settings value)
        //- "settings:zoom" (data.value property contains the new settings value)
        //- "settings:validate_links" (data.value property contains the new settings value)
        //- "settings:random_playback" (data.value property contains the new settings value)
        //- "settings:slideshow_interval" (data.value property contains the new settings value)
        //- "settings:hover_effect" (data.value property contains the new settings value)
        //- "settings:immersive_mode" (data.value property contains the new settings value)
        //- "settings:rounded_style" (data.value property contains the new settings value)
        //- "settings:sleep_timeout" (data.value property contains the new settings value)
        //- "settings:eject_timeout" (data.value property contains the new settings value)
        //- "settings:visual_execution" (data.value property contains the new settings value)
        //- "settings:menu_button" (data.action and data.keyCode properties contain the new button action and key code)
        //- "custom:{EVENT_ID}" (data.data property optionally contains the event-related data)
        //*Note: Video events are usually not handled by the player, since the corresponding player function is also called (e.g. play() -> "video:play").
    };
    this.handleData = function(data) {
        //Handle data
        //Note: User-defined data is optionally available in the data.data property.
    };
    this.handleRequest = function(dataId, data, callback) {
        //Handle request
        //Note: User-defined data is optionally available in the data.data property.
        callback(null);
    };
    this.onError = function(message, error) {
        //Error has occurred      
        //Note: The error is already logged and shown, therefore, the error should only be evaluated here for debug purposes.
    };
}
/******************************************************************************/

/******************************************************************************/
//TVXInteractionPlugin (static class)
//This class provides the interface for an interaction plugin.
/******************************************************************************/
TVXInteractionPlugin.setupHandler(handler);//Sets up the interaction handler (see MyHandler class).
TVXInteractionPlugin.setLoadingDelay(delay);//Sets the loading delay (in milliseconds).
TVXInteractionPlugin.getLoadingDelay();//Gets the loading delay (in milliseconds).
TVXInteractionPlugin.executeAction(action, data);//Executes any action.
TVXInteractionPlugin.debug(message, log);//Logs a debug message.
TVXInteractionPlugin.success(message, log, show);//Logs (and shows) a success message.
TVXInteractionPlugin.info(message, log, show);//Logs (and shows) an info message.
TVXInteractionPlugin.warn(message, log, show);//Logs (and shows) a warning message.
TVXInteractionPlugin.error(message, log, show);//Logs (and shows) an error message.
TVXInteractionPlugin.showMenu(data);//Shows (or loads) a menu (the data parameter can be a JSON or URL).
TVXInteractionPlugin.showContent(data);//Shows (or loads) a content page (the data parameter can be a JSON or URL).
TVXInteractionPlugin.showPanel(data);//Shows (or loads) a panel (the data parameter can be a JSON or URL).
TVXInteractionPlugin.requestData(dataId, callback, data);//Requests any data (e.g. "info", "video", "code", etc.).
TVXInteractionPlugin.requestPlayerResponse(dataId, callback, data);//Requests a response from the player (handled by the video/audio plugin).
TVXInteractionPlugin.validateSettings(callback);//Validates (or revalidates) the settings (see TVXSettings class).
TVXInteractionPlugin.onValidatedSettings(callback);//Validates the settings if they are not validated (see TVXSettings class).
TVXInteractionPlugin.triggerEvent(eventId, data);//Triggers a custom event (that can be handled by the video/audio plugin).
TVXInteractionPlugin.setupSteam(baseSteam);//Sets up a local steam.
TVXInteractionPlugin.createSteam(scope);//Creates a steam.
TVXInteractionPlugin.resolveToken(token, scope);//Resolves a token.
TVXInteractionPlugin.transformString(string, scope);//Transforms a string.
TVXInteractionPlugin.normalizeString(string, scope);//Normalizes a string.
TVXInteractionPlugin.transformStringAsync(string, scope, callback);//Transforms a string asynchronously.
TVXInteractionPlugin.normalizeStringAsync(string, scope, callback);//Normalizes a string asynchronously.
TVXInteractionPlugin.transformUrl(url);//Transforms a URL.
TVXInteractionPlugin.createHashKey(string, scope);//Creates a hash key.
TVXInteractionPlugin.clearDeviceId();//Clears the device ID (if it was created).
TVXInteractionPlugin.getDeviceId(data);//Returns (or creates) the device ID.
TVXInteractionPlugin.requestDeviceId(callback);//Requests the device ID.
TVXInteractionPlugin.hasContentObservers();//Indicates if content observers exist.
TVXInteractionPlugin.addContentObserver(name, handler);//Adds a content observer.
TVXInteractionPlugin.removeContentObserver(name);//Removes a content observer.
TVXInteractionPlugin.clearContentObservers();//Removes all content observers.
TVXInteractionPlugin.init();//Initializes the interaction plugin.
TVXInteractionPlugin.isInitialized();//Indicates if the interaction plugin is initialized.
TVXInteractionPlugin.isReady();//Indicates if the interaction plugin is ready.
TVXInteractionPlugin.startLoading(restart);//Starts a loading process (shows a busy indicator after the loading delay).
TVXInteractionPlugin.stopLoading();//Stops a loading process.
TVXInteractionPlugin.executeHandler(handler, callback);//Executes a handler function and handles all errors that occur.
/******************************************************************************/

/******************************************************************************/
//MyHandler (example class)
//Note: This is an example class (not included in the plugin script) showing the structure of an interaction handler.
/******************************************************************************/
function MyHandler() {
    this.init = function() {
        //Init handler
    };
    this.ready = function() {
        //Handler is ready        
    };
    this.handleEvent = function(data) {
        //Handle event
        //The data.event property can contain following values:
        //- "app:suspend"
        //- "app:resume"
        //- "app:sleep"
        //- "app:awake"
        //- "app:resize"
        //- "app:connect"
        //- "app:disconnect"
        //- "app:time" (data.offset and data.zoneOffset properties contain the new time and zone offset)
        //- "app:result" (data.id property contains the request ID, data.code property contains the result code, and data.extra property contains the extra data)
        //- "video:load" (data.info property contains the loaded video info and data.data property contains the active video data before this event occurred)
        //- "video:play" (data.data property contains the active video data before this event occurred)
        //- "video:pause" (data.data property contains the active video data before this event occurred)
        //- "video:stop" (data.data property contains the active video data before this event occurred)
        //- "video:seek" (data.position property contains the seeked position in seconds and data.data property contains the active video data before this event occurred)
        //- "video:restart" (data.data property contains the active video data before this event occurred)
        //- "video:speed" (data.speed property contains the new speed value and data.data property contains the active video data before this event occurred)
        //- "video:volume" (data.volume and data.muted properties contain the new volume level and muted state)
        //- "slider:load" (data.info property contains the loaded slider info)
        //- "slider:play"
        //- "slider:pause"
        //- "slider:stop"
        //- "slider:position" (data.index, data.number, and data.listIndex properties contain the new item index, number, and list index)
        //- "slider:rotation" (data.rotation property contains the new rotation value in degrees)
        //- "settings:animate" (data.value property contains the new settings value)
        //- "settings:transform" (data.value property contains the new settings value)
        //- "settings:input" (data.value property contains the new settings value)
        //- "settings:remote" (data.value property contains the new settings value)
        //- "settings:layout" (data.value property contains the new settings value)
        //- "settings:scale" (data.value property contains the new settings value)
        //- "settings:zoom" (data.value property contains the new settings value)
        //- "settings:validate_links" (data.value property contains the new settings value)
        //- "settings:random_playback" (data.value property contains the new settings value)
        //- "settings:slideshow_interval" (data.value property contains the new settings value)
        //- "settings:hover_effect" (data.value property contains the new settings value)
        //- "settings:immersive_mode" (data.value property contains the new settings value)
        //- "settings:rounded_style" (data.value property contains the new settings value)
        //- "settings:sleep_timeout" (data.value property contains the new settings value)
        //- "settings:eject_timeout" (data.value property contains the new settings value)
        //- "settings:visual_execution" (data.value property contains the new settings value)
        //- "settings:menu_button" (data.action and data.keyCode properties contain the new button action and key code)
        //- "custom:{EVENT_ID}" (data.data property optionally contains the event-related data)
    };
    this.handleData = function(data) {
        //Handle data
        //Note: User-defined data is optionally available in the data.data property.
    };
    this.handleRequest = function(dataId, data, callback) {
        //Handle request
        //Note: User-defined data is optionally available in the data.data property.
        callback(null);
    };
    this.onError = function(message, error) {
        //Error has occurred      
        //Note: The error is already logged and shown, therefore, the error should only be evaluated here for debug purposes.
    };
}
/******************************************************************************/
```

## Usage (Classic Module)

JavaScript plugin module file: [http://msx.benzac.de/js/tvx-plugin-module.min.js](http://msx.benzac.de/js/tvx-plugin-module.min.js)

The Classic Module form uses AMD/CommonJS. Each global class (`TVXVideoPlugin`, `TVXInteractionPlugin`, etc.) maps to a shorter name on the `tvx` namespace object (e.g. `tvx.VideoPlugin` ≙ `TVXVideoPlugin`).

```js
/******************************************************************************/
//TVX Plugin v0.0.79 (Module) - API Usage (Classic Module)
//(c) 2026 Benjamin Zachey
//Compatibility: AMD and CommonJS
/******************************************************************************/
require(["./tvx-plugin-module.min"], function(tvx) {
    tvx.Settings;//TVXSettings (static class)
    tvx.Styles;//TVXStyles (static class)
    tvx.Tools;//TVXTools (static class)
    tvx.DateTools;//TVXDateTools (static class)
    tvx.DateFormatter;//TVXDateFormatter (static class)
    tvx.PropertyTools;//TVXPropertyTools (static class)
    tvx.VideoState;//TVXVideoState (static class)
    tvx.Action;//TVXAction (static class)
    tvx.Cookies;//TVXCookies (default class)
    tvx.Storage;//TVXStorage (default class)
    tvx.UrlParams;//TVXUrlParams (default class)
    tvx.Options;//TVXOptions (default class)
    tvx.Observers;//TVXObservers (default class)
    tvx.EventObservers;//TVXEventObservers (default class)
    tvx.Queue;//TVXQueue (default class)
    tvx.Worker;//TVXWorker (default class)
    tvx.Delay;//TVXDelay (default class)
    tvx.Click;//TVXClick (default class)
    tvx.Ajax;//TVXAjax (default class)
    tvx.LogLevel;//TVXLogLevel (static class)
    tvx.Logger;//TVXLogger (default class)
    tvx.Dictionary;//TVXDictionary (default class)
    tvx.Clock;//TVXClock (default class)    
    tvx.DataLoader;//TVXDataLoader (default class)
    tvx.Services;//TVXServices (static class)
    tvx.DataService;//TVXDataService (default class)
    tvx.BlobService;//TVXBlobService (default class)
    tvx.RequestService;//TVXRequestService (default class)
    tvx.BusyService;//TVXBusyService (default class)
    tvx.PluginTools;//TVXPluginTools (static class)
    tvx.VideoPlugin;//TVXVideoPlugin (static class)
    tvx.InteractionPlugin;//TVXInteractionPlugin (static class)
});
/******************************************************************************/
```

## Usage (TypeScript Module)

JavaScript plugin module file: [http://msx.benzac.de/js/tvx-plugin-module.min.js](http://msx.benzac.de/js/tvx-plugin-module.min.js)
TypeScript plugin module declaration file: [http://msx.benzac.de/ts/tvx-plugin-module.min.d.ts](http://msx.benzac.de/ts/tvx-plugin-module.min.d.ts)

For fully functional TypeScript projects, please have a look at the following list.
- [https://github.com/benzac-de/msx-interaction-plugin-examples](https://github.com/benzac-de/msx-interaction-plugin-examples)
- [https://github.com/benzac-de/rbtv-msx](https://github.com/benzac-de/rbtv-msx)

The TypeScript Module form uses `import * as tvx from 'tvx-plugin-module'`. It exposes both declared interfaces/abstract classes (prefixed `TVX…`) and exported instances/instantiable classes (short names). The `tvx.TVXVideoPlugin` is a TypeScript *interface*, while `tvx.VideoPlugin` is the *instance* (equivalent to `TVXVideoPlugin` in the classical form).

```ts
/******************************************************************************/
//TVX Plugin v0.0.79 (Module) - API Usage (TypeScript Module)
//(c) 2026 Benjamin Zachey
//Compatibility: AMD and CommonJS
/******************************************************************************/
import * as tvx from "./tvx-plugin-module.min";
/******************************************************************************/
//Declared interfaces (Common API)
/******************************************************************************/
tvx.AnyObject;//interface
/******************************************************************************/
//Declared interfaces/types (MSX API)
/******************************************************************************/
tvx.MSXStart;//interface
tvx.MSXLauncher;//interface
tvx.MSXMenuRoot;//interface
tvx.MSXMenuRootStyle;//type
tvx.MSXMenuRootLogoSize;//type
tvx.MSXMenuItem;//interface
tvx.MSXMenuItemType;//type
tvx.MSXContentRoot;//interface
tvx.MSXContentRootType;//type
tvx.MSXContentRootPreload;//type
tvx.MSXContentPage;//interface
tvx.MSXContentItem;//interface
tvx.MSXContentItemType;//type
tvx.MSXContentItemIconSize;//type
tvx.MSXContentItemImageFiller;//type
tvx.MSXContentItemImageOverlay;//type
tvx.MSXContentCaptionUnderlay;//type
tvx.MSXRefocus;//type
tvx.MSXTransparent;//type
tvx.MSXRound;//type
tvx.MSXReady;//interface
tvx.MSXLive;//interface
tvx.MSXLiveType;//type
tvx.MSXLiveSource;//type
tvx.MSXLiveAction;//interface
tvx.MSXLiveProperties;//interface
tvx.MSXLiveState;//interface
tvx.MSXLiveStateExtended;//interface
tvx.MSXSelection;//interface
tvx.MSXURLParameters;//interface
tvx.MSXExtendedProperties;//interface
tvx.MSXDictionaryProperties;//interface
tvx.MSXAttachedData;//interface
tvx.MSXAttachedCode;//interface
tvx.MSXAttachedVideo;//interface
tvx.MSXAttachedVideoContainer;//interface
tvx.MSXAttachedVideoInfo;//interface
tvx.MSXAttachedVideoData;//interface
tvx.MSXAttachedVideoResumeInfo;//interface
tvx.MSXAttachedVideoVolumeInfo;//interface
tvx.MSXAttachedResumeMap;//interface
tvx.MSXAttachedResumeMapContainer;//interface
tvx.MSXAttachedSlider;//interface
tvx.MSXAttachedSliderContainer;//interface
tvx.MSXAttachedApplicationMenuButton;//interface
tvx.MSXAttachedApplicationSettings;//interface
tvx.MSXAttachedApplicationInfo;//interface
tvx.MSXAttachedFrameworkSettings;//interface
tvx.MSXAttachedFrameworkInfo;//interface
tvx.MSXAttachedContentState;//interface
tvx.MSXAttachedContentInfo;//interface
tvx.MSXAttachedDictionaryInfo;//interface
tvx.MSXAttachedScreenInfo;//interface
tvx.MSXAttachedTimeInfo;//interface
tvx.MSXAttachedSystemInfo;//interface
tvx.MSXAttachedInfo;//interface
tvx.MSXAttachedInfoContainer;//interface
tvx.MSXAttachedMessage;//interface
tvx.MSXAttachedString;//interface
tvx.MSXAttachedResponse;//interface
tvx.MSXAttachedDictionary;//interface
tvx.MSXAttachedDictionaryContainer;//interface
tvx.MSXAttachedNotification;//interface
tvx.MSXAttachedGeneric;//interface
/******************************************************************************/
//Declared interfaces/abstract classes (TVX API)
/******************************************************************************/
tvx.TVXSettings;//interface
tvx.TVXStyles;//interface
tvx.TVXVersion;//interface
tvx.TVXTools;//interface
tvx.TVXDateTools;//interface
tvx.TVXDateFormatter;//interface
tvx.TVXPropertyTools;//interface
tvx.TVXVideoState;//interface
tvx.TVXAction;//interface
tvx.TVXCookies;//abstract class
tvx.TVXStorage;//abstract class
tvx.TVXUrlParams;//abstract class
tvx.TVXOptions;//abstract class
tvx.TVXObservers;//abstract class
tvx.TVXEventObservers;//abstract class
tvx.TVXQueue;//abstract class
tvx.TVXWorker;//abstract class
tvx.TVXDelay;//abstract class
tvx.TVXClick;//abstract class
tvx.TVXAjaxOptions;//interface
tvx.TVXAjaxCallback;//interface
tvx.TVXAjax;//abstract class
tvx.TVXLogLevel;//interface
tvx.TVXLogger;//abstract class
tvx.TVXDictionary;//abstract class
tvx.TVXClock;//abstract class
tvx.TVXDataLoader;//abstract class
tvx.TVXDataLoaderCallback;//interface
tvx.TVXServices;//interface
tvx.TVXDataService;//abstract class
tvx.TVXDataServiceActionCallback;//interface
tvx.TVXDataServiceEntryCallback;//interface
tvx.TVXBlobService;//abstract class
tvx.TVXBlobServiceCallback;//interface
tvx.TVXRequestService;//abstract class
tvx.TVXRequestServiceCallback;//interface
tvx.TVXBusyService;//abstract class
tvx.TVXPluginTools;//interface
tvx.TVXChangedContentState;//interface
tvx.TVXDeviceId;//interface
tvx.TVXPlayerButtonData;//interface
tvx.TVXVideoUpdateData;//interface
tvx.TVXVideoPlugin;//interface
tvx.TVXVideoPluginPlayer;//interface
tvx.TVXInteractionPlugin;//interface
tvx.TVXInteractionPluginHandler;//interface
/******************************************************************************/
//Exported instances/instantiable classes (TVX API)
/******************************************************************************/
tvx.Settings;//instance of TVXSettings (like static class)
tvx.Styles;//instance of TVXStyles (like static class)
tvx.Tools;//instance of TVXTools (like static class)
tvx.DateTools;//instance of TVXDateTools (like static class)
tvx.DateFormatter;//instance of TVXDateFormatter (like static class)
tvx.PropertyTools;//instance of TVXPropertyTools (like static class)
tvx.VideoState;//instance of TVXVideoState (like static class)
tvx.Action;//instance of TVXAction (like static class)
tvx.Cookies;//instantiable class of TVXCookies (default class)
tvx.Storage;//instantiable class of TVXStorage (default class)
tvx.UrlParams;//instantiable class of TVXUrlParams (default class)
tvx.Options;//instantiable class of TVXOptions (default class)
tvx.Observers;//instantiable class of TVXObservers (default class)
tvx.EventObservers;//instantiable class of TVXEventObservers (default class)
tvx.Queue;//instantiable class of TVXQueue (default class)
tvx.Worker;//instantiable class of TVXWorker (default class)
tvx.Delay;//instantiable class of TVXDelay (default class)
tvx.Click;//instantiable class of TVXClick (default class)
tvx.Ajax;//instantiable class of TVXAjax (default class)
tvx.LogLevel;//instance of TVXLogLevel (like static class)
tvx.Logger;//instantiable class of TVXLogger (default class)
tvx.Dictionary;//instantiable class of TVXDictionary (default class)
tvx.Clock;//instantiable class of TVXClock (default class)
tvx.DataLoader;//instantiable class of TVXDataLoader (default class)
tvx.Services;//instance of TVXServices (like static class)
tvx.DataService;//instantiable class of TVXDataService (default class)
tvx.BlobService;//instantiable class of TVXBlobService (default class)
tvx.RequestService;//instantiable class of TVXRequestService (default class)
tvx.BusyService;//instantiable class of TVXBusyService (default class)
tvx.PluginTools;//instance of TVXPluginTools (like static class)
tvx.VideoPlugin;//instance of TVXVideoPlugin (like static class)
tvx.InteractionPlugin;//instance of TVXInteractionPlugin (like static class)
/******************************************************************************/
```

## See also

- [Video/Audio Plugin](./video-audio-plugin.md)
- [Interaction Plugin](./interaction-plugin.md)
- [Plugin Events Reference](./plugin-events-reference.md)
- [Common Misconceptions → Right property, wrong object](../../reference/common-misconceptions.md#right-property-wrong-object) — using `addContentObserver` to react to a content item losing focus, since there is no built-in "on focus leaves" action
- [Cookbook → Deep dive — building an interaction plugin](../../reference/cookbook.md#deep-dive--building-an-interaction-plugin-plugin_test_2) and [→ Deep dive — building a video plugin (HTML5X)](../../reference/cookbook.md#deep-dive--building-a-video-plugin-plugin_test_4-html5x)
- [Glossary → TVX Plugin Library](../../reference/glossary.md#tvx-plugin-library) — the distributed library files (two variants, one requiring jQuery), distinct from this page's own API-surface description
