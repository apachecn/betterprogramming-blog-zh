# 转换角度应用程序以做出反应

> 原文：<https://betterprogramming.pub/angularjs-react-one-component-at-a-time-4e39c0b14916>

## 一次一个组件

![](img/c1c7dbb98a293e0eed4966a76eb47025.png)

[南安](https://unsplash.com/@bepnamanh?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

你如何吃掉一头大象？希望你对此没有答案。但是如果一个巨大的、传统的 AngularJS 是你的*隐喻*大象，那么我想分享一个我最近使用的方法，至少是朝着现代框架的方向迈出了一步。

我们今天将使用的工具是[n React](https://github.com/ngReact/ngReact)，这是一个库，允许您将新的 React 组件转换为 Angular 指令，以便在旧的(我的用例是 angular@1.3)应用程序中使用。

我的 AngularJS 应用程序由未编译的静态资产组成，由一个 Spring Boot 应用程序提供服务，该应用程序也充当后端。用户点击该站点并检索`index.html`及其所有指定的资源。

显然，这并不理想。JavaScript 没有“构建过程”。依赖性管理是困难的。我说过它的角度是 1.3 吗？但是不要陷入简单地批评现有事物的陷阱。我们真正想要的(我们的问题陈述)是能够在现代 web 开发框架中创建新功能。

*   选项 1 是完全重写应用程序。虽然大多数公司没有时间和/或资源来实施这一方案，但至少应该予以考虑。
*   选项 2 是在新的框架中创建一个单独的应用程序，两者都提供服务。我会考虑在新的框架中运行传统的应用程序(反之亦然)也属于这一类。这种选择有几个复杂之处。一种是从一个“侧面”到另一个侧面——这通常很简单。下一步是复制任何现有的公共服务(例如，将请求变异到后端的`apiService`拦截器)。所有这些可能都必须在新的框架中重新创建。
*   选项 3 是使用一个库来获取用新框架编写的组件，并将它们“翻译”成遗留应用程序可以理解的东西。这种方法允许你一步一步来。你在一个现代的框架中编写新的特性并修复 bug，直到足够多的代码库被转换，这才是有意义的。

在研究转换 angular 1.x 反应的最佳方式之前，我不认为我知道第三种选择。有几个库能够执行这个功能，但是我最终选择了[n react](https://github.com/ngReact/ngReact)。该库的工作方式是获取一个 React 组件，并将其转换为 AngularJS 指令。值得注意的是，ngReact 将允许你传递任何你可以作为依赖注入的东西(比如一个角度服务)作为你的组件的道具，所以在你准备好之前没有必要重写你的`apiService`。

让我们对上面描述的应用程序进行一些修改，替换几个页面上的`Breadcrumb`元素:

![](img/854c129adc117bfbc5a7ea7acad103b3.png)

首先，在我们的 AngularJS 应用程序的入口点，我们将添加两个新模块:

*   应用程序的注入依赖项:`react`。
*   一个单独的模块，作为所有新的 React 组件指令的声明点。

根据现有应用程序中管理依赖关系的方式，下一部分看起来会有所不同。在本例中，我们已经将新的*外部*依赖项(`react`、`react-dom`和`ngReact`)的缩小模块放在了`lib`目录下的应用程序中。`react-components.js`将是我们新指令的上述声明点。`dist/bundle.js`根据应用程序的构建配置，可能看起来有所不同，但在本例中，它是 React 组件的 webpacked 输出。

现在，我们将使用 ngReact 向我们的 angularJS 应用程序注册一个指令，转换新的 React 组件。`reactDirective`函数有四个参数:

1.  与稍后将声明的组件的 AngularJS `value`匹配的字符串。
2.  组件使用的属性数组，当在 HTML 中使用时，将传递给指令。
3.  一个配置对象，用于覆盖指令的一些默认行为([参见文档](https://github.com/ngReact/ngReact#the-reactdirective-service))。
4.  任何有角度的可注入物(服务等。)您希望在您的组件中使用它。

并使用新的指令:

组件`Breadcrumb`的实现并不令人惊讶。请注意我们是如何使用 Angular `navService`的，就像我们可能将道具传递给组件的任何其他模块一样。

最后，对于每个组件指令，我们还将注册一个角度`value`(我们在上面将其用作`reactDirective`函数的第一个参数)。你可以在应用程序的任何地方这样做，但我选择在`react-components/index.js`这样做，我也将把它作为我的 webpack 入口点。当我添加额外的组件指令时，我将为它们重用这个文件。