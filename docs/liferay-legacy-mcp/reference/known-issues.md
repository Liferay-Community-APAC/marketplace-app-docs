---
title: Known Issues
parent: Reference
nav_order: 4
---

# Known Issues

Known limitations in Liferay Legacy MCP 1.2.0.

## Drafts Are Only for New Articles

A change to a published article is published, or submitted for review, directly: Liferay's draft API ignores the ID of an existing article on these releases. Only new articles can be saved as drafts.

## An Article Waiting for Review Can't Be Changed

While an article has a version waiting for review, Liferay can't save it again. The article tools refuse first, and point to `list_review_tasks`.

## A Site's Workflow Can't Be Turned Off by the API

To turn off a site's review workflow for web content, use *Site Settings* → *Workflow*. No API on these releases removes it.

## The Administrator Tools Aren't Available Over HTTPS

`create_site`, `create_object`, the design tools, the administration API areas and JSON web services are available only to a person using the server on their own computer.

## People Signed In Over HTTPS Change Things by Approval Link

People who sign in with Liferay over HTTPS can read. To change something, they approve each change on a page of the server, from 1.5.0 with approval links on ([Step 4](../installing/http.html#step-4-let-signed-in-people-make-changes-optional)). AI assistants can't yet ask them in the chat instead.

## A Restart Drops Sign-Ins

Sign-in sessions are kept in memory. After a restart, people sign in again.

## Related Information

- [Reporting a Bug](reporting-a-bug.html)
- [Compatibility](compatibility.html)
