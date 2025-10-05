---
title: 开源贡献第一步：如何为 MDN 提供中文翻译
date: 2024-03-14 15:46:56
tags:
  - Frontend
---
> **背景**：想尝试一下参与开源项目，从最简单的文档翻译开始是个不错的选择。
>
> 在此记录一下为 MDN 提供翻译文档的流程。

# 关于 MDN

> 官方文档：https://developer.mozilla.org/zh-CN/docs/Web

MDN指的是Mozilla 开发者网络（Mozilla Developer Network），是一个提供Web技术文档和资源的在线平台。它包含了关于HTML、CSS、JavaScript等Web技术的详尽文档、教程和示例代码，是许多开发者在学习和使用Web技术时的重要参考资源。

# 前置准备

-   了解 Git 的使用
-   了解[如何使用 Markdown 来撰写文档](https://developer.mozilla.org/zh-CN/docs/MDN/Writing_guidelines/Howto/Markdown_in_MDN)
-   中等英语水平

# 快速开始

> 一些有用的链接：
>
> -   原文档仓库：https://github.com/mdn/content
> -   翻译文档仓库：https://github.com/mdn/translated-content
> -   MDN Web 文档贡献指南：https://github.com/mdn/translated-content/blob/main/CONTRIBUTING.md#mdn-web-docs-contribution-guide
> -   针对简体中文（zh-CN）文档的翻译指南：https://github.com/mdn/translated-content/blob/main/docs/zh-cn/translation-guide.md
> -   简体中文 web 术语翻译对照表：https://github.com/mdn/translated-content/blob/main/docs/zh-cn/glossary.md

*此处仅考虑**创建新翻译**的场景，简单的错字修正/更新翻译请参考[为 MDN 翻译内容做出贡献](https://github.com/mdn/translated-content/blob/main/CONTRIBUTING.md#fix-a-typo)。

1.  **克隆仓库**

克隆`mdn/content`，用于对照文案以及文件路径

```
git clone https://github.com/mdn/content.git
```

fork 并克隆 `mdn/translated-content`，创建新的分支，用于提交pull request

```
git clone git@github.com:YOUR_GIT_REPOSITORY/translated-content.git
```

2.  **在对应路径创建md文档**

在 `mdn/content` 中找到源文件路径，并复制一份到 `mdn/translated-content`（比如， `files/en-us/web/css/_doublecolon_after/index.md` 对应的翻译文档路径应为 `files/zh-cn/web/css/_doublecolon_after/index.md` ）

3.  **更新文档前的元数据信息**

    1.  删除多余的标题属性（请参阅[翻译指南](https://github.com/mdn/translated-content/blob/main/docs/zh-cn/translation-guide.md)以了解应保留哪些属性）
    1.  翻译 `title`（必须）、`short-title`（可选）
    1.  `slug`（必须）：为与网页 URL 相关的元数据（URL path 部分的规则为：`/<locale>/docs/<slug>`），请与对应的英文文档保持一致
    1.  `l10n.sourceCommit`（可选）：为对应英文文档的最新提交的 SHA 值

最终在简体中文文档中呈现的元数据如下所示：

```
title: HTMLAnchorElement：hash 属性
slug: Web/API/HTMLAnchorElement/hash
l10n:
  sourceCommit: a3d9f61a8990ba7b53bda9748d1f26a9e9810b18
```

4.  **翻译文件**

[按照翻译指南](https://github.com/mdn/translated-content/blob/main/docs/zh-cn/translation-guide.md)翻译文件，需要注意代码块标题的更改、站内链接的翻译、符号和术语表等问题。

5.  **提交 pull request**

提交代码到远程仓库，并在fork仓库中创建一个`pull request`

6.  ***修改 pull request**

如果你（和我一样）首次提交犯了一些错误，reviewer会提changed request，在原分支修改后重新推送即可。

# 参考

[零起点的开源社区贡献指南 - 掘金](https://juejin.cn/post/6844903507892371470)