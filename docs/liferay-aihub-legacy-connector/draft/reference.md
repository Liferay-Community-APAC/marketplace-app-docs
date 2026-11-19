---
title: Reference
nav_exclude: true
search_exclude: true
description: Compatibility, the settings reference, author permissions, how the connector is kept safe, the feature gaps, and versions.
---

# Reference
{: .no_toc }

1. TOC
{:toc}

## Compatibility

The connector supports the eight quarterly releases from **2024.Q1 to 2025.Q4**, each tested on the latest update of the quarter. It deactivates itself on 2026.Q1 and later, which have Liferay's own cell.

| Release | Platform | AI Hub Liferay Search endpoint |
|---|---|---|
| 2024.Q1 | Java EE | Served by the module (`feature.flag.LPS-179669=true`) |
| 2024.Q2 | Java EE | Served by the module (`feature.flag.LPS-179669=true`) |
| 2024.Q3 | Java EE | Liferay's, behind `feature.flag.LPD-11232=true` |
| 2024.Q4 | Java EE | Liferay's, behind `feature.flag.LPD-11232=true` |
| 2025.Q1 | Java EE | Liferay's, behind `feature.flag.LPD-11232=true` |
| 2025.Q2 | Java EE | Liferay's, behind `feature.flag.LPD-11232=true` |
| 2025.Q3 | Jakarta EE | Liferay's, behind `feature.flag.LPD-11232=true` |
| 2025.Q4 | Jakarta EE | Liferay's, on by default |

The chat, tokens, proxy and the in-editor assistant's editor DOM were validated on a real Web Content editor on every one of the eight releases. A deployed-connector end-to-end run (image install → token → inject → proxy → write-back) was done on 2025.Q4.

{: .important }
**A Liferay DXP Enterprise Subscription and a licensed Liferay AI Hub are both required.** The connector is proprietary, for Liferay internal use, and is not supported by Liferay.

## Settings reference

All settings are under **Instance Settings → AI Hub** (AI Hub Chat is site-scoped; AI Hub Tokens is instance-scoped).

| Setting | Group | Default | Notes |
|---|---|---|---|
| Enabled | AI Hub Tokens | off | Master switch for token issuing; also required by the proxy. |
| AI Hub URL | AI Hub Tokens | — | AI Hub service URL. |
| Client ID / Client Secret | AI Hub Tokens | — | OAuth application on AI Hub for this Liferay. |
| Client Secret File | AI Hub Tokens | — | Alternative to the secret; a file in `$LIFERAY_HOME/secrets`. |
| User Token Application | AI Hub Tokens | default | Set to the `-cell` application for the broader scopes. |
| Proxy AI Hub's Chat Calls | AI Hub Tokens | **on** | Keeps AI Hub's access token on the server. |
| AI Hub Assistant | AI Hub Tokens | off | The in-editor assistant. |
| Workflow Agent Step | AI Hub Tokens | off | The Kaleo agent step. |
| Enabled | AI Hub Chat | off | Shows the widget. |
| Chatbot External Reference Code | AI Hub Chat | — | From AI Hub's Chatbots page. |
| Guest Users Allowed | AI Hub Chat | off | Let guests chat anonymously. |
| AI Hub URL | AI Hub Chat | — | Optional per-site override of the browser-facing URL. |

Environment variables: `LIFERAY_AIHUB_LEGACY_SECRETS_DIR` (secrets directory) and `LIFERAY_AIHUB_LEGACY_URLS` (allowed AI Hub URLs on multi-instance servers).

## Author permissions

Every in-editor assistant action runs in the browser **as the signed-in DXP user**, so each is gated by that user's own permissions, not by the connector's token scopes.

| Action | Works for a minimal author? | Needs |
|---|---|---|
| Writing tools, Generate, Translate | ✅ | Nothing beyond being a signed-in author. |
| Suggest tags | ✅ (degraded) | Works; reusing the site's existing tags needs *Categorization → Categories → View*. |
| Suggest categories | ⚠️ | *Categorization → Categories → View* on the site. |
| Generate image | ⚠️ | *Documents and Media → Add Document* on the site. |

To give authors the full assistant, grant their role **Categories → View** and **Documents and Media → Add Document** (plus *Access in Site Administration*) — ordinary content-author permissions, nothing portal-admin.

## How it's kept safe

- **AI Hub's access token stays on the server** when the proxy is on (the default) — the browser gets an opaque handle that works only through the proxy, and only for the widget's and assistant's own calls.
- **Tokens only where the chat or assistant is on** — the token endpoint answers 404 otherwise, so no browser gets a token for nothing.
- **Browser session only** — the endpoint accepts the signed-in session with its CSRF token; a guest, Basic auth or an OAuth bearer gets 403.
- **Revoked on sign-out** — signing out revokes the user's issued tokens and drops them from the cache.
- **Read-only by default** — the default user token carries only user and search read scopes; search runs as the user and follows their permissions.
- **Least privilege on AI Hub** — the connector asks AI Hub only for what an end user already has (running agents and chat as themselves); AI Hub administration stays admin-only.
- **Secrets never leave the server** — errors are logged without the secret, responses are `no-store`, the secret file is bounded to the secrets directory, and multi-instance servers isolate each instance's secret and allowed URLs.

The connector's code (token endpoint, provider, cache, secret file, chat include, search endpoint) and the client extension were security-reviewed; the proxy (keeping AI Hub's token on the server) and the multi-instance and sign-out hardening came out of that review.

## Feature gaps and exceptions

Two AI Hub capabilities are intentionally **not** reproduced, because their targets don't exist on these releases:

- **Bulk content generation** (`L_GENERATE_CONTENT`) targets the 2026.Q1+ Liferay CMS (Spaces, the `/o/cms/…` API), a content model 2024.Q1–2025.Q4 don't have (they use Web Content / journal). Its output has nowhere to land, so it stays with AI Hub.
- **The native translate agent** (`L_TRANSLATE_CONTENT`) is bound to AI Hub's editor session — it reads the content server-side through a page-context node, so a stateless connector call has no context to give it. **Translation itself is shipped**: the connector translates field by field with the portable field-generation agent instead.

One deliberate behavior difference: **Generate image** uploads to **Documents &amp; Media** and inserts into the body, where the native (2026.Q1+) uploads to the CMS — because the CMS document store doesn't exist on these releases.

## Versions

Releases are tracked by the module's `Bundle-Version` in the connector's `CHANGELOG.md`. Highlights:

- Tokens, chat, the server-side proxy, and AI Hub's Liferay Search backport for 2024.Q1/Q2.
- The workflow agent step.
- The in-editor AI Assistant: writing tools, suggest tags, suggest categories, generate field value, generate image, and translate.
- Per-release editor-DOM validation across all eight releases, and a deployed-connector end-to-end run on 2025.Q4.

{: .note }
This documentation is a **draft for review**. Version numbers and the final screenshots will be settled before it replaces the app's coming-soon page.
