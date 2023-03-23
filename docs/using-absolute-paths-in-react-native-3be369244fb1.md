# 在 React Native 中使用绝对路径

> 原文：<https://betterprogramming.pub/using-absolute-paths-in-react-native-3be369244fb1>

## 如何建立理想的项目结构

![](img/1be56b3d85a7647b3bc122483c62cc3a.png)

照片由[尼克·费因斯](https://unsplash.com/@jannerboy62?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

您可能遇到过 React Native 中相对路径过于复杂的问题。让我们看看这是什么样子:

```
import MyComponent from "./my-component";
```

突然间，我们的项目结构变得复杂起来，并且会有许多嵌套的文件夹:

```
import MyComponent from "../../../../components/MyComponent";
```

那是一条可怕的路！但幸运的是，我们有一个解决方案。让我们一起克服它。

假设我们有这样的项目结构:

```
src
--components
----MyComponent.js
--constants
----colors.js
--pages
----home
------index.js
index.js
```

您可以简单地在想要导入的文件夹中创建一个`package.json`文件。是的，乍一看这可能有点奇怪，但是通过练习，你会习惯的。它应该有一个以文件夹名为值的属性。让我们在 components 文件夹中创建我们的`package.json`文件。

`package.json`应该是这样的:

```
{
  "name": "components"
}
```

接下来，您可以使用该名称作为绝对路径来导入它:

```
import MyComponent from "components/MyComponent";
```

如果我们使用`React-Native + TypeScript`，帮助来自一个特殊的库:[react-native-typescript-transformer](https://github.com/ds300/react-native-typescript-transformer)。您可以无缝地使用带有 React Native 的 TypeScript 来创建绝对路径:

```
yarn add --dev react-native-typescript-transformer
```

配置打字稿`tsconfig.json`:

```
{
  "compilerOptions": {
    "target": "es2015",
    "jsx": "react",
    "noEmit": true,
    "moduleResolution": "node",
    "paths": {
      "src/*": ["./*"],
    },
  },
  "exclude": [
    "node_modules",
  ],
}
```

在项目的根目录中，扩展`metro.config.js`，使其包含`transformer.babelTransformerPath`属性:

```
module.export = {
  transformer: {
    babelTransformerPath: require.resolve('react-native-typescript-transformer')
  }
};
```

现在你可以用我们在上面用 TypeScript 反应本地的方法来制作你的`package.json`文件。

我希望这篇文章对你有用。

# 资源

*   [如何在 React Native 中使用绝对路径](https://medium.com/@davidjwoody/how-to-use-absolute-paths-in-react-native-6b06ae3f65d1)
*   [用绝对路径反应本机导入](https://medium.com/react-native-zone/english-react-native-import-with-absolute-path-2fc93faca1bb)
*   [关于 React Native 如何配置绝对路径的问题](https://github.com/facebook/react-native/issues/3099#issuecomment-221815006)
*   用 React Native 打字稿