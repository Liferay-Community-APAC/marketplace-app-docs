---
title: AI Hub Legacy Connector — Full docs (draft)
nav_exclude: true
search_exclude: true
footer_note: AI Hub Legacy Connector is a Liferay Community APAC app that brings a licensed Liferay AI Hub to earlier DXP releases. It requires an active Liferay AI Hub subscription and is proprietary, for Liferay internal use. Copyright &copy; 2026 Liferay, Inc.
description: Draft of the full AI Hub Legacy Connector documentation — overview, install, configuration, usage and reference — for review before it replaces the coming-soon page.
---

# AI Hub Legacy Connector
{: .no_toc }

Full documentation — **draft for review**.
{: .fs-5 .fw-300 }

An OSGi module for **Liferay DXP 2024.Q1 to 2025.Q4** that brings Liferay AI Hub to releases that shipped before the AI Hub cell. It puts AI Hub's chat widget on every page and adds AI Hub's in-editor AI Assistant to the Web Content editor — writing tools, tag and category suggestions, field and image generation, and translation — with a per-user token and a server-side proxy, and without changing Liferay's code. It steps aside automatically on releases that have Liferay's own AI Hub cell (2026.Q1 and later).

{: .warning }
**Draft — not yet linked from the site.** This is the complete documentation set, published for review at its own URL and deliberately kept out of the site navigation and the app's coming-soon page. Nothing here is linked from the public nav yet. Once approved, these pages become the app's real docs (replacing the coming-soon landing). Screenshots are from a development build and will be refreshed against the shipped release.

<div class="doc-cards">
  <a class="card doc" href="getting-started.html"><strong>Getting Started</strong><span>What the connector does, how the pieces fit together, and what you need before installing.</span></a>
  <a class="card doc" href="installing.html"><strong>Installing</strong><span>The connector user, the OAuth client extensions, and the module — with Docker or by hand.</span></a>
  <a class="card doc" href="configuration.html"><strong>Configuration</strong><span>Connect AI Hub, turn on chat and the assistant, the proxy, the secret file, and multi-instance servers.</span></a>
  <a class="card doc" href="using.html"><strong>Using</strong><span>The chat widget, the in-editor AI Assistant, and the workflow agent step.</span></a>
  <a class="card doc" href="reference.html"><strong>Reference</strong><span>Compatibility, the settings reference, author permissions, security, and the feature gaps.</span></a>
</div>

## What it brings

- **AI Hub Chat** — AI Hub's chat widget on every page, configured in Instance and Site Settings, exactly as the 2026.Q1+ cell and Click to Chat's AI Hub provider do it.
- **In-editor AI Assistant** — writing tools (improve, fix spelling &amp; grammar, make shorter/longer, change tone), suggest tags and categories, generate a field value or an image, and translate content into the site's other languages, inside the Web Content editor.
- **Per-user tokens** — the connector mints a token for the signed-in user so the chat and assistant act as that user, the way Liferay's cell does.
- **Server-side proxy** — on by default, so AI Hub's access token stays on the server and browsers only ever call their own Liferay.
- **AI Hub's Liferay Search** on 2024.Q1 and 2024.Q2, which lack the endpoint it calls.
- **Workflow agent step** — run an AI Hub agent as a workflow step through Kaleo, standing in for the 2026.Q1+ AI Hub agent node.

{: .important }
**A licensed Liferay AI Hub is required.** The connector reproduces, on earlier releases, the part of AI Hub that runs on the customer's DXP (the "cell") — it mints the user token and hosts the front end, then calls your AI Hub instance for every chat message and assistant action. It is not a model provider and does nothing without an AI Hub subscription. It is a Liferay Community APAC app for Liferay internal use; Liferay does not support it.
