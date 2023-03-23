# 介绍 Cerializr:(反)像专家一样连载

> 原文：<https://betterprogramming.pub/introducing-cerializr-de-serialize-like-a-pro-3e9143e8ff6e>

## 一个开源库，使序列化变得更好

![](img/1f86e8d8385b5b3a2e31739883c15156.png)

由[约书亚·索蒂诺](https://unsplash.com/@sortino?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/sunny?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

序列化主要用于当我们与发送给我们一个 JSON 文件的后端通信时，我们希望我们的类在 TypeScript/JavaScript 中有实例，反之亦然。为 JSON 文件中的每个属性定义构造函数和创建实例是一件痛苦的事情。

存在许多解决方案——[class-transformer](https://github.com/typestack/class-transformer)、[serializer](https://github.com/mobxjs/serializr)等。

但是有一个库引起了我的注意，它是 [**铈化**](https://github.com/weichx/cerialize) **。它使用简单，非常快，没有硬设置，而且它使用装饰者！**

但是，即使这个库很强大，也没有维持两年。甚至还有一个版本 2，它已经准备好了，但是隐藏在一个拉请求中。[库的作者自己也承认他不再在上面工作了(这就是生活！)](https://github.com/weichx/cerialize/pull/78#issuecomment-403751508)。

使用`npm i — save cerialize`可以轻松安装 cerialize v1。可以使用`cerialize@next`安装 v2。这就是为什么许多人没有使用这个库的所有功能。

所以我花了一点时间来恢复这个项目，分叉，更新 TypeScript 和 jest，并更新测试。现在，我们都可以享受这个图书馆。

**我很高兴地宣布**[**Cerializr**](https://github.com/kmathy/cerializr)**的发布！**

# 快速示例

## 问题是

当我们从服务器接收数据时，它是原始形式的，通常是 JSON 格式的。在客户端，我们需要建立一个到类的映射，否则我们将在一个数组中接收对象。

使用 TypeScript，我们可以指定我们接收的数据的类型，但这只是一种键入！对象的实例没有被创建，所以我们不能使用一个包含放入我们模型的所有方法或特性的对象。

让我们在一个小的 Stackblitz 项目中验证一下…

## 解决方案

做了很多工作却收效甚微——我们能改进吗？当我们从服务器接收数据时，在模型的属性上使用一个装饰器来自动生成我们的实例不是更好吗？

**这就是** [**铈锆**](https://github.com/kmathy/cerializr) **的用武之地！**

存在几个 decorators，但是它们使用一个简单的案例来处理我们属性的序列化和反序列化。

如您所见，我们不再需要管理模型中的构造函数。装潢师为我们做的！

这只是一个简单的反序列化的例子，但实际上， [@autoserializeAs](http://twitter.com/autoserializeAs) 也会负责你的数据序列化到后端！

还有许多其他功能，下面是一个简短的总结:

*   在一种类型中指定序列化，在另一种类型中指定反序列化。
*   指定不同于您的型号的特定键。
*   定义您自己的序列化(反序列化)函数。例如，根据我们模型中的不同属性计算一个金额。
*   定义一个实例化方法(使用 new、object.create 或不使用它们)。
*   从父级继承序列化。例如，从 UserModel 继承属性的 AdminModel。
*   还有更多…

不要犹豫给一些关于你的用户体验的反馈，如果你有任何问题，请随时提出。

祝你愉快，别忘了带着激情工作！