---
title: Configuration
nav_order: 4
description: Connect AI Hub, turn on chat and the in-editor assistant, the proxy, the secret file, multi-instance servers, and the Liferay Search flags.
---

# Configuration
{: .no_toc }

1. TOC
{:toc}

## Connect AI Hub (AI Hub Tokens)

AI Hub Tokens is **per instance** (company) and **off until configured** — while off or incomplete the token endpoint answers 404, exactly as a DXP without the cell does.

Set it either in **Instance Settings → AI Hub → AI Hub Tokens**, or with a configuration file. For the file, copy the package's `.config.sample` to:

```
$LIFERAY_HOME/osgi/configs/com.lgsapac.aihub.legacy.token.configuration.AIHubTokenConfiguration.scoped~token.config
```

and fill in:

- **AI Hub URL** — your AI Hub service URL.
- **Client ID / Client Secret** — of an OAuth application on AI Hub for this Liferay.
- **Company ID** — the instance.
- `enabled=B"true"`.

Make the file readable only by Liferay.

### Keep the secret out of the configuration

Instead of the client secret, set **`aiHubClientSecretFile`** to a file holding the secret alone, on one line, in `$LIFERAY_HOME/secrets` (for example a Kubernetes or Docker secret mounted there). The environment variable `LIFERAY_AIHUB_LEGACY_SECRETS_DIR` sets another directory.

- Only files inside that directory are read (links and `..` are resolved first), up to 1 KB and one line — so an administrator who can edit the setting can't make the module send another file of the server to AI Hub.
- It's read again for each new AI Hub token, so a rotated secret needs no configuration change.
- If the plain Client Secret is also set, it wins.

### Servers with several instances

Where more than one instance runs and their administrators aren't all trusted with each other's secrets:

- **Secret file** must be in the instance's own folder, `secrets/<web ID>/` (for example `secrets/liferay.com/aihub-client-secret`). A file directly in `secrets/` is refused.
- **AI Hub URL** must be one of the URLs in the environment variable `LIFERAY_AIHUB_LEGACY_URLS` (comma-separated), which the server operator sets. Otherwise no credentials are sent.

On a single-instance server neither restriction applies.

## Turn on AI Hub Chat

As an administrator, open **Control Panel → Instance Settings → AI Hub → AI Hub Chat**, tick **Enabled**, enter the **Chatbot External Reference Code** (from AI Hub's Chatbots page), and Save. Every site's pages then load AI Hub's widget.

The settings are **site-scoped**, like Liferay's own site settings: set in Instance Settings they apply to every site; a site's **Site Settings → AI Hub** overrides them for that site (another chatbot, or off). Nothing is added in the Control Panel or in pop-ups, and nothing where Click to Chat already has its AI Hub provider — so the widget is never added twice.

| Setting | Notes |
|---|---|
| **Enabled** | Off by default. |
| **Chatbot External Reference Code** | From AI Hub's Chatbots page. |
| **Guest Users Allowed** | Off by default — guests get no widget. On, guests get it and chat anonymously, if the chatbot allows guest access in AI Hub. |
| **AI Hub URL** | Optional. Empty uses AI Hub Tokens' URL; set it only if browsers reach AI Hub at a different address than the server does. |

## The proxy (on by default)

**AI Hub Tokens → Proxy AI Hub's Chat Calls** is on by default. The widget's files still come from AI Hub, but its calls go to this Liferay's proxy at `/o/ai-hub-cell-proxy`, which adds AI Hub's access token on the server. The browser gets an opaque handle, never AI Hub's token.

- The proxy forwards **only** the calls the widget and assistant make (the chatbot lookup, the chat event stream, messages, reports, and the `agent-instances` family for the assistant). Anything else is refused (404), other methods 405, oversized bodies 413.
- The handle is random, per user per instance, valid for an hour and renewed, dropped on sign-out, and accepted only with that user active.
- **No CORS needed** — the browser only calls its own Liferay. AI Hub's *Environment URLs* only matter with the proxy **off**.

### With the proxy off

Browsers get AI Hub's access token and call AI Hub directly (as with Liferay's cell). You must then let AI Hub accept calls from this Liferay's address: in AI Hub, **Configurations → Credentials** for the account, put this Liferay's address (with the scheme, e.g. `https://liferay.example.com`, no trailing slash) in **Environment URLs**, and Save.

## Turn on the in-editor AI Assistant

**AI Hub Tokens → AI Hub Assistant** (off by default). While off, no trigger is added to the editor. The assistant turns token issuing on by itself, so it works with AI Hub Tokens configured even if AI Hub **Chat** is off. It uses the `agent-instances` endpoints through the same proxy. See [Using → The in-editor AI Assistant](using.html#the-in-editor-ai-assistant).

## Optional: the workflow agent step

**AI Hub Tokens → Workflow Agent Step** (off by default) lets a Kaleo workflow run an AI Hub agent as a step. It runs on the server with AI Hub's token (no browser, no proxy), and needs AI Hub Tokens configured but not Chat. See [Using → Workflow agent step](using.html#workflow-agent-step).

## The cell's scopes (optional)

By default the user token is **read-only** (user + search). AI Hub features that act on this Liferay as the user through other APIs — taxonomy, objects, bulk and batch — need the broader scopes the cell's own application grants. Deploy `liferay-aihub-legacy-token-cell` alongside the default and set **User Token Application** (`userTokenApplicationExternalReferenceCode`) to `liferay-aihub-legacy-token-cell-headless-server`; clear it to go back.

| Scope | Default | Cell scopes |
|---|---|---|
| `Liferay.Headless.Admin.User.everything.read` | ✓ | ✓ |
| `Liferay.Portal.Search.REST.everything.read` | ✓ | ✓ |
| `Liferay.Headless.Admin.Taxonomy.everything.read` | | ✓ |
| `Liferay.Object.Admin.REST.everything.read` | | ✓ |
| `Liferay.Bulk.REST.everything.write` | | ✓ |
| `Liferay.Headless.Batch.Engine.everything` | | ✓ |

{: .note }
The cell token can **change** things as the user, and AI Hub holds it for the chat, so use it only where AI Hub's agents should act on this Liferay directly. The CMP scopes the cell also grants are left out because those APIs don't exist on these releases.

## AI Hub's Liferay Search

AI Hub's Liferay Search node searches the customer's DXP with the user's token (`GET /o/search/v1.0/search`). Whether that endpoint exists depends on the release:

| Release | `GET /o/search/v1.0/search` | Turn on with |
|---|---|---|
| 2024.Q1 and 2024.Q2 | Not in Liferay — **served by the module** | `feature.flag.LPS-179669=true` |
| 2024.Q3 to 2025.Q3 | Liferay's, behind a Developer flag | `feature.flag.LPD-11232=true` |
| 2025.Q4 | Liferay's, on by default | nothing |

The search runs as the token's user, so results follow that user's own permissions.

{: .warning }
`LPD-11232` is a **Developer** feature flag on these releases — enable and test it on a non-production copy first.

## Pages the widget needs

- **Theme:** the widget is added by Liferay's bottom-of-page include (as Click to Chat's is). Liferay's themes and custom themes built from them keep it; a theme whose `portal_normal.ftl` drops `<@liferay_util["include"] page=bottom_include />` gets no widget.
- **Content Security Policy:** allow AI Hub's host in `script-src`, `style-src`, `img-src` and `font-src` (the widget's files come from AI Hub). With the proxy on, `connect-src 'self'` is enough; with it off, add AI Hub's host to `connect-src` too.

## Check it

- `POST /o/ai-hub-cell/v1.0/authorization-tokens` as a guest returns **403** (404 means it's off or not configured).
- Once Chat is on, from a signed-in page with header `x-csrf-token: Liferay.authToken` it returns `accessToken`, `serviceURL` and `userToken`.
- A **503** means it couldn't get a token from AI Hub; Liferay's log says why, without the secret.

Next: [Using](using.html).
