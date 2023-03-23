# 与 Gatsby 的国际化

> 原文：<https://betterprogramming.pub/internationalization-with-gatsby-ae3991c39e92>

## 如何用 gatsby-plugin-i18n 和 react-intl 国际化你的 Gatsby 网站

![](img/f9cc7b974344399742585a782c28a92a.png)

尼古拉·纳托尔在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

我挑战自己每天分享一篇文章，直到 4 月 19 日新冠肺炎隔离期结束。还剩 30 天，希望会有更好的日子。

# 该项目

我和两个朋友开始了一个新项目。我还不能谈论太多，但我们只能说它符合我们的价值观。出于这个目的，我们需要一个网站——显然，它将是开源的，我将与 [Gatsby](https://www.gatsbyjs.org) 一起开发。

尽管这不是我的第一个 Gatsby 网站(我的[个人网站](https://daviddalbusco.com)是用同样的栈开发的)，但这是我第一次必须国际化一个。

我期望这样的实现相当简单，但是在简单的文档、过时的博客文章、甚至是示例项目之间，我实际上不得不在今天早上投入两个小时来最终实现我的目标。

这就是为什么我认为在这个新教程中分享结果可能是一个好主意。

# SEO 友好插件

您的老朋友需要不同的 URL(路由)来抓取和呈现每种语言的页面。例如，如果你的网站支持英语和法语，谷歌会很高兴你提供`https://domain.com/en/`和`[https://domain.com/fr/](https://domain.com/en/)`。

为了让盖茨比做到这一点，要记住的第一件重要的事情是你所有的页面都要复制。按照上面的例子，这意味着网站将包含一个`index.en.js`和一个`index.fr.js`。

为了帮助我们的网站理解这样的路由，我们可以使用插件 [gatsby-plugin-i18n](https://github.com/angeloocana/gatsby-plugin-i18n) 。

```
npm install gatsby-plugin-i18n —-save
```

安装完成后，我们在`gatsby-config.js`中添加所需的配置，并添加一些关于支持语言列表和默认语言的元信息。

注意，我指定了`prefixDefault`到`true`来禁止根路由。甚至默认语言英语的网址也必须加上前缀`/en/`。老实对你说，这背后的原因之一是我无法让它发生。

```
siteMetadata: {
  languages: {
    langs: ['en', 'fr'],
    defaultLangKey: 'en'
  }
},
plugins: [
  {
    resolve: 'gatsby-plugin-i18n',
    options: {
      langKeyDefault: 'en',
      useLangKeyLayout: true,
      prefixDefault: true
    }
  }
]
```

因为我们在任何情况下都使用前缀，没有任何其他变化，访问我们网站的根目录将不会显示任何内容——这就是为什么我们编辑`gatsby-browser.js`将根目录请求重定向到默认主页。

```
exports.onClientEntry = () => {
  if (window.location.pathname === '/') {
    window.location.pathname = `/en`
  }
}
```

# 国际化库

盖茨比和上面的插件要么兼容 [react-i18next](https://react.i18next.com) 要么兼容 [react-intl](https://github.com/formatjs/react-intl) 。我在[领带追踪器里用 i18next 因此，我选择了另一个方案，因为我喜欢学习新事物。](https://tietracker.app.link/)

React Intl 依赖于`Intl`API——这也是我们安装 polyfill `[intl-pluralrules](https://www.npmjs.com/package/@formatjs/intl-pluralrules)`的原因。

```
npm install react-intl @formatjs/intl-pluralrules --save
```

# 动手编码

足够的安装和配置—让我们开始编码。我们必须应用的主要修改发生在`layout.js`中，顺便说一下，我把它移到了一个名为`src/components/layout/`的子文件夹中，原因就是我喜欢干净的结构。

“这里发生了什么？”你可能会问。总而言之，我们添加了两个新的必需属性，`location`和`messages`。第一个用于猜测应该应用的地区，第二个包含翻译列表。如您所见，我们导入了 React Intl。我们还从`ptz-i18n`中导入了一个名为`getCurrentLangKey`的函数，它实际上是上述插件的一个实用程序。

我还使用`<FormattedMessage/>`组件打印出`Hello`以确保我们的实现工作正常。

```
import React from "react"
import PropTypes from "prop-types"
import { useStaticQuery, graphql } from "gatsby"

import Header from "../header"
import "./layout.css"

import { FormattedMessage, IntlProvider } from "react-intl"
import "@formatjs/intl-pluralrules/polyfill"

import { getCurrentLangKey } from 'ptz-i18n';

const Layout = ({ children, location, messages }) => {
  const data = useStaticQuery(graphql`
    query SiteTitleQuery {
      site {
        siteMetadata {
          title
          languages {
            defaultLangKey
            langs
          }
        }
      }
    }
  `)

  const { langs, defaultLangKey } = 
                                data.site.siteMetadata.languages;
  const langKey = 
     getCurrentLangKey(langs, defaultLangKey, location.pathname);

  return (
    <IntlProvider locale={langKey} messages={messages}>
      <Header siteTitle={data.site.siteMetadata.title} />

      <p>
        <FormattedMessage id="hello" />
      </p>

    </IntlProvider>
  )
}

Layout.propTypes = {
  children: PropTypes.node.isRequired,
  location: PropTypes.any.isRequired,
  messages: PropTypes.any.isRequired,
}

export default Layout
```

为了扩展每种语言和地区的布局，我们为每种支持的语言创建了一个新文件，例如，在英语中，我们创建了`layout/en.js`，在其中我们导入了自定义消息和特定的 polyfill。

```
import Reactfrom 'react';
import Layout from "./layout"

import messages from '../../i18n/en';
import "@formatjs/intl-pluralrules/dist/locale-data/en"

export default (props) => (
  <Layout
    {...props}
    messages={messages}
  />
);
```

此时，我们的代码无法编译，因为在这些语言中，这些消息丢失了。这就是为什么我们也为这些创建文件—例如，`i18n/en.js`。

```
module.exports = {
  hello: "Hello world",
}
```

正如我在介绍中简要说明的那样，每一页都将被复制。这就是我们创建相应索引页面的原因。在默认情况下，英语，我们重命名`index.js`为`index.en.js`。此外，因为布局现在需要一个位置属性，所以我们也为每个页面传递它。

还要注意，因为我已经决定给所有路由加前缀，所以我还修改了从`/page-2/`到`/en/page-2`的链路路由。

```
import Reactfrom "react"
import { Link } from "gatsby"

import Layout from "../components/layout/en"
import SEO from "../components/seo/seo"

const IndexPage = (props) => (
  <Layout location={props.location}>
    <SEO />
    <h1>Hi people</h1>

    <Link to="/en/page-2/">Go to page 2</Link>
  </Layout>
)

export default IndexPage
```

我们为`index`实现的相同修改应该传播到每个页面——在这个例子中，我也重命名了`page-2.en.js`中的`page-2.js`,并应用了与上面相同的修改。

```
import Reactfrom "react"
import { Link } from "gatsby"

import Layout from "../components/layout/en"
import SEO from "../components/seo/seo"

const SecondPage = (props) => (
  <Layout location={props.location}>
    <SEO title="Page two" />
    <p>Welcome to page 2</p>
    <Link to="/en/">Go back to the homepage</Link>
  </Layout>
)

export default SecondPage
```

同样，在我们的`404.js`页面中，`<Layout/>`组件的使用必须通过 location 对象得到增强。

```
import Reactfrom "react"

import Layout from "../components/layout/layout"
import SEO from "../components/seo/seo"

const NotFoundPage = (props) => (
  <Layout location={props.location}>
    <SEO />
    <h1>NOT FOUND</h1>
  </Layout>
)

export default NotFoundPage
```

就这样，我们的 Gastby 工厂实现了国际化。当然，你可能想添加一些其他语言。为此，对英语重复上述步骤，并再次复制页面。

# 更多提示和技巧

我随后发表了一篇后续文章，关于盖茨比网站的国际化。如果您渴望阅读更多的提示和技巧，请查看一下😉。

# 摘要

嗯，对我来说，不得不花这么多时间在一个新项目中释放国际化真的是意想不到的——这就是为什么我希望这个小方法可以在未来帮助到某人。

呆在家里，注意安全！