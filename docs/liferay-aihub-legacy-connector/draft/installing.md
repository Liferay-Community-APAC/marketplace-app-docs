---
title: Installing
nav_exclude: true
search_exclude: true
description: Install the AI Hub Legacy Connector — the connector user, the OAuth client extensions, and the module — with Docker or by hand, then verify it.
---

# Installing
{: .no_toc }

For DXP 2024.Q1 to 2025.Q4. After installing, connect AI Hub in [Configuration](configuration.html).
{: .fs-5 .fw-300 }

1. TOC
{:toc}

## Before you start: the connector user

Create the connector's service user first. In **Control Panel → Users and Organizations**, add a user with screen name `aihub-connector` and any email (for example `aihub-connector@example.com`) and a long random password nobody keeps. Give it **no roles** — remove any Liferay added by default other than User.

The OAuth client extension names this user as its application's user. Nothing ever acts *as* it: the module issues user tokens with `LocalOAuthClient` and never uses the client-credentials grant (a client-credentials request with the application's own credentials is refused). So the user is an identity for the OAuth application, not an account with access.

{: .warning }
**Create the user before deploying the client extension.** If the client extension is deployed first, Liferay logs `NoSuchUserException … screenName=aihub-connector` and creates no OAuth application. Creating the user afterward and redeploying is not enough — the configuration hasn't changed, so Liferay doesn't re-create it. **Restart Liferay** after creating the user.

## With Docker (recommended)

The installer image holds the module for all eight releases and its client extensions, and writes them into a Liferay deploy folder you mount. It is a public package on GitHub Container Registry — [`liferay-aihub-legacy-connector`](https://github.com/orgs/Liferay-Community-APAC/packages/container/package/liferay-aihub-legacy-connector) — so it pulls without authentication.

Create the `aihub-connector` user first (above), then:

```bash
docker run --rm \
  -v /path/to/liferay/deploy:/deploy \
  -e LIFERAY_RELEASE=2025.q1 \
  -e LIFERAY_URL=https://liferay.example.com \
  ghcr.io/liferay-community-apac/liferay-aihub-legacy-connector:latest install
```

This writes the module jar and the `liferay-aihub-legacy-token` client extension into the deploy folder; Liferay installs them from there (module **Active**, the OAuth application created with its two read scopes). For the broader cell scopes, add `-e CELL_SCOPES=true` to also write `liferay-aihub-legacy-token-cell` (see [Configuration → The cell's scopes](configuration.html#the-cells-scopes-optional)).

Print the configuration template with:

```bash
docker run --rm ghcr.io/liferay-community-apac/liferay-aihub-legacy-connector:2.9.1 config
```

The installer validates its inputs: missing variables, an unknown release, a non-`http(s)` URL and a missing `/deploy` mount each stop it with a message.

## Without Docker

Build and package for the instance's release from the connector repository:

```bash
DXP_RELEASE=dxp-2025.q1.29-lts aihub-legacy-module/scripts/aihub-token-module.sh package
```

`package` writes a zip with the bundle, the `liferay-aihub-legacy-token` client extension (and the optional `-cell`), a configuration template, a README and SHA-256 checksums. Use the package built for the instance's release.

Then:

1. **Deploy the OAuth client extension.** `liferay-aihub-legacy-token` creates the OAuth application that issues the user token, acting as the `aihub-connector` user, with two read scopes: `Liferay.Headless.Admin.User.everything.read` (who the user is) and `Liferay.Portal.Search.REST.everything.read` (AI Hub's Liferay Search). Remove the second from its `client-extension.yaml` if AI Hub's agents shouldn't search this Liferay.
2. **Install the module.** Copy `com.lgsapac.aihub.legacy.token-<version>.jar` into `$LIFERAY_HOME/osgi/modules/` on every node, readable by the user Liferay runs as. Liferay starts it with no restart; in the Gogo shell, `lb aihub.legacy` lists it **Active**.
3. **Configure the tokens** — see [Configuration](configuration.html).

## The OAuth applications

| Application | Deploy | User token scopes | When |
|---|---|---|---|
| `liferay-aihub-legacy-token` | Always (default) | User (read) + Search (read) | The read-only user token — proves who the user is and lets AI Hub's search node search as them. |
| `liferay-aihub-legacy-token-cell` | Optional | Adds taxonomy, objects, bulk, batch | Only where AI Hub's agents should act on this Liferay as the user. See [Configuration → The cell's scopes](configuration.html#the-cells-scopes-optional). |

## Pages the widget needs

- **Theme:** the widget is added by Liferay's bottom-of-page include (as Click to Chat's is). Liferay's themes and custom themes built from them keep it; a theme whose `portal_normal.ftl` drops `<@liferay_util["include"] page=bottom_include />` gets no widget.
- **Content Security Policy:** allow AI Hub's host in `script-src`, `style-src`, `img-src` and `font-src` (the widget's files come from AI Hub). With the proxy on, `connect-src 'self'` is enough; with it off, add AI Hub's host to `connect-src` too.

## Check the install

- The module is **Active**: Gogo `lb aihub.legacy`.
- Instance Settings → AI Hub shows **AI Hub Tokens (Legacy Connector)** and **AI Hub Chat (Legacy Connector)**.
- `POST /o/ai-hub-cell/v1.0/authorization-tokens` as a guest returns **403** (404 means it's off or not configured).
- Once Chat or the Assistant is on, from a signed-in page with header `x-csrf-token: Liferay.authToken` it returns `accessToken`, `serviceURL` and `userToken`.

## Upgrade and remove

- **Upgrade (by hand):** uninstall the bundle first (Gogo `lb aihub.legacy`, then `uninstall <id>`), delete its file from `osgi/modules`, and copy the new one in. The installer image upgrades in place — installing a newer image over a running one replaces the jar under the same name and Liferay updates the same bundle.
- **Remove:** uninstall the bundle and delete its file, delete the `.config` file, and undeploy the `liferay-aihub-legacy-token` client extension (and `-cell`, if deployed). The AI Hub Chat settings stay in the database but do nothing without the bundle.

{: .note }
Do the **Remove** steps before upgrading the DXP to 2026.Q1 or later, which has Liferay's own cell and Click to Chat AI Hub provider. If you forget, the connector detects the cell and deactivates itself, logging a warning to uninstall it.
