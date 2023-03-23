# 在模块联合中使用动态远程的 4 种方式

> 原文：<https://betterprogramming.pub/4-ways-to-use-dynamic-remotes-in-module-federation-985e9fb69817>

## 如何动态配置联邦远程模块的 URL。

![](img/986d15d61a4be6074ada3bb32e29785a.png)

由 [Mourizal Zativa](https://unsplash.com/@mourimoto?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

如何将相同的微前端应用程序部署到测试、试运行和生产环境中？您的应用程序如何同时支持本地、云和混合部署？如何扩展同时在架构的不同部分工作的多个团队？动态引入新的远程应用程序？

答案是动态定义的远程应用。

# 为什么使用动态遥控器？

在之前的[文章](https://medium.com/better-programming/how-to-use-webpack-module-federation-in-react-70455086b2b0)中，我们讨论了在主机远程配置中使用 Webpack 模块联合来配置一个简单的 React 应用程序。当我们为远程应用程序定义 URL 时，我们使用本地主机 URL:

以上对于本地开发来说非常有效，但是如果我们部署和托管应用程序，这就行不通了。当然，我们可以用产品 URL 替换字符串——但是本地开发会发生什么呢？我们是否一直手动来回切换，希望没有人合并到错误的 URL 中？

如果有多个不同 URL 和环境的部署阶段，这种模式也不起作用。如果我们不能在部署管道中的隔离环境中测试远程应用，当开发人员引入突破性变化时，我们会陷入混乱和沮丧。每个人都会对开发流程失去信心。

代替`localhost`,我们希望根据环境动态定义 URL 来指向正确的主机。

# Webpack 模块联盟中的动态远程

幸运的是，Webpack 模块联盟支持为我们的远程应用程序动态定义 URL。我们将考虑四种可用的解决方案:

1.  环境变量
2.  网络包`[external-remotes-plugin](https://www.npmjs.com/package/external-remotes-plugin)`
3.  基于承诺的动态遥控器:[文档](https://webpack.js.org/concepts/module-federation/#dynamic-remote-containers)
4.  动态远程容器:[文档](https://webpack.js.org/concepts/module-federation/#dynamic-remote-containers)

# 1.环境变量

最直接的选择是在构建时使用环境变量。您可以在本地或部署管道中用环境变量替换 Webpack 配置中的`localhost`:

您可以在构建时将每个远程应用程序的 URL 定义为本地或托管生产部署。这种方法的优点是简单，但有一个问题。我们需要为每个环境构建一个新版本来更新 URL。对于我们的企业用例以及多种部署模型，这是行不通的。

如果您可以在运行时更改远程 URL 会怎么样？

# 2.Webpack 外部远程插件

在幕后，模块联合允许我们动态加载远程容器。我们一会儿会谈到这一点。但是首先，有一个方便的 Webpack 插件是由模块联盟的创造者之一 Zack Jackson 开发的，叫做 T1。它允许我们在运行时使用模板来解析 URL:

在从远程应用程序加载任何代码之前，您需要做的就是在应用程序中定义`window.appAUrl`和`window.appBUrl`。

现在你可以灵活地定义你的网址。例如，您可以为每个环境维护一个主机 URL 到远程 URL 的映射，使用服务器端呈现来允许后端定义 URL，创建一个定制的配置管理服务，以及许多其他方法。

这种方法是完全动态的，可以解决我们的用例，但是这种方法仍然有一点限制。我们无法完全控制装载生命周期。

# 3.基于承诺的动态遥控器

模块联合允许我们将远程 URL 定义为承诺，而不是 URL 字符串。你可以使用任何承诺，只要它符合模块联盟定义的`get/init`接口:

在这个承诺中，我们创建了一个新的脚本标记，并将其注入 DOM 以获取远程 javascript 文件。`window.appAUrl`包含远程应用程序的 URL。

Webpack 远程配置仍然必须是字符串格式，所以我将 promise 与配置分开定义，并在稍后将其字符串化，以提高可读性。不幸的是，这种方法不是最容易调试或维护的，因为它是配置文件中的字符串化代码。

到目前为止，所有的方法都有另一个限制。我们仅限于加载`RemoteA`和`RemoteB`应用，这可能是一个安全优势。但是如果一个开发者正在开发一个我们还不知道存在的新的远程应用呢？如果合作伙伴或客户希望将他们的远程模块注入到我们应用的部署中，该怎么办？

# 4.动态远程容器

模块联合允许我们以编程方式加载远程应用程序，而无需在我们的 Webpack 配置中定义任何 URL:

但是怎么做呢？

与上面的方法类似，在您尝试加载任何远程应用程序之前，您首先需要使用动态脚本标签获取远程模块。然后，您可以手动初始化远程容器。

来自 [Webpack 文档:](https://webpack.js.org/concepts/module-federation/#dynamic-remote-containers)

`container`指的是我们在主机应用的 Webpack 配置中的`"remotes"`字段中配置的远程应用。

`module`是指我们在远程应用的 Webpack 配置的`"exposes"`字段中定义的项目之一。

使用注入脚本标签的方法，您可以获取远程容器并将其存储在`window.someContainer`中，只要代码解析为我们之前使用的相同的`get/init`模式。

当您想要使用我们的远程应用程序公开的模块之一时，您需要做的就是像上面的例子那样调用:`container.get(moduleName)`。

如果你想像我们在基本的 [React 主机-远程应用](/how-to-use-webpack-module-federation-in-react-70455086b2b0)中所做的那样加载 React 远程应用，这看起来像什么？

查看我的另一篇文章中的例子:

[](https://medium.com/@oskari.rautiainen/rendering-dynamic-remote-containers-in-a-react-micro-frontend-1dd97019824f) [## 在 React 微前端中呈现动态远程容器

### 我们如何在一个 React 微前端应用程序中安全、方便地使用 Webpack 模块联盟的动态远程容器…

medium.com](https://medium.com/@oskari.rautiainen/rendering-dynamic-remote-containers-in-a-react-micro-frontend-1dd97019824f) 

# 最后的想法

使用动态加载的遥控器，您已经学习了四种不同的机制来分解您的微前端部署。当您部署我们的微前端时，您可以从您定义的任何 URL 获取您的远程应用程序。您可以将它部署到多个测试环境、内部或云中。开发人员可以选择是使用其他远程应用程序的生产版本，还是动态引入新版本。

你会选择哪种方法？