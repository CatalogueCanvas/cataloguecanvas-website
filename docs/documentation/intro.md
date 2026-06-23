---
title: Introduction
---

<p class="kicker">Showcase</p>

# Introduction

<p class="lead">A domain-agnostic catalogue server: ingest ZIP items into a self-hosted app, organise them into collections, and share public portfolios.</p>

## What it is

CatalogueCanvas is a self-hosted catalogue server. You ingest your digital art work as a ZIP file, organise them into collections, enrich them with metadata and optional AI-generated descriptions, and
publish curated portfolios as shareable slide-deck pages. It runs from a single Docker container.

   ![Item](../assets/Items_CC.png)

## What it does

- **Ingest** — one ZIP becomes one catalogue item; the main image is auto-converted to a WebP preview.
- **Organise** — group items into collections and a built-in Favourites set.
- **Exploration** — simple quick exploration of the work uploaded, searchable.
- **Enrich** — edit titles, tags, and Markdown notes; optionally describe items with a vision LLM[^llm].
- **Publish** — assemble portfolios and share them publicly at a `/p/<slug>` link.
- **Store & back up** — keep assets across multiple libraries; export the database and all files.

## Key concepts

| Concept | In one line |
|---|---|
| **Item** | A single catalogued work, created from one uploaded ZIP. |
| **Collection** | A named grouping of items. |
| **Portfolio** | A curated, shareable slide-deck of items. |
| **Library** | A storage location for item assets. |

→ Full definitions in the [Glossary](glossary.md).

## Next steps

- New here? Read **[Uses & for whom](uses.md)**.
- Want to run it? See **[Install](install.md)**.
- Using it day to day? See the **[User documentation](user.md)**.

## Frequently asked questions

<!-- TEMPLATE: collapsible FAQ. `???` = collapsed, `???+` = open by default.
     Indent the answer by four spaces. Duplicate a block per question. -->

??? question "What is CatalogueCanvas?"
    A domain-agnostic, self-hosted catalogue server. You ingest items as ZIP files, organise them into collections, and publish curated portfolios to a shareable link or to print. It runs from a single Docker container (bare metal install are possible, check [install](install.md)).

??? question "How is it licensed?"
    CatalogueCanvas is open source under the [GNU AGPL v3.0](https://www.gnu.org/licenses/agpl-3.0.en.html). You can use it, modify it, distribute it, and build on it for any project, with no fees.

??? question "Can I contribute?"
    Yes. On the project's GitHub open an issue for problems or to suggest a new feature, or code them yourself and open a pull request. The link is at the top of the page.

??? question "Is CatalogueCanvas production ready?"
    CatalogueCanvas is a stable beta. It may change as development continues. I avoid breaking changes, but if one lands you will get a warning, and I will test transition scripts so existing installations keep working. I would not run it on a server exposed to the internet yet.

??? question "How do I report a problem or request a feature?"
    Open an issue on [GitHub](https://github.com/ToroRojo-code/CatalogueCanvas/). For a bug, attach the system log from the settings script. For a feature, describe the use case.

[^llm]: **LLM** — a large language model. I have tested local models in LM Studio, Ollama, and llama-cpp.
