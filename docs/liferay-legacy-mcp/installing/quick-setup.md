---
title: Quick Setup
parent: Installing
nav_order: 0
---

# Quick Setup
{: .no_toc }

One command, as a Liferay administrator, does steps 1 and 2 of [Installing](index.html): the service user, its roles and sites, and the OAuth 2 connection. Then it checks that the connection works. You end with the same **routes folder** as by hand, and go on to [step 3](index.html#3-connect-the-ai-assistant).
{: .fs-5 .fw-300 }

{: .note }
Quick Setup is for Liferay Self-Hosted, where you can reach Liferay's folders. On Liferay PaaS, or to see and do each piece yourself, follow the [step-by-step pages](index.html#steps) instead. Each step below links to the one it replaces.

1. TOC
{:toc}

## Before You Begin

- Liferay Legacy MCP 1.4.0 or later (`1-slim` is always the newest 1.x).
- An administrator's email and password. `setup` uses them for this run only, and keeps nothing.
- The sites the server works in: their friendly URL paths (as in `/web/my-site`) or IDs.
- A folder for the server's credentials, owned by user ID 1000, the user the container runs as:

    ```bash
    sudo mkdir -p /srv/liferay-mcp && sudo chown 1000:1000 /srv/liferay-mcp
    ```

    `setup` writes the routes folder there: `/srv/liferay-mcp/routes`.

## Run It

Pass the password with `-e LIFERAY_ADMIN_PASSWORD` and no value, so it comes from your shell and isn't in the command:

```bash
read -rs LIFERAY_ADMIN_PASSWORD && export LIFERAY_ADMIN_PASSWORD
```

### Liferay on a Server (Tomcat)

Mount Liferay's `deploy` folder and its `routes/default` folder, where `<liferay-home>` is the folder that contains `osgi` and `routes`:

```bash
docker run --rm \
  -v <liferay-home>/deploy:/deploy \
  -v <liferay-home>/routes/default:/liferay-routes:ro \
  -v /srv/liferay-mcp:/data \
  -e LIFERAY_URL=https://liferay.example.com \
  -e LIFERAY_ADMIN_USERNAME=admin@example.com \
  -e LIFERAY_ADMIN_PASSWORD \
  ghcr.io/liferay-community-apac/liferay-legacy-mcp:1-slim setup --sites my-site
```

`LIFERAY_URL` must be an address Liferay accepts as its host: its own address, or `localhost` with `--network host`. Liferay answers other names with HTTP 500 (*Invalid host name*).

### Liferay in Docker

Start Liferay with volumes for its `deploy` and `routes` folders, as in [step 1 of the Docker page](docker.html#step-1-give-liferay-a-routes-volume), adding `-v liferay-deploy:/opt/liferay/deploy`. Then run `setup` on Liferay's network, so it reaches Liferay at `localhost:8080`:

```bash
docker run --rm --network container:liferay \
  -v liferay-deploy:/deploy \
  -v liferay-routes:/liferay-routes:ro \
  -v /srv/liferay-mcp:/data \
  -e LIFERAY_URL=http://localhost:8080 \
  -e LIFERAY_ADMIN_USERNAME=admin@example.com \
  -e LIFERAY_ADMIN_PASSWORD \
  ghcr.io/liferay-community-apac/liferay-legacy-mcp:1-slim setup --sites my-site --liferay-routes /liferay-routes/default
```

### When It's Done

It ends with:

```text
✓ connected: Liferay says the server acts as mcp-service (mcp-service@example.com)
```

Your routes folder is `/srv/liferay-mcp/routes`, holding `liferay-mcp-oauth`. Go on to [step 3: Connect the AI Assistant](index.html#3-connect-the-ai-assistant), and use that folder wherever a page asks for the routes folder (on another computer, copy it there first, keeping it private). The lines `setup` prints after *Next* are the same settings, as seen from inside its own container.

If it stops at the check instead, see [If the Service User Is Refused](#if-the-service-user-is-refused).

## What It Does

Each step is looked up first, so running `setup` again changes only what's missing or different: after adding a site, or to switch to read-only and back.

| `setup` | By hand |
| --- | --- |
| Creates the service user `mcp-service` (email `mcp-service@` and the administrator's domain), with a random password nobody keeps: OAuth 2 doesn't need one. | [Step 1: Create the User](liferay-setup.html#step-1-create-the-user) |
| Says what stops the new user, and prints a fix for that user alone ([below](#if-the-service-user-is-refused)). | [Step 2: Verify Its Email Address](liferay-setup.html#step-2-verify-its-email-address) |
| Creates the **MCP Content Editor** site role and the **MCP OAuth Client** role, with the same permissions. | [Step 3: Create Its Roles](liferay-setup.html#step-3-create-its-roles) |
| Adds the user to each site in `--sites`, with the site role, and assigns the OAuth role. | [Step 4: Assign Them](liferay-setup.html#step-4-assign-them) |
| Writes the OAuth 2 client extension into Liferay's `deploy` folder, and waits for Liferay to create the application. | Deploy the client extension: [Tomcat](tomcat.html#step-1-deploy-the-oauth-2-client-extension), [Docker](docker.html#step-2-deploy-the-oauth-2-client-extension) |
| Copies the credentials Liferay publishes into `/srv/liferay-mcp/routes`, readable only by the server. | [Copy the Credentials for the Server](tomcat.html#step-2-copy-the-credentials-for-the-server) |
| Gets a token as the service user and asks Liferay who it is. | [Checking the Installation](checking.html), later |

### Options

| Option | |
| --- | --- |
| `--read-only` | The client extension asks for read scopes only, so Liferay refuses every write, as the read-only zip does ([Changing the Client Extension](liferay-setup.html#changing-the-client-extension)). Run `setup` again without it to allow writes. |
| `--service-email <email>` | The service user's email. |
| `--client-id <id>` and `--client-secret-file <file>` | Instead of Liferay's routes, where its folders can't be mounted. See [Without Liferay's Folders](#without-liferays-folders). |
| `--timeout <seconds>` | How long to wait for Liferay to publish the credentials (default 300). |

## If the Service User Is Refused

A new Liferay user may be refused until it changes its password (the password policy's **Change Required**) or verifies its email address (**Require strangers to verify their email address?**). A new Liferay instance has both on, so it's usual to see this once. `setup` doesn't change those settings, which apply to every user. It says which one stops the service user, and prints a script that fixes that user alone, for example:

```groovy
import com.liferay.portal.kernel.security.auth.CompanyThreadLocal
import com.liferay.portal.kernel.service.UserLocalServiceUtil

def user = UserLocalServiceUtil.getUserByScreenName(CompanyThreadLocal.getCompanyId(), "mcp-service")
UserLocalServiceUtil.updatePasswordReset(user.getUserId(), false)
UserLocalServiceUtil.updateEmailAddressVerified(user.getUserId(), true)
out.println("Done for " + user.getScreenName())
```

1. In *Control Panel* → *Server Administration* → *Script*, choose **Groovy**, and paste the script `setup` printed.
1. Fill in **Text Verification**, and click **Execute**. It prints *Done for mcp-service*.
1. Run `setup` again. It finds everything already there, and ends connected.

The other way, as in [Step 2](liferay-setup.html#step-2-verify-its-email-address) by hand: sign in as the user once and follow Liferay's pages.

## Without Liferay's Folders

Where you can't mount Liferay's `deploy` and `routes` folders, leave out those two mounts, and `setup` does the rest in two runs:

1. Run `setup`. It writes `liferay-mcp-oauth.zip` to `/srv/liferay-mcp` and asks you to deploy it.
1. Deploy the zip the way your Liferay deploys client extensions.
1. Copy the application's **Client ID** and **Client Secret** from *Control Panel* → *Security* → *OAuth 2 Administration* → *Liferay MCP server*. Save the secret alone in a file, for example `/srv/liferay-mcp/client-secret`.
1. Run `setup` again, adding `--client-id <the Client ID> --client-secret-file /data/client-secret`. It writes the routes folder, as on [Liferay PaaS by hand](paas.html#step-3-copy-the-credentials-for-the-server), and checks the connection.

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

- [Installing](index.html): the steps by hand, and step 3
- [Checking the Installation](checking.html)
- [Upgrading and Removing](upgrading-and-removing.html)
