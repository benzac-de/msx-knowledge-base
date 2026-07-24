---
title: MSX Knowledge Base — Master Index
category: Navigation
summary: Master navigation page for the MSX LLM Knowledge Base; mirrors the API taxonomy from the official MSX wiki.
---

# MSX Knowledge Base — Master Index

**Media Station X (MSX)** is a white-label TV app driven entirely by JSON served over HTTP. This knowledge base covers the MSX API from version `0.1.0` to `0.1.166`.

**Note:** This page is a human-readable navigation index. If you are an LLM/agent, use [`llms.txt`](llms.txt) and [`AGENTS.md`](AGENTS.md) instead — see [`README.md`](README.md) for full usage and integration instructions.

---

## Overview

General information about MSX, platform support, release notes, and the quick-start guide.

- [Home](overview/home.md) — Overview of MSX features and capabilities
- [Quick-Start Guide](overview/quick-start-guide.md) — Step-by-step setup instructions
- [Platform Support](overview/platform-support.md) — Supported platforms and current app versions per platform
- [Release Notes](overview/release-notes.md) — Version history and changelog
- [Tips & Tricks](overview/tips-tricks.md) — Practical tips: legacy TV distribution, the Web Version Loader, safely opening external links/apps, unique device IDs, the Launcher MSX service, common errors, platform quirks, and the Emergency Combination
- [Showcases](overview/showcases.md) — Real-world MSX implementations
- [About](overview/about.md) — About Media Station X
- [License](overview/license.md) — License terms
- [Data Privacy](overview/data-privacy.md) — Data privacy information
- [Welcome](overview/welcome.md) — Original wiki welcome page and API taxonomy overview

---

## Main API

### Start API

The start object sets up which menu or content is loaded at startup.

- [Start Object](main-api/start/start-object.md)
- [Setup Precondition](main-api/start/setup-precondition.md)
- [Setup Start Parameter](main-api/start/setup-start-parameter.md)

### Menu API

Menu objects group content objects; displayed as vertical lists on the left side of the screen.

- [Menu Root Object](main-api/menu/menu-root-object.md)
- [Menu Item Object](main-api/menu/menu-item-object.md)
- [Menu Examples](main-api/menu/menu-examples.md)

### Content API

Content objects display content items on a grid; can be `pages` (horizontal) or `list` (vertical).

- [Content Root Object](main-api/content/content-root-object.md)
- [Content Page Object](main-api/content/content-page-object.md)
- [Content Item Object](main-api/content/content-item-object.md)
- [Content Examples](main-api/content/content-examples.md)
- [Content Guide](main-api/content/content-guide.md)

### Common API

Used in both menu and content objects.

- [Requests](main-api/common/requests.md)
- [Responses](main-api/common/responses.md)
- [Colors](main-api/common/colors.md)
- [Icons](main-api/common/icons.md)
- [Inline Expressions](main-api/common/inline-expressions.md)
- [Actions](main-api/common/actions.md)

---

## Extended API

Integration of third-party media sources and playlist formats.

- [YouTube, Vimeo & Co.](extended-api/youtube-vimeo-co.md)
- [M3U/PLS Files](extended-api/m3u-pls-files.md)
- [MRSS Feeds](extended-api/mrss-feeds.md)

---

## Experts API

> The experts API is designed for users who are already familiar with the main API.

### Special API

Special use cases, internal actions, URL parameters, and platform-specific integrations.

- [Internal Actions](experts-api/special/internal-actions.md)
- [URL Parameters](experts-api/special/url-parameters.md)
- [Extended Properties](experts-api/special/extended-properties.md)
- [Attached Data Examples](experts-api/special/attached-data-examples.md)
- [Dictionary Structure](experts-api/special/dictionary-structure.md)
- [Tizen Player](experts-api/special/tizen-player.md)
- [NetCast Menu](experts-api/special/netcast-menu.md)
- [Android Player](experts-api/special/android-player.md)

### Live API

Periodic content item updates (live data, playback progress, etc.) and live inline expressions.

- [Live Object](experts-api/live/live-object.md)
- [Live Inline Expressions](experts-api/live/live-inline-expressions.md)
- [Live Examples](experts-api/live/live-examples.md)

### Selection API

Control the behavior of a content item when it receives focus.

- [Selection Object](experts-api/selection/selection-object.md)
- [Selection Examples](experts-api/selection/selection-examples.md)

### Plugin API

Create custom video/audio or interaction plugins.

- [Video/Audio Plugin](experts-api/plugins/video-audio-plugin.md)
- [Interaction Plugin](experts-api/plugins/interaction-plugin.md)
- [Image Plugin](experts-api/plugins/image-plugin.md)
- [HTML5X Plugin](experts-api/plugins/html5x-plugin.md)
- [Panorama Plugin](experts-api/plugins/panorama-plugin.md)
- [Background Plugin](experts-api/plugins/background-plugin.md)
- [Backdrop Plugin](experts-api/plugins/backdrop-plugin.md)
- [Ad Plugin](experts-api/plugins/ad-plugin.md)
- [Android Plugin](experts-api/plugins/android-plugin.md)
- [Play Plugin](experts-api/plugins/play-plugin.md)
- [IMA Plugin](experts-api/plugins/ima-plugin.md)
- [Paging Plugin](experts-api/plugins/paging-plugin.md)
- [Input Plugin](experts-api/plugins/input-plugin.md)
- [Plugin API Reference](experts-api/plugins/plugin-api-reference.md)
- [Plugin Events Reference](experts-api/plugins/plugin-events-reference.md)

### Hidden Features

Features not originally intended as official API but useful for advanced use cases.

- [Start Action](experts-api/hidden-features/start-action.md)
- [Settings Menu Item](experts-api/hidden-features/settings-menu-item.md)
- [Execute Property](experts-api/hidden-features/execute-property.md)
- [Focus Separator](experts-api/hidden-features/focus-separator.md)
- [Hide Images](experts-api/hidden-features/hide-images.md)
- [Number Inline Expressions](experts-api/hidden-features/number-inline-expressions.md)
- [Color Inline Expressions](experts-api/hidden-features/color-inline-expressions.md)
- [Key Property](experts-api/hidden-features/key-property.md)
- [Large Offsets](experts-api/hidden-features/large-offsets.md)
- [Page Action](experts-api/hidden-features/page-action.md)
- [Dictionary Inline Expressions](experts-api/hidden-features/dictionary-inline-expressions.md)
- [Compress Property](experts-api/hidden-features/compress-property.md)
- [Resolve Action](experts-api/hidden-features/resolve-action.md)
- [Option Shortcut](experts-api/hidden-features/option-shortcut.md)
- [Replace Action](experts-api/hidden-features/replace-action.md)
- [Image Options](experts-api/hidden-features/image-options.md)
- [Advanced Text & Image](experts-api/hidden-features/advanced-text-image.md)
- [Keep Ratio Keyword](experts-api/hidden-features/keep-ratio.md)
- [Advanced Templated Items](experts-api/hidden-features/advanced-templated-items.md)

### Benchmark Tests

Performance and capability tests for TV devices.

- [Renderer Plugin](experts-api/benchmark/renderer-plugin.md)
- [Drawing Plugin](experts-api/benchmark/drawing-plugin.md)
- [Particles Plugin](experts-api/benchmark/particles-plugin.md)

---

## Reference

Consolidated cross-cutting reference documents synthesized from all sources.

- [Type Definitions](reference/type-definitions.md) — TypeScript interfaces for all MSX JSON objects (`MSX*` types) and TVX plugin classes
- [Actions Reference](reference/actions-reference.md) — Complete reference for all main and internal actions
- [JSON Building Guide](reference/json-building-guide.md) — Step-by-step guide to building valid MSX JSON pages
- [Cookbook](reference/cookbook.md) — Curated tour of the most practical real examples: functional pages (media lists, adaptive playback, live data, interaction, plugins) and beautiful layouts. Includes deep dives on resuming playback position, the live TV/EPG guide backend, building your own interaction/video plugin, and `layout`-vs-`offset` navigation
- [Best Practices & Good to Know](reference/best-practices.md) — Recommendations learned from building real pages, plus rendering behaviors/quirks worth knowing — not covered on any single API reference page
- [Versioning](reference/versioning.md) — Version model, compatibility rules, feature minimum versions
- [Glossary](reference/glossary.md) — Key MSX terms defined
- [Common Misconceptions](reference/common-misconceptions.md) — Anti-hallucination reference: plausible-sounding things that do NOT exist
- [In-App Settings Reference](reference/settings-reference.md) — The native, end-user-facing Settings screen: every panel, its allowed values, how to open/set it from a JSON action, and how a plugin reads it. Covers what the URL Parameters and Settings Menu Item pages don't: which settings are runtime-adjustable vs. launch-only, the Context Menu (Home/Player/Volume/Settings/Exit), and the `layout` `fix:` prefix

---

## Examples

101 JSON example files with a searchable catalog.

- [Examples Index](examples/index.md) — Catalog of all examples with metadata
- [guide/](examples/guide/) — 29 basic examples (since `0.1.0`)
- [github/base/](examples/github/base/) — 4 baseline examples (since `0.1.0`; `youtube.json` requires the YouTube plugin, since `0.1.40`)
- [github/extended/](examples/github/extended/) — 14 extended examples (require `0.1.112`, except `list3_section0.json`–`list3_section3.json` which require `0.1.144`)
- [github/](examples/github/) — 2 top-level examples (since `0.1.0`)
- [xp/](examples/xp/) — 52 experimental/advanced tests

---

## Related Links

- [Media Station X — App](https://msx.benzac.de/)
- [Media Station X — Info](https://msx.benzac.de/info/)
- [Media Station X — Icons reference](https://msx.benzac.de/info/icons.html)
- [Media Station X — Actions reference](https://msx.benzac.de/info/xp/actions.html)
- [GitHub Examples](https://github.com/benzac-de/msx)
- [Project README (LLM usage & integration)](README.md)
- [License for this KB (CC BY 4.0)](LICENSE.md)
