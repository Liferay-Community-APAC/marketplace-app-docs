---
title: For Services and People
parent: Installing
nav_order: 4
---

# For Services and People (HTTPS)
{: .no_toc }

Over HTTPS, one server is shared: services call it with API keys, and people sign in with their Liferay account from an AI assistant that connects to remote MCP servers. Signed-in people act as themselves, with their own Liferay permissions, and are read-only.

1. TOC
{:toc}

## Step 1: Start the Server

Run the server next to Liferay, behind a reverse proxy or load balancer that terminates TLS at an `https` address, for example `https://mcp.example.com`, and forwards to port 3333:

```bash
docker run -d --name liferay-mcp --restart unless-stopped -p 127.0.0.1:3333:3333 \
  -v /srv/liferay-mcp/routes:/run/liferay-routes:ro \
  -v /srv/liferay-mcp/secrets:/run/mcp-secrets:ro \
  -v liferay-mcp-data:/data \
  -e LIFERAY_URL=https://liferay.example.com \
  -e LIFERAY_ROUTES_CLIENT_EXTENSION=/run/liferay-routes/liferay-mcp-oauth \
  -e LIFERAY_ROUTES_DXP=/run/liferay-routes/dxp \
  -e MCP_HTTP_HOST=0.0.0.0 \
  -e MCP_PUBLIC_URL=https://mcp.example.com \
  -e MCP_TRUST_PROXY=private \
  -e MCP_HTTP_API_KEYS_FILE=/run/mcp-secrets/api-keys \
  ghcr.io/liferay-community-apac/liferay-legacy-mcp:1.1.0-slim --http
```

The server refuses to start on an address other than loopback without an `https` public address and at least one way to identify callers: API keys (Step 2) or Liferay sign-in (Step 3).

## Step 2: Give Services API Keys

Each service gets its own key, with `read` or `write` access. The keys file holds only a hash of each key. To make a key and its line for the file:

```bash
KEY="lmcp_$(openssl rand -base64 32 | tr '+/' '-_' | tr -d '=')"
echo "Give the caller this key, once: $KEY"
echo "reporting:read:$(printf '%s' "$KEY" | openssl dgst -sha256 | awk '{print $NF}')" >> /srv/liferay-mcp/secrets/api-keys
```

Replace `reporting` with the service's name, and `read` with `write` for a service that may write. Callers send `Authorization: Bearer <key>`. To revoke a key, remove its line and restart the server.

{: .note }
Over HTTPS, writes stay off even for `write` keys, unless you also set `MCP_WRITE_MODE=confirm` and `MCP_HTTP_WRITES=true`. Nobody sees a service's preview, so turn this on only for an unattended job you trust.

## Step 3: Let People Sign In With Liferay (Optional)

1. In *Control Panel* → *Security* → *OAuth 2 Administration*, add an application:

    | Field | Value |
    | --- | --- |
    | Name | `Liferay MCP sign-in` |
    | Website URL | The server's address, for example `https://mcp.example.com` |
    | Callback URIs | `https://mcp.example.com/oauth/callback` |
    | Client Profile | Web Application |
    | Allowed Authorization Types | Authorization Code and Refresh Token only |
    | Trusted Application | Off, so people see what they're allowing |

1. On its **Scopes** tab, select these read scopes, and save: `Liferay.Headless.Delivery.everything.read`, `Liferay.Headless.Admin.Content.everything.read`, `Liferay.Headless.Admin.User.everything.read`, `Liferay.Headless.Admin.Taxonomy.everything.read`, `Liferay.Headless.Admin.Workflow.everything.read`, `Liferay.Data.Engine.REST.everything.read`, `Liferay.Headless.Form.everything.read` and `liferay-json-web-services.everything.read`.
1. Decide who may sign in: create a regular role, for example **MCP Sign-In User**. On the application's **Permissions**, give that role **View** and **Create Token**, and assign the role to the people who may use the server.
1. Save the application's client secret in a file in `/srv/liferay-mcp/secrets/`, and add to the `docker run` command:

    ```bash
    -e LIFERAY_SIGN_IN_CLIENT_ID=<the application's client ID> \
    -e LIFERAY_SIGN_IN_CLIENT_SECRET_FILE=/run/mcp-secrets/sign-in-secret \
    ```

People then add the server to their AI assistant as a remote MCP server at `https://mcp.example.com/mcp`. The first time, they sign in on Liferay's own page, and the server asks them to approve the AI assistant by name.

## Related Information

- [Settings](../reference/settings.html)
- [Checking the Installation](checking.html)
