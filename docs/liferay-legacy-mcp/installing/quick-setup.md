---
title: Quick Setup
parent: Installing
nav_order: 0
---

# Quick Setup
{: .no_toc }

From 1.4.0, the image's `setup` command does the Liferay side of an install in one command, as an administrator: the service user, its roles and sites, and the OAuth 2 connection. Then it checks that the connection works. It replaces [Creating the Service User](liferay-setup.html) and deploying the client extension by hand.

1. TOC
{:toc}

## On Liferay Self-Hosted

Mount Liferay's `deploy` folder and its `routes/default` folder, and give `setup` an administrator and the sites the server works in:

```bash
docker run --rm \
  -v $LIFERAY_HOME/deploy:/deploy \
  -v $LIFERAY_HOME/routes/default:/liferay-routes:ro \
  -v liferay-mcp-data:/data \
  -e LIFERAY_URL=https://liferay.example.com \
  -e LIFERAY_ADMIN_USERNAME=admin@example.com \
  -e LIFERAY_ADMIN_PASSWORD \
  ghcr.io/liferay-community-apac/liferay-legacy-mcp:1-slim setup --sites my-site
```

`-e LIFERAY_ADMIN_PASSWORD` without a value takes the password from your shell, so it isn't in the command; `setup` uses it for this run only and keeps nothing. If Liferay runs in a container, mount the same folders or volumes that container uses.

It ends with:

```text
✓ connected: Liferay says the server acts as mcp-service (mcp-service@example.com)

Next: run the server with these settings (stdio for one person, or add --http):
  -v liferay-mcp-data:/data
  -e LIFERAY_URL=https://liferay.example.com
  -e LIFERAY_ROUTES_CLIENT_EXTENSION=/data/routes/liferay-mcp-oauth
  -e LIFERAY_DEFAULT_SITE=my-site
```

Use those settings in [Connect the AI Assistant](index.html#3-connect-the-ai-assistant), with the same `liferay-mcp-data` volume.

## What It Does

Each step is looked up first, so running it again changes only what's missing or different:

1. Creates the service user `mcp-service` (email `mcp-service@` and the administrator's domain, or `--service-email`), with a random password nobody keeps: OAuth 2 doesn't need one.
1. Creates the **MCP Content Editor** site role and the **MCP OAuth Client** role, with the permissions in [Creating the Service User](liferay-setup.html#step-3-create-its-roles), and adds the user to each site in `--sites` with the site role. Sites are friendly URL paths (as in `/web/my-site`) or IDs, comma-separated.
1. Writes the OAuth 2 client extension into Liferay's `deploy` folder, waits for Liferay to publish its credentials, and copies them into the `/data` volume, readable only by the server.
1. Gets a token as the service user and asks Liferay who it is.

| Option | |
| --- | --- |
| `--read-only` | The client extension asks for read scopes only, so Liferay refuses every write. Run `setup` again without it to allow writes. |
| `--service-email <email>` | The service user's email. |
| `--client-id <id>` and `--client-secret-file <file>` | Instead of Liferay's routes, where its folders can't be mounted. See [Without Liferay's Folders](#without-liferays-folders). |
| `--timeout <seconds>` | How long to wait for Liferay to publish the credentials (default 300). |

`LIFERAY_URL` must be an address Liferay accepts as its host: its own address, or `localhost`. Liferay answers a Docker container's name with HTTP 500 (*Invalid host name*).

## If the Service User Is Refused

A new Liferay user may be refused until it changes its password (the password policy's **Change Required**) or verifies its email address (**Require strangers to verify their email address?**). `setup` doesn't change those instance settings, which apply to every user. It says which one stops the service user, and prints a script that fixes that user alone, for example:

```groovy
import com.liferay.portal.kernel.security.auth.CompanyThreadLocal
import com.liferay.portal.kernel.service.UserLocalServiceUtil

def user = UserLocalServiceUtil.getUserByScreenName(CompanyThreadLocal.getCompanyId(), "mcp-service")
UserLocalServiceUtil.updatePasswordReset(user.getUserId(), false)
UserLocalServiceUtil.updateEmailAddressVerified(user.getUserId(), true)
out.println("Done for " + user.getScreenName())
```

Run it once in *Control Panel* → *Server Administration* → *Script*, with **Groovy** as the language: fill in **Text Verification** and click **Execute**. Then run `setup` again. A new Liferay instance has both settings on, so it's usual to need this once.

## Without Liferay's Folders

On Liferay PaaS or SaaS, or where you can't mount Liferay's folders, leave out the two Liferay mounts:

1. Run `setup`. It writes `liferay-mcp-oauth.zip` to the `liferay-mcp-data` volume and asks you to deploy it.
1. Deploy the zip the way your Liferay deploys client extensions.
1. Copy the application's **Client ID** and **Client Secret** from *Control Panel* → *Security* → *OAuth 2 Administration* → *Liferay MCP server*, put the secret in a file, and run `setup` again with `--client-id <id> --client-secret-file /path/in/container`.

## With Docker Compose

For a self-hosted Liferay on the same machine, one `docker-compose.yml` runs `setup`, the optional [AI Hub Legacy Connector](../../liferay-aihub-legacy-connector/installing.html) installer, and the server over HTTP:

```yaml
x-liferay-admin: &liferay-admin
  LIFERAY_URL: ${LIFERAY_URL:?set LIFERAY_URL in .env}
  LIFERAY_ADMIN_USERNAME: ${LIFERAY_ADMIN_USERNAME:-}
  LIFERAY_ADMIN_PASSWORD: ${LIFERAY_ADMIN_PASSWORD:-}

services:
  setup:
    image: ghcr.io/liferay-community-apac/liferay-legacy-mcp:1-slim
    profiles: [setup]
    command: ["setup", "--sites", "${MCP_SITES:?set MCP_SITES in .env}"]
    environment: *liferay-admin
    volumes:
      - ${LIFERAY_HOME:?set LIFERAY_HOME in .env}/deploy:/deploy
      - ${LIFERAY_HOME}/routes/default:/liferay-routes:ro
      - mcp-data:/data

  connector:
    image: ghcr.io/liferay-community-apac/liferay-aihub-legacy-connector:latest
    profiles: [connector]
    command: ["install"]
    environment:
      <<: *liferay-admin
      LIFERAY_RELEASE: ${LIFERAY_RELEASE:?set LIFERAY_RELEASE in .env, e.g. 2025.q1}
      AIHUB_URL: ${AIHUB_URL:-}
      AIHUB_CLIENT_ID: ${AIHUB_CLIENT_ID:-}
      AIHUB_CLIENT_SECRET_FILE: /run/secrets/aihub-client-secret
      AIHUB_CHATBOT_ERC: ${AIHUB_CHATBOT_ERC:-}
    volumes:
      - ${LIFERAY_HOME}/deploy:/deploy
      - ${LIFERAY_HOME}/osgi/configs:/configs
      - ${AIHUB_CLIENT_SECRET_FILE:-./secrets/aihub-client-secret}:/run/secrets/aihub-client-secret:ro

  mcp:
    image: ghcr.io/liferay-community-apac/liferay-legacy-mcp:1-slim
    command: ["--http"]
    restart: unless-stopped
    ports:
      - "127.0.0.1:${MCP_PORT:-3333}:3333"
    environment:
      LIFERAY_URL: ${LIFERAY_URL}
      LIFERAY_ROUTES_CLIENT_EXTENSION: /data/routes/liferay-mcp-oauth
      LIFERAY_DEFAULT_SITE: ${LIFERAY_DEFAULT_SITE:-}
      MCP_HTTP_HOST: 0.0.0.0
      MCP_PUBLIC_URL: ${MCP_PUBLIC_URL:?set MCP_PUBLIC_URL in .env (https)}
      MCP_TRUST_PROXY: ${MCP_TRUST_PROXY:-private}
      MCP_HTTP_TRUSTED_ISSUER_URL: ${MCP_HTTP_TRUSTED_ISSUER_URL:-}
      MCP_HTTP_TRUSTED_ISSUER_ALLOW: ${MCP_HTTP_TRUSTED_ISSUER_ALLOW:-}
      MCP_HTTP_API_KEYS_FILE: ${MCP_HTTP_API_KEYS_FILE_IN_CONTAINER:-}
    volumes:
      - mcp-data:/data
      - ${MCP_SECRETS_DIR:-./secrets}:/run/mcp-secrets:ro

volumes:
  mcp-data:
```

With an `.env` next to it setting `LIFERAY_URL`, `LIFERAY_HOME`, `LIFERAY_RELEASE`, `LIFERAY_ADMIN_USERNAME`, `MCP_SITES`, `MCP_PUBLIC_URL`, and the AI Hub and authentication settings you use:

```bash
LIFERAY_ADMIN_PASSWORD='...' docker compose run --rm setup
```

```bash
LIFERAY_ADMIN_PASSWORD='...' docker compose run --rm connector
```

```bash
docker compose up -d mcp
```

The server listens on `127.0.0.1:3333`: put the TLS reverse proxy that answers at `MCP_PUBLIC_URL` in front, as in [For Services and People](http.html). It needs a way to authenticate callers, AI Hub's tokens or API keys, or it doesn't start. Blank settings in `.env` count as unset.

## Related Information

- [Creating the Service User](liferay-setup.html), the same steps by hand
- [Checking the Installation](checking.html)
