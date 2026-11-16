---
title: Upgrading and Removing
parent: Installing
nav_order: 12
---

# Upgrading and Removing
{: .no_toc }

1. TOC
{:toc}

## Upgrading the Server

For a shared server over HTTPS:

1. Pull the new tag:

    ```bash
    docker pull ghcr.io/liferay-community-apac/liferay-legacy-mcp:<new tag>
    ```

1. Stop and remove the running container. Its `/data` volume, with the audit log, stays:

    ```bash
    docker stop liferay-mcp && docker rm liferay-mcp
    ```

1. Start it again with the same `docker run` command as before, with the new tag.

For an AI assistant that starts the server itself, change the tag in its `mcpServers` setting, and restart the assistant.

### From 1.1.0 to 1.2.0

1.2.0's client extension adds one read scope, `Liferay.Object.Admin.REST.everything.read`, so the server can read object definitions. Deploy the new [`liferay-mcp-oauth.zip`](../assets/downloads/liferay-mcp-oauth.zip) (or the read-only one) over the old one, then restart the server so it reads the new scopes. If you made the application by hand, as on [Liferay PaaS](paas.html), add the scope on its **Scopes** tab and to the routes' scopes file. Without it, everything else works, but Liferay refuses the server's requests for object definitions.

The server keeps nothing between restarts except its audit log in `/data`. Sign-in sessions are in memory, so a restart drops them, and people sign in again. After changing the OAuth application, copy the routes again if they changed, and restart the server.

## Removing

1. Stop and remove the container. Keep the `/data` volume if you need the audit log.
1. In Liferay, undeploy the client extension, or delete the application in *OAuth 2 Administration*.
1. Delete the sign-in application and its role, if you created them.
1. Deactivate the service user, and delete its two roles.

## Related Information

- [Installing](./)
