# CertiBuddy Blog — Content Repository

This repository is the **source of truth** for the blog. The CMS publishes to
it as Git commits, and the public site is built statically from it — the
database in the CMS is only a draft/index layer and can be rebuilt from here
at any time.

See the application repository's `docs/content-format.md` for the full
reference. The essentials:

## Directory structure

```
config/site.json          site configuration (name, locales, navigation, …)
posts/<entry-path>/       one directory per article
  entry.json              stable entry descriptor (see below)
  zh-CN.md                one Markdown file per locale
  en-US.md
pages/<entry-path>/       standalone pages, same layout
```

## `entry.json`

```json
{
  "id": "01JHELLO0000000000000000AA",
  "type": "post",
  "canonicalLocale": "zh-CN",
  "locales": ["zh-CN", "en-US"],
  "createdAt": "2026-09-24T00:00:00.000Z",
  "updatedAt": "2026-09-24T00:00:00.000Z"
}
```

`id` never changes — not on rename, not on re-slugging. `locales` must list
exactly the Markdown files that exist beside it.

## Markdown frontmatter

```markdown
---
title: "文章标题"
slug: "article-slug"
description: "可选，用于 SEO 与列表摘要。"
date: "2026-09-24"
status: "published"       # published | draft | scheduled
category: "cloudflare"    # 可选
tags: ["cloudflare"]      # 可选
cover: "/assets/cover.webp"  # 可选，站点根相对路径或绝对 URL
---

正文使用 GitHub-flavored Markdown。
```

Only `status: published` files are rendered. Unpublishing flips this field in
a commit; the file stays and its history is preserved.

## Locale naming

Files are named by canonical locale (`zh-CN`, `en-US`, `ja-JP`…). URLs are
`/<locale>/posts/<slug>/`. A missing localization simply means no page —
there is no automatic fallback.

## Media

Images are referenced as absolute URLs or site-root-relative paths
(`/assets/…`). Do not commit large media here unless it is part of an article;
the CMS stores uploads in this repository under the paths it manages and
serves them through the configured public media host.

## Site configuration

`config/site.json` — name, description, domain, `defaultLocale`,
`supportedLocales`, optional `navigation`, `comments` (public comment widget)
and `theme` (which installed theme renders the site). Everything is
validated at build time; an invalid value fails the build with a message
naming the field.

## Editing by hand

Hand edits are fine — external Git changes are a supported workflow. Push to
`main` and the site rebuilds automatically (this repository's push workflow
notifies the application repository). The CMS detects external changes and
never silently overwrites them; run the sync/rebuild from the admin to bring
its index up to date.
