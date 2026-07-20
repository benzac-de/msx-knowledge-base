---
title: Backdrop Plugin
category: Experts API - Plugin
summary: Reference for the MSX backdrop plugin that displays an atmospheric backdrop image behind content.
---

# Backdrop Plugin

This is a special interaction plugin that generates an atmospheric backdrop from an image (by extracting the main colors and creating a gradient background). The plugin can be used with version **0.1.82** or higher.

## Usage

The plugin must be loaded with a data object containing an image url and optionally a type. Please see following action syntax example.

- `interaction:load:http://msx.benzac.de/interaction/backdrop.html`

```json
{
    "url": "http://link.to.image",
    "type": 0
}
```

If you would like to use the plugin as reference to implement your own plugin, please have a look at this implementation script: [http://msx.benzac.de/interaction/js/backdrop.js](http://msx.benzac.de/interaction/js/backdrop.js).

## Syntax

Property syntax of backdrop plugin.

| Property | Type | Default Value | Mandatory | Description |
|----------|------|---------------|-----------|-------------|
| `url` | `string` | `null` | **Yes** | The URL of the backdrop image.<br><br>**Note: Please note that CORS HTTP headers must be present for the loaded backdrop image.** |
| `type` | `number` | `0` | No | The type of the backdrop.<br><br>- `0`: Plain atmospheric backdrop.<br>- `1`: Extended atmospheric backdrop (by embedding the image in the upper right corner). |

## Example

Please note that this example uses some properties that are only available in version **0.1.142** or higher.

### Screenshot

![Example Screenshot (Backdrop Plugin)](../../img/backdrop_plugin.png)

### Code

```json
{
    "type": "list",
    "headline": "Backdrop Plugin Test",
    "transparent": 2,
    "preload": "next",
    "underlay": {
        "action": "[quiet|delay:backdrop:1:interaction:load:http://msx.benzac.de/interaction/backdrop.html]",
        "data": {               
            "url": "none"
        },
        "items": [{
                "id": "text",
                "type": "space",
                "layout": "0,0,6,3",
                "headline": "",
                "text": ""
            }]
    },
    "template": {
        "type": "default",
        "layout": "0,0,2,3",
        "area": "0,3,12,3",
        "wrapperColor": "msx-glass",
        "imageHeight": 1.83,
        "color": "msx-glass",
        "imageFiller": "cover",
        "truncation": "titleHeader|titleFooter",
        "titleHeader": "",
        "titleFooter": "",
        "description": "No description available.",
        "backdrop": "none",
        "image": "none",
        "selection": {
            "important": true,
            "action": "data",
             "data": {
                "actions": [{
                        "action": "invalidate:content:underlay:text"
                    }, {
                        "action": "delay:text:1:update:content:underlay:text",
                        "data": {
                            "headline": "{context:titleHeader}",
                            "text": "{context:titleFooter}{br}{br}{context:description}"
                        }
                    }, {
                        "action": "delay:backdrop:1:interaction:load:http://msx.benzac.de/interaction/backdrop.html",
                        "data": {
                            "url": "{context:backdrop}",
                            "type": 1
                        }
                    }]
            }
        },
        "properties": {
            "image:extension": "| {context:titleHeader}"
        }
    },
    "items": [{
            "titleHeader": "{col:msx-white}First Item",
            "titleFooter": "Duis rutrum orci purus",
            "description": "Lorem ipsum dolor sit amet, omittam vivendum ad mea, putant voluptaria mei an, cum saperet probatus ea. Gloriatur adolescens est id, denique oporteat inciderint an nec, ut mea veniam aliquando signiferumque. Quod periculis adversarium qui te. Ea pri facer semper, stet eleifend per ut. Eum soluta pertinacia an, vel tation tollit pertinax et. Aperiri scribentur.",
            "backdrop": "https://picsum.photos/seed/msx_item0/960/540",
            "image": "https://picsum.photos/seed/msx_item0/264/264",
            "action": "image:https://picsum.photos/seed/msx_item0/1920/1080"
        }, {
            "titleHeader": "{col:msx-white}Second Item",
            "titleFooter": "Orci varius natoque penatibus et",
            "description": "Lorem ipsum dolor sit amet, duo iisque erroribus comprehensam no, at pri quis apeirian perpetua. Ea ius harum oportere, idque audiam impedit mea ei, eam ea utinam moderatius. No nam albucius voluptua fabellas, cibo ancillae at nam. Cu viris volutpat.",
            "backdrop": "https://picsum.photos/seed/msx_item1/960/540",
            "image": "https://picsum.photos/seed/msx_item1/264/264",
            "action": "image:https://picsum.photos/seed/msx_item1/1920/1080"
        }, {
            "titleHeader": "{col:msx-white}Third Item",
            "titleFooter": "Sed et sem augue",
            "description": "Lorem ipsum dolor sit amet, habeo singulis vel ex. Cu nec regione constituam, ne has rebum oratio regione. Ei simul vivendo reformidans usu, id nibh.",
            "backdrop": "https://picsum.photos/seed/msx_item2/960/540",
            "image": "https://picsum.photos/seed/msx_item2/264/264",
            "action": "image:https://picsum.photos/seed/msx_item2/1920/1080"
        }, {
            "titleHeader": "{col:msx-white}Fourth Item",
            "titleFooter": "Mauris tincidunt non mauris ut",
            "description": "Lorem ipsum dolor sit amet, ad nam vero efficiendi. At legere abhorreant mei, usu cu tollit delenit invenire. Vis cu quando tractatos concludaturque, an wisi pertinax vis. Ut vis civibus eleifend neglegentur, sea feugait quaestio cu, homero delicata gloriatur at sit. Te ius facer timeam, an eum veniam mnesarchum elitr.",
            "backdrop": "https://picsum.photos/seed/msx_item3/960/540",
            "image": "https://picsum.photos/seed/msx_item3/264/264",
            "action": "image:https://picsum.photos/seed/msx_item3/1920/1080"
        }, {
            "titleHeader": "{col:msx-white}Fifth Item",
            "titleFooter": "Sed et sem augue",
            "description": "Lorem ipsum dolor sit amet, ad noster vocibus albucius pro, alia latine aeterno est cu, sententiae posidonium cu sea. Ex primis placerat sed, nam tollit persecuti ad. Idque maluisset contentiones id nam, pro ex sint impetus dissentiet. Est ne omittantur consectetuer, audiam ocurreret qui eu.",
            "backdrop": "https://picsum.photos/seed/msx_item4/960/540",
            "image": "https://picsum.photos/seed/msx_item4/264/264",
            "action": "image:https://picsum.photos/seed/msx_item4/1920/1080"
        }, {
            "titleHeader": "{col:msx-white}Sixth Item",
            "titleFooter": "Vestibulum ex lectus",
            "description": "Lorem ipsum dolor sit amet, sit no hinc quot doctus.",
            "backdrop": "https://picsum.photos/seed/msx_item5/960/540",
            "image": "https://picsum.photos/seed/msx_item5/264/264",
            "action": "image:https://picsum.photos/seed/msx_item5/1920/1080"
        }, {
            "titleHeader": "{col:msx-white}Seventh Item",
            "titleFooter": "Cras sapien tortor",
            "description": "Lorem ipsum dolor sit amet, duo iisque erroribus comprehensam no, at pri quis apeirian perpetua. Ea ius harum oportere, idque audiam impedit mea ei, eam ea utinam moderatius. No nam albucius voluptua fabellas, cibo ancillae at nam. Cu viris volutpat.",
            "backdrop": "https://picsum.photos/seed/msx_item6/960/540",
            "image": "https://picsum.photos/seed/msx_item6/264/264",
            "action": "image:https://picsum.photos/seed/msx_item6/1920/1080"
        }, {
            "titleHeader": "{col:msx-white}Eighth Item",
            "titleFooter": "Duis rutrum orci purus",
            "description": "Lorem ipsum dolor sit amet, omittam vivendum ad mea, putant voluptaria mei an, cum saperet probatus ea. Gloriatur adolescens est id, denique oporteat inciderint an nec, ut mea veniam aliquando signiferumque. Quod periculis adversarium qui te. Ea pri facer semper, stet eleifend per ut. Eum soluta pertinacia an, vel tation tollit pertinax et. Aperiri scribentur.",
            "backdrop": "https://picsum.photos/seed/msx_item7/960/540",
            "image": "https://picsum.photos/seed/msx_item7/264/264",
            "action": "image:https://picsum.photos/seed/msx_item7/1920/1080"
        }, {
            "titleHeader": "{col:msx-white}Ninth Item",
            "titleFooter": "Aliquam nulla orci",
            "description": "Lorem ipsum dolor sit amet, habeo singulis vel ex. Cu nec regione constituam, ne has rebum oratio regione. Ei simul vivendo reformidans usu, id nibh.",
            "backdrop": "https://picsum.photos/seed/msx_item8/960/540",
            "image": "https://picsum.photos/seed/msx_item8/264/264",
            "action": "image:https://picsum.photos/seed/msx_item8/1920/1080"
        }, {
            "titleHeader": "{col:msx-white}Tenth Item",
            "titleFooter": "Fusce posuere sapien turpis",
            "description": "Lorem ipsum dolor sit amet, ad nam vero efficiendi. At legere abhorreant mei, usu cu tollit delenit invenire. Vis cu quando tractatos concludaturque, an wisi pertinax vis. Ut vis civibus eleifend neglegentur, sea feugait quaestio cu, homero delicata gloriatur at sit. Te ius facer timeam, an eum veniam mnesarchum elitr.",
            "backdrop": "https://picsum.photos/seed/msx_item9/960/540",
            "image": "https://picsum.photos/seed/msx_item9/264/264",
            "action": "image:https://picsum.photos/seed/msx_item9/1920/1080"
        }, {
            "titleHeader": "{col:msx-white}Eleventh Item",
            "titleFooter": "Fusce convallis sagittis tempus",
            "description": "Lorem ipsum dolor sit amet, ad noster vocibus albucius pro, alia latine aeterno est cu, sententiae posidonium cu sea. Ex primis placerat sed, nam tollit persecuti ad. Idque maluisset contentiones id nam, pro ex sint impetus dissentiet. Est ne omittantur consectetuer, audiam ocurreret qui eu.",
            "backdrop": "https://picsum.photos/seed/msx_item10/960/540",
            "image": "https://picsum.photos/seed/msx_item10/264/264",
            "action": "image:https://picsum.photos/seed/msx_item10/1920/1080"
        }, {
            "titleHeader": "{col:msx-white}Twelfth Item",
            "titleFooter": "Aliquam nulla orci",
            "description": "Lorem ipsum dolor sit amet, scaevola perpetua ut qui. Te per partem definitiones, id has modus delenit scriptorem. Eu eos.",
            "backdrop": "https://picsum.photos/seed/msx_item11/960/540",
            "image": "https://picsum.photos/seed/msx_item11/264/264",
            "action": "image:https://picsum.photos/seed/msx_item11/1920/1080"
        }]
}
```

### Demo

- [Launch via App](https://msx.benzac.de/?start=content:https://msx.benzac.de/info/xp/data/plugin_test_8.json)
- [Launch via Demo Page](https://msx.benzac.de/info/?start=content:https://msx.benzac.de/info/xp/data/plugin_test_8.json)

## See also

- [Interaction Plugin](./interaction-plugin.md)
- [Plugin API Reference](./plugin-api-reference.md)
- [Cookbook → Plugins (media, immersive, platform, ads)](../../reference/cookbook.md#plugins-media-immersive-platform-ads)
- [Common Misconceptions → Right property, wrong object](../../reference/common-misconceptions.md#right-property-wrong-object) — using this plugin's `quiet` mode plus a neutral reload as one way to reset a selection `action`'s lasting effect once focus moves on
- [Cookbook → M3U/PLS vs. MRSS](../../reference/cookbook.md#m3upls-vs-mrss-two-different-playlist-import-mechanisms) — why this plugin's `url` needs CORS when an ordinary `image`/`background` doesn't
