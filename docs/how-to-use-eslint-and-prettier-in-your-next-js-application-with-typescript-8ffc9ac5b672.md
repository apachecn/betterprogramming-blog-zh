# 如何在带有 TypeScript 的 Next.js 应用程序中使用 ESLint 和 appearlier

> 原文：<https://betterprogramming.pub/how-to-use-eslint-and-prettier-in-your-next-js-application-with-typescript-8ffc9ac5b672>

## **以相同的风格让每个人都在同一页面上**

![](img/aac78fd677026a96e6affd6d98ac6c21.png)

[赛·基兰·阿纳加尼](https://unsplash.com/@_imkiran?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片。

自动化 lint 工具可以帮助开发人员节省时间，并让每个人都在同一页面上。当团队中的每个人都遵循相同的规则时，你就不必浪费时间讨论代码风格问题。这些工具可以捕捉许多语法和类型错误。

将 [ESLint](https://eslint.org/) 与[appellister](https://prettier.io/)结合起来是一种对代码进行自动语法扫描和重新格式化的好方法。这将确保在缩进、间距、分号等方面遵循一致的规则。

让我们设置这些技术，以便在使用[类型脚本](https://www.typescriptlang.org/)的 [Next.js](https://nextjs.org/) 项目中使用它们。如果你正在开始一个新项目，你可以[阅读我的文章](https://medium.com/better-programming/how-to-set-up-next-js-with-typescript-5f912766c88)关于如何用 TypeScript 设置 Next.js。

# 埃斯林特

让我们从添加核心的 ESLint 林挺库、Lint 类型脚本代码的解析器、特定于类型脚本的插件和特定于 React 的插件开始。

在终端中，转到项目的根目录，运行以下命令:

```
yarn add -D eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin eslint-plugin-react
```

我们现在应该在项目的根目录下创建一个`.eslintrc.json`文件。我们可以在这个文件中添加我们的林挺规则:

```
touch .eslintrc.json
```

您已经可以将默认选项添加到该文件中。我们将继续添加我们的选项:

```
{
  "parser": {},
  "extends": [],
  "rules": {},
  "globals": {}
}
```

默认情况下，ESLint 依赖解析器来读取 JavaScript 代码。我们使用[@ typescript-eslint/parser](https://github.com/typescript-eslint/typescript-eslint#readme)(可以读取 TypeScript 的替代解析器)，所以我们需要告诉 ESLint 改用它。除此之外，我们使用[@ typescript-eslint/eslint-plugin](https://github.com/typescript-eslint/typescript-eslint)(可以打开或关闭的规则列表)。我们还扩展了插件 [react/recommended](https://github.com/yannickcr/eslint-plugin-react) ，它包含 react 特定的林挺规则。

为了配置解析器和扩展插件，我们需要修改我们需要更新配置的文件，如下所示:

Next.js 不要求您将 React 导入每个组件，这意味着如果您在 Next.js 应用程序中使用这个配置，您将开始得到错误。我们可以通过将`React`添加到`global`中并关闭`react-in-jsx-scope`规则来解决这个问题。

我们的最终配置将如下所示:

# 较美丽

现在让我们添加更漂亮的和一些插件，使它能很好地与 ESLint 一起工作:

```
yarn add -D prettier eslint-config-prettier eslint-plugin-prettier
```

[eslint-config-appellister](https://github.com/prettier/eslint-config-prettier)将禁用任何可能干扰现有更漂亮规则的林挺规则，并且[ESLint-plugin-appellister](https://github.com/prettier/eslint-plugin-prettier)将作为 ESLint 的一部分运行更漂亮的分析。

让我们将它们添加到我们的 ESLint 配置中，以完成我们的 ESLint 配置。确保将更漂亮的放在最后，以便它可以覆盖其他配置:

## 配置更漂亮

更漂亮的是固执己见，故意限制选项的数量([读为什么](https://prettier.io/docs/en/why-prettier.html))。如果你愿意，你可以通过在你的项目的根目录下创建一个`.prettierrc`文件来覆盖一些漂亮的规则:

```
touch .prettierrc
```

以下配置是一个示例文件。我个人使用这个作为我的设置，但是你可以在官方文档中找到所有可用的配置[。](https://prettier.io/docs/en/configuration.html)

```
{
	"semi": true,
	"singleQuote": true
}
```

## 添加一个 git 挂钩

可选地，您可以使用 [husky](https://github.com/typicode/husky) 和 [pretty-quick](https://github.com/azz/pretty-quick) 来添加一个 git 挂钩，它将始终 lint 并格式化您更改的文件。

首先，将它们作为开发依赖项安装:

```
yarn add pretty-quick husky -D
```

现在将以下`husky`和`lint-staged`配置添加到您的`package.json`中:

```
"scripts": {
  ...
  "lint": "eslint --ext .ts,.tsx",
},
```

在您的`package.json`中添加预提交钩子:

```
"husky": {
  "hooks": {
    "pre-commit": "pretty-quick --staged && npm run lint"
   }
},
```

现在，每当您提交您的更改时，它都会格式化并 lint 您的新代码。

# 代码编辑器扩展

如果你还没有这样做，我推荐你为你的代码编辑器安装[漂亮的](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)和 [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) 扩展。如果您不想每次都手动格式化文件，也可以在保存时格式化。我强烈推荐这个。对于 Visual Studio 代码，您需要做的就是打开 VSCode 用户设置/首选项，并将`Format On Save`选项设置为`true`。

就是这样！感谢阅读。我希望它有帮助。