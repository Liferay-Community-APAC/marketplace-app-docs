---
title: For Services and People
parent: Installing
nav_order: 10
---

# For Services and People (HTTPS)
{: .no_toc }

Over HTTPS, one server is shared: services call it with API keys, and people sign in with their Liferay account from an AI assistant that connects to remote MCP servers. Signed-in people act as themselves, with their own Liferay permissions. They're read-only, unless you add approval links (1.5.0 and later): then each change they ask for is approved by them on a page, and made as them.

1. TOC
{:toc}

## Step 1: Start the Server

Run the server behind a reverse proxy or load balancer that terminates TLS at an `https` address, for example `https://mcp.example.com`, and forwards to port 3333:

- **On the Liferay machine, with Liferay Self-Hosted:** use the shared server command on the [Tomcat](tomcat.html#step-3-run-the-server) or [Docker](docker.html#step-3-run-the-server) page. It reaches Liferay through the machine's or the container's network.
- **On another machine,** for example with [Liferay PaaS](paas.html): use this command, with `LIFERAY_URL` set to Liferay's address and the routes folder you copied:

```bash
docker run -d --name liferay-mcp --restart unless-stopped -p 127.0.0.1:3333:3333 \
  -v /srv/liferay-mcp/routes:/run/liferay-routes:ro \
  -v /srv/liferay-mcp/secrets:/run/mcp-secrets:ro \
  -v liferay-mcp-data:/data \
  -e LIFERAY_URL=https://liferay.example.com \
  -e LIFERAY_ROUTES_CLIENT_EXTENSION=/run/liferay-routes/liferay-mcp-oauth \
  -e MCP_HTTP_HOST=0.0.0.0 \
  -e MCP_PUBLIC_URL=https://mcp.example.com \
  -e MCP_TRUST_PROXY=private \
  -e MCP_HTTP_API_KEYS_FILE=/run/mcp-secrets/api-keys \
  ghcr.io/liferay-community-apac/liferay-legacy-mcp:1.4.0-slim --http
```

The server refuses to start on an address other than loopback without an `https` public address and at least one way to identify callers: API keys (Step 2) or Liferay sign-in (Step 3).

## Step 2: Give Services API Keys

Each service gets its own key, with `read` or `write` access. The keys file, `/srv/liferay-mcp/secrets/api-keys`, holds only a hash of each key.

1. Create the secrets folder, once:

    ```bash
    sudo mkdir -p /srv/liferay-mcp/secrets
    ```

1. Make a key, and add its line to the keys file. Replace `reporting` with the service's name, and `read` with `write` for a service that may write:

    ```bash
    KEY="lmcp_$(openssl rand -base64 32 | tr '+/' '-_' | tr -d '=')"
    echo "Give the caller this key, once: $KEY"
    echo "reporting:read:$(printf '%s' "$KEY" | openssl dgst -sha256 | awk '{print $NF}')" | sudo tee -a /srv/liferay-mcp/secrets/api-keys >/dev/null
    unset KEY
    ```

1. Make the folder readable only by the server, which runs as user ID 1000 in the container:

    ```bash
    sudo chown -R 1000:1000 /srv/liferay-mcp/secrets && sudo chmod -R go-rwx /srv/liferay-mcp/secrets
    ```

Callers send `Authorization: Bearer <key>`. To revoke a key, remove its line and restart the server.

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

1. On its **Scopes** tab, select these read scopes, and save: `Liferay.Headless.Delivery.everything.read`, `Liferay.Headless.Admin.Content.everything.read`, `Liferay.Headless.Admin.User.everything.read`, `Liferay.Headless.Admin.Taxonomy.everything.read`, `Liferay.Headless.Admin.Workflow.everything.read`, `Liferay.Data.Engine.REST.everything.read`, `Liferay.Headless.Form.everything.read`, `Liferay.Object.Admin.REST.everything.read` and `liferay-json-web-services.everything.read`.
1. Decide who may sign in: create a regular role, for example **MCP Sign-In User**. On the application's **Permissions**, give that role **View** and **Create Token**, and assign the role to the people who may use the server.
1. Save the application's client secret in a file. This asks for it without showing it, and keeps it out of your shell history:

    ```bash
    printf 'Client secret: '; read -rs SECRET; echo
    printf '%s' "$SECRET" | sudo tee /srv/liferay-mcp/secrets/sign-in-secret >/dev/null; unset SECRET
    sudo chown 1000:1000 /srv/liferay-mcp/secrets/sign-in-secret && sudo chmod 600 /srv/liferay-mcp/secrets/sign-in-secret
    ```

1. Add to the `docker run` command:

    ```bash
    -e LIFERAY_SIGN_IN_CLIENT_ID=<the application's client ID> \
    -e LIFERAY_SIGN_IN_CLIENT_SECRET_FILE=/run/mcp-secrets/sign-in-secret \
    ```

People then add the server to their AI assistant as a remote MCP server at `https://mcp.example.com/mcp`. The first time, they sign in on Liferay's own page, and the server asks them to approve the AI assistant by name.

## Step 4: Let Signed-In People Make Changes (Optional)

Available in 1.5.0 and later. The AI assistant could confirm its own changes over HTTPS, so a person approves each one on a page of the server instead:

1. The assistant asks for a change. The server answers with a preview and a link, and changes nothing.
1. The person opens the link, signs in to Liferay, sees the change, and clicks **Approve** or **Reject**.
1. An approved change is made **as that person**, with their own Liferay permissions. Only the person who asked can approve it, as the same Liferay user, within 15 minutes. The assistant can ask how it went.

To turn it on:

1. In *OAuth 2 Administration*, add a second application, separate from the sign-in one:

    | Field | Value |
    | --- | --- |
    | Name | `Liferay MCP approvals` |
    | Website URL | The server's address, for example `https://mcp.example.com` |
    | Callback URIs | `https://mcp.example.com/approve/callback` |
    | Client Profile | Web Application |
    | Allowed Authorization Types | Authorization Code and Refresh Token only |
    | Trusted Application | Off |

1. On its **Scopes** tab, select: `Liferay.Headless.Delivery.everything`, `Liferay.Headless.Admin.Content.everything`, `Liferay.Headless.Admin.Taxonomy.everything`, `Liferay.Headless.Admin.Workflow.everything`, `Liferay.Headless.Batch.Engine.everything`, `Liferay.Headless.Form.everything`, `Liferay.Headless.Admin.User.everything.read`, `Liferay.Data.Engine.REST.everything.read`, `Liferay.Object.Admin.REST.everything.read` and `liferay-json-web-services.everything.read`. The sign-in application keeps read scopes only, so Liferay itself keeps sign-ins read-only.
1. Decide who may approve: create a regular role, for example **MCP Approver**, with **View** and **Create Token** on this application, and assign it to the people who may make changes.
1. Save its client secret in `/srv/liferay-mcp/secrets/approvals-secret`, as in Step 3.
1. Add to the `docker run` command:

    ```bash
    -e MCP_WRITE_MODE=confirm \
    -e MCP_HTTP_APPROVALS=true \
    -e LIFERAY_APPROVAL_CLIENT_ID=<the application's client ID> \
    -e LIFERAY_APPROVAL_CLIENT_SECRET_FILE=/run/mcp-secrets/approvals-secret \
    ```

{: .tip }
Approved changes don't use the server's own connection, so it can stay read-only: use the read-only client extension, or `setup --read-only`.

## Related Information

- [Settings](../reference/settings.html)
- [Checking the Installation](checking.html)
