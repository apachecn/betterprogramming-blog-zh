# 如何通过对代码进行未来检验来提高开发速度

> 原文：<https://betterprogramming.pub/how-to-increase-development-velocity-by-future-proofing-your-code-dd3ea8c98357>

## 通过编写灵活的代码来帮助未来的自己

![](img/7605d2cc810582ae76cf6941024112b6.png)

照片由 [micheile dot com](https://unsplash.com/@micheile?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

> “编写代码的时候，要把最终维护你代码的人想象成一个知道你住哪儿的暴力精神病患者。”—古老的开发者谚语

当我们第一次学习编码时，很难去想我们正在处理的故事的最初需求。你已经有很多关于设计、单元测试、边缘案例等等的事情要做了。更不用说担心几个月后它会如何影响你的应用了。但是，如果您想要一个灵活的、不难维护的代码库，这一点非常重要。

作为工程师，我们希望不断学习和改进我们的行业。小小的收获会累积成生产力上的巨大成就。

# 抽象

> "实际上，你的应用程序中的任何东西都应该被认为是可能改变的."—务实的程序员

对代码进行分层，以便将稳定的部分与将来可能改变的依赖项分开，这将允许您在需要用新的服务或技术替换旧的服务或技术时做好准备。

编程是一个不断变化的领域，很多时候，很难跟上。您正在使用的一项技术可能不是您未来需要的。这意味着为了能够快速做出改变，我们需要适当地抽象代码。

抽象不仅仅是增加接口层。命名约定也很重要。在我们已经创建并公开了某个东西之后，很难对它进行重命名，因此在实现代码之前考虑它是很重要的。我们可以通过提供者做什么而不是谁在做什么来命名他们，从而避免巨大的耗时的变化。

假设您的公司目前使用`BigBrotherAnalytics`，您的任务是为您的页面添加跟踪功能。因此，您创建了一个类来创建带有`track`函数的`BigBrother`实例。当需要触发跟踪事件时，实例化并调用跟踪函数。看起来是这样的:

```
class BigBrotherAnalytics {
  constructor() {
    this.BigBrotherInstance = new window.BigBrother.initialize()
  }
  track() {
    this.BigBrotherInstance.track();
  }
}
```

这可能看起来不错，但它有一个缺陷。您可能不会经常使用这项服务。散落在你的代码库中的是对`BigBrother`的引用。当你不得不关掉它时，会有大量的公关来重新命名一切。这也是一个简单的例子。随着时间的推移，人们将遵循相同的命名约定，很快，用所有特定于供应商的代码来更改代码库可能会非常困难。

我们知道分析提供商的功能将保持不变，向服务发送事件以处理和编译信息。

类名的简单改变提取了不相关的信息，并标识了组件正在做什么。

```
class AnalyticsProvider {
 constructor() {
   this.BigBrotherInstance = new BigBrotherAnalytics()
 }
 track() {
   this.BigBrotherInstance.track();
 }
}

AnalyticsProvider.Track()
```

我们只需要改变类内部的内容，而不是每个导入语句和实例化。

这个想法是为了最小化特定技术和服务的影响。我们可以创建模块化代码，将实现的具体细节抽象到一个地方，并留下一个接口来连接具体细节。

# 正交性

正交性与去耦密切相关。它的思想是改变一段代码不应该对其他领域产生副作用。这使您在进行更改时充满信心，并使您能够更快地编码和更新所有受影响的代码。

继承是紧密耦合代码的一个例子。它允许子类继承方法和成员变量。这看起来像是节省了打字时间，但这是有代价的。父类现在被绑定到每个扩展它的类上，这使得将来的更改变得不直观和难以更改。

这可以通过使用接口而不是继承来缓解。

接口提供了一个契约，允许定义一个类必须实现的函数。这里的好处是对象的结构仍然被定义，但是方法的实现被构建到类中，当您需要修改它时，留下的副作用更少。

JavaScript 没有接口，但是我们可以使用 TypeScript 添加功能。TypeScript 是 JavaScript 的超集，增加了 JavaScript 没有的类型特性。

```
interface IEmployee {
    empCode: number;
    name: string;
    getSalary:(empCode: number) => number;
}

class Employee implements IEmployee { 
    empCode: number;
    name: string;

    constructor(code: number, name: string) { 
        this.empCode = code;
        this.name = name;
    }

    getSalary(empCode:number):number { 
        return 20000;
    }
}
```

这使得实现保持在`Employee`类内部，而不用担心方法从外部被改变并影响雇员如何工作。假设我们想要创建一个`Intern`类，我们仍然可以扩展`IEmployee`接口，我们可以迭代这两种类型的`Employee`，我们保证它会有一个`getSalary`方法。

拉开相互依赖关系会产生正交码。不仅对于您的实现，而且对于单元测试来说，更改会更容易，因为您的更改只会影响您接触的代码。

# 单一责任

在进行代码更改时，复杂性是一个巨大的障碍。您必须能够理解您正在更改的代码。否则，你就是在问生产问题。

单一责任原则是坚实的基础，也是创建易于维护的灵活代码的重要概念。它声明如下:

> 一个类应该做一件事，因此，它应该只有一个改变的理由。—单一责任原则

我们希望我们的类和方法易于评估它们做了什么。如果你正在写一个类，并且它开始膨胀到多个目的，那么它应该被分解。这样做可以保持代码简单明了。

将类和方法分开也允许团队更好地同时编程。每个程序员都可以改变分离的小块，减少冲突。

# 分离的代码库

所有这些原则都集中在解耦代码上。这是让您的代码经得起未来考验的基础。能够自信地、正确地快速更改代码，而不会妨碍自己或队友的未来。

感谢您的阅读。敬请关注更多内容。