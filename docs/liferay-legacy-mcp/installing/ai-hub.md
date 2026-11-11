---
title: For Liferay AI Hub
parent: Installing
nav_order: 4
---

# For Liferay AI Hub
{: .no_toc }

Liferay AI Hub agents can call the server as an MCP server. AI Hub sends each request with a Liferay token for the user who's chatting, and the server checks that token with the Liferay that issued it. Reads use the server's own connection. Changes become **approval links**: nothing changes until the person who asked signs in to your Liferay and approves the change, and it then runs as them, with their permissions.

1. TOC
{:toc}

## Before You Begin

- **Who issues the token.** AI Hub's chat gets its user's token from a Liferay DXP with the AI Hub cell, 2026.Q1 or later: AI Hub's own Liferay, or a customer DXP. On 2024.Q1 to 2025.Q4, the separate **AI Hub module** gives AI Hub's chat widget the token instead, and adds the widget to your pages.
- **The same people on both.** When the token comes from another Liferay, the approver is matched by email, so the same email must be the same person on both Liferays, as with single sign-on.
- **The server over HTTPS,** set up as in [For Services and People](http.html), Step 1, at an address AI Hub can reach.

## Step 1: Trust the Liferay That Issues the Tokens

Add to the server's settings:

| Setting | Value |
| --- | --- |
| `MCP_HTTP_TRUSTED_ISSUER_URL` | The Liferay that issues AI Hub's tokens, for example `https://aihub-dxp.example.com` |
| `MCP_HTTP_TRUSTED_ISSUER_CHECK` | `userinfo`: the server asks that Liferay, with the token, who it belongs to |
| `MCP_HTTP_TRUSTED_ISSUER_ALLOW` | Who may use the server: emails, `@domain` entries and Liferay user IDs, separated by commas, or `*` for every user of that Liferay |

A user with a valid token who isn't allowed gets an error. One server trusts one issuing Liferay: run one server per customer. The token is only ever sent back to the Liferay that issued it.

## Step 2: Turn On Approval Links

1. In your Liferay's *Control Panel* → *Security* → *OAuth 2 Administration*, add an application named `Liferay MCP approvals`, as the sign-in application in [For Services and People](http.html#step-3-let-people-sign-in-with-liferay-optional), but with:
    - **Callback URIs:** `https://mcp.example.com/approve/callback`
    - **Scopes:** the write scopes listed in the [client extension file](../assets/downloads/liferay-mcp-oauth.client-extension.yaml), since approved changes run with them.
1. Decide who may approve: create a regular role, for example **MCP Approver**, give it **View** and **Create Token** on this application's **Permissions**, and assign it to the people who use AI Hub.
1. Save the application's client secret in a file, and add to the server's settings:

    | Setting | Value |
    | --- | --- |
    | `MCP_HTTP_APPROVALS` | `true` |
    | `MCP_WRITE_MODE` | `confirm` |
    | `LIFERAY_APPROVAL_CLIENT_ID` | The application's client ID |
    | `LIFERAY_APPROVAL_CLIENT_SECRET_FILE` | The file holding its secret |

## Step 3: Add the Server in AI Hub

Ask your AI Hub administrator to add the server as an MCP server, at `https://mcp.example.com/mcp`, and to give it to your agent. AI Hub sends no API key: the user's token is its only credential.

Then, in the agent's chat, ask *"What Liferay server are you connected to?"* The answer names your Liferay and the user you're chatting as.

## Related Information

- [Writes, Previews, and Approvals](../using/writes.html)
- [Settings](../reference/settings.html)
- [Checking the Installation](checking.html)
