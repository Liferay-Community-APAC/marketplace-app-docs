---
title: Upgrading and Removing
parent: Installing
nav_order: 12
---

# Upgrading and Removing
{: .no_toc }

1. TOC
{:toc}

## Knowing When There's a New Version

The server asks the public image registry, once when it starts and once a day after that, whether a newer version is published. If one is, `get_server_info` says so, under `server.update`, with a link to this page, and so does the server's log. It sends nothing about your Liferay or its users; to turn it off, set `MCP_UPDATE_CHECK=false`.

The server never updates itself: it runs from the image tag its AI assistant or deployment names.

## Updating Automatically

Each release also moves the tags `1-slim` and `1-full` to itself. To use each new 1.x release automatically, name one of them, and add `--pull=always` to the `docker run` command, before the image:

```bash
docker run -i --rm --pull=always \
  ... \
  ghcr.io/liferay-community-apac/liferay-legacy-mcp:1-slim
```

Docker then checks for a newer image whenever the server starts, and downloads it only when there is one. A new major version, such as 2.0, which could need changes in Liferay or in your settings, is never used this way.

For a shared server over HTTPS, update it the way you deploy it, for example by pulling and restarting it on a schedule.

{: .note }
Automatic updates trade control for convenience: a new release runs without anyone choosing it. Where changes must be planned, keep a version tag such as `1.3.0-slim`, or a digest, and upgrade by hand.

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

The server keeps nothing between restarts except its audit log in `/data`. Sign-in sessions are in memory, so a restart drops them, and people sign in again, unless the server keeps them in a file, with `MCP_SESSION_STORE_FILE` (from 1.3.0). After changing the OAuth application, copy the routes again if they changed, and restart the server.

### From 1.2.0 to 1.3.0

Nothing changes in Liferay: pull the new tag, and restart the server.

### If You Turned Off Email Verification

Earlier versions of this guide said to clear **Require strangers to verify their email address?** in *Control Panel* → *Instance Settings* → *User Authentication*. That setting applies to the whole instance: while it's off, people who sign up, and people who change their email address, aren't asked to prove they own it. If you cleared it for this server, turn it back on, in this order:

1. Verify the service user's email address, as in [Step 2 of Creating the Service User](liferay-setup.html#step-2-verify-its-email-address). Do this first: once the setting is on, Liferay refuses every API call from the service user until its email address is verified.
1. Select **Require strangers to verify their email address?** again, and click **Save**.
1. Ask the AI assistant *"What Liferay server are you connected to?"* to check that the server still connects.

{: .note }
The same applies to every other user created while the setting was off: their email addresses aren't verified, so once it's on, they're asked to verify them at their next sign-in, and API calls as them are refused until they do. Check for other integration or service users before you turn it back on, and verify them the same way.

## Removing

1. Stop and remove the container. Keep the `/data` volume if you need the audit log.
1. In Liferay, undeploy the client extension, or delete the application in *OAuth 2 Administration*.
1. Delete the sign-in application and its role, if you created them.
1. Deactivate the service user, and delete its two roles.

## Related Information

- [Installing](./)
