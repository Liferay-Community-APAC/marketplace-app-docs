---
title: Settings
parent: Reference
nav_order: 2
---

# Settings
{: .no_toc }

The server is set up with environment variables. It checks them when it starts, and refuses to start with a clear message when a combination is unsafe. Secrets are always files: the routes folders and the `*_FILE` settings.

| Area | Settings |
| --- | --- |
| Liferay | `LIFERAY_URL` (required), `LIFERAY_DEFAULT_SITE`, `LIFERAY_TIMEOUT_MS`, `MCP_REQUIRE_DXP` |
| Connection | `LIFERAY_ROUTES_CLIENT_EXTENSION` and `LIFERAY_ROUTES_DXP`, or `LIFERAY_USERNAME` and `LIFERAY_PASSWORD` |
| Administrator connection | `LIFERAY_ROUTES_BUILDER_EXTENSION`, `MCP_SITE_TEMPLATES`, `MCP_SITE_TEMPLATE_CONTRACTS`, `MCP_CONTENT_EDITOR_ROLE` |
| Writes | `MCP_WRITE_MODE` (`off` or `confirm`), `MCP_ADMIN_WRITES`, `MCP_DESTRUCTIVE_WRITES`, `MCP_BATCH_LIMIT` (default 50), `MCP_DISABLED_OPERATIONS` |
| Audit and files | `AUDIT_LOG_PATH`, `ARTIFACTS_DIR`, both under `/data` in the images |
| Page inspection | `BROWSER_SIGN_IN`, `LIFERAY_BROWSER_USERNAME`, `LIFERAY_BROWSER_PASSWORD_FILE` |
| HTTPS | `MCP_HTTP_HOST`, `MCP_HTTP_PORT` (default 3333), `MCP_PUBLIC_URL`, `MCP_TRUST_PROXY`, `MCP_HTTP_API_KEYS_FILE`, `MCP_HTTP_WRITES` |
| Sign-in | `LIFERAY_SIGN_IN_CLIENT_ID`, `LIFERAY_SIGN_IN_CLIENT_SECRET_FILE`, `MCP_OAUTH_REDIRECT_ALLOWLIST`, `MCP_OAUTH_CLIENT_METADATA` |
| Liferay AI Hub | `MCP_HTTP_TRUSTED_ISSUER_URL`, `MCP_HTTP_TRUSTED_ISSUER_CHECK`, `MCP_HTTP_TRUSTED_ISSUER_ALLOW`, `MCP_HTTP_TRUSTED_ISSUER_CLIENT_ID`, `MCP_HTTP_TRUSTED_ISSUER_CLIENT_SECRET_FILE` |
| Approvals | `MCP_HTTP_APPROVALS`, `LIFERAY_APPROVAL_CLIENT_ID`, `LIFERAY_APPROVAL_CLIENT_SECRET_FILE` |
| Logging | `LOG_LEVEL`. Logs go to standard error. |

## Related Information

- [Installing](../installing/)
