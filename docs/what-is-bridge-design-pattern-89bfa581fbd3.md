# 什么是桥梁设计模式？

> 原文：<https://betterprogramming.pub/what-is-bridge-design-pattern-89bfa581fbd3>

## 使用桥接模式避免不必要的复杂性

![](img/123ad89239e61ac641a04e7bbb53cdbc.png)

[Modestas Urbonas](https://unsplash.com/@modestasu?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/bridge?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

有时候，一个类的重要特性可以——甚至应该——独立开发。桥帮助我们将代码分割成更小的部分，并分别开发它们。在这个设计模式中，我们有两个元素:

*   抽象
*   履行

*抽象*是客户直接使用的部分。它还将作业委托给*实现*。假设我们有一个为显示准备 HTML 内容的应用程序。根据显示器的配置，我们希望我们的内容看起来有点不同。所有显示器共享相同的界面:

现在我们有两种不同的类型。标准的只是显示内容。

我真的很高兴自从 PHP 7.3 以来，我们能够格式化 heredocs 和 nowdocs。这只会让代码更容易阅读。

另一种显示器总是显示模糊的内容。

还有两种不同类型的内容:图像和视频。它们也共享相同的接口。

`Image`简单地将我们的内容包装到一个`img`标签中，并添加一些基本的 CSS 规则。

并且`Video`为视频准备我们的内容。

让我们检查一下内容是否会正确显示。

太好了。如果我们想添加一种新的显示类型，我们不必接触内容。如果我们想添加一种新的内容，所有的显示器都可以开箱即用。如果与呈现 HTML 相关的逻辑被转移到`contents`层次结构中，一切都会变得更加复杂。

该桥具有以下元件:

*   抽象(`Display`):定义抽象的接口
*   精化抽象(`Standard`，`Blurred`):实现抽象接口并使用实现者组件
*   实现者(`Content`):定义精化抽象所使用的接口(它不一定与抽象相关)
*   具体实现者(`Video`，`Image`):实现者的具体实现

# 优势

*   允许独立开发组件的功能
*   促进将层引入系统
*   使代码更容易扩展

# 源代码

完整的源代码和其他一些模式可以在这里找到:

[](https://github.com/jkapuscik2/design-patterns-php) [## jkapuscik 2/设计-模式-php

### 这个项目是一组在现实世界中使用不同设计模式的简单例子。每个人都有一个…

github.com](https://github.com/jkapuscik2/design-patterns-php) 

如果您想了解一些更有用的设计模式，您可以在以下文章中找到它们:

*   [工厂方法](https://medium.com/@j.kapuscik2/getting-started-with-design-patterns-in-php-4d451ccdfb71)
*   [创作模式](https://medium.com/@j.kapuscik2/creational-design-patterns-in-php-db365d3245ce)
*   [观察者](https://medium.com/@j.kapuscik2/observer-pattern-in-php-2ba240f89fb2)
*   [迭代器](https://medium.com/@j.kapuscik2/iterator-pattern-in-php-b7624f6bdbcf)
*   [状态&策略](https://medium.com/@j.kapuscik2/state-strategy-design-patterns-by-example-f57ebd7b6211)
*   [模板方法](https://medium.com/@j.kapuscik2/template-method-pattern-in-php-6116fd7e8ccc?source=friends_link&sk=ac4c483446bd5a5323c09a662bd54116)
*   [飞锤](https://medium.com/swlh/flyweight-design-pattern-in-php-edcda0486fb0?source=friends_link&sk=a0fa3083d5afd7e41af8a4f7a1df05f1)
*   [代理](https://medium.com/better-programming/proxy-design-pattern-and-how-to-use-it-acd0f11e5330)
*   [装饰者](https://medium.com/better-programming/decorator-c04fae63dfff)
*   [依赖注入](https://medium.com/better-programming/dependency-injection-8f09a93ec995)
*   [合成](https://medium.com/swlh/composite-908878748d0e)
*   [适配器](https://medium.com/swlh/building-cloud-storage-application-with-adapter-design-pattern-8b0105a1bda7)
*   [立面](https://medium.com/better-programming/what-is-facade-design-pattern-67cb09ce35d4)
*   [责任链](https://medium.com/@j.kapuscik2/what-is-chain-of-responsibility-design-pattern-ff4d22abd124)
*   [来访者](https://medium.com/@j.kapuscik2/what-is-visitor-design-pattern-8451fb75876)
*   [命令](https://medium.com/@j.kapuscik2/what-is-cqrs-command-design-pattern-5d400fd9f93a)
*   [空对象](https://medium.com/@j.kapuscik2/what-is-null-object-design-pattern-f3b4d3d28636)
*   [流畅的界面](https://medium.com/@j.kapuscik2/what-is-the-fluent-interface-design-pattern-2797645b2a2e)
*   [规格](https://medium.com/@j.kapuscik2/what-is-the-specification-design-pattern-4051dd9e71c3)