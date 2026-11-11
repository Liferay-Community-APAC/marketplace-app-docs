---
title: Getting Started
nav_order: 2
---

# Getting Started
{: .no_toc }

Liferay Legacy MCP is a server that speaks the Model Context Protocol (MCP), the standard way AI assistants call tools. Connected to your Liferay, it lets an AI assistant work with your sites and content. This page explains how it fits together, and the shortest way to try it.

1. TOC
{:toc}

## How It Works

The server runs next to Liferay, as a container, and calls Liferay's APIs:

- **As one service user.** A Liferay user you create, with a site role that lets it view content and add and edit what the write tools write, but not delete or change permissions.
- **Through an OAuth 2 application.** A Headless Server application that acts as the service user, with only the scopes the tools need. Liferay gives the server its credentials as *routes*, a folder with one file per value, so no password goes in the server's settings.

An AI assistant then reaches the server in one of three ways:

| Who | How | Page |
| --- | --- | --- |
| One person, in a desktop AI assistant or an IDE | The assistant starts the server on their computer (stdio) | [For One Person](installing/stdio.html) |
| Services, and people who sign in with Liferay | Over HTTPS, with API keys or Liferay sign-in | [For Services and People](installing/http.html) |
| Liferay AI Hub agents | Over HTTPS, with AI Hub's token for the signed-in user | [For Liferay AI Hub](installing/ai-hub.html) |

## Try It

The quickest way to see it work is one person, with a desktop AI assistant, on a test copy of your Liferay:

1. [Create the service user and its roles](installing/liferay-setup.html#step-1-create-the-service-user-and-its-roles).
1. [Create the OAuth 2 connection](installing/liferay-setup.html#step-2-create-the-oauth-2-connection), and save its credentials in a routes folder.
1. [Add the server to your AI assistant](installing/stdio.html), and restart the assistant.
1. Ask: *"What Liferay server are you connected to?"* The assistant calls `get_server_info`, which names your Liferay's release, the connection, and which tools your Liferay supports.

Then try a few questions from [What to Ask](using/what-to-ask.html), such as *"Find articles in my-site that haven't been modified in six months."*

{: .tip }
Writes are off until you turn them on. Start read-only, and turn on writes once you've seen what the assistant does. See [Writes, Previews, and Approvals](using/writes.html).

## Related Information

- [Installing](installing/)
- [Using the Server](using/)
- [Compatibility](reference/compatibility.html)
