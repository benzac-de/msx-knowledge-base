---
title: Requests
category: Main API - Common
summary: Reference for MSX request types and their usage in content and menu objects.
---

# Requests

If JSON files are requested from a server, a version parameter `v` (which indicates the current Media Station X version) and a timestamp parameter `t` (which indicates the current timestamp) are automatically appended. Please see following action examples and the corresponding HTTP requests.

- `menu:http://msx.benzac.de/info/data/guide/actions.json` → `http://msx.benzac.de/info/data/guide/actions.json?v=0.1.128&t=1612867695816`.
- `content:http://msx.benzac.de/info/data/guide/videos.json` → `http://msx.benzac.de/info/data/guide/videos.json?v=0.1.128&t=1612867695816`.

Since version **0.1.128**, it is also possible to replace specific keywords inside a JSON URL with the corresponding values. Please see following available keywords (please note that the keyword `{ID}` is only available in version **0.1.142** or higher, and the keyword `{LANGUAGE}` is only available in version **0.1.160** or higher).

`{HOST}`, `{SECURE}`, `{CLIENT}`, `{PLATFORM}`, `{ID}`, `{PLAYER}`, `{LANGUAGE}`, `{WIDTH}`, `{HEIGHT}`.

Please see following action examples and the corresponding HTTP requests.

- `menu:http://msx.benzac.de/info/data/guide/actions.json?platform={PLATFORM}` → `http://msx.benzac.de/info/data/guide/actions.json?platform=default&v=0.1.128&t=1612867695816`.
- `content:http://msx.benzac.de/info/data/guide/videos.json?platform={PLATFORM}&player={PLAYER}` → `http://msx.benzac.de/info/data/guide/videos.json?platform=default&player=html5%2Fplugin&v=0.1.128&t=1612867695816`.
- `slideshow:http://msx.benzac.de/info/data/guide/images.json?width={WIDTH}&height={HEIGHT}` → `http://msx.benzac.de/info/data/guide/images.json?width=1280&height=720&v=0.1.128&t=1612867695816`.
- `video:resolve:http://msx.benzac.de/services/resolve.php?type=video&host={HOST}&secure={SECURE}&platform={PLATFORM}&player={PLAYER}` → `http://msx.benzac.de/services/resolve.php?type=video&host=msx.benzac.de&secure=false&platform=default&player=html5%2Fplugin&v=0.1.128&t=1612867695816`.

**Note: If you want to use the `{LANGUAGE}` keyword for the startup data, you have to use the `reference` property, because the dictionary (which defines the language) is loaded after the startup data. The language at startup (when no dictionary is loaded yet) always has the value `"default"`.**

## See also

- [Common Misconceptions → Dictionary file](../../reference/common-misconceptions.md#dictionary-file) — why a `dictionary` property only takes effect when loaded at startup, the same restriction behind the `{LANGUAGE}`/`reference` note above
- [Responses](responses.md)
