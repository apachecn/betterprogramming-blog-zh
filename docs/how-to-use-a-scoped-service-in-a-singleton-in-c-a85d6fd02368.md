# 在 C#中如何在单例中使用作用域服务

> 原文：<https://betterprogramming.pub/how-to-use-a-scoped-service-in-a-singleton-in-c-a85d6fd02368>

## 对这一过程的简要讨论

![](img/7ccd341cf5f76fdf07146ea07a4fd34b.png)

照片由[新闻社](https://unsplash.com/@olloweb?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上追踪

这听起来不干净，没有达到示波器的目的，对吗？通常，当您拥有一个作用域服务时，它只能在相同作用域的其他作用域服务中使用。作用域是一个相互关联的请求的封闭环境。例如，当您向一个 API 发出请求时，这个 API 可能会在一个封闭的范围内处理您的请求，这就是实例化只在这个范围内使用的服务。

与此相反，单例服务在每个应用程序生命周期只托管一次，只有在应用程序结束时才被释放。自然，您不能在单例服务中引用封闭范围的服务，而且在大多数情况下，您也不应该这样做。

然而，在极少数情况下，托管使用作用域服务的单例可能是有意义的。例如，在我最近的项目中，我在 [Azure](https://azure.microsoft.com/) 上托管了一个[函数应用](https://azure.microsoft.com/de-de/services/functions/)，其中每个请求产生一个作用域。这个功能应用程序还使用定时触发器和后端触发器，这将有应用程序的范围。

现在，每次后端请求进来时，应用程序都会实例化一个新的范围，其中包含新的 HttpClients、BlobClients 等等。当然，有许多后端请求…应用程序将创建成千上万的客户端，这也将是可重用的。然而，使它们成为单例是不可能的，因为身份验证服务必须被限定范围。

如果您从未使用过依赖注入，请查看本文档。

解决方案是简单地手动创建一个限定了作用域的，它的生存期与 singleton 的生存期相同。

# IServiceScopeFactory

负责创建新作用域的服务称为:`IServiceScopeFactory`。该接口可以在`Microsoft.Extensions.DependencyInjection.Abstractions`库中找到。你可以简单地把这个接口放在一个构造函数中，并且有能力创建你自己的作用域。代码如下:

使用 IServiceScopeFactory 手动实现

现在您只需要将这个服务作为单例添加到 DependencyInjection 容器中，就可以开始了！

如果您现在想知道是否需要重写所有代码，以便可以在单例中使用作用域服务，请不要担心，我已经开发了一个替代方案:

# AddScopedSingleton()扩展方法

就像您将服务添加到`IServiceCollection`中一样，我希望添加我的作用域单件服务，而不需要改变服务本身。这就是我创建以下方法的原因:

扩展方法 AddScopedSingleton

如您所见，该方法在技术上向`IServiceCollection`添加了一个单例实例，然而，服务本身是在一个新的范围内创建的。

这些扩展现在使我的函数应用程序在每个应用程序生命周期中只托管一种服务。请记住，您不能使用这个方法来包装所有的作用域服务。对于大多数 API 请求来说，作用域通常是必需的，只有极小一部分可以在单例中运行。

感谢您花时间阅读这篇文章。我希望，你会觉得它有知识性、教育性和趣味性。
非常感谢您的支持和参与。

如果您有兴趣了解干净架构、干净编码和最新技术栈的最新趋势、技巧和诀窍，尤其是在 C#环境中。净和有棱角——如果你考虑跟踪我，我会很感激。

祝你有美好的一天！

![](img/429378e6a7aee01df92851fb0e90e385.png)

如果你还没有每天使用媒体来增长你的知识，现在是开始的最佳时机！借助 Medium，您可以轻松获得更多关于高度专业的主题的知识，发布高质量的内容，并接触到更广泛的受众。要开始，只需使用以下链接创建一个中型帐户:

[*加入中现*](https://medium.com/@tobias.streng/membership)

通过这样做，你将获得一个强大的平台，可以帮助你联系新的作者和读者，每天学习新的东西。