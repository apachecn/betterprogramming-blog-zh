# 如何用 Next.js 10 设置 Tailwind CSS V2.0

> 原文：<https://betterprogramming.pub/how-to-set-up-tailwind-css-v2-0-with-next-js-10-4a82c8f8caaa>

## 在 React 项目中使用新的 Tailwind CSS 和 Next.js

![](img/c3861966ea7fa742e01fa358e4c8197a.png)

[TJ K.](https://unsplash.com/@tbk_f?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

在 2020 年的最后几个月，发布了几个伟大的框架更新。首先是 [Next.js 10](https://nextjs.org/blog/next-10) 的发布，它有很多不错的特性，比如自动优化图像的新图像组件。

其次，在 1.0 发布 18 个月后，Tailwind 团队发布了他们 CSS 框架的 [v2.0](https://blog.tailwindcss.com/tailwindcss-v2) 。这次新的更新包括很多改进，比如[新的调色板](https://blog.tailwindcss.com/tailwindcss-v2#all-new-color-palette)和[黑暗模式支持](https://blog.tailwindcss.com/tailwindcss-v2#dark-mode)。

使用最新版本的 Next.js 和 Tailwind CSS 是创建和设计 web 应用程序的一个非常强大的组合。让我们看一下建立新项目的步骤。

# 启动一个新的 Next.js 项目

假设您已经安装了 [Yarn](https://classic.yarnpkg.com/en/docs/install/#mac-stable) ，打开您的终端并运行项目文件夹中的`yarn create next-app`。

您将收到以下消息提示:

```
What is your project named?
```

填写一个名称，按 enter 键，然后等待，直到您的项目准备就绪。然后，键入`cd <your-project-name>`以确保您在可以运行`yarn dev`来启动开发服务器的目录中。现在，您应该已经启动并运行了新的 Next.js 10 项目。

# 安装 Tailwind 及其依赖项

Tailwind CSS 是一个建立在 [PostCSS](https://postcss.org/) 之上的插件，一个用 JavaScript 转换 CSS 的工具。v2.0 已经更新为最新的 PostCSS 版本，该版本需要安装`postcss`和`autoprefixer`作为与 Tailwind 本身的对等依赖项。

使用 npm 或 yarn 添加 Tailwind 并安装 PostCSS 以及 autoprefixer:

```
yarn add tailwindcss postcss autoprefixer
```

# 创建配置文件

我们需要向应用程序的根目录添加一个`tailwind.config.js`和一个`postcss.config.js`文件。使用以下命令进行设置:

```
npx tailwindcss init -p
```

这将在项目的根目录下创建一个`tailwind.config.js`文件:

在[配置文档](https://tailwindcss.com/docs/configuration)中了解更多关于配置顺风的信息。

它还将创建一个包含已配置的`tailwindcss`和`autoprefixer`的`postcss.config.js`文件:

# 导入 CSS

让我们创建一个`styles`文件夹，从 CSS 文件中导入 Tailwind CSS:

```
touch styles/tailwind.css
```

内部`tailwind.css`:

```
@tailwind base;
@tailwind components;
@tailwind utilities;
```

要将全局 CSS 添加到 Next.js 应用程序中，我们需要覆盖默认的`App`组件。在 Next.js 10 中，pages 文件夹中应该已经有了`_app.js`。现在导入我们创建的样式表:

```
import '../styles/globals.css'
import '../styles/tailwind.css';export default function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />;
}
```

酷，现在我们准备给我们的主页添加一些 CSS 魔法。转到`/pages/index.js`(或者`/pages/index.tsx`如果你使用 TypeScript)并用 Tailwind CSS 类添加一些元素。例如:

运行`yarn dev`在浏览器中查看 [http://localhost:3000](https://localhost:4202/settings/departments) 上的应用。

# 配置 PurgeCSS

顺风 CSS 的一个问题是文件太大，但是 [PurgeCSS](https://github.com/FullHuman/purgecss) 可以解决这个问题。PurgeCSS 通过扫描 HTML 并删除任何不使用的类来减小文件大小。我们只希望在产品中这样做，因为如果我们正在开发，我们希望能够在不运行构建过程的情况下使用任何 Tailwind CSS 类。

现在有了 Tailwind CSS v2，PurgeCSS 已经包含在内了。你所要做的就是更新`tailwind.config.js`文件，这样 Tailwind 就可以在产品构建中对未使用的样式进行树抖动。像这样更新您的文件:

现在，我们检查位于`pages/`或`components/`文件夹中的`.js, .jsx, .ts or .tsx`文件中的所有代码。如果您计划将 HTML 添加到其他文件夹中，如`containers/`或其他文件夹，请确保将该文件夹添加到此配置文件中。

您可以阅读 Tailwind 关于[优化生产](https://tailwindcss.com/docs/optimizing-for-production)的指南，了解更多关于树摇动未使用的样式以获得最佳性能的信息。

# 结论

现在我们已经准备好使用最新版本的 Next.js 和 Tailwind CSS，而不必担心包的大小！

就是这样！感谢阅读。我希望它有帮助。