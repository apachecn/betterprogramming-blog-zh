# 用 GatsbyJS 编辑多语言网站

> 原文：<https://betterprogramming.pub/multiple-language-websites-with-gatsbyjs-b4985746b9eb>

![](img/f3061910374b34e5c42afe0717722501.png)

照片由[史蒂文·罗](https://unsplash.com/@steveroe_?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/seoul?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

在通过 GatsbyJS +插件的出色组合提供了大量服务之后，将 GatsbyJS 项目本地化为多种语言比预期的更困难。

插件解决了我的许多问题——例如..偷懒加载图片，制作网站地图，洗衣服。等等。所以当需要添加本地化的时候，我在插件中寻找一个合适的，可以帮助我把我的网站翻译成我的访问者所说的 12 种不同语言的插件。不幸的是，几乎没有几个插件有显著的 GitHub 星级和牵引力，我找不到一个满足下面要求的插件。

(不过，GatsbyJS repo 中的[使用-i18n](https://github.com/gatsbyjs/gatsby/tree/master/examples/using-i18n) 示例为设置提供了一个良好的开端。)

## 本地化要求:

1.  SEO 优化的预渲染翻译。当机器人抓取您的页面时，您希望页面的内容被翻译成该页面的语言。
2.  动态创建的页面。我不想手动跟踪同一页面的副本，但在不同的语言中，这将是一个更大的项目的噩梦。
3.  我希望能够轻松地将字符串翻译成翻译程序(例如 Crowdin、Locize)。
4.  更小的束尺寸以提高性能。如果你有大量的文本需要翻译，你最终会得到大量的源翻译文件，而你只想把与特定语言相关的翻译发送到该语言的页面。

# 设置

查看[这个回购](https://github.com/OskarAhl/Gatsby-react-i18next)的基本实现。

`gatsby-node.js`提供了一个 API，用于在页面构建过程中访问生命周期事件，在这里您可以动态地创建页面，并将数据传递给所创建的页面(我们希望为每种语言创建一次页面)。

所以我想做的是用正确的路径为每种语言创建一个页面。我已经在`language-config.js`中设置了路径:

假设我们的网站上有三个页面:主页、关于和联系我们。然后，我们希望在我们的站点上有这些路径:

```
// English is our default language
www.myawesomesite.com
www.myawesomesite.com/about
www.myawesomesite.com/contact// for our Spanish friends:
www.myawesomesite.com/es
www.myawesomesite.com/es/about
www.myawesomesite.com/es/contact// for our French friends:
www.myawesomesite.com/fr
www.myawesomesite.com/fr/about
www.myawesomesite.com/fr/contact
```

要创建(和删除页面)，您可以使用在`gatsby-node.js`中称为`createPages()`的生命周期方法。在那里，您可以动态地为我们所有的语言制作每个页面的副本，如下所示:

这里，我还将与特定语言相关的翻译(在 JSON 文件中)只发送到该语言的页面。翻译后的 JSON 文件如下所示:

# react-i18 接下来

对于实际的翻译本身，我使用了 [react-i18next](https://react.i18next.com/) ，因为它带有复数、上下文、插值、格式，这是一个健壮的、经过良好测试的、使用过的解决方案。

使用`crc32`翻译键值是可选的，但是我发现这比必须使用命名键更方便，因为在实际收集需要翻译的字符串时可以更灵活一些——这可以通过 NodeJS 脚本来完成。

设置:

# 预先呈现的翻译

对于要以各自语言预呈现的页面，我添加了一个 HOC(这样页面就不会在客户机上被动态翻译)。我还使用了`I18nextProvider`来包装带有`i18n`实例的页面。

# 链接

由于我们在页面 routes 中有语言路径(除了默认语言)，我们需要稍微修改一下 Gatsby 链接，以使路由适用于不同的语言。

## 如何使用

要翻译一个字符串，导入上面的`react-i18next`配置文件中添加的本地化函数，然后键入:

```
<h1>{localize('Hello world')}</h1>
```

# 回购示例

[](https://github.com/OskarAhl/Gatsby-react-i18next) [## 奥斯卡拉尔/盖茨比-react-i18 下一个

### GatsbyJS-oskar ahl/Gatsby-react-i18next 中 react-i18 next 的示例实现

github.com](https://github.com/OskarAhl/Gatsby-react-i18next)