---
title: Setting Up Liferay
parent: Installing
nav_order: 1
---

# Setting Up Liferay
{: .no_toc }

The server acts as one Liferay user, through an OAuth 2 application. This page creates both, and saves the application's credentials where the server reads them. Do it once per Liferay, as an administrator.

1. TOC
{:toc}

## Step 1: Create the Service User and Its Roles

1. **Turn off email verification for new users,** if it's on: *Control Panel* → *Instance Settings* → *User Authentication* → clear **Require strangers to verify their email address?**, and click **Save**. Otherwise the new user can wait on the *Verify Email Address* page, and every API call as it fails with 403.
1. **Create the user,** for example with the screen name `mcp-service` and the email `mcp-service@example.com`. Give it a password nobody uses, since OAuth doesn't need one, and make sure it isn't required to change it at its first sign-in (the password policy's **Change Required**), or every API call as it fails with 403.
1. **Create a site role,** for example **MCP Content Editor**, in *Control Panel* → *Users* → *Roles*, with these permissions. It lets the user view content, and add and edit what the write tools write, but not delete or change permissions:

    | Resource | Actions |
    | --- | --- |
    | Web Content | Add Article, Add Folder, View |
    | Web Content Article | Update, View |
    | Web Content Folder | Add Article, Add Subfolder, View |
    | Documents and Media | Add Document, View |
    | Document | Update, View |
    | Documents Folder | View |
    | Page | View |
    | Blogs / Blogs Entry | Add Entry / Update, View |
    | Categories / Category / Vocabulary | Add Category, Add Vocabulary / Add Category, Update, View / View |
    | Navigation Menus / Navigation Menu | Add Navigation Menu / Update, View |
    | Knowledge Base / Article / Folder | Add Article, Add Folder / Update, View / Add Article, View |
    | Wiki / Wiki Node / Wiki Page | Add Node / Add Page, View / Update, View |
    | Message Boards / Category / Message | Add Message, Reply to Message / Add Message, View / Update, View |
    | Form | View |

    Leave out the areas you don't want the AI assistant to write.
1. **Make the user a member of each site** the server should work in, and give it the site role there. If it should review content in a workflow, also give it **Site Content Reviewer** in those sites.
1. **Create a regular role,** for example **MCP OAuth Client**, with *Control Panel* → *Security* → *OAuth 2 Administration* → **OAuth 2 Application: Create Token**, and assign it to the user. Liferay only issues a token that acts as a user who has it.

{: .tip }
For signed-in page inspection, also create a second, view-only user that is only a site member, with no roles. Without it, the page inspection tools view pages as a guest.

## Step 2: Create the OAuth 2 Connection

Choose one way. Both create a **Headless Server** application that acts as the service user.

### With a Client Extension

Download [`liferay-mcp-oauth.client-extension.yaml`](../assets/downloads/liferay-mcp-oauth.client-extension.yaml), and check two fields:

- `userAccountScreenName`: the service user's screen name from Step 1.
- `scopes`: `Liferay.Headless.Delivery.everything` allows writes. Change it to `Liferay.Headless.Delivery.everything.read` to make the server read-only; Liferay enforces it. The file's comments list the rest.

Save it as `client-extension.yaml` in a folder of your Liferay Workspace's `client-extensions` folder, for example `client-extensions/liferay-mcp-oauth/`, and deploy it the way you deploy client extensions: `blade gw deploy` on Liferay Self-Hosted, or your build pipeline on Liferay PaaS and SaaS. Liferay creates the application, shown as **Liferay MCP server** in *Control Panel* → *Security* → *OAuth 2 Administration*, and publishes its credentials as routes.

### In the Control Panel

In *Control Panel* → *Security* → *OAuth 2 Administration*, add an application:

| Field | Value |
| --- | --- |
| Name | `Liferay MCP server` |
| Client Profile | Headless Server |
| Allowed Authorization Types | Client Credentials only |
| Client Credentials User | The service user from Step 1 |

Save it, then on its **Scopes** tab select the scopes listed in the [client extension file](../assets/downloads/liferay-mcp-oauth.client-extension.yaml), and save again.

## Step 3: Save the Credentials for the Server

The server reads the application's credentials from a **routes folder**, one value per file.

- **Liferay Self-Hosted, with the client extension:** Liferay writes them to `$LIFERAY_HOME/routes/default/liferay-mcp-oauth/`, and the instance's own to `$LIFERAY_HOME/routes/default/dxp/`. Copy both folders to where the server runs.
- **Liferay PaaS, with the server as a microservice:** the platform gives it the routes itself, as `LIFERAY_ROUTES_CLIENT_EXTENSION` and `LIFERAY_ROUTES_DXP`.
- **Otherwise,** including Liferay SaaS and an application made in the Control Panel: create a folder with these four files, copying the values from the application's page in *OAuth 2 Administration*:

    | File | Content |
    | --- | --- |
    | `liferay-mcp-oauth.oauth2.headless.server.client.id` | The **Client ID** |
    | `liferay-mcp-oauth.oauth2.headless.server.client.secret` | The **Client Secret** |
    | `liferay-mcp-oauth.oauth2.headless.server.scopes` | The application's scopes, separated by spaces |
    | `liferay-mcp-oauth.oauth2.token.uri` | `https://<your-liferay>/o/oauth2/token` |

    Copy the scopes file too: without it, the server treats the connection as read-only.

Keep the folder readable only by the server, like any secret. The container runs as user ID 1000:

```bash
sudo chown -R 1000:1000 /srv/liferay-mcp/routes && sudo chmod -R go-rwx /srv/liferay-mcp/routes
```

{: .note }
A password connection, with `LIFERAY_USERNAME` and `LIFERAY_PASSWORD` instead of the routes, also works, and is the only one on 7.4 update releases before U92. It puts a password in the server's settings, so prefer the routes where you can.

## Related Information

- [For One Person](stdio.html)
- [For Services and People](http.html)
- [For Liferay AI Hub](ai-hub.html)
