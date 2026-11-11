---
title: Liferay Legacy MCP
nav_order: 1
footer_note: Liferay Legacy MCP is available under Liferay's dual license, LGPL-2.1-or-later or the Liferay DXP EULA. Copyright &copy; 2026 Liferay, Inc. A Liferay Community APAC app for experimental use, not supported by Liferay, and not meant for production.
description: Liferay Legacy MCP connects AI assistants to Liferay DXP 2024.Q1 to 2025.Q4, releases with no MCP support of their own.
---

# Liferay Legacy MCP
{: .no_toc }

An MCP server for Liferay DXP releases that don't have one: 2024.Q1 to 2025.Q4, and the 7.4 update releases. AI assistants, such as desktop chat apps, IDEs and coding agents like Claude Code, can then find, read and change your Liferay's content in plain language, with Liferay's own permissions, and with a preview a person confirms before anything changes.

{: .warning }
**A community app, for experimental use.** Liferay Legacy MCP is shared by Liferay Community APAC. It isn't a Liferay product: Liferay doesn't support it, and it comes as is, with no warranty and no support commitment. Use it to explore and evaluate on a test or development copy of your Liferay, not in production. AI assistants can misread a request, so start with writes off, and check every preview before you confirm it.

<div class="doc-cards">
  <a class="card doc" href="getting-started.html"><strong>Getting Started</strong><span>What the server does, and the shortest way to try it with a desktop AI assistant.</span></a>
  <a class="card doc" href="installing/"><strong>Installing</strong><span>The service user, the OAuth connection, and the server for one person, or for services and people over HTTPS.</span></a>
  <a class="card doc" href="using/"><strong>Using the Server</strong><span>What to ask, how writes are previewed and confirmed, and the page inspection tools.</span></a>
  <a class="card doc" href="reference/"><strong>Reference</strong><span>Tools, settings, compatibility, and known issues.</span></a>
</div>

## Highlights

- **For releases with no MCP.** Liferay DXP 2026.Q1 and later can be reached by AI through Liferay's own features. This server brings the same to 2024.Q1 to 2025.Q4 and the 7.4 update releases, without changing Liferay.
- **Content tools.** Search, read, create and update web content, documents, blogs, categories and tags, navigation menus, knowledge base articles and translations, and run review workflows.
- **Every API, when you need it.** Generic tools reach all of Liferay's Headless REST APIs and its JSON web services, so the AI assistant isn't limited to the dedicated tools.
- **Safe writes.** Writes are off by default. When turned on, every change is shown as a preview, and runs only after it's confirmed. Deletes, batches and permission changes need their own setting. Every attempt is written to an audit log.
- **Least privilege.** The server acts as one service user, through an OAuth 2 application whose scopes limit what it can reach. Liferay enforces both.
- **Page inspection.** See a page's headings, links, missing image descriptions and errors, and take screenshots, as a guest or as a view-only user.

## Supported Versions

| Liferay Legacy MCP | Liferay DXP |
| --- | --- |
| 1.1.0 | 2024.Q1 to 2025.Q4, tested on the latest update of each quarter. |
| 1.1.0 | 7.4 update releases from U92, tested on U92. |
| 1.1.0 | 7.4 update releases U1 to U91, with a [password connection](reference/compatibility.html#74-update-releases-before-u92) instead of OAuth 2. |

The server is a container image, `ghcr.io/liferay-community-apac/liferay-legacy-mcp`, for `linux/amd64` and `linux/arm64`. To install it, follow [Installing](installing/).

