---
title: With GitHub Copilot
parent: Installing
nav_order: 7
---

# With GitHub Copilot
{: .no_toc }

GitHub Copilot starts MCP servers itself, in Visual Studio Code and in the Copilot CLI, so each person runs their own copy of the server on their computer. Both read the same file, `~/.copilot/mcp-config.json`, so setting the server up once covers both.

{: .important }
You need Docker, GitHub Copilot, and a copy of the server's credentials on your computer, as in the page for where your Liferay runs ([Tomcat](tomcat.html), [Docker](docker.html), [PaaS](paas.html)), readable only by you. Your organization may also need to allow MCP servers in its Copilot policies.

1. TOC
{:toc}

## Step 1: Download the Image

Copilot waits only a limited time for a server to start, and the first start would also download the image. Download it first:

```bash
docker pull ghcr.io/liferay-community-apac/liferay-legacy-mcp:1.3.0-slim
```

## Step 2: Add the Server

Add the server to `~/.copilot/mcp-config.json`, with your Liferay's address, your routes folder, and the site to work in by default. Create the file if it isn't there, or add `liferay-legacy` to its `mcpServers`:

```json
{
  "mcpServers": {
    "liferay-legacy": {
      "type": "stdio",
      "command": "docker",
      "args": [
        "run", "-i", "--rm",
        "-v", "/Users/me/liferay-mcp/routes:/run/liferay-routes:ro",
        "-v", "liferay-mcp-data:/data",
        "-e", "LIFERAY_URL=https://liferay.example.com",
        "-e", "LIFERAY_ROUTES_CLIENT_EXTENSION=/run/liferay-routes/liferay-mcp-oauth",
        "-e", "LIFERAY_DEFAULT_SITE=my-site",
        "ghcr.io/liferay-community-apac/liferay-legacy-mcp:1.3.0-slim"
      ]
    }
  }
}
```

- `/Users/me/liferay-mcp/routes` is the folder that holds the `liferay-mcp-oauth` folder. Give its full path: `~` isn't expanded here.
- For the page inspection tools, use the `1.3.0-full` image.
- `liferay-legacy` is the server's name in Copilot. To connect more than one Liferay, add one server for each, named after it, such as `liferay-legacy-prod` and `liferay-legacy-test`.
- No password goes in this file: the server reads the OAuth credentials from the routes folder when it starts.

In the Copilot CLI, you can add the same server with one command instead:

```bash
copilot mcp add liferay-legacy -- docker run -i --rm \
  -v ~/liferay-mcp/routes:/run/liferay-routes:ro \
  -v liferay-mcp-data:/data \
  -e LIFERAY_URL=https://liferay.example.com \
  -e LIFERAY_ROUTES_CLIENT_EXTENSION=/run/liferay-routes/liferay-mcp-oauth \
  -e LIFERAY_DEFAULT_SITE=my-site \
  ghcr.io/liferay-community-apac/liferay-legacy-mcp:1.3.0-slim
```

Everything after `--` is the command Copilot runs to start the server.

{: .note }
To share the server with a project instead, put the same `mcpServers` setting in the project's `.mcp.json`. Copilot uses it once you trust the folder, and each person still needs their own routes folder. Visual Studio Code also reads a project's `.vscode/mcp.json`, which has the same server under a top-level `servers` key instead of `mcpServers`; the Copilot CLI doesn't read that file.

## Step 3: Check That It Connects

- **In Visual Studio Code,** open the Chat view and choose Copilot's agent. Confirm that you trust the server when you're asked, and its tools show in the tools list. The **MCP: List Servers** command shows whether it's running.
- **In the Copilot CLI,** run `copilot mcp list`, or `/mcp` in a session.

Then ask:

> What Liferay server are you connected to?

Copilot calls `get_server_info`, which names your Liferay's release, the connection, the write mode, and which tools your Liferay supports. It asks you to allow each Liferay tool before using it.

## Step 4: Turn On Writes (Optional)

Writes are off until you add this line to the `args`, before the image name:

```json
"-e", "MCP_WRITE_MODE=confirm",
```

Restart the server afterwards: in Visual Studio Code with **MCP: List Servers** → `liferay-legacy` → **Restart Server**, and in the Copilot CLI by starting a new session.

Each write is then shown to you as a preview, and runs only once you confirm it. See [Writes and Previews](../using/writes.html).

## Related Information

- [What to Ask](../using/what-to-ask.html)
- [With Codex](codex.html)
- [Checking the Installation](checking.html)
