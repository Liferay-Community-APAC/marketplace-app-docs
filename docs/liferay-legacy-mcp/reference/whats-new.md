---
title: What's New
parent: Reference
nav_order: 6
---

# What's New
{: .no_toc }

1. TOC
{:toc}

## 1.4.0

- **Quick setup:** the image's `setup` command creates the service user, its roles and sites, and the OAuth 2 connection as an administrator, then checks that it works. When the instance's password policy or email verification stops the new user, it says which and prints a fix for that user alone. See [Quick Setup](../installing/quick-setup.html).
- **Docker Compose:** one file runs `setup`, the AI Hub Legacy Connector's installer and the server. See [With Docker Compose](../installing/quick-setup.html#with-docker-compose).
- **AI Hub workflows:** an AI Hub agent's workflow can call the server's read-only tools, and write tools as approval links, through the AI Hub Legacy Connector (`MCP_HTTP_TOOL_ROUTES`). Access can also be limited to holders of chosen Liferay roles (`MCP_HTTP_TRUSTED_ISSUER_ROLES`).
- **Blank settings** in an env file or compose file count as unset, instead of being refused.

Nothing changes in Liferay when upgrading from 1.3.0.

## 1.3.0

- **New version notice:** the server says, in `get_server_info` and its log, when a newer version is published. See [Knowing When There's a New Version](../installing/upgrading-and-removing.html#knowing-when-theres-a-new-version). `MCP_UPDATE_CHECK=false` turns it off.
- **Automatic updates, if you want them:** each release also moves the tags `1-slim` and `1-full`, for use with `docker run --pull=always`. See [Updating Automatically](../installing/upgrading-and-removing.html#updating-automatically).
- **Sign-ins survive a restart:** over HTTPS, with `MCP_SESSION_STORE_FILE` and `MCP_SESSION_STORE_KEY_FILE`, people stay signed in when the server restarts. See [Settings](settings.html#sign-in).

Nothing changes in Liferay when upgrading from 1.2.0.

## 1.2.0

### Liferay DXP 2026.Q1 and 2026.Q2

- **Supported and tested** on 2026.Q1.9 LTS and 2026.Q2.9, with OAuth 2 or a password, as on the earlier releases. See [Compatibility](compatibility.html#liferay-dxp-2026q1-and-2026q2).
- **Spaces:** the article tools write web content fields the way the new *Spaces* content management needs. Without that, Liferay saved new and changed fields empty.
- **Liferay's own MCP server:** if it's on, the server says so when it starts, in its log and in `get_server_info`, since the two servers' tools overlap. It still runs.
- **Their new APIs:** the generic API tools cover the Headless APIs new in these releases, 33 areas in all.

### Tools

- `list_pages` also says whether each page is hidden from navigation, and gives its SEO title and description.
- `list_users` and the objects API area work without the administrator connection, reading as the service user. To list every user and object definition that way, see [Tools](tools.html#users-and-object-definitions-without-the-administrator-connection).
- `read_api` also reads operations that answer with HTML, and accepts encoded characters in an item's path.
- Content searches and lists include items in folders.
- `list_navigation_menus` and `add_navigation_menu_item` read and write menu links the way Liferay's own menu items keep them.

### Installing

- The client extension adds one read scope, `Liferay.Object.Admin.REST.everything.read`, for object definitions. When upgrading, deploy the new zip: see [Upgrading and Removing](../installing/upgrading-and-removing.html#from-110-to-120).
- [In Docker](../installing/docker.html): reach Liferay through its container's network, not `host.docker.internal`.

## 1.1.0

The first release published here, for Liferay DXP 7.4 U1–U92 and 2024.Q1 to 2025.Q4, as public images for `linux/amd64` and `linux/arm64`.
