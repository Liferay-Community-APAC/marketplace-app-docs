---
title: Author Permissions
nav_order: 12
description: What a non-admin content author needs for each in-editor assistant action.
---

# Author Permissions
{: .no_toc }

Every in-editor assistant action runs in the browser **as the signed-in DXP user**, so each is gated by that user's own permissions — not by the connector's token scopes (those are for AI Hub's server-side search node). The token endpoint itself is role-agnostic: any signed-in, active, non-guest user gets a token (a guest, Basic auth or a request without the session CSRF token gets 403).

| Action | Works for a minimal author? | Needs |
|---|---|---|
| Writing tools (improve / fix / shorter / longer / tone) | ✅ | Nothing beyond being a signed-in author. |
| Generate (field value) | ✅ | Same. |
| Translate | ✅ | Same. |
| Suggest tags | ✅ (degraded) | Works; reusing the site's existing tags needs *Categorization → Categories → View*. |
| Suggest categories | ⚠️ | *Categorization → Categories → View* on the site. |
| Generate image | ⚠️ | *Documents and Media → Add Document* on the site. |

To give authors the full assistant, grant their role **Categorization → Categories → View** and **Documents and Media → Add Document** (plus *Access in Site Administration*) — ordinary content-author permissions, nothing portal-admin. A bare "add web content only" role has writing tools, Generate and Translate, but not Suggest categories or Generate image until those two grants are added.

The actions fail honestly without the permission: Generate image reports it couldn't save to Documents &amp; Media, and Suggest categories distinguishes "no categories" from "no permission to read them".
