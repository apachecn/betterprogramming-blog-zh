# TailwindCSS 和 Symfony 的 Webpack Encore

> 原文：<https://betterprogramming.pub/tailwindcss-and-symfonys-webpack-encore-7bfc8c18665b>

## 将 TailwindCSS 集成到 Webpack encore 的快速指南

![](img/42c03b84d4a7b3d87401e0895da4fd7c.png)

[天一马](https://unsplash.com/@tma?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

我最近有机会在工作中为一个项目选择和使用[**TailwindCSS**](https://tailwindcss.com/)**框架。**

**在[最初](http://www.zeldman.com/2017/01/03/kiss-my-classname/)对其 [**效用至上的 CSS**](https://tailwindcss.com/docs/utility-first/) 概念有些犹豫之后，我进行了一点[更多的研究](https://mrmrs.cc/writing/scalable-css/)并与顺风社区进行了互动。我最终获得了足够的认同，修正了我最初的判断，并尝试了一次。**

**最愉快的经历！**

**我目前正在做一个项目，在后端使用 PHP 的 [**Symfony**](https://symfony.com/) 框架，任务是将 Tailwind 集成到[**Webpack encore**](https://symfony.com/doc/current/frontend.html#webpack-encore)**，** Symfony 版本的 web pack 中。**

**以下是我如何整合它们的。**

## **属国**

**首先，我们需要安装几个依赖项:**

*   **`tailwindcss`本身。**
*   **顺风需要的一个叫`postcss-loader`的包。**
*   **帮助处理厂商前缀的`autoprefixer`包。**

**假设您已经安装了 Webpack-encore，那么您可以删除`@symfony/webpack-encore`，否则，所有的依赖项都将存放在一起，我们最终会得到以下命令:**

```
yarn add --dev @symfony/webpack-encore tailwindcss postcss-loader autoprefixer
```

**[**作曲**](https://getcomposer.org/) 做我们的包经理。我使用的是`yarn add`命令，但是通过`npm install`用`npm`也可以得到同样的结果。**

## **配置**

**我配置和/或创建了四个文件:**

*   **`tailwind.css`**
*   **`tailwind.config.js`**
*   **`postcss.config.js`**
*   **`webpack.config.js`**

*****tailwind . css*:**我在我的项目的 CSS 资产目录下创建了这个文件— `assets/css/tailwind.css` —但是你可以把它放在你喜欢的任何地方。“@tailwind”指令将`base`、`components`和`utilities`样式注入到您的 CSS 中。**

*****tailwind . config . js*:**该文件是可选的，但是如果您需要以任何方式[定制 tailwind](https://tailwindcss.com/docs/configuration) (例如，给类名添加前缀、定制颜色或字体大小等)，您将需要通过运行`npx tailwind init`经由 [tailwind CLI](https://tailwindcss.com/docs/installation/#3-create-your-tailwind-config-file-optional) 创建该文件。这将在您的根文件夹中生成文件。**

*****postcss . config . js*:**我在我的项目根目录下创建了这个文件。这是我们指定要用来处理 CSS 的 PostCSS 插件的地方。**

*****web pack . config . js*:**如果你已经在使用 Webpack-encore，那么这个文件应该已经配置好了，否则[这个可以帮助](https://symfony.com/doc/current/frontend/encore/installation.html)。这个文件的配置和其他文件一样快捷:我添加了指向我们在 *tailwind.css* 中注入的样式的`.addStyleEntry('tailwind', './assets/css/tailwind.css')`，并启用了 PostCSS loader，如下所示:**

## **模板**

**[Twig](https://twig.symfony.com/) 是我们项目的模板引擎，因此我也必须在我的`base.html.twig`模板文件中包含一个`encore_entry_link_tags()`函数。这很像添加一个样式表链接，所以如果你忘记包含它并开始写你的类，什么都不会发生，疯狂会随之而来。**

# **瞧啊。**

**您现在可以为您的开发环境运行`yarn run dev`或者为生产环境运行`yarn run build`,您应该可以开始设计风格了！我希望这个小指南有所帮助。**

# ****资源****

*   **[尾翼](https://tailwindcss.com/)**
*   **[实用第一 CSS](https://tailwindcss.com/docs/utility-first/)**
*   **[亲亲我的班名](https://www.zeldman.com/2017/01/03/kiss-my-classname/)**
*   **[CSS 和可扩展性](https://mrmrs.cc/writing/scalable-css/)**
*   **[Symfony](https://symfony.com/)**
*   **[Webpack-encore](https://symfony.com/doc/current/frontend/encore/simple-example.html)**
*   **[作曲家](https://getcomposer.org/)**
*   **[安装 Encore](https://symfony.com/doc/current/frontend/encore/installation.html)**
*   **[Github Gists](https://gist.github.com/Iambizi)**