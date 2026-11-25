---
title: With Codex
parent: Installing
nav_order: 8
---

# With Codex
{: .no_toc }

OpenAI's Codex starts MCP servers itself, so each person runs their own copy of the server on their computer. The Codex CLI and the Codex IDE extension read the same settings, `~/.codex/config.toml`, so adding the server once covers both.

{: .important }
You need Docker, Codex, and a copy of the server's credentials on your computer, as in the page for where your Liferay runs ([Tomcat](tomcat.html), [Docker](docker.html), [PaaS](paas.html)), readable only by you.

1. TOC
{:toc}

## Step 1: Download the Image

Codex waits 30 seconds for a server to start, and the first start would also download the image. Download it first:

```bash
docker pull ghcr.io/liferay-community-apac/liferay-legacy-mcp:1.3.0-slim
```

## Step 2: Add the Server

Run this once, with your Liferay's address, your routes folder, and the site to work in by default:

```bash
codex mcp add liferay-legacy -- docker run -i --rm \
  -v ~/liferay-mcp/routes:/run/liferay-routes:ro \
  -v liferay-mcp-data:/data \
  -e LIFERAY_URL=https://liferay.example.com \
  -e LIFERAY_ROUTES_CLIENT_EXTENSION=/run/liferay-routes/liferay-mcp-oauth \
  -e LIFERAY_DEFAULT_SITE=my-site \
  ghcr.io/liferay-community-apac/liferay-legacy-mcp:1.3.0-slim
```

Everything after `--` is the command Codex runs to start the server. `~/liferay-mcp/routes` is the folder that holds the `liferay-mcp-oauth` folder.

The command adds the server to `~/.codex/config.toml`. You can also add it there yourself:

```toml
[mcp_servers.liferay-legacy]
command = "docker"
args = [
  "run", "-i", "--rm",
  "-v", "/Users/me/liferay-mcp/routes:/run/liferay-routes:ro",
  "-v", "liferay-mcp-data:/data",
  "-e", "LIFERAY_URL=https://liferay.example.com",
  "-e", "LIFERAY_ROUTES_CLIENT_EXTENSION=/run/liferay-routes/liferay-mcp-oauth",
  "-e", "LIFERAY_DEFAULT_SITE=my-site",
  "ghcr.io/liferay-community-apac/liferay-legacy-mcp:1.3.0-slim",
]
```

- In the file, give the routes folder's full path: `~` isn't expanded there.
- For the page inspection tools, use the `1.3.0-full` image.
- `liferay-legacy` is the server's name in Codex. To connect more than one Liferay, add one server for each, named after it, such as `liferay-legacy-prod` and `liferay-legacy-test`.
- No password goes in these settings: the server reads the OAuth credentials from the routes folder when it starts.
- If the server still takes too long to start, add `startup_timeout_sec = 60` under `[mcp_servers.liferay-legacy]`.

## Step 3: Check That It Connects

```bash
codex mcp list
```

It lists `liferay-legacy`. In a Codex session, `/mcp` shows the servers that started and their tools.

Then ask:

> What Liferay server are you connected to?

Codex calls `get_server_info`, which names your Liferay's release, the connection, the write mode, and which tools your Liferay supports.

## Step 4: Turn On Writes (Optional)

Writes are off until the server runs with `MCP_WRITE_MODE=confirm`. Add this line to the `args` in `~/.codex/config.toml`, before the image name:

```toml
  "-e", "MCP_WRITE_MODE=confirm",
```

Start a new Codex session afterwards.

Each write is then shown to you as a preview, and runs only once you confirm it. See [Writes and Previews](../using/writes.html).

## Related Information

- [What to Ask](../using/what-to-ask.html)
- [With GitHub Copilot](copilot.html)
- [Checking the Installation](checking.html)
