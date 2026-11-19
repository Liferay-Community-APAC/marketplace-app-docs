---
title: AI Hub Legacy Connector — Full docs v2 (draft)
nav_exclude: true
search_exclude: true
footer_note: AI Hub Legacy Connector is a Liferay Community APAC app that brings a licensed Liferay AI Hub to earlier DXP releases. It requires an active Liferay AI Hub subscription and is proprietary, for Liferay internal use. Copyright &copy; 2026 Liferay, Inc.
description: Expanded draft of the AI Hub Legacy Connector documentation — a page per feature, with screenshots — for review before it replaces the current docs.
---

# AI Hub Legacy Connector
{: .no_toc }

Full documentation — **expanded draft for review** (v2, with screenshots).
{: .fs-5 .fw-300 }

An OSGi module for **Liferay DXP 2024.Q1 to 2025.Q4** that brings Liferay AI Hub to releases that shipped before the AI Hub cell. It puts AI Hub's chat widget on every page and adds AI Hub's in-editor AI Assistant to the Web Content editor — writing tools, tag and category suggestions, field and image generation, and translation — with a per-user token and a server-side proxy, and without changing Liferay's code. It steps aside automatically on releases that have Liferay's own AI Hub cell (2026.Q1 and later).

{: .warning }
**Draft — not linked from the site.** This is a more complete rewrite of the live docs, published at its own URL and kept out of the site navigation and the app's page. Once approved it replaces the current documentation. Screenshots are from a development build on DXP 2025.Q1 with the connector deployed.

![AI Hub's chat widget on a page, answering as the signed-in user.](assets/images/chat-widget.png){: .screenshot }

![The Web Content editor with the connector's in-editor AI Assistant: an AI menu open on the Title with Improve writing, Fix spelling &amp; grammar, Make shorter/longer, Change tone, Generate and Translate.](assets/images/change-tone.png){: .screenshot }

## Documentation

<div class="doc-cards">
  <a class="card doc" href="getting-started.html"><strong>Getting Started</strong><span>What it does, how the pieces fit, and what you need.</span></a>
  <a class="card doc" href="installing.html"><strong>Installing</strong><span>The connector user, OAuth client extensions, and the module — Docker or by hand.</span></a>
  <a class="card doc" href="configuration.html"><strong>Configuration</strong><span>Connect AI Hub, the secret file, and multi-instance servers.</span></a>
  <a class="card doc" href="chat.html"><strong>Chat Widget</strong><span>Turn on AI Hub Chat and how it behaves.</span></a>
  <a class="card doc" href="in-editor-assistant.html"><strong>In-editor Assistant</strong><span>Writing tools, tags, categories, generate, image — with the proxy.</span></a>
  <a class="card doc" href="translate.html"><strong>Translate</strong><span>Translate localizable fields into the site's languages.</span></a>
  <a class="card doc" href="workflow-agent-step.html"><strong>Workflow Agent Step</strong><span>Run an AI Hub agent as a Kaleo workflow step.</span></a>
  <a class="card doc" href="liferay-search.html"><strong>Liferay Search</strong><span>AI Hub's RAG search node across the eight releases.</span></a>
  <a class="card doc" href="compatibility.html"><strong>Compatibility</strong><span>The eight supported releases and platforms.</span></a>
  <a class="card doc" href="settings.html"><strong>Settings Reference</strong><span>Every setting and environment variable.</span></a>
  <a class="card doc" href="permissions.html"><strong>Author Permissions</strong><span>What a non-admin author needs for each action.</span></a>
  <a class="card doc" href="security.html"><strong>Security</strong><span>How the connector keeps tokens and secrets safe.</span></a>
  <a class="card doc" href="feature-gaps.html"><strong>Feature Gaps</strong><span>What stays with AI Hub, and why.</span></a>
</div>

{: .important }
**A licensed Liferay AI Hub is required.** The connector reproduces, on earlier releases, the part of AI Hub that runs on the customer's DXP (the "cell") — it mints the user token and hosts the front end, then calls your AI Hub instance for every chat message and assistant action. It is not a model provider and does nothing without an AI Hub subscription. It is a Liferay Community APAC app for Liferay internal use; Liferay does not support it.
