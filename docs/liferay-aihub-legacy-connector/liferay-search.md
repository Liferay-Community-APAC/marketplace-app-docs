---
title: Liferay Search
nav_order: 9
description: How AI Hub's Liferay Search (RAG) node works across the eight supported releases, and the feature flags it needs.
---

# Liferay Search
{: .no_toc }

1. TOC
{:toc}

AI Hub's Liferay Search node (an agent's Retrieval-Augmented Generation set to `{"contentRetriever": {"key": "liferay"}}`) searches the customer's DXP with the user's token: `GET <Liferay DXP URL>/o/search/v1.0/search`. It runs as the token's user, so results follow that user's own permissions. Whether that endpoint exists depends on the release.

| Release | `GET /o/search/v1.0/search` | Turn on with |
|---|---|---|
| 2024.Q1 and 2024.Q2 | Not in Liferay — **served by the module** | `feature.flag.LPS-179669=true` |
| 2024.Q3 to 2025.Q3 | Liferay's, behind a Developer flag | `feature.flag.LPD-11232=true` |
| 2025.Q4 | Liferay's, on by default | nothing |

On 2024.Q1 and 2024.Q2 the module registers `GET /o/search/v1.0/search` (and nowhere else) and answers it with the release's own search. It is gated the same way as Liferay's own search — 404 until the flag is on and while AI Hub Tokens is off — and accepts only a valid user token carrying the search scope (401 without a token, 403 without the scope). Basic auth and sessions are not accepted.

{: .warning }
`LPD-11232` is a **Developer** feature flag on these releases — enable and test it on a non-production copy first.

## Requirements

- AI Hub must be able to reach this DXP (the Liferay DXP URL in AI Hub → Configurations).
- The Liferay DXP URL's host must be in `virtual.hosts.valid.hosts` on the DXP.
- The user token needs `Liferay.Portal.Search.REST.everything.read` (the default `liferay-aihub-legacy-token` application carries it unless you removed it).
