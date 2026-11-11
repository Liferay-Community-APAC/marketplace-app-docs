---
title: Compatibility
parent: Reference
nav_order: 3
---

# Compatibility
{: .no_toc }

1. TOC
{:toc}

## Tested Releases

Liferay Legacy MCP 1.1.0 supports Liferay DXP 7.4 U1–U92 and 2024.Q1 to 2025.Q4. It was tested on the latest update of each quarterly release, and on 7.4 U92:

| Release | Tested Update | Connection |
| --- | --- | --- |
| 2024.Q1 | 2024.Q1.32 | OAuth 2 or password |
| 2024.Q2 | 2024.Q2.13 | OAuth 2 or password |
| 2024.Q3 | 2024.Q3.13 | OAuth 2 or password |
| 2024.Q4 | 2024.Q4.7 | OAuth 2 or password |
| 2025.Q1 LTS | 2025.Q1.29 | OAuth 2 or password |
| 2025.Q2 | 2025.Q2.12 | OAuth 2 or password |
| 2025.Q3 | 2025.Q3.10 | OAuth 2 or password |
| 2025.Q4 | 2025.Q4.12 | OAuth 2 or password |
| 7.4 U1–U92 | U92 | OAuth 2 or password; before U92, password only |

Another update of these releases is expected to work, but wasn't tested. The server says so, without refusing: `get_server_info` reports whether your update was among those tested.

## 7.4 Update Releases Before U92

The server works on every 7.4 update release, down to U1, with a **password connection** (`LIFERAY_USERNAME` and `LIFERAY_PASSWORD`). The OAuth 2 connection through a client extension doesn't: before U92, Liferay either can't create the application from the client extension, or doesn't publish its credentials. To keep the password out of command lines and settings, see [The Password Connection](settings.html#the-password-connection).

On the oldest updates, about 27 of the 37 tools are available. The others need Liferay APIs that didn't exist yet, and show as unavailable in `get_server_info`.

## Liferay DXP 2026.Q1 and Later

Liferay DXP 2026.Q1 and later have their own ways for AI to reach Liferay. The server isn't tested on them, and says so if connected to one.

## Liferay SaaS

The server works with Liferay SaaS, set up through client extensions and the Control Panel only. Since portal properties can't be set there, `get_server_info` reports the release as unknown.

## Related Information

- [Known Issues](known-issues.html)
