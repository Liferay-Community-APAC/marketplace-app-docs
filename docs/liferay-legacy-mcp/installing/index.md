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

- **A Liferay DXP Enterprise Subscription,** for Liferay DXP 7.4 U1–U92, or 2024.Q1 to 2025.Q4. Liferay Legacy MCP is experimental and not supported by Liferay, but it runs only with Liferay DXP. See [Compatibility](../reference/compatibility.html) for which connection each release supports.
- **An administrator account** on that Liferay, to create a user, roles and an OAuth 2 application.
- **Docker,** or another way to run containers, where the server will run: the person's computer, or a server next to Liferay.
- **A test or development copy** of your Liferay. Don't install it in production.

## What You'll Install

| Piece | Where | What It Is |
| --- | --- | --- |
| A service user and two roles | Liferay | The Liferay user the server acts as, with least privilege. |
| An OAuth 2 client extension | Liferay | [`liferay-mcp-oauth.zip`](../assets/downloads/liferay-mcp-oauth.zip): the server's connection to Liferay, an OAuth 2 application limited to the scopes the tools need. |
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

### 1. Create the Service User

Follow [Creating the Service User](liferay-setup.html), on every platform.

### 2. Connect Liferay, Where It Runs

Deploy the OAuth 2 connection, and get its credentials for the server:

<div class="doc-cards">
  <a class="card doc" href="tomcat.html"><strong>Liferay Self-Hosted (Tomcat)</strong><span>Copy the client extension zip into Liferay, and run the server on the Liferay machine or people's computers.</span></a>
  <a class="card doc" href="docker.html"><strong>Liferay Self-Hosted (Docker)</strong><span>Copy the zip into Liferay's container, and run the server in a container that shares Liferay's network.</span></a>
  <a class="card doc" href="paas.html"><strong>Liferay PaaS</strong><span>Add the client extension to your PaaS project, and copy its credentials for the server.</span></a>
</div>

### 3. Connect the AI Assistant

<div class="doc-cards">
  <a class="card doc" href="claude-code.html"><strong>With Claude Code</strong><span>Add the server with one <code>claude mcp add</code> command, in a terminal.</span></a>
  <a class="card doc" href="claude-desktop.html"><strong>With the Claude Desktop App</strong><span>Use the server in the desktop app's chats and its Code tab.</span></a>
  <a class="card doc" href="stdio.html"><strong>Other AI Assistants</strong><span>Desktop AI assistants and IDEs that read an <code>mcpServers</code> setting.</span></a>
  <a class="card doc" href="http.html"><strong>For Services and People</strong><span>One shared server over HTTPS, with API keys for services and Liferay sign-in for people.</span></a>
</div>

Then follow [Checking the Installation](checking.html).

## Related Information

- [Getting Started](../getting-started.html)
- [Upgrading and Removing](upgrading-and-removing.html)
