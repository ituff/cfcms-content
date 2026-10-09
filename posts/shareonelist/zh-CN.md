---
title: "ShareOneList：一个应用，串起三种 Microsoft 365 账户"
slug: "shareonelist-microsoft-365"
date: "2026-10-09"
status: "published"
category: "猫叔手作"
tags:
  - "shareonelist"
  - "sharepoint"
  - "onedrive"
  - "ai"
---

在企业协作场景里，Microsoft 365 是绕不开的生产力套件。但它有一个让 IT 同事和重度用户都头疼的“老毛病”——

- **账户体系分裂**：国际版（Global）、世纪互联（China 21Vianet）、个人版（Microsoft Account）三套身份系统互不通用，下载策略、应用入口、API 域名都各自为政；
- **平台割裂**：Windows 端好用，macOS 端要么缺功能、要么需要绕第三方；
- **Teams 会议录像下载受限**：当组织者或租户策略关闭了“允许下载”，在 Teams 网页里只能播放、不能下载，重要回看经常被卡在云端。

[ituff/ShareOneList](https://github.com/ituff/ShareOneList) 是一个开源、跨平台的 Microsoft 365 文件管理工具。它把上面三个问题一起解决了。本文带你速览它的核心能力。

**一、三种账户，一个客户端**

ShareOneList 把“账户类型”作为一等公民来对待，登录时它会按云环境自动路由到对应的 Microsoft Graph 端点。每种账户类型在侧边栏里只显示其拥有的服务入口——OneDrive、SharePoint、Teams 录像——既不会把不支持的入口暴露给你，也不需要你在两个客户端之间来回切换。

![](https://img.maowoo.top/assets/posts/01M4F4RH7N5YR9QPEWWF513W9Q/adf9774de0bb2d09a5b8b73baf6a61bd97521ac5f4ae0dc60c226fabf1df4e00.png)

*图 1　文件视图：国际版与世纪互联版账户并行管理*

更进一步，它支持**多账户并存**与**自定义别名/图标**：同一个云环境可同时登录多个账号（凭据按 homeAccountId 隔离），登录时按 driveType 修正个人/组织的识别，避免组织版被误认为个人版。设置页提供 12 个内置图标与别名，区分工作账号、私人账号、海外账号一目了然。

**二、Windows 与 macOS，同一套代码**

项目在 v2.0.0 完成了从 WinUI 3 到 **Tauri 2 + Rust + React** 的彻底重写，从此 Windows（x64 / arm64）和 macOS（Apple Silicon）由同一份代码构建：

| \*\*平台\*\* | \*\*安装包\*\* |
| --- | --- |
| Windows x64 | .exe 安装版 / .msi / 绿色 .zip |
| Windows arm64 | .exe 安装版 / .msi / 绿色 .zip |
| macOS Apple Silicon | .dmg（自带 Gatekeeper 修复脚本） |

应用内置中英文 UI、深色/浅色主题（跟随系统或手动切换）、文件列表排序、面包屑折叠、缩略图与多视图布局。下面是文件浏览的默认效果：

![](https://img.maowoo.top/assets/posts/01M4F4RH7N5YR9QPEWWF513W9Q/63c2494988fe888c227d270d30695ecfcec51ae7ab6472564004571963c5e40e.png)

*图 2　资源管理器式 UI，文件夹 / 文档 / 视频统一呈现*

下载安装：直接前往 [Releases](https://github.com/ituff/ShareOneList/releases) 即可。macOS 首次打开若提示“应用已损坏”，执行 xattr -cr /Applications/ShareOneList.app 即可，dmg 内也已附一键修复脚本。

**三、流式提取，下载受限的 Teams 会议录像也能存**

这才是 ShareOneList 的“杀手锏”。

**场景**：Teams 会议录像默认保存在组织者的 OneDrive / SharePoint 中。当组织者或租户策略设置了“阻止下载”时，你在网页里只能播放、看不到“下载”按钮。

**ShareOneList 的做法**：在应用内置的播放器里，视频其实是以 **DASH 分段流式传输** 的。ShareOneList 用自己的 WebView 拦截嵌入播放器的 DASH manifest 与 x-spopactoken，下载并解密 AES-128-CBC 分段，重新封装为单一 MP4 文件，最后通过本地 loopback receiver 写盘——整个过程**不绕过任何权限**，只是把“你能播放的内容”完整保存下来。

**操作很简单**：

1. 左侧菜单「文件」→ 选择账户 →「Teams 录像」，自动聚合你有权访问的全部录像（包括他人分享给你的）；
2. 单击录像打开内置预览，先播放几秒让捕获就绪；
3. 点击播放器右上角的**摄像机图标**，选择保存位置即可。

流式提取按钮在捕获就绪前会显示为灰色，悬停提示“请先在预览中播放视频几秒”；如果录像本身有 DRM 保护，应用会明确提示无法保存。若想提速，到「设置 → Teams 录像下载」里把分段并发数从默认 4 调到 8–16（过高可能触发服务限流）。

请仅保存你有权播放的内容，并遵守所在组织的合规政策与版权要求。

**四、不止“能下载”：更完整的生产力体验**

围绕 OneDrive / SharePoint 的日常高频动作，ShareOneList 也都做了：

- **断点续传**：下载中断后重启应用可继续；
- **批量任务**：一次发起的多文件下载归并为一条任务，统一显示进度与下载速度；
- **文件预览**：图片、视频、Markdown、Office 文档（Office Online）直接预览；
- **书签**：常用文件夹 / 文件一键收藏；
- **拖拽上传**：本地到云端，反向亦可；
- **AI 助手（v2.2.0-beta 新增）**：接入任意 OpenAI 兼容模型（OpenAI / Azure OpenAI / DeepSeek / 阿里百炼 / Moonshot / 智谱 / Ollama 预设），可基于你的云端文件回答问题，回答附带可点击的引用卡片。

![](https://img.maowoo.top/assets/posts/01M4F4RH7N5YR9QPEWWF513W9Q/40b4f11a8a5684cfa49ea5bb3b1c56afe8b5a218ede8932da8e10aaac2739926.png)

*图 3　下载/上传任务统一管理，进度与速度一目了然*

**五、写在最后**

ShareOneList 不是一个花哨的“AI Wrapper”——它把“在三种 Microsoft 365 账户下，统一、跨平台、可控地访问你的云端文件”这件最朴素的事，做得相当扎实。尤其是**流式提取下载受限的 Teams 录像**这个能力，在很多企业 IT 场景里几乎是刚需。

如果你也在为账户体系割裂、Teams 录像下载受限、跨平台客户端缺失而困扰，不妨试试看。

- **项目地址**：https://github.com/ituff/ShareOneList
- **下载安装**：https://github.com/ituff/ShareOneList/releases

**欢迎在 GitHub 上 Star 支持一下，这是开源作者继续迭代的最大动力。**
