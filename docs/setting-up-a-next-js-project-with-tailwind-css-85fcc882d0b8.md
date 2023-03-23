# 使用 TailWind CSS 设置 Next.js 项目

> 原文：<https://betterprogramming.pub/setting-up-a-next-js-project-with-tailwind-css-85fcc882d0b8>

## 今天整合 Tailwind 和 Next.js

![](img/5ce4c1211f01f45cf73131ea2218df08.png)

照片由[马科斯·曼特](https://unsplash.com/@markos_mant?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

在这篇文章中，我想告诉你为什么 Tailwind CSS 和 Next.js 形成了一个伟大的组合。

有人讨厌写 CSS，有人爱写。我是后者中的一员，但是使用一个工具来帮助我更快地创建一个网站，并给我的工作带来稳定性，总是会让我兴奋不已。

在我的博客上，我以前使用过 Tailwind CSS 我已经被说服在我的更多 web 开发项目中使用它。如果你不知道 Next.js，我推荐你看[我之前的文章](https://byrayray.dev/posts/2020-12-21-nextjs-jamstack-getting-started)。

> 这个故事最初发表在[by ray . dev](https://byrayray.dev/posts/2020-12-27-why-use-tailwind-css-with-nextjs)上

# 1.为什么要用 Tailwind CSS？

将 Tailwind CSS 与 Bootstrap 或 Foundation 等 CSS 框架进行比较，你会发现它们完全不同。

我可以从几英里外嗅到一个引导网站或应用程序。你不会注意到一个网站或应用程序正在使用 Tailwind CSS(除非你检查源代码中的类名)。

Tailwind CSS 是一个与众不同的实用框架。没有一组预先构建的组件。在这个框架中，你可以找到像`text-white`、`grid`、`object-fit`以及更多的类。

所以你的设计看起来怎么样并不重要。Tailwind CSS 是构建每一个令人惊叹的设计的理想伙伴。

所以问题来了:“为什么要用顺风 CSS？”为什么不呢？

Tailwind CSS 将为您提供构建任何您想要的东西所需的所有能力。您可以轻松调整颜色、大小和媒体查询。

从外面看，你不会注意到 Tailwind CSS 是它背后的驱动力。

# 2.你必须了解 CSS

但是在使用 Tailwind CSS 之前，有一点你需要知道:你必须了解 CSS。

如果你不能用普通的 CSS 构建一个设计，我不会推荐你选择顺风 CSS。

了解最基本的东西——如字体大小、填充、边距、位置、伸缩和网格——的开发人员使用 Tailwind 不会有问题。所以如果你知道 CSS 的这些东西，我强烈推荐你使用 Tailwind CSS。

例如，在特定的媒体查询上设置不同的字体大小就像添加类名(`md:font-size`)或更改高度(`sm:w-16`)一样简单。

您可以在配置文件的类名中更改这些数字。

所以试一试，把它添加到你的 Next.js 项目中。

# 3.如何在 Next.js 中添加 Tailwind CSS

将 Tailwind CSS 添加到 Next.js 非常简单，因此您将能够立即开始。在这种情况下，我将假设您使用 Next.js v10，因为您为什么不使用它呢？

## 装置

```
# If you're on Next.js v10
npm install tailwindcss@latest postcss@latest autoprefixer@latest
```

通过上面的命令，您将安装 Tailwind CSS 和 PostCSS(您也可以将其用于任何其他 CSS 预处理器)以及 autoprefixer 依赖项。

## 配置

使用这个命令，您可以生成配置文件`tailwind.config.js`和`postcss.config.js`:

```
npx tailwindcss init -p
```

这些文件将位于项目的根目录下。在`tailwind.config.js`文件中，你可以配置尺寸、字体和颜色。在`postcss.config.js`中，您可以添加所有想要使用的 PostCSS 插件:

在配置中，我们想要定义页面和组件的位置。通过定义这一点，我们可以让 Tailwind tree-shake 我们的产品构建中所有不需要的样式。

## 包括顺风 CSS

打开`globals.css`，添加下面的代码:

```
/* ./styles/globals.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

你也可以在`pages/_app.js`中包含来自顺风的所有内容，如下所示:

# 4.如何使用 Tailwind CSS

有两种方法可以在 CSS 中使用 Tailwind。

第一种是使用 Tailwind 的所有类名。您可以像下面的例子一样使用它:

您可以使用的第二个选项是:

```
.content__container {
  @apply container mx-auto px-4 mb-16 flex justify-center flex-col;
}.content {
  @apply grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-8;
}
```

只是喜好问题。在第一个例子中，你的 HTML 将充满类名。在第二个例子中，你可能有一个更干净的方法。

但是在这两种方法中，您应该熟悉 Tailwind 的所有类名。幸运的是，Tailwind 创建了大量的文档。

# 结论

感谢阅读。希望你现在可以在你的 Next.js 网站中使用 Tailwind CSS。如果你有任何问题或者想展示你的 Next.js 网站，请在评论区告诉我。

*快乐编码🚀*

# 阅读更多

[](https://medium.com/better-programming/5-rules-to-improve-code-readability-83eda50ca780) [## 提高代码可读性的 5 条规则

### 代码可读性是你的应用程序的一个特性(即使你的用户看不到)

medium.com](https://medium.com/better-programming/5-rules-to-improve-code-readability-83eda50ca780) [](https://medium.com/better-programming/how-to-build-and-deploy-a-jamstack-website-fast-with-next-js-a61df3c822f) [## 如何使用 Next.js 快速构建和部署 Jamstack 网站

### 为什么 Next.js 是明智的选择

medium.com](https://medium.com/better-programming/how-to-build-and-deploy-a-jamstack-website-fast-with-next-js-a61df3c822f) [](https://medium.com/better-programming/tips-to-create-developer-tutorials-62cb3a25b8e5) [## 创建开发人员教程的技巧

### 想写更多的教程，但你不知道从哪里开始？从这里开始

medium.com](https://medium.com/better-programming/tips-to-create-developer-tutorials-62cb3a25b8e5) [](https://medium.com/dev-together/how-to-learn-javascript-the-easy-way-2aa37007c481) [## 如何以最简单的方式学习 JavaScript？

### 1.先从理论开始！

medium.com](https://medium.com/dev-together/how-to-learn-javascript-the-easy-way-2aa37007c481)