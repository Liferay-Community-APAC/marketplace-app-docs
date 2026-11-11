---
title: With the Claude Desktop App
parent: Installing
nav_order: 6
---

# With the Claude Desktop App
{: .no_toc }

The Claude desktop app's **Code** tab runs Claude Code, and uses the same MCP servers as Claude Code in a terminal. Add the server once with `claude mcp add`, and it's available in the app's Code sessions.

{: .important }
You need Docker, the Claude desktop app, the [Claude Code command line tool](https://code.claude.com/docs/en/quickstart) to add the server, and a copy of the server's credentials on your computer, as in the page for where your Liferay runs ([Tomcat](tomcat.html), [Docker](docker.html), [PaaS](paas.html)), readable only by you.

1. TOC
{:toc}

## Step 1: Add the Server for Every Folder

In a terminal, add the server with `-s user`, so it's available in every folder you open in the Code tab:

```bash
claude mcp add -s user liferay-legacy -- docker run -i --rm \
  -v ~/liferay-mcp/routes:/run/liferay-routes:ro \
  -v liferay-mcp-data:/data \
  -e LIFERAY_URL=https://liferay.example.com \
  -e LIFERAY_ROUTES_CLIENT_EXTENSION=/run/liferay-routes/liferay-mcp-oauth \
  -e LIFERAY_DEFAULT_SITE=my-site \
  ghcr.io/liferay-community-apac/liferay-legacy-mcp:1.1.0-slim
```

To add it for one folder only, run the command without `-s user` in that folder. To connect more than one Liferay, add one server for each, named after it, such as `liferay-legacy-prod` and `liferay-legacy-test`.

{: .note }
The app's **+** → **Connectors** menu adds Claude's own connectors, not local servers like this one. The chat's *Developer* settings (`claude_desktop_config.json`) apply to the Chat tab, not to Code sessions.

## Step 2: Start a Code Session

In the desktop app, open the **Code** tab, and start a session in a folder. Type `/mcp` to see the server: it shows **liferay-legacy** as connected once the app could start it.

## Step 3: Ask

Ask *"What Liferay server are you connected to?"* The app asks you to allow each Liferay tool the first time it's used. The answer names your Liferay's release, the connection, the write mode, and how many tools your Liferay supports. Then try the questions in [What to Ask](../using/what-to-ask.html).

## Related Information

- [With Claude Code](claude-code.html)
- [Writes and Previews](../using/writes.html)
- [Checking the Installation](checking.html)
