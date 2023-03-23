# 八个节省时间的盖茨比插件

> 原文：<https://betterprogramming.pub/here-are-8-useful-gatsby-plugins-9d80d9e3853f>

## 盖茨比已经很容易共事了——现在快一点

![](img/9b06cb7965edafb4c5612e59d46f4e8b.png)

本·科尔德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

盖茨比越来越受欢迎，这是有充分理由的。它非常有趣，也很容易使用——它让一个 [React](https://reactjs.org/) 开发人员的生活变得更加顺畅和高效。

什么是盖茨比外挂？简而言之，Gatsby 插件是可重用的代码模块。从盖茨比主页:

> Gatsby 插件是实现 Gatsby APIs 的 Node.js 包。对于更大、更复杂的站点，插件允许您将站点定制模块化到特定于站点的插件中。

事不宜迟，这里有八个方便的 Gatsby 插件，你可以从今天开始使用。注:本文假设您熟悉盖茨比的基本知识。

# 盖茨比-插件-反应-头盔

`gatsby-plugin-react-helmet`插件为添加了 [React 头盔](https://github.com/nfl/react-helmet)的服务器渲染数据提供插件支持，React 头盔是一个让你控制文档头的组件。

使用这个插件，你在组件中添加的属性(标题，元属性，等等。)将被添加到 Gatsby 构建的静态 HTML 页面中。

这不仅对站点浏览者很重要，对 SEO 也很重要:存储在文档头中的标题和描述元数据是 Google 用来决定在搜索结果中位置的关键组件。

安装简单:

`npm install --save gatsby-plugin-react-helmet react-helmet`

## 如何使用它

只需将插件添加到你的`gatsby-config.js`中的插件数组中

```
plugins: [`gatsby-plugin-react-helmet`]
```

如果你正在使用`gatsby-plugin-offline`，你可能会注意到当你在后台打开一个链接时，标题不会出现在标签栏中，直到你切换到那个标签。这是 React 头盔的[上游问题；然而，可以通过将`defer={false}`道具传递给`Helmet`组件来解决这个问题。例如:](https://github.com/nfl/react-helmet/issues/315)

```
<Helmet title="foo bar" defer={false} />
```

点击阅读完整文档[。](https://www.gatsbyjs.org/packages/gatsby-plugin-react-helmet/?=helmet)

# 盖茨比源文件系统

这个插件用于从您的本地文件系统获取数据到您的 Gatsby 应用程序中。

插件从文件中创建`File`节点。各种“transformer”插件可以将`File`节点转换成其他类型的数据。例如，`gatsby-transformer-json`将 JSON 文件转换成 JSON 数据节点，`gatsby-transformer-remark`将 [Markdown](https://en.wikipedia.org/wiki/Markdown) 文件转换成`MarkdownRemark`节点，从中可以查询 Markdown 的 HTML 表示。

安装:

`npm install --save gatsby-source-filesystem`

## 如何使用它

在您的`gatsby-config.js` `module.exports`插件中，您可以拥有该插件的多个实例来从文件系统的不同位置读取源节点。下面建立了一个 [Jekyll](https://jekyllrb.com/) 模式，其中一个`pages`目录用于降价文件，一个`data`目录用于`.json`、`.yaml`和`.csv`。

```
 {
      resolve: `gatsby-source-filesystem`,
      options: {
        name: `pages`,
        path: `${__dirname}/src/pages/`,
      },
    },
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        name: `data`,
        path: `${__dirname}/src/data/`,
        ignore: [`**/\.*`], // ignore files starting with a dot
      },
    },
  ],
}
```

点击阅读完整文档[。](https://www.gatsbyjs.org/packages/gatsby-source-filesystem/?=)

# 盖茨比-插件-离线

`gatsby-plugin-offline`为使 Gatsby 站点脱机工作提供插入式支持，使其能够更好地抵御不良网络连接。它为站点创建一个服务工作者，并将服务工作者加载到客户机中。

如果你使用这个插件和`gatsby-plugin-manifest`(推荐)这个插件应该列在*、*之后，这样清单文件就可以包含在服务人员中。

安装:

`npm install --save gatsby-plugin-offline`

## 如何使用它

```
// In your gatsby-config.js
plugins: [`gatsby-plugin-offline`]
```

点击阅读完整文档[。](https://www.gatsbyjs.org/packages/gatsby-plugin-offline/?=)

# 盖茨比插件清单

这个插件支持的 web-app manifest(PWA 规范的一部分)允许用户将你的站点添加到大多数移动浏览器的主屏幕上。清单向电话提供配置和图标。

这个插件提供了清单配置之外的几个特性，让您的生活更加轻松:

*   自动图标生成:从单一来源生成多种图标尺寸
*   [图标支持](https://www.w3.org/2005/10/howto-favicon)
*   传统图标支持(iOS)
*   [缓存破坏](https://www.keycdn.com/support/what-is-cache-busting)
*   本地化:为基于路径的本地化提供唯一的清单( [Gatsby 示例](https://github.com/gatsbyjs/gatsby/tree/master/examples/using-i18n))

这些功能中的每一项都有丰富的配置，因此您可以随时掌控。

安装:

```
$ npm install --save gatsby-plugin-manifest
```

添加插件和清单设置—此步骤是必需的:

```
// in gatsby-config.js
module.exports = {
  plugins: [
    {
      resolve: `gatsby-plugin-manifest`,
      options: {
        name: `GatsbyJS`,
        short_name: `GatsbyJS`,
        start_url: `/`,
        background_color: `#f7f0eb`,
        theme_color: `#a2466c`,
        display: `standalone`,
      },
    },
  ],
}
```

如果你同时使用这个插件和`[gatsby-plugin-offline](https://www.gatsbyjs.org/packages/gatsby-plugin-offline)`(推荐)，这个插件应该列在离线插件之前，这样它可以缓存创建的`manifest.webmanifest`。

有关配置您的 web 应用程序的更多信息，[请参见此处](https://developers.google.com/web/fundamentals/web-app-manifest/)。关于插件的更多信息，请阅读完整文档[这里](https://www.gatsbyjs.org/packages/gatsby-plugin-manifest/?=)。

# 盖茨比插件网站地图

`gatsby-plugin-sitemap`为你的盖茨比网站创建一个网站地图。有用的搜索引擎爬虫和搜索结果优化。

注意:该插件仅在`production`模式下运行时产生输出。如果你想测试你的站点地图，运行:`gatsby build && gatsby serve`

安装:

`npm install --save gatsby-plugin-sitemap`

## 如何使用它

在你的`gatsby-config.js`:

```
siteMetadata: {
  siteUrl: `https://www.example.com`,
},
plugins: [`gatsby-plugin-sitemap`]
```

以上是它工作所需的最低配置。默认情况下，生成的站点地图将包括您站点的所有页面，除了您排除的页面。

点击阅读完整文档[。](https://www.gatsbyjs.org/packages/gatsby-plugin-sitemap/?=)

# 盖茨比-插件-谷歌-分析

轻松添加谷歌分析到您的盖茨比网站。

安装:

`npm install --save gatsby-plugin-google-analytics`

## 如何使用它

在您的`gatsby-config.js` `module.exports`插件中:

```
 {
      resolve: `gatsby-plugin-google-analytics`,
      options: {
        trackingId: "YOUR_GOOGLE_ANALYTICS_TRACKING_ID",
        // Defines where to place the tracking script - `true` in the head and `false` in the body
        head: false,
        // Setting this parameter is optional
        anonymize: true,
        // Setting this parameter is also optional
        respectDNT: true,
        // Avoids sending pageview hits from custom paths
        exclude: ["/preview/**", "/do-not-track/me/too/"],
        // Delays sending pageview hits on route update (in milliseconds)
        pageTransitionDelay: 0,
        // Enables Google Optimize using your container Id
        optimizeId: "YOUR_GOOGLE_OPTIMIZE_TRACKING_ID",
        // Enables Google Optimize Experiment ID
        experimentId: "YOUR_GOOGLE_EXPERIMENT_ID",
        // Set Variation ID. 0 for original 1,2,3....
        variationId: "YOUR_GOOGLE_OPTIMIZE_VARIATION_ID",
        // Any additional optional fields
        sampleRate: 5,
        siteSpeedSampleRate: 10,
        cookieDomain: "example.com",
      },
    },
  ],
}
```

点击此处查看[可选字段](https://www.gatsbyjs.org/packages/gatsby-plugin-google-analytics/?=#optional-fields)的完整列表。

注意，这个插件在运行`gatsby develop`时被禁用。这样，当您仍在开发项目时，不会跟踪操作。一旦你运行了`gatsby build`，插件就被激活了。用`gatsby serve`测试一下。

点击阅读完整文档[。](https://www.gatsbyjs.org/packages/gatsby-plugin-google-analytics/?=)

# 盖茨比-插件-排版

一个 Gatsby 插件，以最小的配置使用[排版](https://kyleamathews.github.io/typography.js/)库。在[教程](https://www.gatsbyjs.org/tutorial/part-three/) ( [来源](https://github.com/gatsbyjs/gatsby/tree/master/packages/gatsby-plugin-typography))中查看它的运行情况

安装:

`npm install --save gatsby-plugin-typography react-typography typography`

## 如何使用它

利用其主题之一的典型`typography.js`文件可能如下所示:

```
import Typography from "typography"
import grandViewTheme from "typography-theme-grand-view"const typography = new Typography(grandViewTheme)// Export helper functions
export const { scale, rhythm, options } = typography
export default typography
```

然后，您必须获取导出的样式表并将它们内联到您的条目文件中。因为一个主题有两种字体，你也必须确保你有可用的字体。

通过使用`gatsby-plugin-typography`并通过`pathToConfigModule`选项指定到`typography.js`文件的路径(见下文)，您的排版样式*和*任何相关字体的包含由一对辅助方法负责，将您的排版相关配置保存在一个位置，并使您的条目文件稀疏。

```
// In your gatsby-config.js
module.exports = {
  plugins: [
    {
      resolve: `gatsby-plugin-typography`,
      options: {
        pathToConfigModule: `src/utils/typography`,
      },
    },
  ],
}
```

点击阅读完整文档[。](http://gatsbyjs.org/packages/gatsby-plugin-typography/?=)

# 盖茨比插件网络包捆绑包分析器

一个 Gatsby 插件，帮助使用[web pack-bundle-analyzer](https://github.com/webpack-contrib/webpack-bundle-analyzer)分析您的 bundle 内容。

用[纱线](https://yarnpkg.com/)安装:

```
yarn add gatsby-plugin-webpack-bundle-analyzer
```

或者用 [npm](https://www.npmjs.com/) :

```
npm install --save gatsby-plugin-webpack-bundle-analyzer
```

## 如何使用它

```
// In your gatsby-config.js
plugins: [
  'gatsby-plugin-webpack-bundle-analyzer',
]
```

更多选项可在这里找到[，包括:](https://github.com/webpack-contrib/webpack-bundle-analyzer#options-for-plugin)

*   `production`:在生产版本上运行分析器。默认为`false`。
*   `disable`:设置为`true`关闭分析仪。默认为`false`。

```
plugins: [{ resolve: 'gatsby-plugin-webpack-bundle-analyzer', options: { analyzerPort: 3000, production: true, }}]
```

点击阅读完整文档[。](https://www.gatsbyjs.org/packages/gatsby-plugin-webpack-bundle-analyzer/?=)

# 结论

感谢您的阅读，我希望您至少为您现有的或新的 Gatsby 项目找到了一个有用的插件。万一我错过了你最喜欢的插件，把它贴在下面的回复里。编码快乐！