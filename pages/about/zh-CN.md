---
title: "关于"
slug: "about"
description: "这个站点是什么。"
date: "2026-09-24"
status: "published"
---

# 关于

这是一个 Git-native、static-first 的博客 CMS 的示例站点。

公开站点在构建期读取内容仓库并生成静态文件，运行时不查询任何数据库 ——
没有 Worker、没有 D1，也没有 CMS API 依赖。

内容仓库保存已发布的内容，D1 只保存草稿、索引和状态。
删掉 D1，站点仍然能从 Git 重建。
