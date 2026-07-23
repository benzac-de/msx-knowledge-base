---
title: MSX Knowledge Base — Project README
category: Meta
summary: Human-readable project entry point for the MSX LLM Knowledge Base repository.
---

# MSX Knowledge Base

**KB Version:** `2026-07-23` — the date this snapshot was last generated/updated. A future update always carries a later date, so this value alone tells you whether you're looking at the current KB or an older copy.

An LLM-optimized knowledge base for **Media Station X (MSX)**, covering the MSX app API from version `0.1.0` to `0.1.166`. It is aimed at MSX JSON authors and at LLMs that need to answer MSX questions or build valid MSX JSON pages from scratch.

## What is this?

This knowledge base (KB) is a complete, self-contained conversion of the official MSX documentation — wiki pages, HTML documentation, TypeScript type definitions, and JSON examples — into structured Markdown files. A language model that loads the relevant files from this KB should be able to:

1. Answer any general question about Media Station X.
2. Answer detailed API questions (objects, properties, actions, plugins, inline expressions, versions).
3. Build complete, valid MSX JSON pages from scratch.

## How was it generated?

The KB was produced by converting the official MSX sources (wiki pages in MediaWiki markup, HTML documentation pages, TypeScript type definitions, and JSON examples) to self-contained Markdown. No information was invented; every property, value, version number, and code example is traceable to a source document. This conversion, structuring, and cross-checking work was carried out with the assistance of a powerful AI (a large language model), directed and reviewed by the MSX developer. A machine-readable index (`llms.txt`) and a LLM usage guide (`AGENTS.md`) were added to enable efficient retrieval.

The curated reference documents (`AGENTS.md`, `reference/actions-reference.md`, `reference/glossary.md`, `reference/versioning.md`, `reference/json-building-guide.md`, `reference/cookbook.md`, `reference/best-practices.md`, `reference/common-misconceptions.md`, `reference/settings-reference.md`) went through several rounds of dedicated consistency review, and every one of them was read and signed off by the original MSX developer. Beyond cross-checking these files against each other and against the converted source pages, a substantial amount of additional detail was clarified, corrected, and in some cases contributed from scratch in direct collaboration with the developer — from small scoping fixes (e.g. the exact version scope and correct syntax of the Resolve Action, or the practical status of HbbTV/broadcast actions) to entire documents built primarily from developer input, most notably `reference/settings-reference.md`: the native in-app Settings screen isn't documented anywhere in the original wiki/HTML sources at all, so that page — every panel's purpose, default, and platform availability, the distinction between app-level and framework-level settings, and dozens of individual value meanings — exists only because the developer provided extensive, detailed input directly. Some documents go further still and organize existing knowledge into genuinely new products that never existed in this form in the original sources: `reference/cookbook.md` turns real JSON examples from `examples/` into a practical, use-case-driven tour with property-by-property deep dives, and `reference/best-practices.md` distills recommendations and behavioral facts that were scattered across many source pages into one dedicated reference. These developer-confirmed clarifications and syntheses fill gaps that the source documentation itself left ambiguous or never stated at all, and every claim in them still traces back to a real example, a converted source page, or an explicit developer confirmation — they are treated with the same accuracy standard as the converted source content, not as speculation.

## Repository structure

| Path | Contents |
|---|---|
| `index.md` | Master entry point — full API taxonomy with navigation links |
| `llms.txt` | Machine-readable index of every public KB file (load this first) |
| `AGENTS.md` | Usage guide for the LLM consuming this KB |
| `LICENSE.md` | License for this KB itself (CC BY 4.0) |
| `overview/` | App overview, home page, welcome page (API taxonomy), quick-start, platform support, release notes, tips & tricks, showcases, license, data privacy, about |
| `main-api/` | Start, Menu, Content, and Common API (actions, colors, icons, inline expressions, requests, responses) |
| `extended-api/` | YouTube/Vimeo, M3U/PLS, MRSS |
| `experts-api/` | Special, Live, Selection, Plugin, Hidden Features, Benchmark |
| `reference/` | Type definitions, actions reference, JSON-building guide, cookbook, best practices & good to know, versioning model, glossary, common misconceptions, in-app Settings screen reference |
| `examples/` | 101 JSON example files with a searchable catalog (`index.md`) |
| `img/` | Copied images referenced by the converted pages |

## How to use with an LLM

For an LLM/agent fetching files by URL, use the raw Markdown source under the base path `https://raw.githubusercontent.com/benzac-de/msx-knowledge-base/main/` — e.g. [`llms.txt`](https://raw.githubusercontent.com/benzac-de/msx-knowledge-base/main/llms.txt) and [`AGENTS.md`](https://raw.githubusercontent.com/benzac-de/msx-knowledge-base/main/AGENTS.md). Every workflow below starts the same way: get the LLM to load `llms.txt` (the file index) and `AGENTS.md` (the usage guide) first, then fetch or read further files on demand rather than loading the whole KB at once. All internal links are relative paths, so they resolve correctly on the raw source, the hosted Pages site below, and in a local clone.

As a nice extra for human readers, the same content is also hosted at **[https://benzac-de.github.io/msx-knowledge-base/](https://benzac-de.github.io/msx-knowledge-base/)**, where GitHub Pages renders the Markdown as HTML for browsing. Its Markdown source lives in the repository at **[https://github.com/benzac-de/msx-knowledge-base](https://github.com/benzac-de/msx-knowledge-base)**.

**Language:** the KB's own text is written in English on purpose (consistent terminology, higher information density), but that doesn't limit which language you *use* it in — an LLM that has read these English files can just as well explain MSX or build JSON while conversing in German, Spanish, French, Portuguese, Italian, Dutch, Russian, Japanese, Chinese, or any other widely-used language; translating on the fly from an English source is a general LLM capability, not something this KB needs to support explicitly. Expect the most reliable results in major, well-resourced world languages; very low-resource or niche languages/dialects may read a little less fluently, simply because the underlying model has less training data there, independent of this KB. One rule holds no matter the conversation language: literal MSX syntax — property names, action strings, color/icon names, JSON keys, and code itself (e.g. `titleFooter`, `video:plugin:{URL}`, `msx-red`) — must stay exactly as documented and never get translated, even when every word around it is.

### Option A — LLM with a URL-fetch / browsing tool

If the LLM (or the agent/client it runs in) can fetch arbitrary URLs, point it at the hosted `llms.txt` and let it pull further files as needed.

**Many chat UIs don't fetch URLs by default — the browsing/fetch capability usually needs to be turned on first, or the model will just answer from its general training instead of the actual KB files:**

- **ChatGPT:** make sure browsing or another URL-access capability is enabled for your setup before sending the starting prompt below — without a retrieval tool, the model may answer from its own general knowledge instead of the actual KB files.
- **Claude (claude.ai):** turn on the web-fetch/browsing tool in the chat's tool settings; agent clients with a built-in HTTP/fetch tool (Claude Code, other coding agents) work without extra setup.
- **Gemini:** its URL Context tool needs to be enabled for the model/surface you're using (Gemini app vs. AI Studio), and it has proven unreliable for these specific URLs — uploading the files directly, pasting them into the chat, or adding them to a Gem's knowledge base is more reliable than URL fetch here.
- **Perplexity:** retrieves web content as part of its normal search behavior, so pointing it at these URLs typically works without extra setup, though a narrow Focus/mode setting can restrict what it's willing to fetch.

Exact menu locations change between app versions and subscription plans — if the model doesn't seem to have actually fetched anything, look for a browsing/web-search/tool-use setting in that client rather than assuming the KB itself is unreachable.

Example starting prompt:

```
Act as my Media Station X (MSX) expert.

Directly fetch and read these files first:
- https://raw.githubusercontent.com/benzac-de/msx-knowledge-base/main/llms.txt
- https://raw.githubusercontent.com/benzac-de/msx-knowledge-base/main/AGENTS.md

This is a direct document retrieval task, not a web search.
Do not search for alternative sources.

Use llms.txt as the index and AGENTS.md as the usage guide.
Fetch additional referenced files from https://raw.githubusercontent.com/benzac-de/msx-knowledge-base/main/ whenever you need them.

From now on, answer all my Media Station X questions and build any MSX JSON I ask for strictly from this KB rather than general knowledge or guesses.
```

**Confirm the files were actually loaded before relying on the answers.** A model can acknowledge the fetch instruction and then answer from general training instead of the retrieved content, without any visible error. Before asking real MSX questions, send a quick follow-up such as: "Please confirm you successfully loaded llms.txt and AGENTS.md, and name a few of the specific files or topics you found in the index." A model that actually fetched the files can name concrete file names/topics from them; one that didn't will typically give a vague answer or admit it couldn't fetch anything.

### Option B — Local clone + local/CLI LLM client

Clone the repository and point a local coding agent or LLM client at the checkout; it then reads files from disk on demand instead of fetching over HTTP.

```
git clone https://github.com/benzac-de/msx-knowledge-base.git
```

Example starting prompt (run from inside the cloned repository):

```
Act as my Media Station X (MSX) expert.

Read these files first:
- llms.txt
- AGENTS.md

Use llms.txt as the index and AGENTS.md as the usage guide.
Read additional referenced files from this repository whenever you need them.

From now on, answer all my Media Station X questions and build any MSX JSON I ask for strictly from this KB rather than general knowledge or guesses.
```

This works with any tool that reads local files on demand — Claude Code, other CLI coding agents, or IDE-integrated assistants (Cursor, Continue, etc.) run with the cloned repository open as the workspace.

**Two things worth checking here:**

- **Working directory:** the relative paths in the prompt above (`llms.txt`, `AGENTS.md`) only resolve if the tool's working directory or open workspace is the cloned repository's root — point it there, not at a parent folder or a subfolder.
- **RAG/semantic-search clients:** some IDE assistants (e.g. Cursor, Continue) don't necessarily read a file in full on request — they index the repository semantically and pull back only the chunks that match your query, which can mean `llms.txt`/`AGENTS.md` never actually get loaded in full even though the files are technically accessible. The same follow-up used for Option A applies here too: ask the model to confirm it loaded `llms.txt` and `AGENTS.md`, and to name a few specific files or topics it found in the index, before relying on its answers.

### A note on these starting prompts

They're a general-purpose default, not a guarantee. `AGENTS.md` is trimmed and optimized to cover the large majority of cases well, but even a highly-optimized KB doesn't guarantee a correct result every time — a big, open-ended ask (e.g. "build me a beautiful example app with several content pages") can still come back with real mistakes, depending on the LLM. That's a limit of the LLM you're talking to, not something this KB alone can fully rule out.

### Other ways to use this KB

- **Paste into a chat:** For one-off questions, paste the content of `AGENTS.md` plus the specific topic file(s) you need directly into a chat window — no tools or hosting required.
- **Project/knowledge upload:** Most chat UIs with a "Project" or custom-knowledge feature (Claude Projects, ChatGPT Projects/Custom GPTs, Gemini Gems) let you upload a handful of files — e.g. `AGENTS.md`, `llms.txt`, and the reference files you need most — as persistent knowledge, from a local clone or, where the client supports it, a direct GitHub connection. Uploading the whole KB as a zip? Leave out `img/` — those images only exist so the converted Markdown's image links resolve for human readers browsing the hosted site, not for the LLM to look at, and skipping that folder keeps the zip well under 1 MB instead of tens of megabytes.
- **Bulk ingestion / RAG:** To build your own retrieval system, clone the repo (or fetch individual files via `https://raw.githubusercontent.com/benzac-de/msx-knowledge-base/main/...`) and index the raw Markdown in a vector store; `llms.txt` gives you the full file list to seed the crawl/embedding job.
- **MCP server:** If your client supports the Model Context Protocol, point an MCP filesystem server at a local clone, or an MCP GitHub server at `benzac-de/msx-knowledge-base`, so the LLM can list and read files directly through the tool.

## Example conversation

A worked example of what this KB enables in practice — one JSON page, refined turn by turn and grounded entirely in KB files: a YouTube video list, extended with `resume:key`, a live playback progress footer, and a background image, then carried through to hosting it and setting it up on a real TV. See [`example.md`](example.md) for the full, verbatim transcript.

## Attribution & License

Original content source: the official Media Station X wiki at [https://msx.benzac.de/wiki/](https://msx.benzac.de/wiki/), the documentation/demo pages at [https://msx.benzac.de/info/](https://msx.benzac.de/info/), and the public example repository at [https://github.com/benzac-de/msx](https://github.com/benzac-de/msx). Media Station X itself, all of its official Showcases, and all of the original source content behind this knowledge base were solely developed and authored by Benjamin Zachey.

For license terms applicable to the original content, see the converted license page: [`overview/license.md`](overview/license.md). In short: Media Station X is **freeware, not open source** — its own application code is closed-source. All video/audio/interaction plugins documented in this KB (e.g. `youtube.html`, `html5x.html`, `template.html`, …) are open source, their HTML/JS/CSS can be freely viewed in the browser — Showcases may use additional custom plugins internally that are not documented in this KB. The [Showcases](overview/showcases.md) themselves are not open source, with the sole exception of **RBTV MSX**: [https://github.com/benzac-de/rbtv-msx](https://github.com/benzac-de/rbtv-msx).

This KB itself is a derived, generated artifact, maintained in this repository by Benjamin Zachey — the original MSX developer, publishing here as `benzac-de`. It is licensed separately from the original content under **[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)**: free to use, share, and adapt for any purpose, including commercial projects built on top of MSX — attribution to the original author is the only formal requirement. See [`LICENSE.md`](LICENSE.md) for the full terms, plus a non-binding request about the spirit in which this KB is shared.

## Coverage & version scope

- **KB Version (this snapshot): `2026-07-23`.** Not to be confused with the MSX app version range below — this date tracks the KB document set itself. Whenever this KB is regenerated or updated, this value is replaced with the new generation date.
- MSX app version range covered: **`0.1.0`** to **`0.1.166`**.
- TVX Framework version mentioned in sources: `0.1.35` (no changelog or per-version feature list available in the sources).
- TVX Plugin Library version mentioned in sources: `0.0.79` (no changelog available).
- This KB is a **snapshot** generated at a point in time. For updates beyond `0.1.166`, refer to the official MSX wiki.
