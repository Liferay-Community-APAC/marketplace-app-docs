---
title: With Claude Code
parent: Installing
nav_order: 5
---

# With Claude Code
{: .no_toc }

[Claude Code](https://claude.com/claude-code) adds MCP servers with one command, `claude mcp add`, and starts them itself, so each person runs their own copy of the server on their computer.

{: .important }
You need Docker, Claude Code, and a copy of the server's credentials on your computer, as in the page for where your Liferay runs ([Tomcat](tomcat.html), [Docker](docker.html), [PaaS](paas.html)), readable only by you.

1. TOC
{:toc}

## Step 1: Add the Server

Run this once, with your Liferay's address, your routes folder, and the site to work in by default:

```bash
claude mcp add liferay-legacy -- docker run -i --rm \
  -v ~/liferay-mcp/routes:/run/liferay-routes:ro \
  -v liferay-mcp-data:/data \
  -e LIFERAY_URL=https://liferay.example.com \
  -e LIFERAY_ROUTES_CLIENT_EXTENSION=/run/liferay-routes/liferay-mcp-oauth \
  -e LIFERAY_DEFAULT_SITE=my-site \
  ghcr.io/liferay-community-apac/liferay-legacy-mcp:1.1.0-slim
```

Everything after `--` is the command Claude Code runs to start the server. `~/liferay-mcp/routes` is the folder that holds the `liferay-mcp-oauth` folder.

{: .tip }
`liferay-legacy` is the server's name in Claude Code, and its tools are named after it, such as `mcp__liferay-legacy__search_content`. Keep it distinct from other Liferay connections: a server of the same name in another scope replaces this one. If you connect more than one Liferay, add one server for each, named after it, such as `liferay-legacy-prod` and `liferay-legacy-test`, so the assistant can tell which one it's working on.

By default, the server is added for the current folder only, and only for you. Add `-s user` after `add` to use it in every folder, or `-s project` to share it with your team in the folder's `.mcp.json`; to use it in the Claude desktop app's Code tab, see [With the Claude Desktop App](claude-desktop.html); teammates then approve it the first time they start Claude Code there, and need their own routes folder.

## Step 2: Check That It Connects

```bash
claude mcp get liferay-legacy
```

It shows **Status: ✔ Connected** once Claude Code could start the server and list its tools. In a Claude Code session, `/mcp` shows the same.

![Adding the server with claude mcp add, then claude mcp get liferay-legacy showing Status: Connected.](../assets/images/claude-code-mcp-add.png){: .screenshot }

{: .note }
These screenshots were taken against a test Liferay in a local Docker container named `liferay-legacy-mcp-liferay`, so the server joins that container's network (`--network container:…`) and reaches Liferay at `http://localhost:8080`. Against your own Liferay, use its address, as in Step 1.

## Step 3: Ask

Start Claude Code with `claude`, and ask *"What Liferay server are you connected to?"* Claude Code asks you to allow each Liferay tool the first time it uses it. The answer names your Liferay's release, the connection, the write mode, and how many tools your Liferay supports:

![Claude Code answering what Liferay server it's connected to: Liferay DXP 2025.Q1.29, OAuth 2 client credentials as the service user, writes off, and the tools available.](../assets/images/claude-code-server-info.png){: .screenshot }

Then ask about your content:

![Claude Code listing the three most recently modified articles in a site, as a table, and pointing out that their publish dates say more about which content is old.](../assets/images/claude-code-search.png){: .screenshot }

These were asked with `claude -p`, which runs one question and prints the answer; the same questions work in an interactive session.

## Step 4: Turn On Writes (Optional)

Writes are off until you add `-e MCP_WRITE_MODE=confirm` before the image name. Remove the server and add it again with that line:

```bash
claude mcp remove liferay-legacy
```

Each write is then shown as a preview in Claude Code, and runs only once you confirm it. See [Writes and Previews](../using/writes.html).

## Related Information

- [What to Ask](../using/what-to-ask.html)
- [With the Claude Desktop App](claude-desktop.html)
- [Checking the Installation](checking.html)
