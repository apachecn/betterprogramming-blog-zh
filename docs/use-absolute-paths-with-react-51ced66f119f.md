# 对 React 使用绝对路径

> 原文：<https://betterprogramming.pub/use-absolute-paths-with-react-51ced66f119f>

## 导入模块从来都不容易

![](img/084da344a53883d222ed9d6362ab11ca.png)

绝对路径

> ***最后更新于 2021 年 6 月 4 日。***

也许这对你来说不是问题，但是使用 ***绝对路径*** 导入模块是我不能缺少的东西。

在本文中，我将带您使用 [*Webpack*](https://webpack.js.org/) *为您的项目添加对绝对路径的支持。*

## TL；博士:

不喜欢读书？下面是一个使用[*create-react-app*](https://create-react-app.dev/)并启用绝对导入的回购链接。实际上不需要`eject`。

[https://github . com/3 tmaan/create-react-app-with-absolute-imports](https://github.com/3tmaan/create-react-app-with-absolute-imports)

默认情况下，*相对路径*是 React 和其他框架支持的导入模块的方式。您通常会看到或写下类似这样的内容:

```
import MyModule from './MyModule';
```

看起来很干净！没错，但是如果你有一个复杂的文件夹结构，并且想在其中**向上**移动，该怎么办呢？你最终会得到这样的结果:

```
import MyModule from '../../../components/MyModule'; 
```

这看起来很难看——但是除此之外，使用*相对路径*有一些主要问题:

*   越陷越深，情况越糟
*   这使得代码重构相当困难
*   如果您想将代码提取到 NPM 模块中供外部使用，您必须更改整个代码库

如果您能让前面的代码示例看起来像这样，会怎么样呢:

```
import MyModule from 'Components/MyModule';
```

整洁干净，对吧？

实现这种行为很简单。这可以通过使用**解析器—** 来完成，解析器是一个库，它通过模块的**绝对路径**来帮助**定位**模块。

在 **Webpack** 配置文件中，添加`resolve.alias`以创建别名，并使**更容易导入**模块。

```
module.exports = {
  //...
  resolve: {
    alias: {
      Components: path.resolve(__dirname, 'src/components/')
    }
  }
};
```

例如，所有位于`src/components/`内部的模块现在都可以使用*绝对路径*。

# 奖金

如果出于任何原因你不想接触你的 **Webpack** 配置或者你正在使用[*create-react-app*](https://facebook.github.io/create-react-app/docs/getting-started)并且你不想使用`eject`命令，你可以使用一个[*Babel*](https://babeljs.io)*插件，名为[*Babel-plugin-module-resolver*](https://github.com/tleunen/babel-plugin-module-resolver)*。* 要激活这个插件，创建一个`.babelrc`文件(如果它还不是你的文件夹结构的一部分)并添加以下代码:*

```
*{
  "plugins": [
    ["module-resolver", {
      "root": ["./"],
      alias: {
        Components: './src/components')
      }
    }]
  ]
}*
```

*您可以添加任意数量的别名。*

*现在有了`.babelrc`文件，剩下的步骤就很少了，因为[*react-scripts*](https://www.npmjs.com/package/react-scripts)[*【CRA】*](https://create-react-app.dev/docs/getting-started/)不允许你覆盖 **Webpack** config，这就是为什么你还需要 2 个包来为你的应用程序提供所有底层操作。*

```
*npm install customize-cra react-app-rewired --dev
//or
yarn add customize-cra react-app-rewired --dev*
```

*安装后，您需要重新配置`package.json`。
典型的[](https://create-react-app.dev/docs/getting-started/)*剧本片段看起来是这样的:**

```
**"scripts": {
   "start": "react-scripts start",
   "build": "react-scripts build",
   "test": "react-scripts test",
}**
```

**它运行`react-scripts`命令。然而，您需要连接到`react-app-rewired`来重新定义配置。**

```
**"scripts": {
    "start": "react-app-rewired start",
    "build": "react-app-rewired build",
    "test": "react-app-rewired test",
}**
```

**顺便说一下，不需要修改`eject`脚本。**

**最后但同样重要的是，您需要创建一个`config-overrides.js`文件，代码如下:**

```
**const { useBabelRc, override } = require("customize-cra");
module.exports = override(useBabelRc());**
```

**这实际上会告诉 **Webpack** 使用这个定制的`.babelrc`文件。**

**仅此而已:)**

# **我的代码编辑器能识别这些路径吗？**

**嗯，不…但是为了让你的 **IDE** 足够聪明，以便在导入模块时理解*绝对路径*，你必须做以下事情:**

## **对于 VSCode**

**创建一个`jsconfig.js`，并向其中添加以下代码:**

```
**{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "components/*": ["src/components/*"]
    }
  },
  "exclude": ["node_modules"] 
}**
```

## **对于 IntelliJ Idea**

**右键点击`src`文件夹**使**目录成为**资源根目录**。**

**有了这些配置，您就可以开始享受导入您喜欢的模块了。**

**祝编码愉快！**

# **资源**

**回购链接:
[https://github . com/3 tmaan/create-react-app-with-absolute-imports](https://github.com/3tmaan/create-react-app-with-absolute-imports)**