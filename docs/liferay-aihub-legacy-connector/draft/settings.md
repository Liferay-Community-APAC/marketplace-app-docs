---
title: Settings Reference
nav_exclude: true
search_exclude: true
description: Every AI Hub Legacy Connector setting and environment variable.
---

# Settings Reference
{: .no_toc }

All settings are under **Instance Settings → AI Hub** — AI Hub Tokens is instance-scoped; AI Hub Chat is site-scoped (and overridable per site in Site Settings → AI Hub).

## AI Hub Tokens (Legacy Connector)

| Setting | Key | Default | Notes |
|---|---|---|---|
| Enabled | `enabled` | off | Master switch for token issuing; also required by the proxy. |
| AI Hub URL | `aiHubURL` | — | AI Hub service URL, as this Liferay reaches it. |
| AI Hub Client ID | `aiHubClientId` | — | OAuth application on AI Hub for this Liferay. |
| AI Hub Client Secret | `aiHubClientSecret` | — | The client secret (wins over the file if both are set). |
| AI Hub Client Secret File | `aiHubClientSecretFile` | — | A file in `$LIFERAY_HOME/secrets` holding the secret alone. |
| User Token Application | `userTokenApplicationExternalReferenceCode` | default | Set to the `-cell` application for the broader scopes. |
| Proxy AI Hub's Chat Calls | `proxyEnabled` | **on** | Keeps AI Hub's access token on the server. |
| AI Hub Assistant | `assistantEnabled` | off | The in-editor assistant. |
| Workflow Agent Step | `workflowAgentStepEnabled` | off | The Kaleo agent step. |

## AI Hub Chat (Legacy Connector)

| Setting | Key | Default | Notes |
|---|---|---|---|
| Enabled | `enabled` | off | Shows the widget. |
| Chatbot External Reference Code | `chatbotExternalReferenceCode` | — | From AI Hub's Chatbots page. |
| Guest Users Allowed | `guestUsersAllowed` | off | Let guests chat anonymously. |
| AI Hub URL | `aiHubURL` | — | Optional per-site override of the browser-facing URL. |

## Environment variables

| Variable | Purpose |
|---|---|
| `LIFERAY_AIHUB_LEGACY_SECRETS_DIR` | Directory for the client secret file (default `$LIFERAY_HOME/secrets`). |
| `LIFERAY_AIHUB_LEGACY_URLS` | Comma-separated allowed AI Hub URLs, required on multi-instance servers. |

## Feature flags (for Liferay Search)

| Flag | Releases |
|---|---|
| `feature.flag.LPS-179669=true` | 2024.Q1, 2024.Q2 (the module's search endpoint) |
| `feature.flag.LPD-11232=true` | 2024.Q3 to 2025.Q3 (Liferay's search endpoint) |

See [Liferay Search](liferay-search.html).
