---
title: IMA Plugin
category: Experts API - Plugin
summary: Reference for the MSX IMA (Interactive Media Ads) plugin.
---

# IMA Plugin

IMA stands for Interactive Media Ads and is a special video plugin that integrates ads (i.e. pre-rolls, mid-rolls, and post-rolls) into a video by using the Google IMA SDK ([https://developers.google.com/interactive-media-ads](https://developers.google.com/interactive-media-ads)). The plugin can be used with version **0.1.146** or higher.

## Usage

The plugin must be loaded with a video URL. Optionally, the ad tag URL can be indicated. Please see following action syntax example.

- `video:plugin:http://msx.benzac.de/plugins/ima.html?url={URL}&ad={AD_TAG_URL}`

If you would like to use the plugin as reference to implement your own plugin, please have a look at these implementation scripts:
- [http://msx.benzac.de/plugins/js/ima.js](http://msx.benzac.de/plugins/js/ima.js)
- [http://msx.benzac.de/plugins/js/ima-manager.js](http://msx.benzac.de/plugins/js/ima-manager.js)

## Syntax

### Parameter syntax of IMA plugin

| Parameter | Type | Default Value | Mandatory | Description |
|-----------|------|---------------|-----------|-------------|
| `url` | `string` | `null` | **Yes** | The URL of the video. It is recommended to encode the value to ensure that it is evaluated correctly (e.g. `"http://msx.benzac.de/media/video1.mp4"` → `"http%3A%2F%2Fmsx.benzac.de%2Fmedia%2Fvideo1.mp4"`). |
| `ad` | `string` | `null` | No | The URL of the ad tag. It is recommended to encode the value to ensure that it is evaluated correctly (e.g. `"https://pubads.g.doubleclick.net/gampad/ads?iu=/21775744923/external/single_ad_samples&sz=640x480..."` → `"https%3A%2F%2Fpubads.g.doubleclick.net%2Fgampad%2Fads%3Fiu%3D%2F21775744923%2Fexternal%2Fsingle_ad_samples%26sz%3D640x480..."`).<br><br>Optionally, the ad tag URL can be indicated as base-64 encoded URL by using the ID syntax `id:{BASE_64_ENCODED_URL}` (e.g. `id:aHR0cHM6Ly9wdWJhZHMuZy5kb3VibGVjbGljay5uZXQvZ2FtcGFkL2Fkcz9pdT0vMjE3NzU3NDQ5MjMvZXh0ZXJuYWwvc2luZ2xlX2FkX3NhbXBsZXMmc3o9NjQweDQ4MA...`)<br><br>**Note: Please note that it is also possible to indicate the ad tag URL via the [Extended Properties](../../experts-api/special/extended-properties.md) of a content item. In this case, this parameter is ignored.** |
| `debug` | `number` | `0` | No | If this parameter is set to `1`, debug events of the HTML5 content (and possible ads) player will be logged.<br><br>**Note: Depending on the platform, the ads are played in a separate HTML5 player or in a composite HTML5 player (a player that plays ads and content in the same element).** |

### Property syntax of extended properties for IMA plugin

| Property | Value | Example | Description |
|----------|-------|---------|-------------|
| `ima:url` | `{AD_TAG_URL}` | `"ima:url": "https://pubads.g.doubleclick.net/gampad/ads"`<br>`"ima:url": "https://pubads.g.doubleclick.net/gampad/ads?iu=/21775744923/external/single_ad_samples&sz=640x480..."` | The base or complete URL of the ad tag.<br><br>**Note: If this property is not set, the `ad` URL parameter is used.** |
| `ima:param:{PARAM_NAME}` | `{PARAM_VALUE}` | `"ima:param:iu": "/21775744923/external/single_ad_samples"`<br>`"ima:param:output": "vast"` | Optional ad tag URL parameters to be appended to the base URL of the ad tag.<br><br>**Note: This property is ignored if no `ima:url` property is set.** |
| `ima:content:duration` | `{DURATION}` | `"ima:content:duration": "60"` | The content duration in seconds used by the Google IMA SDK. For more information, please see the `contentDuration` property of the `AdsRequest` class ([https://developers.google.com/interactive-media-ads/docs/sdks/html5/client-side/reference/js/google.ima.AdsRequest#contentDuration](https://developers.google.com/interactive-media-ads/docs/sdks/html5/client-side/reference/js/google.ima.AdsRequest#contentDuration)). |
| `ima:content:keywords` | `{KEYWORDS}` | `"ima:content:keywords": "Nature, Education"` | The content keywords used by the Google IMA SDK. For more information, please see the `contentKeywords` property of the `AdsRequest` class ([https://developers.google.com/interactive-media-ads/docs/sdks/html5/client-side/reference/js/google.ima.AdsRequest#contentKeywords](https://developers.google.com/interactive-media-ads/docs/sdks/html5/client-side/reference/js/google.ima.AdsRequest#contentKeywords)). |
| `ima:content:title` | `{TITLE}` | `"ima:content:title": "Example Video"` | The content title used by the Google IMA SDK. For more information, please see the `contentTitle` property of the `AdsRequest` class ([https://developers.google.com/interactive-media-ads/docs/sdks/html5/client-side/reference/js/google.ima.AdsRequest#contentTitle](https://developers.google.com/interactive-media-ads/docs/sdks/html5/client-side/reference/js/google.ima.AdsRequest#contentTitle)). |
| `ima:pauseable` | `{BOOLEAN_VALUE}` | `"ima:pauseable": "false"` | Indicates if ads can be paused. By default, pausing ads is allowed. |
| `ima:skippable` | `{BOOLEAN_VALUE}` | `"ima:skippable": "false"` | Indicates if ads can be skipped. By default, skipping ads is allowed.<br><br>**Note: Please note that this property has no effect if an ad itself does not support skipping (i.e. no skip offset is indicated).** |

## Example

Please note that this example uses the ad tag URLs from the Google IMA SDK samples ([https://developers.google.com/interactive-media-ads/docs/sdks/html5/client-side/tags](https://developers.google.com/interactive-media-ads/docs/sdks/html5/client-side/tags)).

### Screenshot

![Example Screenshot (IMA Plugin)](../../img/ima_plugin.png)

### Code

```json
{
    "type": "list",
    "headline": "IMA Plugin Test",
    "note": "https://developers.google.com/interactive-media-ads/docs/sdks/html5/client-side/tags",
    "compress": true,
    "template": {
        "type": "control",
        "layout": "0,0,16,1",
        "icon": "movie"
    },
    "items": [{
            "label": "Single Inline Linear",
            "playerLabel": "Single Inline Linear",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/single_ad_samples",
                "ima:param:sz": "640x480",
                "ima:param:cust_params": "sample_ct=linear",
                "ima:param:ciu_szs": "300x250,728x90",
                "ima:param:gdfp_req": "1",
                "ima:param:output": "vast",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",
                "ima:param:correlator": ""
            }
        }, {
            "label": "Single Skippable Inline",
            "playerLabel": "Single Skippable Inline",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/single_preroll_skippable",
                "ima:param:sz": "640x480",
                "ima:param:ciu_szs": "300x250,728x90",
                "ima:param:gdfp_req": "1",
                "ima:param:output": "vast",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",
                "ima:param:correlator": ""
            }
        }, {
            "label": "Single Redirect Linear",
            "playerLabel": "Single Redirect Linear",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/single_ad_samples",
                "ima:param:sz": "640x480",
                "ima:param:cust_params": "sample_ct=redirectlinear",
                "ima:param:ciu_szs": "300x250,728x90",
                "ima:param:gdfp_req": "1",
                "ima:param:output": "vast",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",
                "ima:param:correlator": ""
            }
        }, {
            "label": "Single Redirect Error",
            "playerLabel": "Single Redirect Error",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/single_ad_samples",
                "ima:param:sz": "640x480",
                "ima:param:cust_params": "sample_ct=redirecterror",
                "ima:param:ciu_szs": "300x250,728x90",
                "ima:param:gdfp_req": "1",
                "ima:param:output": "vast",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",
                "ima:param:correlator": ""
            }
        }, {
            "label": "Single Redirect Broken (Fallback)",
            "playerLabel": "Single Redirect Broken (Fallback)",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/single_ad_samples",
                "ima:param:sz": "640x480",
                "ima:param:cust_params": "sample_ct=redirecterror",
                "ima:param:ciu_szs": "300x250,728x90",
                "ima:param:gdfp_req": "1",
                "ima:param:output": "vast",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",
                "ima:param:nofb": "1",
                "ima:param:correlator": ""
            }
        }, {
            "label": "Single VPAID 2.0 Linear",
            "playerLabel": "Single VPAID 2.0 Linear",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/single_ad_samples",
                "ima:param:sz": "640x480",
                "ima:param:cust_params": "sample_ct=linearvpaid2js",
                "ima:param:ciu_szs": "300x250,728x90",
                "ima:param:gdfp_req": "1",
                "ima:param:output": "vast",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",               
                "ima:param:correlator": ""
            }
        }, {
            "label": "Single VPAID 2.0 Non-Linear",
            "playerLabel": "Single VPAID 2.0 Non-Linear",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/single_ad_samples",
                "ima:param:sz": "640x480",
                "ima:param:cust_params": "sample_ct=nonlinearvpaid2js",
                "ima:param:ciu_szs": "300x250,728x90",
                "ima:param:gdfp_req": "1",
                "ima:param:output": "vast",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",               
                "ima:param:correlator": ""
            }
        }, {
            "label": "Single Non-linear Inline",
            "playerLabel": "Single Non-linear Inline",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/nonlinear_ad_samples",
                "ima:param:sz": "480x70",
                "ima:param:cust_params": "sample_ct=nonlinear",
                "ima:param:ciu_szs": "300x250,728x90",
                "ima:param:gdfp_req": "1",
                "ima:param:output": "vast",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",               
                "ima:param:correlator": ""
            }
        }, {
            "label": "VMAP Session Ad Rule Pre-roll",
            "playerLabel": "VMAP Session Ad Rule Pre-roll",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/vmap_ad_samples",
                "ima:param:sz": "640x480",
                "ima:param:cust_params": "sar=a0f2",
                "ima:param:ciu_szs": "300x250",
                "ima:param:gdfp_req": "1",
                "ima:param:ad_rule": "1",
                "ima:param:output": "vmap",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",               
                "ima:param:correlator": ""
            }
        }, {
            "label": "VMAP Pre-roll",
            "playerLabel": "VMAP Pre-roll",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/vmap_ad_samples",
                "ima:param:sz": "640x480",
                "ima:param:cust_params": "sample_ar=preonly",
                "ima:param:ciu_szs": "300x250",               
                "ima:param:gdfp_req": "1",
                "ima:param:ad_rule": "1",
                "ima:param:output": "vmap",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",               
                "ima:param:correlator": ""
            }
        }, {
            "label": "VMAP Pre-roll + Bumper",
            "playerLabel": "VMAP Pre-roll + Bumper",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/vmap_ad_samples",
                "ima:param:sz": "640x480",
                "ima:param:cust_params": "sample_ar=preonlybumper",
                "ima:param:ciu_szs": "300x250",               
                "ima:param:gdfp_req": "1",
                "ima:param:ad_rule": "1",
                "ima:param:output": "vmap",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",               
                "ima:param:correlator": ""
            }
        }, {
            "label": "VMAP Post-roll",
            "playerLabel": "VMAP Post-roll",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/vmap_ad_samples",
                "ima:param:sz": "640x480",
                "ima:param:cust_params": "sample_ar=postonly",
                "ima:param:ciu_szs": "300x250",               
                "ima:param:gdfp_req": "1",
                "ima:param:ad_rule": "1",
                "ima:param:output": "vmap",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",               
                "ima:param:correlator": ""
            }
        }, {
            "label": "VMAP Post-roll + Bumper",
            "playerLabel": "VMAP Post-roll + Bumper",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/vmap_ad_samples",
                "ima:param:sz": "640x480",
                "ima:param:cust_params": "sample_ar=postonlybumper",
                "ima:param:ciu_szs": "300x250",               
                "ima:param:gdfp_req": "1",
                "ima:param:ad_rule": "1",
                "ima:param:output": "vmap",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",               
                "ima:param:correlator": ""
            }
        }, {
            "label": "VMAP Pre-, Mid-, and Post-rolls, Single Ads",
            "playerLabel": "VMAP Pre-, Mid-, and Post-rolls, Single Ads",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/vmap_ad_samples",
                "ima:param:sz": "640x480",
                "ima:param:cust_params": "sample_ar=premidpost",
                "ima:param:ciu_szs": "300x250",               
                "ima:param:gdfp_req": "1",
                "ima:param:ad_rule": "1",
                "ima:param:output": "vmap",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",           
                "ima:param:cmsid": "496",
                "ima:param:vid": "short_onecue",
                "ima:param:correlator": ""
            }
        }, {
            "label": "VMAP - Pre-roll Single Ad, Mid-roll Standard Pod with 3 ads, Post-roll Single Ad",
            "playerLabel": "VMAP - Pre-roll Single Ad, Mid-roll Standard Pod with 3 ads, Post-roll Single Ad",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/vmap_ad_samples",
                "ima:param:sz": "640x480",
                "ima:param:cust_params": "sample_ar=premidpostpod",
                "ima:param:ciu_szs": "300x250",               
                "ima:param:gdfp_req": "1",
                "ima:param:ad_rule": "1",
                "ima:param:output": "vmap",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",           
                "ima:param:cmsid": "496",
                "ima:param:vid": "short_onecue",
                "ima:param:correlator": ""
            }
        }, {
            "label": "VMAP - Pre-roll Single Ad, Mid-roll Optimized Pod with 3 Ads, Post-roll Single Ad",
            "playerLabel": "VMAP - Pre-roll Single Ad, Mid-roll Optimized Pod with 3 Ads, Post-roll Single Ad",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/vmap_ad_samples",
                "ima:param:sz": "640x480",
                "ima:param:cust_params": "sample_ar=premidpostoptimizedpod",
                "ima:param:ciu_szs": "300x250",
                "ima:param:gdfp_req": "1",
                "ima:param:ad_rule": "1",
                "ima:param:output": "vmap",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",           
                "ima:param:cmsid": "496",
                "ima:param:vid": "short_onecue",
                "ima:param:correlator": ""
            }
        }, {
            "label": "VMAP - Pre-roll Single Ad, Mid-roll Standard Pod with 3 Ads, Post-roll Single Ad (bumpers around all ad breaks)",
            "playerLabel": "VMAP - Pre-roll Single Ad, Mid-roll Standard Pod with 3 Ads, Post-roll Single Ad (bumpers around all ad breaks)",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/vmap_ad_samples",
                "ima:param:sz": "640x480",
                "ima:param:cust_params": "sample_ar=premidpostpodbumper",
                "ima:param:ciu_szs": "300x250",
                "ima:param:gdfp_req": "1",
                "ima:param:ad_rule": "1",
                "ima:param:output": "vmap",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",           
                "ima:param:cmsid": "496",
                "ima:param:vid": "short_onecue",
                "ima:param:correlator": ""
            }
        }, {
            "label": "VMAP - Pre-roll Single Ad, Mid-roll Optimized Pod with 3 Ads, Post-roll Single Ad (bumpers around all ad breaks)",
            "playerLabel": "VMAP - Pre-roll Single Ad, Mid-roll Optimized Pod with 3 Ads, Post-roll Single Ad (bumpers around all ad breaks)",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/vmap_ad_samples",
                "ima:param:sz": "640x480",
                "ima:param:cust_params": "sample_ar=premidpostoptimizedpodbumper",
                "ima:param:ciu_szs": "300x250",
                "ima:param:gdfp_req": "1",
                "ima:param:ad_rule": "1",
                "ima:param:output": "vmap",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",           
                "ima:param:cmsid": "496",
                "ima:param:vid": "short_onecue",
                "ima:param:correlator": ""
            }
        }, {
            "label": "VMAP - Pre-roll Single Ad, Mid-roll Standard Pods with 5 Ads Every 10 Seconds, Post-roll Single Ad",
            "playerLabel": "VMAP - Pre-roll Single Ad, Mid-roll Standard Pods with 5 Ads Every 10 Seconds, Post-roll Single Ad",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/vmap_ad_samples",
                "ima:param:sz": "640x480",
                "ima:param:cust_params": "sample_ar=premidpostlongpod",
                "ima:param:ciu_szs": "300x250",
                "ima:param:gdfp_req": "1",
                "ima:param:ad_rule": "1",
                "ima:param:output": "vmap",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",           
                "ima:param:cmsid": "496",
                "ima:param:vid": "short_onecue",
                "ima:param:correlator": ""
            }
        }, {
            "label": "SIMID Survey Pre-roll",
            "playerLabel": "SIMID Survey Pre-roll",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/simid",
                "ima:param:description_url": "https://developers.google.com/interactive-media-ads",
                "ima:param:sz": "640x480",             
                "ima:param:gdfp_req": "1",           
                "ima:param:output": "vast",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",              
                "ima:param:correlator": ""
            }
        }, {
            "label": "OM SDK Sample Pre-roll",
            "playerLabel": "OM SDK Sample Pre-roll",
            "action": "video:plugin:http://msx.benzac.de/plugins/ima.html?url=http://msx.benzac.de/media/video3.mp4",
            "properties": {
                "control:type": "extended",
                "ima:url": "https://pubads.g.doubleclick.net/gampad/ads",
                "ima:param:iu": "/21775744923/external/omid_ad_samples",
                "ima:param:description_url": "http://test_site.com/homepage",
                "ima:param:sz": "640x480",
                "ima:param:gdfp_req": "1",           
                "ima:param:output": "vast",
                "ima:param:unviewed_position_start": "1",
                "ima:param:env": "vp",
                "ima:param:impl": "s",
                "ima:param:vpmute": "0",
                "ima:param:vpa": "0",
                "ima:param:vad_forma": "linear",
                "ima:param:url": "http://test_site.com",
                "ima:param:vpos": "preroll",             
                "ima:param:correlator": ""
            }
        }]
}
```

### Demo

- [Launch via App](https://msx.benzac.de/?start=content:https://msx.benzac.de/info/xp/data/plugin_test_11.json)
- [Launch via Demo Page](https://msx.benzac.de/info/?start=content:https://msx.benzac.de/info/xp/data/plugin_test_11.json)

**Note: This demo will only work properly if ad blockers are disabled.**

## See also

- [Video/Audio Plugin](./video-audio-plugin.md)
- [Ad Plugin](./ad-plugin.md)
- [Plugin API Reference](./plugin-api-reference.md)
- [Cookbook → Plugins (media, immersive, platform, ads)](../../reference/cookbook.md#plugins-media-immersive-platform-ads)
