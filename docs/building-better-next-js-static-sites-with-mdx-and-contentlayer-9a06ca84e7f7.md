# 用 MDX 和 Contentlayer 构建更好的 Next.js 静态站点

> 原文：<https://betterprogramming.pub/building-better-next-js-static-sites-with-mdx-and-contentlayer-9a06ca84e7f7>

## 使用 MDX 和 Contentlayer 发布静态内容变得如此轻松。我会告诉你怎么做

![](img/c8a11c385801ab22dafa9c10f9504def.png)

# TL；速度三角形定位法(dead reckoning)

*   💨Contentlayer 减少了在我的静态网站上发布内容的很多摩擦。
*   😍带有 remark 和 rehype 插件的 MDX 非常方便和强大。
*   🍵Next.js 与 Contentlayer 和 MDX 结合得非常好

我完全接受了我的网站的静态站点生成(SSG)，以优化网站速度和未来的扩展。

最近我一直在研究如何减少在我的网站上写一篇新文章的工作量。在我的 [Next.js](https://nextjs.org/) 项目设置中有许多接触点，以便:

*   发表一篇新文章
*   估计阅读时间
*   更新[文章列表](https://dawchihliou.github.io/articles)
*   创建新的 RSS 源
*   更新网站地图

# 出了什么问题？

我又分析了一下，发现摩擦出在我的 MDX 使用上。我的文件结构是这样的:

```
my-blog
├── public
├── data
│   └── blogs.json
├── components
│   └── Blog.tsx
└── pages
    ├── blogs
    │   ├── blog-one.mdx
    │   ├── blog-two.mdx
    │   └── blog-three.mdx
    └── index.tsx
```

是 Next.js 推荐的[标准设置](https://nextjs.org/docs/advanced-features/using-mdx)，我当时是用 [@mdx-js/loader](https://github.com/mdx-js/mdx/tree/main/packages/loader) 和 [@next/mdx](https://www.npmjs.com/package/@next/mdx) 把 mdx 转换成页面。

以`pages/blogs/blog-one.mdx`为例，内容是这样的:

`pages/blogs/blog-one.mdx`

`blog-one.mdx`命名——导出了一个元数据。它被负责布局和呈现元数据的默认组件选中。

`<Blog />`组件看起来像这样:

components/Blog.tsx

我将 MDX 文件视为页面。

因为每个 MDX 文件中的`meta`数据被捕获在页面中，所以我复制了所有的元数据，并在`data/blogs.json`中聚合它们。我用它来维护我的网站上的文章列表、RSS 提要和用于 SEO 的站点地图。

> 如果我能将 MDX 文件视为数据，并根据数据生成页面，那就更好了。

这样，我可以同时将 MDX 文件用作数据点和页面内容。理想情况下，发表一篇新文章会更加顺畅。

我偶然看到[李·罗宾逊的网站](https://leerob.io/)，发现他正在使用一个名为 [Contentlayer](https://github.com/contentlayerdev/contentlayer) 的 alpha 库来解决这个问题。

# 什么是内容层

[Contentlayer](https://github.com/contentlayerdev/contentlayer) 是一个处于早期阶段的库，它将内容转化为数据。它大致是这样工作的:

*   它接受 YAML、JSON、MDX 或 Markdown 中的 headless CMS 或本地内容作为源代码。
*   它将内容转换成 JSON 中的 TypeScript 类型和数据文件，包括原始内容、元数据和我们指定的任何派生数据。
*   它聚合 JSON 中的所有数据，并将它们导出为 [ESM](https://nodejs.org/api/esm.html#modules-ecmascript-modules) 。

对于我的用例，我可以使用 Contentlayer 生成的聚合数据来取代我以前的手动流程:

*   我使用生成的数据来构建文章的新页面。
*   我使用生成的数据来呈现文章列表。
*   我使用生成的数据创建一个新的 RSS 提要。
*   我使用新的文件结构来生成新的站点地图。
*   全自动！

Contentlayer 提供了与 Next.js 的简单集成。我将在接下来的部分中向您展示如何集成。

# 使用 MDX 作为数据

我们先来探讨一下如何使用 MDX 作为数据点。

MDX 为 YAML frontmatter 提供了自定义解析器的支持。您可以这样表达元数据:

```
---
title: 'Blog One🚀'
publishedAt: 'February 4, 2022'
description: 'Learn how to build a Next.js blog with MDX and Contentlayer!'
cover: '/optimized/articles/blog-one/hero.webp'
---Hey There👋Welcome to Blog One✨ Let's learn together!
```

您可以看到，YAML 语法中的元数据在`---`块中，内容主体遵循 MDX 语法。与将 MDX 文件视为页面的[旧设置](https://dawchihliou.github.io/#what-was-the-problem)相比，新的 MDX 文件仅包含元数据和内容。

我们需要做的下一件事是生成博客页面，该页面使用来自`<Blog />`组件的布局呈现元数据和内容。

# 在 Next.js 中集成 Contentlayer

现在我们已经更新了 MDX 文件，只包含数据和内容，让我们把它们移到`data`目录中。

新的文件结构如下所示:

```
my-blog
├── public
├── components
│   └── Blog.tsx
├── pages
│   ├── blogs
│   │   └── [slug].tsx
│   └── index.tsx
└── data
    └──blogs
       ├── blog-one.mdx
       ├── blog-two.mdx
       └── blog-three.mdx
```

注意，我们用一个[动态路由](https://nextjs.org/docs/routing/dynamic-routes) `[slug].tsx`替换了`pages/blogs`目录中的 MDX 文件。稍后我们将使用这个页面静态生成博客页面。

# 配置内容层

Contentlayer 提供了与 Next.js 的无缝集成。

要安装依赖项，请执行以下操作:

```
yarn add contentlayer next-contentlayer
```

Contentlayer 从`contentlayer.config.ts`读取配置。让我们创建一个。

```
touch contentlayer.config.ts
```

在`contentlayer.config.ts`中，我们需要添加指令来告诉 Contentlayer 如何解析:

*   `name`:名称空间
*   `filePathPattern`:输入文件
*   `bodyType`:解析的内容体类型
*   `fields`:元数据字段
*   `computedFields`:派生元数据字段

内容层. config.ts

在`computedFields`中，我们可以像`readingTime`一样从内容体中计算数据🤩。我用`[reading-time](https://github.com/ngryman/reading-time)`来计算基于字数的阅读时间。`slug`字段用于稍后在`[slug].tsx`页面中生成动态路线。

在幕后，Contentlayer 使用`[mdx-bundler](https://github.com/kentcdodds/mdx-bundler)`解析 MDX 和 YAML frontmatter，并提取内容和数据。如果你对它背后的魔力感兴趣，你可以阅读更多关于`[gray-matter](https://github.com/jonschlinkert/gray-matter)`和`[remark-mdx-frontmatter](https://github.com/remcohaszing/remark-mdx-frontmatter)`的内容。这些是`mdx-bundler`内部使用的库。

在配置结束时，`makeSource`将在`data`目录下寻找与`blogs/*.mdx`模式匹配的文件，并在项目根目录下的`.contentlayer`目录中生成博客数据。

最后，用`next-contentlayer`包装 Next.js 配置，以便与 Next.js 的实时重新加载和构建过程集成。

next.config.js

# 使用内容层数据生成静态站点

我们准备使用 Contentlayer 生成的数据并构建静态页面🤩

我们需要做的就是使用来自`.contentlayer/data`的`allBlogs`来构建与`[getStaticPaths](https://nextjs.org/docs/api-reference/data-fetching/get-static-paths)`的动态路由，并使用`[getStaticProps](https://nextjs.org/docs/api-reference/data-fetching/get-static-props)`将博客数据传递给`[slug].tsx`页面。

页面/博客/[slug]。tsx

项目完成后，你会在✨的`/blogs/blog-one`、`/blogs/blog-two`和`/blogs/blog-three`看到博客

# 额外奖励:备注和重新键入插件

通过利用`contentlayer.config.ts`中的 remark 和 rehype 插件，我们可以用 MDX 做更多的事情。

*   [备注](https://github.com/remarkjs/remark)是一个改变 markdown 的令人敬畏的插件生态系统。
*   re type 是另一个改变 HTML 的强大插件生态系统。

它们是两个独立的生态系统，但是我们可以将 remark 转换为 rehype 并生成 HTML 标记。转换如下所示:

```
MDX ----> remark AST ------> rehype AST --------> HTML
    parse            convert            stringify
```

Contentlayer 负责流程。我们需要做的就是添加插件来为转换提供指导。我正在使用以下插件:

*   `[remark-gfm](https://github.com/remarkjs/remark-gfm)`支持 [GitHub 风味降价](https://github.github.com/gfm/)。
*   `[rehype-slug](https://github.com/rehypejs/rehype-slug)`和`[rehype-autolink-headings](https://github.com/rehypejs/rehype-autolink-headings)`渲染标题链接。
*   `[rehype-prism-plus](https://github.com/timlrx/rehype-prism-plus)`在代码块中呈现语法高亮。
*   `[rehype-code-titles](https://github.com/rockchalkwushock/rehype-code-titles)`渲染代码块标题。
*   `[rehype-accessible-emojis](https://www.npmjs.com/package/rehype-accessible-emojis)`提供表情符号的可访问性。

内容层. config.ts

# 内容层应用

我们可以利用这些数据做更多的事情。

# 应用#1: RSS 提要

我现在可以写一个脚本来生成基于`allBlogs`数据的 RSS 提要了！

脚本/rss.mjs

# 应用#2: XML 站点地图

为站点地图生成编写一个脚本更容易。我们所需要的是`data`和`page`目录中的文件结构。

脚本/sitemap.mjs

在构建项目后运行这两个脚本，并自动生成一个新的 RSS 提要和站点地图。

在`package.json`中，添加:

```
"scripts": {
*+*    "sitemap": "node scripts/sitemap.mjs",
*+*    "rss": "node scripts/rss.mjs",
*+*    "postbuild": "yarn sitemap && yarn rss",
  },
```

# 最后的想法

使用 MDX 和 Contentlayer，用 Next.js 构建静态站点变得如此轻松。

MDX 与 remark 和 rehype 生态系统相结合，丰富了使用 Markdown 构建一致页面的可能性。Contentlayer 使 MDX 文件中的数据和内容可供 Next.js 项目使用。

如果您正在探索构建自己的静态站点的方法，请查看前面提到的库。它不仅缩短了上市时间，而且构建起来非常有趣！🦄

# 参考

*   [GitHub:内容层](https://github.com/contentlayerdev/contentlayer)
*   [GitHub: rss](https://github.com/dylang/node-rss)
*   [GitHub:备注](https://github.com/remarkjs/remark)
*   [GitHub:重新输入](https://github.com/rehypejs/rehype)
*   [GitHub:retype-slug](https://github.com/rehypejs/rehype-slug)
*   [GitHub:重新键入-自动链接-标题](https://github.com/rehypejs/rehype-autolink-headings)
*   [GitHub:retype-prism-plus](https://github.com/timlrx/rehype-prism-plus)
*   GitHub:重新类型代码标题
*   [GitHub: @mdx-js/loader](https://github.com/mdx-js/mdx/tree/main/packages/loader)
*   [GitHub:阅读时间](https://github.com/ngryman/reading-time)
*   [GitHub: remark-gfm](https://github.com/remarkjs/remark-gfm)
*   [GitHub: mdx-bundler](https://github.com/kentcdodds/mdx-bundler)
*   [GitHub:灰质](https://github.com/jonschlinkert/gray-matter)
*   [GitHub:remark-mdx-front matter](https://github.com/remcohaszing/remark-mdx-frontmatter)
*   [GitHub: rss](https://github.com/dylang/node-rss)
*   [GitHub: globby](https://github.com/sindresorhus/globby)
*   [npm: @next/mdx](https://www.npmjs.com/package/@next/mdx)
*   [npm:可访问表情符号](https://www.npmjs.com/package/rehype-accessible-emojis)
*   [网站:李·罗宾逊作品集](https://leerob.io/)
*   [网址:MDX](https://mdxjs.com/)
*   [网站:使用 MDX 和 Next.js](https://nextjs.org/docs/advanced-features/using-mdx)
*   [网址:Next.js](https://nextjs.org/)
*   [网址:Next.js 动态路线](https://nextjs.org/docs/routing/dynamic-routes)
*   [网址:Next.js getStaticPaths](https://nextjs.org/docs/api-reference/data-fetching/get-static-paths)
*   [网址:Next.js getStaticProps](https://nextjs.org/docs/api-reference/data-fetching/get-static-props)
*   [网站:Frontmatter](https://mdxjs.com/guides/frontmatter/)
*   [网址:URI 片段](https://en.wikipedia.org/wiki/URI_fragment)
*   [网站:了解网站地图](https://developers.google.com/search/docs/advanced/sitemaps/overview)
*   [网站:ESMAScript 模块](https://nodejs.org/api/esm.html#modules-ecmascript-modules)
*   [网站:GitHub 风味降价规格](https://github.github.com/gfm)
*   [网站:抽象语法树](https://en.wikipedia.org/wiki/Abstract_syntax_tree)
*   [网站:什么是静态站点生成器？3 种方法找到最好的一个](https://www.netlify.com/blog/2020/04/14/what-is-a-static-site-generator-and-3-ways-to-find-the-best-one)
*   [网站:更漂亮](https://prettier.io/)

```
**Want to Connect?**This article was originally posted on [Daw-Chih’s website](https://dawchihliou.github.io/articles/build-better-nextjs-static-sites-with-mdx-and-contentlayer).
```