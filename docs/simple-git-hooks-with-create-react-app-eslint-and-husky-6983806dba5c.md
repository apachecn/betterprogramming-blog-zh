# 与 Create React App、ESLint 和 Husky 的简单 Git 挂钩

> 原文：<https://betterprogramming.pub/simple-git-hooks-with-create-react-app-eslint-and-husky-6983806dba5c>

## 让您的 Git 挂钩自动运行您的测试和 lint 代码

![](img/90057ea98e69a0ded04729d0e99a1ebc.png)

由[布鲁克·安德森](https://unsplash.com/@brookanderson)在[上拍摄的照片](https://unsplash.com/photos/gTQbZXL417Q)

Git 挂钩是 Git 在提交、推送等事件之前或之后执行的脚本。在 Git 允许您签入代码之前，这是一种非常酷且快速的验证代码的方式。

在本文中，我们将看一个使用 Husky 库在 React 应用程序上实现 Git 挂钩的简单示例。然后，我们将创建一个 Git 挂钩，通过在每次提交之前针对您的代码调用 ESLint 来验证您的更改。

惟一的先决条件是在您的机器上安装了 Node (well 和 Git)。我将假设您知道如何创建 Git 存储库。将您的存储库放入您计划放置 React 应用程序的目录中。

# 入门指南

先说 React app。网上有很多用 React 创建应用程序的模板，但最流行的是脸书自己的 Create React 应用程序。

Create React App 不需要任何配置，只需一个命令即可创建一个非常简单的 React 应用程序:

```
npx create-react-app my-app-name
```

现在您应该有了 React 应用程序。如果您想验证一切正常，您可以从您的终端运行``npm run start``并导航到 [http://localhost:3000](http://localhost:3000) 。

# 设置 ESLint 和 Husky

好了，现在是有趣的部分。让我们设置 ESLint 和 Husky。

让我们通过从您的终端运行`“npm install -g eslint”`在您的机器上全局安装 ESLint。

接下来，导航到您的 React 应用程序的根目录；这是`package.json`文件所在的地方。从你的终端启动 ESLint。

```
eslint --init
```

回答关于你的 ESLint 用法的问题，比如你正在使用的框架，你的代码风格等等。随着 ESLint 的发展，这组问题也在不断变化，所以我不打算在这里发布。毕竟，再过几个月，它们可能就过时了。

请确保为您的框架选择 React。

最终，你会问这样一个问题，“你想如何为你的项目定义一种风格？”选择:“使用流行的风格指南。”

你会得到一个后续的问题，“你想遵循哪个风格指南？”选择:[标准](https://github.com/standard/standard)

如果您现在通过从终端运行`“eslint src/**/*.js”`来运行 ESLint，您会看到相当多的错误和警告(可能超过 50 个)。

让我们修理他们！

大多数错误(90%)可以由 ESLint 自己修复，所以运行 ESLint 时要有一个修复标志:

```
eslint --fix src/**/*.js
```

您应该只剩下一些错误，它们应该是这样的:

```
1:8 error ‘React’ is defined but never used no-unused-vars3:8 error ‘App’ is defined but never used no-unused-vars5:1 error ‘it’ is not defined no-undef
```

为了修复这些错误，我们必须告诉 ESLint 考虑到我们的应用程序正在 Create React 应用程序上运行，并且我们正在使用 Jest 作为测试运行程序。

打开您的 ESLint 配置文件，可能名为`.eslintrc`。根据您选择的配置格式，您的文件名可能会有所不同。我推荐 JSON 格式。

我们需要在`.eslintrc`文件中做一些定制

在`“env”:`部分，添加`jest`。不添加`jest`会使 ESLint 错误出现在 Jest 特有的函数上，如`describe`和`it`:

```
"env": { "browser": true, "es6": true, "jest": true
}
```

在`“extends”:`部分，添加`“plugin:react/recommended”`:

```
"extends": [ "standard", "plugin:react/recommended"]
```

现在你都准备好了。如果您现在尝试运行 ESLint，它将会无错误地通过。

# 一些额外的定制

如果您真的不想总是从终端手动运行 linter，请添加运行 linter 的 npm 脚本:

打开`package.json`文件。在`“scripts”:`部分，添加新的 ESLint 脚本。

```
“scripts”: {
  ...“lint”: “eslint src/**/*.js”,“lint:fix”: “eslint --fix src/**/*.js”},
```

您可以通过调用以下命令使用 npm 运行这些脚本:

```
npm run lintnpm run lint:fix
```

此时，您应该已经准备好使用 ESLint 了。现在使用 Husky 设置 Git 挂钩真的很容易。

# 设置 Husky

最后一部分，我们最后来设置哈士奇吧！

导航到根目录安装 Husky(这是`package.json`所在的目录)。打开终端并运行:

```
npm install husky --save-dev
```

成功安装后，将您的`“husky”:`部分添加到您的`package.json`文件中。

现在我们有了用于提交和推送操作的 Git 挂钩。

每次运行 Git commit 时，Husky 都会运行一个预提交脚本(`“npm run lint:fix”`)，并且只在验证通过时才允许您提交更改。这与 Git 推送的方式类似。

# 最后一件事

如果我们想在提交前和推送前运行测试和 lint 脚本(或任何其他更复杂的脚本)，该怎么办？

我们可以通过在`package.json`中添加一个新脚本来实现:

设置`CI=true`是为了确保 Jest 只运行一次测试，并且不会在监视模式下运行测试，因为监视模式对 Git 挂钩没有意义。

顺便说一下，上面的语法是针对 Windows 的。在 Mac 上，应该是这样的:

```
“test:all”: “CI=true react-scripts-ts test && npm run lint”
```

现在，我们需要做的就是为预提交或预推送挂钩使用一个新的脚本:

恭喜你！现在您有了 Git 挂钩，它将在您每次提交代码时自动运行您的测试并 lint 您的代码。

很方便，是吧？