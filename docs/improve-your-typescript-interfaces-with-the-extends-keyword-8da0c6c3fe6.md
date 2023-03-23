# 使用 Extends 关键字改进您的 TypeScript 接口

> 原文：<https://betterprogramming.pub/improve-your-typescript-interfaces-with-the-extends-keyword-8da0c6c3fe6>

## 如何使用以及何时使用

![](img/7e3dffa1b5283b33e1c9d6239de31658.png)

由[丹尼斯克 11](https://stock.adobe.com/contributor/205580073/denisik11?load_type=author&prev_url=detail)

本文将介绍 TypeScript 中 extends 关键字的基础知识。它还将提供我自己工作中的例子，在这些例子中`extends`派上了用场。

随着这些年来我对 TypeScript 知识的提高，一些特性被证明比其他特性更有影响力。`extends`关键字就是一个很好的例子。

# 什么是 Extends 关键字？

extends 关键字允许您将其他命名类型的成员复制到新的接口中。

在上面的例子中，我创建了一个通用的`PersonDetails`接口。在第 6 行和第 10 行，我使用`extends`关键字将`PersonDetails`中的类型添加到`Member`和`TicketHolder`中。现在，当我实例化一个模型时，我可以看到`PersonDetails`中的类型是可用的。

使用`extends`关键字的主要好处是减少了您需要在模型中编写的样板文件。尽管样板文件的减少不会影响应用程序包的大小，但最终，这仍然是一种有效的策略，可以使您的模型更加简洁，可读性更好。

# 链接

`extends`关键字还允许链接多个接口。

这些没有限制你可以添加到一个接口的`extends`的数量。然而，过度使用`extends`关键字会使界面难以阅读。

如果你想将`extends`整合到你的应用程序中，我会考虑对每个接口允许的`extends`数量进行限制。

我尝试将接口限制为两个`extends`。任何超出这个范围的东西都会使寻找特定值变得更加困难。

# 灵感

如果你在构建界面时还没有使用`extends`，下面是我在项目中使用的一些例子。希望他们能给你一些自己使用它们的想法。

## 简单对象

## 模态物体

## 状态切片对象

## 用户对象

# 包扎

`extends`关键字是 TypeScript 提供的一个很好的工具，可以减少模型中的样板代码。我建议任何希望提升其应用程序架构的人尝试一下，看看它能带来什么变化。