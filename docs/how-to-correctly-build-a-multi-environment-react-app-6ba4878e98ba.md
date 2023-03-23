# 如何正确构建多环境 React App

> 原文：<https://betterprogramming.pub/how-to-correctly-build-a-multi-environment-react-app-6ba4878e98ba>

## 为每个环境保留一个专用的构建文件夹

![](img/d7e81f00b410553a89995c0e33353220.png)

[Jexo](https://unsplash.com/@jexo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片。

在开发 React 应用程序时，您可能需要[处理不同的环境](/environment-based-error-handling-with-spring-boot-and-kotlin-b36b901135ad)。根据我的经验，你至少应该考虑以下三种[环境](https://en.wikipedia.org/wiki/Deployment_environment):

*   发展
*   脚手架
*   生产

在将 React 应用程序部署到最后两个环境之一之前，您需要构建它。这通常通过运行一个命令来实现，该命令旨在创建一个保存在项目的`build`文件夹中的可部署构建。

[](/environment-based-error-handling-with-spring-boot-and-kotlin-b36b901135ad) [## Spring Boot 和科特林基于环境的错误处理

### 简化登台环境中的调试

better 编程. pub](/environment-based-error-handling-with-spring-boot-and-kotlin-b36b901135ad) 

这种方法有一个巨大的缺点。例如，您不能并行启动许多构建命令。事实上，它们都依赖于同一个文件夹。因此，在处理多种环境时，这可能是一个瓶颈。

另外，由于每个环境都共享相同的构建文件夹，所以很难直观地判断出您最后一次构建的目标环境是什么。这也会导致错误。

让我们看看如何通过在 React 中正确构建多环境应用来避免这种情况。

# 1.安装所需的依赖项

首先，您需要将`[react-app-rewired](https://www.npmjs.com/package/react-app-rewired)`和`[env-cmd](https://www.npmjs.com/package/env-cmd)`添加到项目的依赖项中。您可以使用以下命令安装这两者:

```
npm install react-app-rewired env-cmd --save
```

为了能够指定定制的构建路径，您必须更改您的 [webpack 配置](https://webpack.js.org/configuration/)。如果你使用`[create-react-app](https://reactjs.org/docs/create-a-new-react-app.html)`创建了你的应用程序，你可能会被迫[退出你的项目](https://create-react-app.dev/docs/available-scripts/#npm-run-eject)——如果没有严格的要求，这应该避免。

这就是`react-app-rewired`发挥作用的地方！

*注意:如果你的项目已经使用了* `*react-app-rewired*` *，可以跳过下面两步。*

多亏了它，你可以很容易地调整`create-react-app`网络包配置。为了让一切正常工作，您需要遵循以下两个步骤:

1.  在根目录下创建一个有效的`config-overrides.js`文件。
2.  在您的`package.json`文件的`scripts`部分，将现有的引用从`react-scripts`更改为`react-app-rewired`。

以前

在...之后

您刚刚重新连接了 React 应用程序，现在可以定义基于环境的构建路径了。

此外，`react-app-rewired`还能让你实现其他不可思议的目标。例如，[定义绝对路径别名以使你的导入易于重构](https://javascript.plainenglish.io/avoid-the-fear-of-refactoring-with-absolute-imports-in-react-804bce61dc31)。

[](https://javascript.plainenglish.io/avoid-the-fear-of-refactoring-with-absolute-imports-in-react-804bce61dc31) [## 避免对 React 中绝对导入的重构的恐惧

### 在 React 中使用绝对路径别名使导入易于重构

javascript.plainenglish.io](https://javascript.plainenglish.io/avoid-the-fear-of-refactoring-with-absolute-imports-in-react-804bce61dc31) 

# 2.定义环境文件

现在，是时候为您的每个环境定义一个`.env`文件了。这样的文件应该包含其值取决于当前环境的任何变量。

让我们定义两个`.env`文件，我们将根据当前环境在其中放置构建路径。

登台环境文件

生产环境文件。

*注意:如* [*Create React App 文档*](https://create-react-app.dev/docs/adding-custom-environment-variables/) *中所述，每个环境变量都必须以* `*REACT_APP_*` *开头。*

# 3.使用环境变量

要从环境文件中读取构建路径并将其用作构建的基本路径，定义一个`config-overrides.js`文件，如下所示:

如您所见，我确保了`appBuild`变量有一个有效的回退值。这是极其重要的。空的、`null`或`undefined` 字符串可能会导致构建过程严重出错，潜在地影响您的整个代码库。

然后，您应该为每个`.env`文件定义一个构建命令。通过使用`env-cmd`，您可以通过使用`[-f](https://www.npmjs.com/package/env-cmd#using-custom-env-file-path)` [标志](https://www.npmjs.com/package/env-cmd#using-custom-env-file-path)向您的构建命令传递一个定制的`.env`文件。

确保您的`package.json`文件的`scripts`部分包含以下两行:

```
"build": "react-app-rewired build",
"build:staging": "env-cmd -f .env.staging npm run build"
```

默认情况下，将使用`env.production` 文件，因此不需要在第一个命令中指定它。

现在，您可以使用以下命令创建一个基于环境的构建。

对于登台环境:

```
npm run build:staging
```

对于生产环境:

```
npm run build
```

构建将被放置在`REACT_APP_BUILD_PATH`环境变量中定义的目标路径中。如果您遵循这个示例，您的试运行构建将在`build-staging`文件夹中创建，而您的生产构建将在`build`文件夹中创建。

瞧啊！现在，您的每个环境都有一个专用的构建文件夹。

# 结论

今天，我们学习了如何为 React 应用程序的每个环境定义一个构建文件夹。这样，您可以并行启动不同的构建命令，并确保它们都有自己专用的目标文件夹。正如我刚刚展示的，这可以通过使用`react-app-rewire`和`env-cmd`很容易地实现。此外，这种方法可以很容易地扩展到任何数量的环境。

感谢阅读！我希望这篇文章对你有所帮助。请随意留下任何问题、评论或建议。