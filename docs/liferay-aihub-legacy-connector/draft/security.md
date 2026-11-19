---
title: Security
nav_exclude: true
search_exclude: true
description: How the AI Hub Legacy Connector keeps AI Hub's access token, the user tokens, and the client secret safe.
---

# Security
{: .no_toc }

1. TOC
{:toc}

The connector's code (token endpoint, provider and cache, secret file, chat include, search endpoint, registrars) and the client extension were security-reviewed. The proxy and the multi-instance and sign-out hardening came out of that review.

## AI Hub's access token stays on the server

With the proxy on (the default), the browser gets an opaque handle that works only through this Liferay's proxy, and only for the widget's and assistant's own calls. The proxy forwards the widget's four calls (the chatbot lookup, the chat event stream, messages, reports) and the assistant's `agent-instances` family — and nothing else (anything else is 404, other methods 405, a body over 256 KB 413). The handle is random, per user per instance, valid for an hour and renewed, dropped on sign-out, and accepted only with that user active.

## Tokens only where they're used

The token endpoint answers 404 unless AI Hub Chat or the Assistant is on, so no browser gets a token for nothing. It accepts only the signed-in session with its CSRF token — a guest, Basic auth or an OAuth bearer gets 403. Signing out revokes the user's issued tokens and drops them from the cache.

## Read-only by default

The default user token carries only user and search **read** scopes; search runs as the user and follows their permissions. On the AI Hub side, the connector asks only for what an end user already has (running agents and chat as themselves) — AI Hub administration, provisioning and OAuth 2 administration stay admin-only.

## Secrets never leave the server

- Errors are logged without the secret; the browser gets a bare 503. Responses are `Cache-Control: no-store, private`.
- The client secret can be kept out of the configuration in a file bounded to `$LIFERAY_HOME/secrets` (links and `..` resolved, max 1 KB, one line), so an administrator who can edit the setting can't make the module send another file to AI Hub.
- On multi-instance servers, each instance's secret file must be in its own `secrets/<web ID>/` folder, and the AI Hub URL must be in `LIFERAY_AIHUB_LEGACY_URLS` (set by the server operator), so one instance's administrator can't send another's secret or pick an arbitrary host.
- Every value written into the chat page snippet is escaped; the token caches and OAuth lookups are keyed per instance.

## Out of the way of Liferay's own code

If a `com.liferay.ai.hub.cell` bundle is installed (2026.Q1+ or the cell backport), the token endpoint isn't registered; if Click to Chat has its AI Hub provider, the chat adds nothing. Both log a warning to uninstall the connector.
