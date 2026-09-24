---
title: "用 Cloudflare Workers 搭一个博客 CMS"
slug: "hello"
description: "Git-native、static-first 的博客 CMS 是怎么工作的。"
date: "2026-09-24"
status: "published"
category: "cloudflare"
tags:
  - cloudflare
  - astro
---

# 用 Cloudflare Workers 搭一个博客 CMS

这是一篇示例文章，用来验证构建流程。它同时演示了几件事：

- Markdown 渲染（含 GFM 表格、任务列表）
- 语法高亮
- 标题锚点与目录
- 中英双语路由

## 为什么是 Git-native

已发布的内容**只**存在于内容仓库里。D1 保存的是草稿、索引和状态，删掉它之后系统仍然能从 Git 重建：

| 存在哪里 | 存什么 |
| --- | --- |
| GitHub | 已发布的内容、媒体、站点配置 |
| D1 | 草稿、索引、发布任务、审计日志 |

> 这条边界是整个设计的地基：渲染公开站点时不该碰任何数据库。

## 一段代码

```ts
export async function publish(input: PublishInput): Promise<PublishResult> {
  const currentRevision = await content.getRevision();

  if (input.expectedRevision !== undefined && input.expectedRevision !== currentRevision) {
    throw errors.conflict();
  }

  return content.writeFiles(files, message);
}
```

## 待办

- [x] 领域模型与 D1
- [x] GitHub 集成
- [x] CMS API
- [ ] 主题市场

## 小结

发布是**一次**提交，冲突会**失败**而不是覆盖。剩下的都从这两条推导出来。
