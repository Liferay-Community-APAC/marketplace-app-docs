---
title: Checking the Installation
parent: Installing
nav_order: 5
---

# Checking the Installation
{: .no_toc }

Three checks tell you the server works. If one fails, the fixes below cover the usual causes.

1. TOC
{:toc}

## Check 1: The Server Connects

Ask the AI assistant *"What Liferay server are you connected to?"* `get_server_info` answers with:

- the Liferay release, for example *Liferay Digital Experience Platform 2025.Q1.29*;
- the connection, **OAuth 2 client credentials** when the routes are used;
- the write mode;
- each tool, and whether this Liferay supports it.

## Check 2: A Read

Ask *"List the sites you can see"*, then *"Find the five most recently modified articles in my-site."* The assistant calls `list_sites` and `search_content`, and the results match what the service user can see in Liferay.

## Check 3: A Write (If Turned On)

Ask *"Create a draft article in my-site titled MCP test."* The assistant shows a preview and asks you to confirm. Once confirmed, the draft appears in *Content & Data* → *Web Content*, created by the service user. Delete it afterwards.

## If Something's Wrong

### Every Call Fails With 403

- The service user still has to verify its email or change its password. See [Step 1 of Setting Up Liferay](liferay-setup.html#step-1-create-the-service-user-and-its-roles).
- The service user doesn't have the **MCP OAuth Client** role, so Liferay won't issue it a token.

### The Server Reports `invalid_client`

The routes folder holds old credentials, for example after the client extension was deployed again. Copy the routes again, and restart the server.

### The Write Tools Show as Unavailable

The OAuth application's scopes are read-only, or the routes folder has no scopes file. Check the scopes, then restart the server.

### The Release Shows as Unknown

The server reads the release from Liferay's `Liferay-Portal` response header, which names it only with the portal property `http.header.version.verbosity=full`. Nothing else depends on it. On Liferay SaaS, where portal properties can't be set, it stays unknown.

## Related Information

- [Known Issues](../reference/known-issues.html)
- [Compatibility](../reference/compatibility.html)
