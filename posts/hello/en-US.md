---
title: "Building a Blog CMS on Cloudflare Workers"
slug: "hello"
description: "How a Git-native, static-first blog CMS fits together."
date: "2026-09-25"
status: "published"
category: "cloudflare"
tags:
  - cloudflare
---

# Building a Blog CMS on Cloudflare Workers

This is a sample post that exercises the build pipeline: Markdown rendering,
GFM tables, syntax highlighting, heading anchors and a table of contents.

## Why Git-native

Published content lives **only** in the content repository. D1 holds drafts,
indexes and state — delete it and the system rebuilds from Git.

> That boundary is the foundation: rendering the public site touches no
> database at all.

## A snippet

```ts
const revision = await content.getRevision();
```

## Summary

One publish, one commit. A conflict fails rather than overwrites.
