---
title: Settings
parent: Reference
nav_order: 2
---

# Settings
{: .no_toc }

The server is set up with environment variables, passed with `-e` to `docker run`. It checks them when it starts, and refuses to start with a clear message when a combination is unsafe. Settings not given take the default below; in the images, files go under `/data`.

1. TOC
{:toc}

## Liferay

| Setting | Meaning | Default |
| --- | --- | --- |
| `LIFERAY_URL` | Liferay's address, as the server reaches it. | Required, unless `LIFERAY_ROUTES_DXP` is set |
| `LIFERAY_DEFAULT_SITE` | The site the tools use when a request doesn't name one: its friendly URL, name or ID. | None |
| `LIFERAY_TIMEOUT_MS` | How long to wait for each Liferay request, in milliseconds. | `15000` |
| `MCP_REQUIRE_DXP` | `true`: refuse to start unless the Liferay is confirmed to be Liferay DXP. `false`: run anyway, with a notice. | `true` |

## Connection

| Setting | Meaning | Default |
| --- | --- | --- |
| `LIFERAY_ROUTES_CLIENT_EXTENSION` | The routes folder of the OAuth 2 application, such as `liferay-mcp-oauth`. The server uses OAuth 2 when it's set. | None |
| `LIFERAY_ROUTES_DXP` | Liferay's own routes folder (`dxp`), which holds its address. Not needed when `LIFERAY_URL` is set. | None |
| `LIFERAY_USERNAME`, `LIFERAY_PASSWORD` | The password connection, used only when `LIFERAY_ROUTES_CLIENT_EXTENSION` isn't set. See [The Password Connection](#the-password-connection). | None |

## Administrator Connection

For `create_site`, `create_object`, the design tools, `list_users`, the administration API areas and the JSON web service tools, on a person's own computer only.

| Setting | Meaning | Default |
| --- | --- | --- |
| `LIFERAY_ROUTES_BUILDER_EXTENSION` | The routes folder of the administrator OAuth 2 application (`liferay-mcp-builder`). | None: the administrator tools are unavailable |
| `MCP_SITE_TEMPLATES` | The site templates (site initializer keys) `create_site` may use, separated by commas. | None |
| `MCP_SITE_TEMPLATE_CONTRACTS` | A file listing what each site template expects to be created. | None |
| `MCP_CONTENT_EDITOR_ROLE` | The site role `create_site` gives the service user in each site it creates: its external reference code or name. | `mcp-content-editor` |

## Writes

| Setting | Meaning | Default |
| --- | --- | --- |
| `MCP_WRITE_MODE` | `off`: write tools refuse, and show the request they would have sent. `confirm`: every write is previewed, and runs only once it's confirmed. | `off` |
| `MCP_ADMIN_WRITES` | `true` lets the generic tools change the administration areas, such as users and roles, objects and workflow, and lets `write_jsonws` call methods that change something. | `false` |
| `MCP_DESTRUCTIVE_WRITES` | `true` allows deletes, batch requests and permission changes, each previewed and confirmed. | `false` |
| `MCP_BATCH_LIMIT` | The most items in one batch request. | `50` |
| `MCP_DISABLED_OPERATIONS` | Operations to treat as unsupported, separated by commas, such as `createArticle`. `get_server_info` lists their names. | None |

## Audit and Files

| Setting | Meaning | Default |
| --- | --- | --- |
| `AUDIT_LOG_PATH` | Where every write attempt is logged. | `/data/logs/audit.jsonl` |
| `ARTIFACTS_DIR` | Where screenshots and exports are saved. | `/data/artifacts` |

## Page Inspection

For `inspect_page` and `take_screenshot`, in the `1.1.0-full` image.

| Setting | Meaning | Default |
| --- | --- | --- |
| `BROWSER_SIGN_IN` | `true`: view pages signed in, as `LIFERAY_BROWSER_USERNAME`. `false`: view them as a guest. Over HTTPS, pages are always viewed as a guest. | `false` |
| `LIFERAY_BROWSER_USERNAME` | The view-only user to sign in as. | None |
| `LIFERAY_BROWSER_PASSWORD_FILE` | A file holding that user's password. | None |

## HTTPS

| Setting | Meaning | Default |
| --- | --- | --- |
| `MCP_HTTP_HOST` | The address the server listens on, with `--http`. Anything other than loopback needs `MCP_PUBLIC_URL` and a way to identify callers. | `127.0.0.1` |
| `MCP_HTTP_PORT` | The port it listens on. | `3333` |
| `MCP_PUBLIC_URL` | The `https` address callers use, through your TLS proxy. | None |
| `MCP_TRUST_PROXY` | `private`: the server is behind a TLS proxy on a private network, so rate limits use the client address the proxy forwards. `none`: they use the connection's address. | `none` |
| `MCP_HTTP_API_KEYS_FILE` | The API keys file: one `name:read` or `name:write` line with a key's hash, for each service. | None |
| `MCP_HTTP_WRITES` | Over HTTPS, writes stay off unless this is `true`, whatever `MCP_WRITE_MODE` says: nobody sees a service's preview. | `false` |

## Sign-In

For people who sign in with Liferay over HTTPS.

| Setting | Meaning | Default |
| --- | --- | --- |
| `LIFERAY_SIGN_IN_CLIENT_ID` | The sign-in OAuth 2 application's client ID. | None |
| `LIFERAY_SIGN_IN_CLIENT_SECRET_FILE` | A file holding its client secret. | None |
| `MCP_OAUTH_REDIRECT_ALLOWLIST` | Which AI assistants may register, by the start of their redirect addresses, separated by commas. Desktop assistants on loopback are always allowed. | Any |
| `MCP_OAUTH_CLIENT_METADATA` | `true`: accept assistants named by the `https` address of their metadata document. | `true` |

## Logging

| Setting | Meaning | Default |
| --- | --- | --- |
| `LOG_LEVEL` | `debug`, `info`, `warn` or `error`. Logs go to standard error: `docker logs`, or the AI assistant's MCP logs. | `info` |

## Secrets

Secrets are kept in files, readable only by the server: the routes folders, the API keys file, and the `*_FILE` settings. The server reads them when it starts, and never logs them.

### The Password Connection

The one exception is the password connection, which the server takes only as `LIFERAY_PASSWORD`, an environment variable. Prefer the OAuth 2 connection; the password connection is for 7.4 update releases before U92, which can't create the OAuth 2 application from a client extension.

To keep the password out of command lines and AI assistants' settings, put both settings in an environment file readable only by you, and pass the file to Docker:

```bash
mkdir -p ~/liferay-mcp
printf 'Password: '; read -rs PASSWORD; echo
printf 'LIFERAY_USERNAME=mcp-service@example.com\nLIFERAY_PASSWORD=%s\n' "$PASSWORD" > ~/liferay-mcp/connection.env; unset PASSWORD
chmod 600 ~/liferay-mcp/connection.env
```

Then use `--env-file ~/liferay-mcp/connection.env` in the `docker run` command, instead of the `LIFERAY_ROUTES_CLIENT_EXTENSION` setting and the routes volume. In an AI assistant's `mcpServers` setting, give the file's full path, without `~`.

## Related Information

- [Installing](../installing/)
- [Writes and Previews](../using/writes.html)
