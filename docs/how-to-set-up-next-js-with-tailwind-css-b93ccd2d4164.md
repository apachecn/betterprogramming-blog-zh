# 如何用 Tailwind CSS 设置 Next.js

> 原文：<https://betterprogramming.pub/how-to-set-up-next-js-with-tailwind-css-b93ccd2d4164>

## 在 React 项目中使用 Tailwind CSS 和 Next.js

![](img/eb35c045c41d6b3412070ee9148e5d89.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Khamkéo Vilaysing](https://unsplash.com/@mahkeo?utm_source=medium&utm_medium=referral) 拍摄的照片。

*更新:顺风社 CSS 2.0 已经发布。请按照这个新指南使用最新版本的 Tailwind 与最新版本的 next . js:*[*https://medium . com/better-programming/how-to-set-up-Tailwind-CSS-v2-0-with-next-js-10-4a 82 c8 F8 caaa*](https://medium.com/better-programming/how-to-set-up-tailwind-css-v2-0-with-next-js-10-4a82c8f8caaa)

如果你正在开始一个新的 React 项目，你可能想要考虑 [Next.js](https://nextjs.org) 和 [Tailwind CSS](https://tailwindcss.com) 。在本文中，我们将解释为什么这是一个很好的选择，并带您完成使用这些技术建立一个新项目的过程。

# 为什么是 Next.js？

当您使用 React 启动一个新项目时，有许多重要的细节需要考虑。你的代码必须使用像 webpack 这样的捆绑器捆绑，并使用像 Babel 这样的编译器转换。 [Create React App](https://reactjs.org/docs/create-a-new-react-app.html) 可能是一个很好的工具，可以帮你处理这个问题，并给你一个巨大的开端，但是代码分割、性能预渲染以及 SEO 或服务器端渲染呢？

要构建一个完整的 React 应用程序，你需要的不仅仅是 CRA 提供给你的。您可以通过使用 Next.js 为自己节省一些时间，这是一个 React 框架，为上述所有问题提供了一个解决方案。如果你想了解更多关于 CRA vs. Next.js 的信息，请查看[Stack choices:Create React App vs next . js](https://codeburst.io/stack-choices-create-react-app-vs-next-js-f0f3c4db7083)。

# 为什么要用顺风 CSS？

[Tailwind CSS](https://tailwindcss.com/) 是一个 CSS 框架，但它不同于大多数其他框架，如 Bootstrap、Material 或 Ant Design。这是一个实用优先的框架，这意味着它不关注预先设计的组件，如按钮、卡片和警告。它提供了低级的工具类，让你不用离开你的 HTML 就可以构建完全定制的设计。

尽管学习所有的实用程序类需要一些时间，但是一旦你开始使用它，你几乎不需要再写任何 CSS 了。它可以极大地提高您的工作效率，并且在设计 HTML 元素时不需要切换文件，这是一种福气。

# 我们开始吧

如果您还没有使用 Next.js 设置项目，首先打开您的终端并运行项目文件夹中的`yarn create next-app`。

您将收到以下消息提示:

```
What is your project named?
```

填写一个名字，然后按回车键。下一条消息是:

```
Pick a template
```

选取“默认启动应用程序”,等待您的项目准备就绪。然后，键入`cd <your-project-name>`以确保您在可以运行`yarn dev`来启动开发服务器的目录中。现在，您应该已经启动并运行了新的 Next.js 项目。

# 添加顺风 CSS

首先使用 npm 或 yarn 将 Tailwind CSS 库添加到您的开发依赖项中:

```
yarn add tailwindcss -D
```

然后，我们需要将一个`tailwind.config.js`文件添加到我们的应用程序的根目录中。使用以下命令进行设置:

```
npx tailwind init
```

现在您的项目中应该有一个包含以下内容的`tailwind.config.js`:

# 添加后置 CSS

接下来，我们需要配置 [PostCSS](https://postcss.org/) ，一个用 JavaScript 转换 CSS 的工具，因为 Tailwind CSS 是一个 PostCSS 插件。让我们创建一个`postcss.config.js`并添加以下内容，这样 Next.js 将加载已定义的插件。

在项目的根目录下，运行:

```
touch postcss.config.js
```

然后将以下内容添加到该文件中:

```
module.exports = {
  plugins: ['tailwindcss'],
}
```

除了 TailwindCSS 之外，我们还应该添加 [PostCSS Preset Env](https://github.com/csstools/postcss-preset-env) 模块，它将现代 CSS 转换成大多数浏览器都能理解的东西:

```
yarn add postcss-preset-env -D
```

现在让我们更新我们的`postcss.config.js`,以包含这个模块:

```
module.exports = {
  plugins: ['tailwindcss', 'postcss-preset-env'],
}
```

现在让我们创建一个`styles`文件夹，并从 CSS 文件中导入 Tailwind CSS:

```
mkdir styles touch styles/tailwind.css
```

内部`tailwind.css`:

```
@tailwind base;
@tailwind components;
@tailwind utilities;
```

要将全局 CSS 添加到 Next.js 应用程序中，我们需要覆盖默认的`App`组件。Next.js 使用`App`组件来初始化页面，如果我们需要控制页面初始化，我们需要创建一个名为`pages/_app.js`的文件:

```
touch pages/_app.js
```

现在导入我们创建的样式表:

```
import '../styles/tailwind.css';export default function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />;
}
```

酷，现在我们准备给我们的主页添加一些 CSS 魔法。转到`/pages/index.js`(或者`/pages/index.tsx`，如果你使用 TypeScript 的话)用 Tailwind CSS 类添加一些元素。例如:

运行`yarn dev`在浏览器中的`[http://localhost:3000](https://localhost:4202/settings/departments)`上查看您的应用。

# 采购

Tailwind CSS 的一个问题是文件太大，但是我们可以使用 [PurgeCSS](https://github.com/FullHuman/purgecss) 来解决这个问题。PurgeCSS 通过扫描 HTML 并删除任何不使用的类来减小文件大小。我们只希望在产品中这样做，因为如果我们正在开发，我们希望能够在不运行构建过程的情况下使用任何 Tailwind CSS 类。为此，我们首先将 PurgeCSS 模块作为开发依赖项安装在我们的项目中:

```
yarn add @fullhuman/postcss-purgecss -D
```

然后用下面的代码更新您的`postcss.config.js`文件:

出口顺序很重要。首先，我们告诉 PostCSS 加载 Tailwind CSS。然后，我们清除未使用的 CSS(仅在生产中)。最后，我们让 PostCSS 预设 Env 插件完成它的工作，使剩余的 CSS 兼容所有浏览器。

现在，我们检查位于`pages/`或`components/`文件夹中的`.js, .jsx, .ts or .tsx`文件中的所有代码。如果你计划在其他文件夹中添加 HTML，比如`containers/`或者其他什么，确保你把那个文件夹添加到这个配置文件中。

最后，建议只将 PurgeCSS 应用于 Tailwind CSS 的实用程序类，而不要应用于[基本样式](https://tailwindcss.com/docs/adding-base-styles)或[组件类](https://tailwindcss.com/docs/extracting-components#extracting-css-components-with-apply)。这将确保您在使用 Next.js 时不会意外清除重要的基本样式。最简单的方法是使用 PurgeCSS 的[白名单](https://github.com/FullHuman/purgecss-docs/blob/master/whitelisting.md)特性来禁用非实用类的 PurgeCSS。将此添加到您的`tailwind.css`文件中:

```
/* purgecss start ignore */
@tailwind  base;
@tailwind  components;
/* purgecss end ignore */@tailwind  utilities;
```

酷，现在我们已经准备好使用 Next.js 和 Tailwind CSS 了，不用担心包的大小了！

就是这样！感谢阅读。我希望它有帮助。