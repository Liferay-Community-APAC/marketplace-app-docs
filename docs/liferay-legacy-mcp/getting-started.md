---
title: Getting Started
nav_order: 2
---

# Getting Started
{: .no_toc }

Liferay Legacy MCP is a server that speaks the Model Context Protocol (MCP), the standard way AI assistants call tools. Connected to your Liferay, it lets an AI assistant work with your sites and content. This page explains how it fits together, and the shortest way to try it.

{: .warning }
**A community app, for experimental use.** Liferay Legacy MCP is shared by Liferay Community APAC. It isn't a Liferay product: Liferay doesn't support it, and it comes as is, with no warranty and no support commitment. Use it to explore and evaluate on a test or development copy of your Liferay, not in production. AI assistants can misread a request, so start with writes off, and check every preview before you confirm it. Running it still requires a Liferay DXP Enterprise Subscription.

1. TOC
{:toc}

## How It Works

The server runs next to Liferay, as a container, and calls Liferay's APIs:

- **As one service user.** A Liferay user you create, with a site role that lets it view content and add and edit what the write tools write, but not delete or change permissions.
- **Through an OAuth 2 application.** A Headless Server application that acts as the service user, with only the scopes the tools need. Liferay gives the server its credentials as *routes*, a folder with one file per value, so no password goes in the server's settings.

An AI assistant then reaches the server in one of two ways:

| Who | How | Page |
| --- | --- | --- |
| One person, in Claude Code, the Claude desktop app, GitHub Copilot, Codex, or another AI assistant | The assistant starts the server on their computer (stdio) | [With Claude Code](installing/claude-code.html), [With the Claude Desktop App](installing/claude-desktop.html), [With GitHub Copilot](installing/copilot.html), [With Codex](installing/codex.html), [Other AI Assistants](installing/stdio.html) |
| Services, and people who sign in with Liferay | Over HTTPS, with API keys or Liferay sign-in | [For Services and People](installing/http.html) |

### Who Liferay Sees

The service user is one shared Liferay account: whatever it may do, everyone using the server through it may do, and Liferay names it, not the person, on what changes. People who sign in over HTTPS are themselves instead:

| Who | Liferay sees | Changes |
| --- | --- | --- |
| People signed in with Liferay, over HTTPS | **The person**, with their own permissions | By approval link, made as them (1.5.0 and later) |
| AI Hub users | The service user, for reads | By approval link, made as them |
| Services with API keys | The service user | Off, unless you turn them on for a job you trust |
| One person, on their computer (stdio) | The service user | Previewed, then confirmed in the chat |

- **For people, prefer signing in over HTTPS.** Liferay then applies each person's permissions, and nobody can do more through the server than in Liferay itself.
- **Keep the service user narrow:** only the sites and roles that everyone using it may have. With approval links, its connection can also be read-only (1.5.0 and later): approved changes are made with the approver's own Liferay sign-in, never through it.
- **On Liferay DXP 2026.Q3 and later,** consider Liferay's own MCP server, at `/o/mcp`, which acts as each user without a service user. On 2026.Q1 and 2026.Q2 it's a beta behind a feature flag. This server is for the releases before it.

## Try It

The quickest way to see it work is one person, with a desktop AI assistant, on a test copy of your Liferay:

1. [Create the service user](installing/liferay-setup.html).
1. Deploy the OAuth 2 client extension, and copy its credentials, as on [Tomcat](installing/tomcat.html), [Docker](installing/docker.html) or [PaaS](installing/paas.html).
1. Build the command with the [Command Builder](installing/command-builder.html), or add the server to [Claude Code](installing/claude-code.html), [GitHub Copilot](installing/copilot.html), [Codex](installing/codex.html), or [another AI assistant](installing/stdio.html).
1. Ask: *"What Liferay server are you connected to?"* The assistant calls `get_server_info`, which names your Liferay's release, the connection, and which tools your Liferay supports.

Then try a few questions from [What to Ask](using/what-to-ask.html), such as *"Find articles in my-site that haven't been modified in six months."*

{: .tip }
Writes are off until you turn them on. Start read-only, and turn on writes once you've seen what the assistant does. See [Writes and Previews](using/writes.html).

## Related Information

- [Installing](installing/)
- [Using the Server](using/)
- [Compatibility](reference/compatibility.html)
