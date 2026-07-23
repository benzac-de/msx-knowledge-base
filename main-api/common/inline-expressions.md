---
title: Inline Expressions
category: Main API - Common
summary: Reference for MSX inline expressions used in text values, properties, and action strings.
---

# Inline Expressions

Most of the text properties support inline expressions for adding icons or coloring text parts. Each expression starts with character `{` and ends with character `}`. Nested expressions are not supported.

## Expression Syntax

| Expression | Example | Since Version | Description |
|---|---|---|---|
| `{sp}` | `{sp}` | 0.1.0 | Non-breakable space. |
| `{tb}` | `{tb}` | 0.1.0 | Non-breakable tab. |
| `{br}` | `{br}` | 0.1.0 | Line break. |
| `{ico:{ICON}}` | `{ico:home}` | 0.1.0 | Icon. The `{ICON}` part must be replaced with the icon name. Please see [Icons](./icons.md) for possible values. |
| `{ico:{COLOR}:{ICON}}` | `{ico:msx-red:home}` | 0.1.0 | Colored icon. The `{COLOR}` part must be replaced with the color value and the `{ICON}` part must be replaced with the icon name. Please see [Colors](./colors.md) and [Icons](./icons.md) for possible values. |
| `{txt:{COLOR}:{TEXT}}` | `{txt:msx-red:Hello there!}` | 0.1.0 | Colored text. The `{COLOR}` part must be replaced with the color value and the `{TEXT}` part must be replaced with any text. Please see [Colors](./colors.md) for possible color values. |
| `{chr:{CHARACTER}}` | `{chr:copy}`<br>`{chr:#169}` | **0.1.145** | Character entity. The `{CHARACTER}` part must be replaced with an HTML character entity name or number without the leading `&` and trailing `;` character (e.g. `"{chr:copy}"` → `"&copy;"` → "©"). |
| `{chr:{COLOR}:{CHARACTER}}` | `{chr:msx-red:copy}`<br>`{chr:msx-red:#169}` | **0.1.145** | Colored character entity. The `{COLOR}` part must be replaced with the color value and the `{CHARACTER}` part must be replaced with an HTML character entity name or number without the leading `&` and trailing `;` character (e.g. `"{chr:msx-red:copy}"` → `"<span style="color:#c9302c">&copy;</span>"` → "©"). Please see [Colors](./colors.md) for possible color values. |
| `{context:{PROPERTY}}` | `{context:label}` | 0.1.0 | This is a very special inline expression and can only be used inside the `data` property of a template object. It is used to reference properties of a templated item. The `{PROPERTY}` part must be replaced with any property that is available in the templated item (e.g. if the item has a property `"customProperty": "customValue"`, it can be referenced with `"{context:customProperty}"` → `"customValue"`). The referenced property must exist (either in the templated item or in the template object), otherwise the expression can not be replaced and will remain as it is (e.g. `"{context:missingProperty}"` → `"{context:missingProperty}"`). Additionally, the value of the referenced property must be indicated as string, otherwise the expression is removed (e.g. `"{context:invalidProperty}"` → `""`).<br><br>**Note: Since version 0.1.110, also the `properties`, `live`, `selection`, and `options` property of a template object supports this inline expression.** |
| `{unknown}` | `{unknown}` | 0.1.0 | Any unknown expression is returned as it is without the braces (e.g. `"{unknown}"` → `"unknown"`). |

## Related Live API

- [Live Inline Expressions](../../experts-api/live/live-inline-expressions.md)

## Related Hidden Features

- [Number Inline Expressions](../../experts-api/hidden-features/number-inline-expressions.md)
- [Color Inline Expressions](../../experts-api/hidden-features/color-inline-expressions.md)
- [Dictionary Inline Expressions](../../experts-api/hidden-features/dictionary-inline-expressions.md)

## See Also

- [Common Misconceptions → Inline expressions](../../reference/common-misconceptions.md#inline-expressions) — the placeholder-braces trap, why unknown expressions fail silently, and which properties do/don't support inline expressions at all
