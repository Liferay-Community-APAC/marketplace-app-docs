---
title: Inspecting Pages
parent: Using the Server
nav_order: 3
---

# Inspecting Pages
{: .no_toc }

With the `1.1.0-full` image, the server opens your pages in a headless browser:

- `inspect_page` reports a page's title and description, its heading outline and heading issues, its links, images without a description (alt text), console errors and failed requests, and can check that its internal links work.
- `take_screenshot` takes a screenshot, of the visible part or the whole page, at a width you choose.

Ask, for example, *"Inspect the home page of my-site, and show me a screenshot at phone width."*

## What It Can Open

- **Only your Liferay.** The browser opens only pages at `LIFERAY_URL`, and blocks requests a page makes to other sites.
- **Never sign-in, sign-out or action addresses.** It only views pages.
- **As a guest,** unless `BROWSER_SIGN_IN=true`, with a view-only user (`LIFERAY_BROWSER_USERNAME` and a password file, `LIFERAY_BROWSER_PASSWORD_FILE`). Each result says whether the page was viewed signed in. Over HTTPS, pages are always viewed as a guest.

## Related Information

- [The Images](../installing/#the-images)
- [Settings](../reference/settings.html)
