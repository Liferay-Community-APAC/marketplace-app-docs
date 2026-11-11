---
title: Other AI Assistants
parent: Installing
nav_order: 7
---

# Other AI Assistants
{: .no_toc }

Desktop AI assistants and IDEs start an MCP server themselves, on the person's computer, and talk to it over its standard input and output (stdio). Each person runs their own copy of the server, on their computer. For Claude, see [With Claude Code](claude-code.html) and [With the Claude Desktop App](claude-desktop.html).

{: .important }
You need Docker on the person's computer, and a copy of the server's credentials there, as in the page for where your Liferay runs ([Tomcat](tomcat.html), [Docker](docker.html), [PaaS](paas.html)), readable only by them.

1. TOC
{:toc}

## Step 1: Add the Server to the AI Assistant

Most desktop AI assistants and IDEs read an `mcpServers` setting in this format. Add the server to it, with your Liferay's address, the routes folder, and the site to work in by default:

```json
{
  "mcpServers": {
    "liferay": {
      "command": "docker",
      "args": [
        "run", "-i", "--rm",
        "-v", "/path/to/routes:/run/liferay-routes:ro",
        "-v", "liferay-mcp-data:/data",
        "-e", "LIFERAY_URL=https://liferay.example.com",
        "-e", "LIFERAY_ROUTES_CLIENT_EXTENSION=/run/liferay-routes/liferay-mcp-oauth",
        "-e", "LIFERAY_DEFAULT_SITE=my-site",
        "ghcr.io/liferay-community-apac/liferay-legacy-mcp:1.1.0-slim"
      ]
    }
  }
}
```

- `/path/to/routes` is the folder that holds the `liferay-mcp-oauth` folder.
- For the page inspection tools, use the `1.1.0-full` image.
- No password goes in this setting: the server reads the OAuth credentials from the routes folder when it starts.

## Step 2: Restart the AI Assistant

Quit and start the AI assistant again, so it starts the server. Then ask:

> What Liferay server are you connected to?

The assistant calls `get_server_info`, which names your Liferay's release, the connection, the write mode, and which tools your Liferay supports.

## Step 3: Turn On Writes (Optional)

Writes are off until you add this line to the `args`, before the image name:

```json
"-e", "MCP_WRITE_MODE=confirm",
```

Every write is then shown to the person as a preview, and runs only once they confirm it. See [Writes and Previews](../using/writes.html).

## Related Information

- [With Claude Code](claude-code.html)
- [What to Ask](../using/what-to-ask.html)
- [Settings](../reference/settings.html)
- [Checking the Installation](checking.html)
