---
title: Upgrading and Removing
parent: Installing
nav_order: 10
---

# Upgrading and Removing
{: .no_toc }

1. TOC
{:toc}

## Upgrading the Server

Pull the new tag, and start the container again with the same volumes and settings:

```bash
docker pull ghcr.io/liferay-community-apac/liferay-legacy-mcp:<new tag>
```

For an AI assistant that starts the server itself, change the tag in its `mcpServers` setting, and restart the assistant.

The server keeps nothing between restarts except its audit log in `/data`. Sign-in sessions are in memory, so a restart drops them, and people sign in again. After changing the OAuth application, copy the routes again if they changed, and restart the server.

## Removing

1. Stop and remove the container. Keep the `/data` volume if you need the audit log.
1. In Liferay, undeploy the client extension, or delete the application in *OAuth 2 Administration*.
1. Delete the sign-in application and its role, if you created them.
1. Deactivate the service user, and delete its two roles.

## Related Information

- [Installing](./)
