---
title: Colors
category: Main API - Common
summary: Reference for all predefined MSX color values used in icon and element styling.
---

# Colors

All colors are indicated in the **CSS** (Cascading Style Sheets) syntax. Additionally, there are 11 pre-defined colors, starting with the prefix `msx-`.

## Color Syntax

| Syntax | Example | Since Version | Description |
|---|---|---|---|
| CSS | `red`<br>`#f00`<br>`#ff0000`<br>`rgb(255, 0, 0)`<br>`rgb(100%, 0%, 0%)`<br>`hsl(0, 100%, 50%)`<br>`rgba(255, 0, 0, 0.5)`<br>`hsla(0, 100%, 50%, 0.5)` | 0.1.0 | CSS-compliant color. |
| `msx-{KEY}` | `msx-red`<br>`msx-green`<br>`msx-yellow`<br>`msx-blue`<br>`msx-white`<br>`msx-gray`<br>`msx-black`<br>`msx-white-soft`<br>`msx-gray-soft`<br>`msx-black-soft`<br>`msx-glass` | 0.1.0 | Pre-defined color. These colors are used in the Media Station X application. The `{KEY}` part must be replaced with the color key.<br><br>• `red`: Red color.<br>• `green`: Green color.<br>• `yellow`: Yellow color.<br>• `blue`: Blue color.<br>• `white`: White color.<br>• `gray`: Gray color.<br>• `black`: Black color.<br>• `white-soft`: Semi-transparent white color.<br>• `gray-soft`: Semi-transparent gray color.<br>• `black-soft`: Semi-transparent black color.<br>• `glass`: Almost-transparent white color. |

## See also

- [Common Misconceptions → Colors](../../reference/common-misconceptions.md#colors) — confirms there is no `msx-orange`/`msx-purple`/etc. beyond these 11
