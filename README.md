# Marketplace App docs

User guides for Liferay Community APAC Marketplace Apps that don't need a repository of their own, published at
`https://liferay-community-apac.github.io/marketplace-app-docs/<app>/`. Each app has its own folder in `docs/`,
with its own sidebar, breadcrumbs and previous and next links. All apps share the main site's header, footer,
styles and search, so readers move between the site and the guides as if they were one site.

An app that has its own code repository can keep its docs there instead, started from the
[app docs template](https://github.com/Liferay-Community-APAC/app-docs-template), like
[Liferay Forums](https://github.com/Liferay-Community-APAC/liferay-forums).

## What's here

| Path | What it is |
| --- | --- |
| `docs/<app>/` | One folder per app. Its `index.md` is the app's home page. |
| `APP_TEMPLATE/` | A starting point for a new app's folder. |
| `docs/_layouts/default.html` | The page layout. The sidebar and breadcrumbs cover the current app only. |
| `docs/index.md` | Forwards `/marketplace-app-docs/` to the main site's Marketplace Apps page, which lists every app. |
| `.github/workflows/pages.yml` | Publishes the docs with the shared workflow whenever `docs/` changes on `main`. |

## Add an app

1. **Copy `APP_TEMPLATE/` to `docs/<app>/`**, where `<app>` is the app's name in lowercase with dashes, for
   example `docs/event-registrations/`. The folder name becomes the guide's address.
2. **Edit `docs/<app>/index.md`**: the app's name (`title`), a one-sentence `description`, the `footer_note`
   (its licence line, or remove it), and the overview.
3. **Write the guide in Markdown** in the app's folder. Each page starts with front matter:

    ```yaml
    ---
    title: Installing        # the page's name in the sidebar
    parent: Guide            # the section it belongs to (leave out for a section or top page)
    nav_order: 1             # its position, lower first
    ---
    ```

    Pages can use a table of contents (`1. TOC` then `{:toc}` on the next line), callouts
    (`{: .note }`, `{: .tip }`, `{: .important }` or `{: .warning }` on the line before a paragraph),
    and screenshots (`{: .screenshot }` after an image). Put images in the app's folder. Code blocks get a
    **Copy** button.
4. **Add the app to the main site**: add an entry to `data/apps.json` in
   [liferay-community-apac.github.io](https://github.com/Liferay-Community-APAC/liferay-community-apac.github.io)
   with:

    ```json
    "docs": "marketplace-app-docs/<app>/",
    "search": "marketplace-app-docs/pagefind/",
    "sitemap": "marketplace-app-docs/sitemap.xml"
    ```

    That adds the app to the homepages and the Marketplace Apps pages, includes its guide in the site search, and
    tells search engines about it. Every app in this repository shares the one search index and sitemap; the main
    site uses each only once.

## Preview locally

The docs load the header, footer and styles from the main site. To preview them on their own,
set `main_site: https://liferay-community-apac.github.io` in `docs/_config.yml` (don't commit it), then:

```bash
cd docs && jekyll serve --baseurl /marketplace-app-docs
```

Search only works on the published site, where the search index is built.

## Publishing

In **Settings → Pages**, set **Source** to **GitHub Actions**. The repository must be public.

The repository's name is the guides' address, `/<repository-name>/`, on the same host as the main site. A
repository whose name matches one of the main site's own folders, such as `marketplace-apps`, `tutorials`,
`community`, `resources`, `get-help`, `ja`, `zh` or `id`, takes that address over and hides the main site's page
there.
