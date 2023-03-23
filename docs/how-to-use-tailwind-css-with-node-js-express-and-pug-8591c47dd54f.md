# 如何将 Tailwind CSS 与 Node.js、Express 和 Pug 一起使用

> 原文：<https://betterprogramming.pub/how-to-use-tailwind-css-with-node-js-express-and-pug-8591c47dd54f>

## 顺风和模板

![](img/efdc61ce64310c4a65825070e1f72cdd.png)

[Pankaj Patel](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

在本文中，您将学习如何将 [Tailwind CSS](https://tailwindcss.com) 与 Node.js、Express 和 [Pug](https://pugjs.org/api/getting-started.html) 一起使用。本教程的目的是教你如何使用 Tailwind CSS、Express 和 Pug 创建一个节点应用程序。

# 先决条件

*   基本的终端知识。
*   知道如何使用代码编辑器。
*   基本的 NPM/npx/纱线知识。
*   Pug 和 Express.js 知识。

# 项目配置

第一步是创建和配置应用程序。运行以下命令创建一个名为`nodejs-tailwind`的空目录，并将当前目录更改为该目录:

```
mkdir nodejs-tailwind 
cd nodejs-tailwind
```

现在我们在`nodejs-tailwind`目录中，我们必须初始化我们的节点项目。为此，我们需要运行以下命令:

```
npm init
```

对于每个节点项目，我们需要一个名为`package.json`的文件。它的目的是存储关于项目的元数据(例如名称、描述、版本、作者等)。)并管理项目依赖关系和脚本。我们可以手动创建文件，但那会很繁琐。

因此，`npm init`的目的是创建`package.json`文件。运行该命令后，您会得到一个交互式提示，您可以在其中添加有关项目的信息。或者，如果您对建议的信息感到满意，可以按 enter 键直到结束。如果不添加任何信息，您的文件应该如下所示:

现在我们需要创建`index.js`，它存储了我们的基本服务器。您可以通过运行以下命令来创建该文件:

```
touch index.js
```

# 创建基本服务器

创建服务器有各种节点框架。然而，对于本教程，我们将使用 Express，因为它是一个成熟的、众所周知的 web 框架。您可以在项目中安装 Express，如下所示:

```
npm install express
```

如果您转到`package.json`，您可以看到`express`列在依赖项下。这意味着我们已经准备好构建服务器并在浏览器中测试它。

现在确保服务器正常工作。你可以在你的终端里运行`node index.js`然后访问`localhost:3000`来检查。如果你做的一切都是正确的，你应该会看到一个页面，上面写着“它工作了”

# 添加 Pug

在将 Tailwind CSS 添加到我们的项目之前，我们应该安装 Pug。Pug 是一个用于节点的模板引擎，它有一个简化的语法，可以编译成 HTML。它使编写 HTML 代码变得更容易，也更干净。

第一步是在我们的项目中安装 Pug。我们可以通过运行以下命令来安装它:

```
npm install pug
```

一旦安装了 Pug，我们需要创建一个新的目录来存储 Pug 文件。您可以创建一个新目录和一个 pug 文件，如下所示:

```
mkdir views 
touch views/index.pug
```

创建`index.pug`文件后，将以下内容添加到您的文件中:

本教程假设帕格的知识。但是，如果您希望看到 HTML 格式的相同代码，您可以使用 [Pug 到 HTML 转换器](https://pughtml.com/)。如果你想在 Pug 和 HTML 之间转换代码，或者相反，这是一个有用的工具。

# 配置视图和静态文件

在开始编码之前，我们必须设置视图和静态文件。第一步是导入 Node 默认自带的`path`模块。`path`模块允许我们处理目录和文件路径，例如，当我们提供静态资产(如图像)时，我们需要这些。

```
const path = require('path');
```

因此，如上一行所示导入路径模块。现在，让我们看看下面的代码做了什么:

```
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'pug');
app.use(express.static(path.join(__dirname, 'public')))
```

第一行是`app.set('views', path.join(__dirname, 'views'));`，我们告诉 Node 模板的来源是什么。现在，Node 知道在哪里可以找到我们的 Pug 模板。

更进一步，第二行(`app.set('view engine', 'pug');`)告诉节点使用什么引擎。在我们这里，是帕格。

最后一步是指定提供静态资产(如 JavaScript、CSS、图像等)的目录。最后一行`app.use(express.static(path.join(__dirname, 'public')))`，告诉 Node 我们将静态资产存储在一个名为`public`的目录中。

# 更新 index.js

我们需要更新我们的服务器来服务我们之前创建的 Pug 文件。为此，在`app.get`功能中将`res.send('It works');`改为`res.render('index');`。您的“获取”路线应该如下所示:

```
app.get('/', (req, res) => {
    res.render('index');
});
```

运行服务器以确保更改不会破坏我们的应用程序。转到 localhost:3000，您应该看到:

```
Hello world!My starter template
```

# 添加顺风 CSS

本教程的这一步是将 Tailwind 添加到我们的项目中，并对其进行配置。让我们从向我们的项目添加 Tailwind CSS 和 PostCSS 开始:

```
npm install tailwindcss postcss autoprefixer postcss-cli
```

为什么我们还需要添加 PostCSS 和 autoprefixer？首先，PostCSS [允许我们](https://postcss.org/?webdesignrepo)“将现代 CSS 转换成大多数浏览器都能理解的东西，根据目标浏览器或运行时环境确定您需要的 polyfills。”但是我们为什么需要顺风呢？原因是 TailwindCSS 是一个 PostCSS 插件。因此，我们需要一个工具来把现代的 CSS 翻译成浏览器能够理解的东西。

其次，autoprefixer 也是一个 PostCSS 插件。Autoprefixer 使用来自[我可以使用](https://caniuse.com/)的值将供应商前缀添加到 CSS 规则中。换句话说，它确保应用程序在所有浏览器中看起来都一样。

让我们进一步生成`tailwind.config.js`文件。该文件的目的是允许您定制您的 TailwindCSS 安装。它是一个配置文件，您可以在其中添加额外的信息，比如插件、主题、边距、填充，以及所有您需要但 Tailwind 没有的信息。

```
npx tailwindcss init
```

通过运行上面的命令，它会自动创建`tailwind.config.js`文件。如果你想添加定制，我建议检查一下 [TailwindCSS 配置页面](https://tailwindcss.com/docs/configuration)。

这就是将 Tailwind CSS 添加到您的节点项目的全部内容！

# PostCSS

剩下的工作是建立存储 PostCSS 配置的`postcss.config.js`文件。首先，我们必须创建文件，我们可以这样做:

```
touch postcss.config.js
```

创建文件后，向其中添加以下配置:

`postcss.config.js`文件指定了我们的项目使用什么 PostCSS 插件。在我们的例子中，它是 Tailwind CSS 和 autoprefixer。

# 创建必要的文件

在添加和配置了所有的包之后，我们需要为我们的 CSS 创建文件。对于本教程，我选择在“公共”文件夹中创建文件夹`styles`。最后，`tailwind.css`文件保存顺风 CSS，而`style.css`文件包含我们的应用程序使用的最终 CSS。

```
mkdir public 
mkdir public/styles 
touch public/styles/tailwind.css 
touch public/styles/style.css
```

您可以通过运行上面的命令来创建目录和两个文件。`mkdir`创建一个新目录，而`touch`创建一个空文件。另外，在`tailwind.css`中添加以下代码:

```
@tailwind base; 
@tailwind components; 
@tailwind utilities;
```

这段代码将 Tailwind CSS 中的基础、组件和实用程序样式注入到我们的 CSS 中。这些指令在运行时成为实际的 CSS。在运行下一节(`" tailwind:css"`)中的命令后，检查文件`style.css`以查看生成的代码。

# Package.json

我们要做的最后一件事是在我们项目的`package.json`中添加下面一行:

```
"tailwind:css": "postcss public/styles/tailwind.css -o public/styles/style.css"
```

使用上面的命令，我们构建 CSS 并将其添加到我们的应用程序使用的`styles.css`文件中。如果你把 CSS 添加到你的应用程序中，确保每次都运行`npm run tailwind:css`,这样改动才会生效。这样做之后，启动您的应用程序，您应该会看到变化。

# 结论

至此，您应该拥有一个包含 Express、Pug 和 Tailwind CSS 的完整节点应用程序。如果你想检查项目，这里是项目的 [GitHub 链接。请随意使用它作为您项目的开始。](https://catalins.tech/how-to-use-tailwindcss-with-nodejs-express-and-pug?guid=none&deviceId=bd74e7c3-4633-4101-9e97-da3dd2793fb3)

概括地说，我们学习了如何:

*   将 Express、Pug 和 TailwindCSS 添加到节点项目中。
*   将 Pug 设置为应用程序中的视图引擎。
*   为我们的资产建立文件夹。
*   创建一个基本节点服务器。

*如果对 JavaScript 教程感兴趣，我推荐* [*前端高手*](https://catalins.tech/frontend-masters-membership-is-it-worth-it) *！*

*如果你想用技术写作赚钱，去看看那些付钱让你写* *技术文章的* [*网站吧！*](https://catalins.tech/websites-that-pay-you-to-write-technical-articles)

*如果你想学习 JavaScript，我推荐这些* [*5 资源作为初学者学习 JavaScript*](https://catalins.tech/5-best-resources-to-learn-javascript-as-a-beginner)*！*

*谈判你的工资是必不可少的——学习* [*作为一名开发者如何谈判你的工资*](https://catalins.tech/how-to-negotiate-your-salary-as-a-developer) *！*

*用* [*加速你的开发 Git 别名*](https://catalins.tech/git-aliases-what-are-they-and-how-to-use-them) *。*

*如果你想以开发者的身份* [*开博*](https://catalins.tech/how-to-start-your-blog-as-a-developer) *，我推荐阅读《* [*如何以开发者的身份开博*](https://catalins.tech/how-to-start-your-blog-as-a-developer) *》一文！*

*你是否很难跟上科技领域的最新消息？参见* [*作为开发者保持最新状态的一种方法*](https://catalins.tech/one-way-to-stay-up-to-date-as-developer) *！*

*学习* [*如何在 JavaScript*](https://catalins.tech/how-to-use-asyncawait-in-javascript) *中使用 Async/Await！*

GitHub 简介目前风靡一时。了解 [*如何创建 GitHub 个人资料页面*](https://catalins.tech/how-to-create-a-kickass-github-profile-page) *！*

*看看这 7 个* [*资源，帮你通过求职面试*](https://catalins.tech/7-github-repositories-to-help-you-crush-your-job-interviews) *！*

*查看*[*JavaScript ECMAScript 2021 es 2021*](https://catalins.tech/javascript-es2021-you-need-to-see-these-ecmascript-2021-features)*即将推出的新功能！*

*你是初学程序员吗？查看这些* [*编程项目思路适合初学者*](https://catalins.tech/10-programming-project-ideas-for-beginners) *！*

你是在学习编码还是打算做编码？查看 [*免费学习编程的最佳去处*](https://catalins.tech/20-best-places-to-learn-programming-for-free) *！*

[*用这 9 个浏览器扩展提高你的开发者生产力*](https://catalins.tech/my-9-must-have-browser-extensions-for-increased-developer-productivity) *！*

*如果你是 Node.js 的开发者，我建议你查看 Node.js* *中的这些* [*4 种创意设计模式！*](https://catalins.tech/the-4-creational-design-patterns-in-nodejs-you-should-know)

*查看这些惊人的*[*JavaScript ECMAScript 2020 特性*](https://catalins.tech/javascript-es2020-the-features-you-should-know) *！*