# React 设置中需要的 4 个基本 ESLint 插件

> 原文：<https://betterprogramming.pub/4-essential-eslint-plugins-you-need-in-your-react-setup-824b419ce598>

## 专注于编码，让他们帮助你完成剩下的工作

![](img/8de0d7ffb655c05ef3737d1deb9f8d6b.png)

由 [ThisisEngineering RAEng](https://unsplash.com/@thisisengineering?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/collections/8403327/backgrounds-for-website?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄。

在编写代码的过程中，我学到了一个合适的设置环境如何提高您的生产率、代码可读性、更一致地保留代码的能力，并帮助您避免任何错误。换句话说，它提高了代码的质量。

ESLint 是我迄今为止遇到的最流行的 JavaScript 检查器。基本上，它是一个识别和报告 ECMAScript/JavaScript 代码中模式的工具。它可以快速分析您的代码，发现代码中的任何问题，甚至自动修复它们。

在本文中，我们将探讨 React 设置中需要的四个基本 ESLint 插件。但是在继续之前，我需要对你说实话:我不喜欢漫长的教育旅程。这是因为我有时会陷入无尽的教程地狱。我真的很讨厌那样。

相反，我发现当我学习的东西更实用、更简洁、更简短时，我能学到更多。你的大脑无法消化和保留很多东西，即使消化和保留，也只能持续几个小时或几天。这些是我目前为止的发现。我以后会写一篇关于这个话题的文章。

你肯定与我不同，但根据科学家的说法，我们的大脑以类似的方式工作。让我们避开那个有争议的话题，直接进入真正重要的问题。

# 反应的 ESLint

ESLint 的默认解析器和核心规则只支持最新的最终 ECMAScript 标准。那是什么意思？这意味着您不能运行它来检查实验特征。然而，由于 ESLint 允许我们通过插件增加它的功能，例如，你可以使用定制的解析器。所以你可以使用 Babel 的解析器(`babel-eslint`)，它允许 ESLint 在 Babel 转化的源代码上运行。

# 将 ESLint 安装为开发依赖项

```
npm install eslint --save-dev

# or

yarn add eslint --dev
```

# React 应用程序需要的 4 个 ESLint 插件

正如本文的标题所示，下面四个插件是为您的 React 设置推荐的。

注意:所有这些都需要在安装之前安装 ESLint 包。

# 1.巴贝尔-埃斯林

用 ESLint 处理所有有效的 Babel 代码。但是在谈论这个插件之前，请只在使用 ESLint 本身还不支持的类型(流)或 ECMAScript 实验特性时使用它。

既然通知到了，就该说说了。允许你用 ESLint 处理所有有效的 Babel 代码。基本上，它是一个解析器，允许 ESLint 在由 Babel 转换的源代码上运行。

## 安装 babel-eslint

```
npm install babel-eslint --save-dev
# or
yarn add babel-eslint -D
```

# 2.eslint-插件-导入

[这个插件](https://www.npmjs.com/package/eslint-plugin-import)的目的是支持 ECMAScript 2015(ES6)的林挺和更高版本的导入/导出。基本上，它可以防止文件路径和导入名称的拼写错误。它试图让你对进口商品有良好的习惯。

它有不同的规则，比如静态分析、有用的警告、模块系统和风格指南。

仅提几条规则:

*   确保导入指向可以解析的文件/模块(`no-unresolved`)。
*   确保命名导入对应于远程文件中的命名导出(`named`)。
*   确保默认导出存在，给定默认导入(`default`)。
*   报告可能不明确的解析目标(`script`对`module` ) ( `unambiguous`)。
*   报告常见的`require`呼叫和`module.exports`或`exports.*` ( `no-commonjs`)。
*   确保命名导入对应于远程文件中的命名导出(`named`)。
*   给定默认导入(`default`)，确保默认导出存在。

要安装它，只需运行以下命令:

```
npm install eslint-plugin-import --save-dev
```

# 3.eslint-plugin-jsx-a11y

这是针对 JSX 元素的可访问性规则的静态 AST 检查器。换句话说，它的任务是帮助你在 JSX 上使用与可访问性相关的适当的东西。例如:

*   确保`autocomplete`属性是正确的，并且适用于与其一起使用的表单域。
*   确保所有需要可选文本的元素都包含有意义的信息。
*   确保`autoFocus`支柱没有用在元件上。自动对焦元件会给视力正常和视力不正常的用户带来可用性问题。

感谢 Ryan Florence 构建了这个强大的运行时分析工具。

## 安装它

```
*# npm*npm install eslint-plugin-jsx-a11y --save-dev*# yarn*yarn add eslint-plugin-jsx-a11y --dev
```

# 4.eslint-plugin-react-hooks

这是我最喜欢的列表。坦率地说，几个月前我还不知道，因为 React hooks 是 React 生态系统中的新成员，我没有足够的时间来了解它。

这个插件帮助我们执行钩子的规则。

## 只调用顶层的钩子

不要在循环、条件或嵌套函数中调用钩子。这使您可以确保每次呈现组件时都以相同的顺序调用挂钩。

## 仅从 React 函数调用挂钩

不要从常规的 JavaScript 函数中调用钩子。确保您的钩子是从 React 函数组件或自定义钩子中调用的。

但是，不要慌。`eslint-plugin-react-hooks`将保证以前的规则。同时，您可以专注于您的代码。

## 安装它

```
*# npm*npm install eslint-plugin-react-hooks --save-dev*# yarn*yarn add eslint-plugin-react-hooks --dev
```

# 结论

当然，现在有更多的 React 插件。他们所有人的使命都是让我们作为开发人员的生活更加轻松，让我们的代码更加一致和可读。本文的目的是介绍我的四个最爱，你应该在你当前或未来的 React 项目中使用它们。

感谢阅读！我希望这篇文章对你有帮助。