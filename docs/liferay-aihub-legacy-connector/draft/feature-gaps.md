---
title: Feature Gaps
nav_exclude: true
search_exclude: true
description: What the connector intentionally leaves with AI Hub, and the one deliberate behavior difference.
---

# Feature Gaps
{: .no_toc }

Two AI Hub capabilities are intentionally **not** reproduced, because their targets don't exist on 2024.Q1–2025.Q4:

- **Bulk content generation** (`L_GENERATE_CONTENT`) targets the 2026.Q1+ Liferay CMS (Spaces, the `/o/cms/…` API), a content model these releases don't have (they use Web Content / journal). Its output has nowhere to land, so it stays with AI Hub.
- **The native translate agent** (`L_TRANSLATE_CONTENT`) is bound to AI Hub's editor session — it reads the content server-side through a page-context node, so a stateless connector call has no context to give it. **Translation itself is shipped** — the connector translates field by field with the portable field-generation agent instead. See [Translate](translate.html).

One deliberate behavior difference:

- **Generate image** uploads to **Documents &amp; Media** and inserts into the body, where the native (2026.Q1+) uploads to the CMS document store — because that store doesn't exist on these releases.

Everything else the cell does on the DXP side — the chat widget, per-user tokens, the proxy, the in-editor assistant's writing tools, tag and category suggestions, field generation, and the workflow agent step — is reproduced. See the [In-editor Assistant](in-editor-assistant.html) for the full action list.
