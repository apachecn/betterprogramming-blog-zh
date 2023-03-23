# Create React App 3.3 中的新功能

> 原文：<https://betterprogramming.pub/new-features-of-create-react-app-3-3-649f85255e86>

## Create React 应用程序的新特性

![](img/63b50018ef95ea3b543d4e961d2e90a5.png)

[钳工](https://unsplash.com/@benchaccounting?utm_source=medium&utm_medium=referral)对[去毛刺](https://unsplash.com?utm_source=medium&utm_medium=referral)拍照。

[Create React App](https://create-react-app.dev/) 是一个命令行程序，让我们可以轻松地创建一个新的 React 项目，并将该项目构建成我们可以部署的工件。

它由 React 团队创建，为应用程序搭建了一个支架。

在本文中，我们将了解 Create React App 3.3 中提供的很酷的新功能，包括一些 ES2020 功能。

# 自定义模板

我们可以使用自定义模板，通过 Create React App 3.3 创建我们的 React 项目。

例如，如果我们想创建一个 TypeScript React 应用程序，我们可以运行:

```
npx create-react-app foo-app --template typescript
```

其中`foo-app`是我们的应用程序项目的名称。

默认情况下，它附带两个模板:

*   `cra-template`
*   `cra-template-typescript`

我们不需要指定一个`--template`选项来使用第一个模板，因为它是默认的。要搜索更多模板，我们可以访问 NPM 的网站，通过在浏览器中键入以下内容来搜索模板:

```
[https://www.npmjs.com/search?q=cra-template-*](https://www.npmjs.com/search?q=cra-template-*)
```

然后我们可以在设置`template`选项时去掉`cra-template-`前缀。

我们还可以通过创建具有以下结构的文件来构建新模板:

```
my-app/
  README.md (for npm)
  template.json
  package.json
  template/
    README.md (for projects created from this template)
    gitignore
    public/
      index.html
    src/
      index.js (or index.tsx)
```

# 可选的链接和无效合并运算符

这是 ES2020 即将推出的一项功能。我们现在可以使用它来编写 React 应用程序，并使用 Create React App 3.3 或更高版本。

可选的链接操作符由`?.`表示，我们可以用它来引用可能是`null`或`undefined`的嵌套属性。

如果我们试图访问嵌套很深的对象，这将为我们节省大量的`null`和`undefined`检查。例如，我们可以如下使用它:

在上面的代码中，我们在下面的代码中使用了可选的链接运算符:

```
<p>{this.state.msg?.bar?.baz}</p>
```

因为`this.state.msg`可能没有`bar`属性。

然后，当我们点击“点击我”按钮时，我们会看到单词“foo”被打开和关闭。

nullish 合并运算符用于设置表达式的默认值。由`??`符号表示。例如，我们可以将默认值添加到上面的示例中，方法是:

在上面的代码中，我们有:

```
<p>{this.state.msg?.bar?.baz ?? "bar"}</p>
```

如果`this.state.msg?.bar?.baz`是`null`或`undefined`，它将显示`'bar'`。

这与`||`不同，因为`??`只在之前的表达式为`null`或`undefined`时返回默认值，而不是像`||`那样返回所有的假值。

如果我们使用 TypeScript，那么 TypeScript 版本也必须更新才能使用这些特性。

![](img/1ffd9e8a6991e906eb699b353feb5e02.png)

照片由 [Daryan Shamkhali](https://unsplash.com/@daryan?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

# 数字分隔符

数字分隔符也是 ES2020 发布的一个新功能，尽管 Chrome 等一些浏览器已经在其最新版本中支持它。

我们可以使用数字分隔符将数字分成块，以便人们可以轻松阅读。

例如，我们可以如下使用它:

在上面的代码中，我们使用下划线将数字分成组，以便于阅读。我们不必把他们分成三人一组。

它也适用于十进制数。

# 无意外多行

ESLint 规则没有被禁用，因为它与 Prettier 不兼容。

然而，我们仍然可以向我们的`.eslintrc`添加以下内容来重新启用它:

# 升级到 3.3

要升级以创建 React App 3.3 并利用所有这些新功能，请运行:

```
npm install --save --save-exact react-scripts@3.3.0
```

在我们现有的 React 项目中使用 NPM，或者如果我们使用 Yarn，运行:

```
yarn add --exact react-scripts@3.3.0
```

# 结论

Create React App 3.3 是一个次要版本，但它提供了有用的新功能，使开发 React 应用程序变得更容易。

在 ES2020 官方标准发布之前，ES2020 的可选链接和无效合并运算符以及数字分隔符等功能现在都可以使用。