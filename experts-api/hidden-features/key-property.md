---
title: Key Property
category: Experts API - Hidden Features
summary: Explains the MSX key property hidden feature for assigning keyboard shortcuts to items.
---

# Key Property

It is possible to set a `key` property (of type `string`) to a content item to assign a keyboard key (or a remote control button) to the corresponding item action. If you want to assign multiple keys to the same item, please use the separator `|` (e.g. `"shift|caps_lock"`). This feature is available since version **0.1.97**.

**Note: Only if the corresponding content page is active, the keyboard keys are applied. Please also note that some keys can be assigned differently depending on the used keyboard layout.**

Please see following available keys.

`"0"`,
`"1"`,
`"2"`,
`"3"`,
`"4"`,
`"5"`,
`"6"`,
`"7"`,
`"8"`,
`"9"`,
`"a"`,
`"b"`,
`"c"`,
`"d"`,
`"e"`,
`"f"`,
`"g"`,
`"h"`,
`"i"`,
`"j"`,
`"k"`,
`"l"`,
`"m"`,
`"n"`,
`"o"`,
`"p"`,
`"q"`,
`"r"`,
`"s"`,
`"t"`,
`"u"`,
`"v"`,
`"w"`,
`"x"`,
`"y"`,
`"z"`,
`"space"`,
`"tab"`,
`"shift"`,
`"caps_lock"`,
`"insert"`,
`"home"`,
`"delete"`,
`"end"`,
`"multiply"`,
`"divide"`,
`"add"`,
`"subtract"`,
`"decimal"`,
`"period"`,
`"comma"`,
`"semicolon"`,
`"dash"`,
`"equal"`,
`"quote"`,
`"accent"`,
`"slash"`,
`"backslash"`,
`"bracket_open"`,
`"bracket_close"`.

**Note: Please note that following remote control buttons are mapped to keyboard keys: `REWIND` → `"delete"`, `FORWARD` → `"insert"`, `NEXT_TRACK` → `"end"`, `PREVIOUS_TRACK` → `"home"`. Since version 0.1.119, you can also use the keys `"red"`, `"green"`, `"yellow"`, and `"blue"` for the color buttons. However, the `"blue"` key has no effect, because the menu is assigned to this key. Since version 0.1.148, you can also use the keys `"guide"`, `"info"`, `"clear"`, `"settings"`, `"search"`, `"channel_list"`, `"channel_up"`, `"channel_down"`, `"play"`, `"pause"`, `"stop"`, `"toggle_play_pause"`, `"forward"`, `"rewind"`, `"record"`, `"restart"`, `"next_track"`, and `"previous_track"` for special remote control buttons. However, the `"channel_up"` and `"channel_down"` keys have no effect in menu/content objects, because these keys are used there for fast scrolling.**

Please see following example.

## Example

### Screenshot

![Example Screenshot (Key Property)](../../img/key_property.png)

### Code

```json
{
    "headline": "Key Test",
    "wrap": true,
    "pages": [{
            "items": [{
                    "id": "key",
                    "type": "space",
                    "layout": "0,0,12,1",
                    "color": "msx-white",
                    "label": "{txt:msx-black-soft:Executed Key}"
                }, {
                    "type": "button",
                    "layout": "0,1,1,1",
                    "label": "A",
                    "key": "a",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:A}"
                    }
                }, {
                    "type": "button",
                    "layout": "1,1,1,1",
                    "label": "B",
                    "key": "b",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:B}"
                    }
                }, {
                    "type": "button",
                    "layout": "2,1,1,1",
                    "label": "C",
                    "key": "c",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:C}"
                    }
                }, {
                    "type": "button",
                    "layout": "3,1,1,1",
                    "label": "D",
                    "key": "d",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:D}"
                    }
                }, {
                    "type": "button",
                    "layout": "4,1,1,1",
                    "label": "E",
                    "key": "e",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:E}"
                    }
                }, {
                    "type": "button",
                    "layout": "5,1,1,1",
                    "label": "F",
                    "key": "f",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:F}"
                    }
                }, {
                    "type": "button",
                    "layout": "6,1,1,1",
                    "label": "G",
                    "key": "g",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:G}"
                    }
                }, {
                    "type": "button",
                    "layout": "7,1,1,1",
                    "label": "H",
                    "key": "h",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:H}"
                    }
                }, {
                    "type": "button",
                    "layout": "8,1,1,1",
                    "label": "I",
                    "key": "i",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:I}"
                    }
                }, {
                    "type": "button",
                    "layout": "9,1,1,1",
                    "label": "J",
                    "key": "j",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:J}"
                    }
                }, {
                    "type": "button",
                    "layout": "10,1,1,1",
                    "label": "K",
                    "key": "k",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:K}"
                    }
                }, {
                    "type": "button",
                    "layout": "11,1,1,1",
                    "label": "L",
                    "key": "l",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:L}"
                    }
                }, {
                    "type": "button",
                    "layout": "0,2,1,1",
                    "label": "M",
                    "key": "m",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:M}"
                    }
                }, {
                    "type": "button",
                    "layout": "1,2,1,1",
                    "label": "N",
                    "key": "n",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:N}"
                    }
                }, {
                    "type": "button",
                    "layout": "2,2,1,1",
                    "label": "O",
                    "key": "o",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:O}"
                    }
                }, {
                    "type": "button",
                    "layout": "3,2,1,1",
                    "label": "P",
                    "key": "p",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:P}"
                    }
                }, {
                    "type": "button",
                    "layout": "4,2,1,1",
                    "label": "Q",
                    "key": "q",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:Q}"
                    }
                }, {
                    "type": "button",
                    "layout": "5,2,1,1",
                    "label": "R",
                    "key": "r",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:R}"
                    }
                }, {
                    "type": "button",
                    "layout": "6,2,1,1",
                    "label": "S",
                    "key": "s",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:S}"
                    }
                }, {
                    "type": "button",
                    "layout": "7,2,1,1",
                    "label": "T",
                    "key": "t",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:T}"
                    }
                }, {
                    "type": "button",
                    "layout": "8,2,1,1",
                    "label": "U",
                    "key": "u",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:U}"
                    }
                }, {
                    "type": "button",
                    "layout": "9,2,1,1",
                    "label": "V",
                    "key": "v",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:V}"
                    }
                }, {
                    "type": "button",
                    "layout": "10,2,1,1",
                    "label": "W",
                    "key": "w",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:W}"
                    }
                }, {
                    "type": "button",
                    "layout": "11,2,1,1",
                    "label": "X",
                    "key": "x",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:X}"
                    }
                }, {
                    "type": "button",
                    "layout": "0,3,1,1",
                    "label": "Y",
                    "key": "y",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:Y}"
                    }
                }, {
                    "type": "button",
                    "layout": "1,3,1,1",
                    "label": "Z",
                    "key": "z",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:Z}"
                    }
                }, {
                    "type": "button",
                    "layout": "2,3,1,1",
                    "label": "1",
                    "key": "1",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:1}"
                    }
                }, {
                    "type": "button",
                    "layout": "3,3,1,1",
                    "label": "2",
                    "key": "2",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:2}"
                    }
                }, {
                    "type": "button",
                    "layout": "4,3,1,1",
                    "label": "3",
                    "key": "3",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:3}"
                    }
                }, {
                    "type": "button",
                    "layout": "5,3,1,1",
                    "label": "4",
                    "key": "4",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:4}"
                    }
                }, {
                    "type": "button",
                    "layout": "6,3,1,1",
                    "label": "5",
                    "key": "5",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:5}"
                    }
                }, {
                    "type": "button",
                    "layout": "7,3,1,1",
                    "label": "6",
                    "key": "6",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:6}"
                    }
                }, {
                    "type": "button",
                    "layout": "8,3,1,1",
                    "label": "7",
                    "key": "7",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:7}"
                    }
                }, {
                    "type": "button",
                    "layout": "9,3,1,1",
                    "label": "8",
                    "key": "8",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:8}"
                    }
                }, {
                    "type": "button",
                    "layout": "10,3,1,1",
                    "label": "9",
                    "key": "9",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:9}"
                    }
                }, {
                    "type": "button",
                    "layout": "11,3,1,1",
                    "label": "0",
                    "key": "0",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:0}"
                    }
                }, {
                    "type": "button",
                    "layout": "0,4,1,1",
                    "label": ".",
                    "key": "period",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:Period}"
                    }
                }, {
                    "type": "button",
                    "layout": "1,4,1,1",
                    "label": ",",
                    "key": "comma",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:Comma}"
                    }
                }, {
                    "type": "button",
                    "layout": "2,4,1,1",
                    "label": ";",
                    "key": "semicolon",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:Semicolon} {txt:msx-black-soft:(Alt: Accent)} {txt:msx-black-soft:(DE-Char: Ü or Ö)}"
                    }
                }, {
                    "type": "button",
                    "layout": "3,4,1,1",
                    "label": "-",
                    "key": "dash",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:Dash} {txt:msx-black-soft:(Alt: Bracket Open)}  {txt:msx-black-soft:(DE-Char: - or ß)}"
                    }
                }, {
                    "type": "button",
                    "layout": "4,4,1,1",
                    "label": "=",
                    "key": "equal",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:Equal} {txt:msx-black-soft:(Alt: Bracket Close)} {txt:msx-black-soft:(DE-Char: + or ´)}"
                    }
                }, {
                    "type": "button",
                    "layout": "5,4,1,1",
                    "label": "'",
                    "key": "quote",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:Quote} {txt:msx-black-soft:(DE-Char: Ä)}"
                    }
                }, {
                    "type": "button",
                    "layout": "6,4,1,1",
                    "label": "`",
                    "key": "accent",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:Accent} {txt:msx-black-soft:(Alt: Backslash)} {txt:msx-black-soft:(DE-Char: Ö or ^)}"
                    }
                }, {
                    "type": "button",
                    "layout": "7,4,1,1",
                    "label": "/",
                    "key": "slash",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:Slash} {txt:msx-black-soft:(Alt: Dash)} {txt:msx-black-soft:(DE-Char: # or -)}"
                    }
                }, {
                    "type": "button",
                    "layout": "8,4,1,1",
                    "label": "\\",
                    "key": "backslash",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:Backslash} {txt:msx-black-soft:(Alt: Slash)} {txt:msx-black-soft:(DE-Char: ^ or #)}"
                    }
                }, {
                    "type": "button",
                    "layout": "9,4,1,1",
                    "label": "[",
                    "key": "bracket_open",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:Bracket Open} {txt:msx-black-soft:(Alt: Semicolon)} {txt:msx-black-soft:(DE-Char: ß or Ü)}"
                    }
                }, {
                    "type": "button",
                    "layout": "10,4,1,1",
                    "label": "]",
                    "key": "bracket_close",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:Bracket Close} {txt:msx-black-soft:(Alt: Equal)} {txt:msx-black-soft:(DE-Char: ´ or +)}"
                    }
                }, {
                    "type": "button",
                    "layout": "11,4,1,1",
                    "label": "{ico:file-upload}",
                    "key": "shift|caps_lock",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:Shift/Caps Lock}"
                    }
                }, {
                    "type": "button",
                    "layout": "0,5,3,1",
                    "label": "{ico:backspace}",
                    "key": "delete",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:Delete}"
                    }
                }, {
                    "type": "button",
                    "layout": "3,5,3,1",
                    "label": "{ico:clear}",
                    "key": "home|end",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:Home/End (Clear)}"
                    }
                }, {
                    "type": "button",
                    "layout": "6,5,3,1",
                    "label": "{ico:space-bar}",
                    "key": "space|insert",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:Space/Insert}"
                    }
                }, {
                    "type": "button",
                    "layout": "9,5,3,1",
                    "label": "{ico:done}",
                    "key": "none",
                    "action": "update:content:key",
                    "data": {
                        "label": "{txt:msx-black:Done}"
                    }
                }]
        }]
}
```

### Demo

- [Launch via App](https://msx.benzac.de/?start=content:https://msx.benzac.de/info/xp/data/hidden_feature_8.json)
- [Launch via Demo Page](https://msx.benzac.de/info/?start=content:https://msx.benzac.de/info/xp/data/hidden_feature_8.json)
