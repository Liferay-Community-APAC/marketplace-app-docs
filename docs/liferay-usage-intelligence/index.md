---
title: Liferay Usage Intelligence
nav_order: 1
footer_note: A Liferay Community APAC app for experimental use, not supported by Liferay.
description: Liferay Usage Intelligence scans Liferay DXP 2024.Q1 to 2026.Q1 for broken links, unused content, risky accounts and upgrade blockers, with read-only access.
---

# Liferay Usage Intelligence
{: .no_toc }

A read-only health and upgrade-readiness dashboard that runs inside Liferay DXP. It scans your sites through Liferay's headless APIs, with the signed-in administrator's own permissions, and shows what needs attention: broken links, unused and stale content, dormant accounts, and what will break when you upgrade.

{: .warning }
**A community app, for experimental use.** Liferay Usage Intelligence is shared by Liferay Community APAC. It isn't a Liferay product: Liferay doesn't support it, and it comes as is, with no warranty and no support commitment. It only reads, but try it on a test or development copy of your Liferay first.

<div class="doc-cards">
  <a class="card doc" href="installing.html"><strong>Installing</strong><span>Deploy the zip, or add it to your Liferay image with Docker.</span></a>
  <a class="card doc" href="using.html"><strong>Using the Dashboard</strong><span>Running a scan, Site Health, Upgrade Readiness and the reports.</span></a>
  <a class="card doc" href="security.html"><strong>Security</strong><span>What the app can read, where data goes, and how to remove it.</span></a>
</div>

## Highlights

- **Nothing to configure.** Deploying the zip creates a hidden page that only administrators can open, at `/web/usage-intelligence/dashboard`.
- **Read-only, enforced by Liferay.** The app's OAuth 2 application has read scopes only, so Liferay rejects any write it tries, whatever the user's own permissions.
- **Site Health.** Broken page links, images, videos and PDFs in pages, web content, blogs, knowledge base articles, message boards, wiki pages and navigation menus, each confirmed with Liferay before it's reported, with a downloadable HTML report.
- **Upgrade Readiness.** Liferay's breaking changes between your release and the one you plan to upgrade to, checked against your own data, plus deprecated and maintenance-mode features that are in use. Each links to Liferay's own documentation.
- **Content and access hygiene.** Unused, stale and duplicate content, empty folders and vocabularies, unused tags and categories, large files, dormant and never-used accounts, stuck approvals and unused forms.
- **Recommendations with sources.** Every finding says how to fix it and links to the matching Liferay Learn article.

## Supported Versions

| Liferay Usage Intelligence | Liferay DXP |
| --- | --- |
| 1.3.2 | 2024.Q1 to 2026.Q1, tested on the latest update of each quarter. |

## Downloads

| Version | Download | SHA-256 |
| --- | --- | --- |
| 1.3.2 | [`liferay-usage-intelligence-1.3.2.zip`](assets/downloads/liferay-usage-intelligence-1.3.2.zip) | `cf5ee6216906cf80949e09959deda4d7093a8aa9e06b683c3b264e76c3e54b7e` |

Also available as a container image: `ghcr.io/liferay-community-apac/liferay-usage-intelligence:1.3.2` (see [Installing with Docker](installing.html#installing-with-docker)).

## Next Steps

Start with [Installing](installing.html).
