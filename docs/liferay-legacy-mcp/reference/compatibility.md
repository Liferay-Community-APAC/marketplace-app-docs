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

Liferay Legacy MCP 1.2.0 supports Liferay DXP 7.4 U1–U92 and 2024.Q1 to 2026.Q2, with a Liferay DXP Enterprise Subscription. It doesn't run with Liferay Portal Community Edition: by default (`MCP_REQUIRE_DXP=true`), the server refuses to start unless the Liferay it connects to is confirmed as Liferay DXP. It was tested on the latest update of each quarterly release, and on 7.4 U92:

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
| 2026.Q1 LTS | 2026.Q1.9 | OAuth 2 or password |
| 2026.Q2 | 2026.Q2.9 | OAuth 2 or password |
| 7.4 U1–U92 | U92 | OAuth 2 or password; before U92, password only |

Another update of these releases is expected to work, but wasn't tested. The server says so, without refusing: `get_server_info` reports whether your update was among those tested.

## 7.4 Update Releases Before U92

The server works on every 7.4 update release, down to U1, with a **password connection** (`LIFERAY_USERNAME` and `LIFERAY_PASSWORD`). The OAuth 2 connection through a client extension doesn't: before U92, Liferay either can't create the application from the client extension, or doesn't publish its credentials. To keep the password out of command lines and settings, see [The Password Connection](settings.html#the-password-connection).

On the oldest updates, about 27 of the 37 tools are available. The others need Liferay APIs that didn't exist yet, and show as unavailable in `get_server_info`.

## Liferay DXP 2026.Q1 and 2026.Q2

From 1.2.0, the server supports 2026.Q1 and 2026.Q2, the first releases on Jakarta EE and with the new *Spaces* content management. The server runs outside Liferay, so Jakarta EE makes no difference to it, and its article tools write web content fields the way Spaces needs.

These releases also ship **Liferay's own MCP server**, as a beta behind a feature flag (`LPD-63311`), off by default, at `/o/mcp`. Both servers can run side by side, but their tools overlap. When the server starts, it checks whether Liferay's own MCP server answers, and if it does, it says so in its log and in `get_server_info` (`liferay.liferayMcp`, with a notice). It never stops because of it. If both are on:

- Use one of them for a task rather than both, and prefer one for writes.
- Give the two servers distinct names in your AI assistant, so you can tell their tools apart.

## Liferay DXP 2026.Q3 and Later

The server isn't tested on 2026.Q3 and later, and says so if connected to one. Those releases have Liferay's own MCP server, turned on with its `MCPServerConfiguration` setting as well as the feature flag; the server checks for it there too.

## Liferay SaaS

The server works with Liferay SaaS, set up through client extensions and the Control Panel only. Since portal properties can't be set there, `get_server_info` reports the release as unknown.

## Related Information

- [Known Issues](known-issues.html)
