---
title: Tools
parent: Reference
nav_order: 1
---

# Tools
{: .no_toc }

The server offers 37 tools. `get_server_info` lists them with whether your Liferay supports each one: a tool whose Liferay API is missing on your release shows as unavailable, with the reason.

1. TOC
{:toc}

## Server

| Tool | What It Does |
| --- | --- |
| `get_server_info` | The Liferay release, the connection, the write mode, and each tool's availability. |

## Content

| Tool | What It Does |
| --- | --- |
| `list_sites` | The sites the service user can see. |
| `search_content` | Searches web content, with keywords, date ranges, sorting and paging. |
| `get_content` | One article, with its structure's fields and its status. |
| `list_pages` | A site's pages, with their full addresses. |
| `list_documents` | Documents, with their download addresses. |
| `create_article`, `update_article`, `publish_article` | Create an article or a draft, change one, and publish a draft. |
| `translate_article` | Adds a language to an article, leaving the others as they are. |
| `upload_document` | Adds a document. |
| `export_content` | Exports a site's articles as JSON or CSV. |
| `list_review_tasks`, `review_task` | What's waiting for review, and approving, rejecting or resubmitting it. |
| `list_blog_posts`, `create_blog_post` | Blogs. |
| `list_taxonomy`, `create_category`, `tag_content` | Vocabularies, categories and tags. |
| `list_navigation_menus`, `add_navigation_menu_item` | Navigation menus. |
| `create_knowledge_base_article` | Knowledge base articles. |
| `list_users` | Users. |

## Any API

| Tool | What It Does |
| --- | --- |
| `describe_api`, `read_api`, `write_api`, `delete_api` | Every Headless REST API in 28 areas: describe an area's operations, then call one. |
| `describe_jsonws`, `read_jsonws`, `write_jsonws` | Every JSON web service method, for what the Headless APIs don't cover. |

The assistant uses a dedicated tool first, then the Headless APIs, then JSON web services only when Headless has nothing for the task.

## Pages

| Tool | What It Does |
| --- | --- |
| `inspect_page` | A page's outline, links, missing image descriptions and errors. `1.1.0-full` image only. |
| `take_screenshot` | A screenshot of a page. `1.1.0-full` image only. |

## Site Building (Administrator Connection)

| Tool | What It Does |
| --- | --- |
| `create_site`, `get_site_template` | Creates a site from a site template. |
| `create_object` | Defines and publishes a Liferay Object. |
| `create_fragments`, `create_style_book` | Page fragments and a style book. |

These need a second, administrator connection, and are available only to a person using the server on their own computer, never over HTTPS.

## Liferay AI Hub

| Tool | What It Does |
| --- | --- |
| `check_approval` | Whether an approval link was approved, rejected or expired. |

## Related Information

- [Writes, Previews, and Approvals](../using/writes.html)
- [What to Ask](../using/what-to-ask.html)
