---
title: Installing
nav_order: 3
---

# Installing
{: .no_toc }

These pages set up Liferay Legacy MCP 1.1.0 against an existing Liferay DXP: first the Liferay side, then the server for the people or agents who will use it.

{: .warning }
Liferay Legacy MCP is a community app for experimental use, not supported by Liferay. Install it on a test or development copy of your Liferay, not in production.

1. TOC
{:toc}

## Before You Begin

Check that you have:

- **Liferay DXP 2024.Q1 to 2025.Q4,** or a 7.4 update release. See [Compatibility](../reference/compatibility.html) for which connection each release supports.
- **An administrator account** on that Liferay, to create a user, roles and an OAuth 2 application.
- **Docker,** or another way to run containers, where the server will run: the person's computer, or a server next to Liferay.
- **A test or development copy** of your Liferay. Don't install it in production.

## What You'll Install

| Piece | Where | What It Is |
| --- | --- | --- |
| A service user and two roles | Liferay | The Liferay user the server acts as, with least privilege. |
| An OAuth 2 application | Liferay | The server's connection to Liferay, limited to the scopes the tools need. |
| The server | A container | `ghcr.io/liferay-community-apac/liferay-legacy-mcp`, in two variants (below). |
| A sign-in application (optional) | Liferay | Only for people who sign in over HTTPS. |

### The Images

| Tag | Size | What It Includes |
| --- | --- | --- |
| `1.1.0-slim` | about 350 MB | Every tool except the page inspection tools, which show as unavailable. |
| `1.1.0-full` | about 1.2 GB | Also `inspect_page` and `take_screenshot`, with a headless Chromium. |

Both run on `linux/amd64` and `linux/arm64`, as a non-root user, write only to `/data`, and contain no credentials. The package is public:

```bash
docker pull ghcr.io/liferay-community-apac/liferay-legacy-mcp:1.1.0-slim
```

To pin an exact build, use its digest:

| Tag | Digest |
| --- | --- |
| `1.1.0-slim` | `sha256:6c8f4457d3f7ad79c4d3f2a2f79e72449a9838c839dcf327370d0a3ff0316c13` |
| `1.1.0-full` | `sha256:ed68f831c2a7be57c06520fda711c04982f702d76702ee91a95ce8db4c4e7195` |

## Steps

Start with the Liferay side, then choose how the server is used:

<div class="doc-cards">
  <a class="card doc" href="liferay-setup.html"><strong>1. Setting Up Liferay</strong><span>The service user, its roles, and the OAuth 2 connection. Needed for every way of using the server.</span></a>
  <a class="card doc" href="stdio.html"><strong>2a. For One Person</strong><span>A desktop AI assistant or IDE starts the server on the person's computer.</span></a>
  <a class="card doc" href="claude-code.html"><strong>2b. With Claude Code</strong><span>The same, added with one <code>claude mcp add</code> command.</span></a>
  <a class="card doc" href="http.html"><strong>2c. For Services and People</strong><span>The server runs over HTTPS, with API keys for services and Liferay sign-in for people.</span></a>
</div>

Then follow [Checking the Installation](checking.html).

## Related Information

- [Getting Started](../getting-started.html)
- [Upgrading and Removing](upgrading-and-removing.html)
