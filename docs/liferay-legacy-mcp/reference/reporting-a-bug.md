---
title: Reporting a Bug
parent: Reference
nav_order: 5
---

# Reporting a Bug
{: .no_toc }

Report problems with Liferay Legacy MCP, ask questions, and suggest improvements on
[Liferay Discuss](https://discuss.liferay.com), Liferay's community forum. You need a Liferay Discuss account.

1. TOC
{:toc}

## Before You Report

- Check [Known Issues](known-issues.html) for your release.
- If the server doesn't connect, or a tool fails, follow [If Something's Wrong](../installing/checking.html#if-somethings-wrong).
- Ask the AI assistant *"What Liferay server are you connected to?"*: `get_server_info` shows the release, the connection, the write mode, and why a tool is unavailable.

## Report a Bug or Ask a Question

1. Open the Liferay Community APAC site's [Get help](https://liferay-community-apac.github.io/get-help/) page, also linked as **Contact** at the bottom of every page.
1. Under **Community help**, choose **Liferay Legacy MCP**, or **Something else** if it isn't listed, and describe your question or problem in a few words.
1. Click **Post on Liferay Discuss**, sign in, and complete the post. It goes to the **Development** category on [discuss.liferay.com](https://discuss.liferay.com).

Include:

- the Liferay Legacy MCP version and image tag, such as `1.1.0-slim`, and the Liferay DXP version;
- where Liferay runs (Liferay Self-Hosted with Tomcat or Docker, or Liferay PaaS), and how the server is used (Claude Code, the Claude desktop app, another AI assistant, or over HTTPS);
- what you asked, what happened, and what you expected;
- the server's log lines (standard error, or `docker logs`), and errors from the Liferay log.

{: .warning }
Posts on Liferay Discuss are public. Before you paste logs, settings or screenshots, remove credentials, client secrets, API keys, email addresses, user names, hostnames, internal addresses, and any customer data.

## Security Problems

Don't report a security problem in a public post. Use the [Get help](https://liferay-community-apac.github.io/get-help/) page to reach the Liferay Community APAC team, and say it's a security problem, without the details, so the team can follow it up privately.

## What Happens Next

Liferay Legacy MCP is a community app, not a Liferay product: Liferay Support doesn't cover it, even with a Liferay DXP subscription. Community members and the maintainers answer on Liferay Discuss on a best-effort basis.

## Related Information

- [Known Issues](known-issues.html)
- [Checking the Installation](../installing/checking.html)
