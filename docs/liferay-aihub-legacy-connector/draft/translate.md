---
title: Translate
nav_exclude: true
search_exclude: true
description: Translate a Web Content article's localizable fields into the site's other languages with the in-editor assistant.
---

# Translate
{: .no_toc }

1. TOC
{:toc}

On the Title's **✨ AI** menu, **Translate…** lets you pick the site's other languages and translates the article's localizable fields — Title, Description and rich-text body — into each, leaving the default language untouched.

## How it works

The native AI Hub translate agent (`L_TRANSLATE_CONTENT`) can't be used by a stateless connector: it reads the content from the editor session on AI Hub's side, so there is nothing for a plain call to send it. The connector translates with the portable field-generation agent instead:

- **One call per field per language** — the instruction is "translate this field's value into the language, preserving HTML", with the field's current value passed in. The agent fills one field per call, so translate iterates field by field.
- **Write-back uses Liferay's own localization** — the Title through the AUI `InputLocalized` widget's `updateInputLanguage(value, languageId)`, and each rich-text field by switching the article's language (which makes Liferay materialize and swap that language's editor) and then setting the editor's data.

So the *feature* is delivered on 2024.Q1–2025.Q4; only the native *agent* is the dead end (see [Feature Gaps](feature-gaps.html)).

## Permissions

Translate needs only a signed-in author (token + agent) — no extra content permissions. See [Author Permissions](permissions.html).
