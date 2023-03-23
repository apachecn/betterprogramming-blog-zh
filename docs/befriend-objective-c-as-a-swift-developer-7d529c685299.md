# 作为 Swift 开发人员与 Objective-C 友好相处

> 原文：<https://betterprogramming.pub/befriend-objective-c-as-a-swift-developer-7d529c685299>

## 学会驯服野兽

![](img/c5abe0011d34372ad2d9c974760ba853.png)

照片由[爱德华多·马尔曼](https://unsplash.com/@epmallmann?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/evil?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

如果你精通 Swift，并且熟悉 JavaScript 等其他一些语言，那么第一次使用 [Objective-C](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/Introduction/Introduction.html) 代码可能会相当震惊。

如果我看一些我不常使用的其他语言的代码，比如 Python，如果代码写得很好，我通常会立刻知道发生了什么。

如果我看到写得很好的 Objective-C 代码，我的直觉反应是“这到底是什么？这是真正的代码还是随机生成的胡言乱语？”

或者至少，过去是这样的。

如你所见，我并不是最大的目标 C 迷。然而事实是，这并不重要。

许多大型项目仍然至少有一部分代码是用 Objective-C 编写的，所以事实是，为了能够使用它们，你必须能够理解它。

在本文中，我们将通过将代码翻译成 Swift 来学习该语言的基础知识。

我们将要使用的代码来自于本文，这是对 MVVM 设计模式的一个很好的简短介绍。唉！是用 Objective-C 写的！

如果您不熟悉这个重要的设计模式，那么接下来的几分钟您可能会一举两得:在一篇文章中学习 MVVM 和 Objective-C 的基础知识。听起来很酷，不是吗？

# 什么是 MVVM？

MVVM 代表*模型-视图-视图模型*，它是一种[结构化设计模式](https://en.wikipedia.org/wiki/Structural_pattern)(它处理对象之间的关系是如何结构化的*)将对象分成三个不同的组。*

## *模型*

*   *模型保存应用程序数据。*
*   *通常是结构或简单的类。*
*   *数据应该是最简单的格式，这样它就可以重用。*

## *视角*

*   *在屏幕上显示视觉元素。*
*   *几乎都是`UIView`的子类。*
*   *他们应该只处理视图生命周期事件。*

## *视图模型*

*   *将数据从模型转换为视图可以显示的对象。*
*   *一个唯一的网守，通过它数据从`Model`流向`View`，反之亦然。*
*   *典型的例子是在呈现给用户之前，将`Date`对象转换成`String` 。*

> *“[MVVM 是 MVC 的一个增强版本，其中我们正式连接视图和控制器，并将表示逻辑从控制器中移出，移入一个新的对象，即视图模型。”—灰沟*

*重要的是要明白，`View`指的是`View Model`，而`View Model`指的是`Model`，而不是相反。`View`和`Model`没有相互引用。*

*那么，`View`是如何更新的呢？通过[数据绑定](https://en.wikipedia.org/wiki/Data_binding)，这是 MVVM 的一个核心理念。这就是为什么反应式编程经常与 MVVM 成对出现的原因。*

*这篇介绍只是触及了这种设计模式的表面，足以让您开始。如果您想了解更多，我会在本文末尾的参考资料部分提供一些有用文章的链接。*

# *目标-C 与 Swift*

*既然我们已经介绍了 MVVM 的基础知识，是时候使用 Objective-C 和 Swift 来实现它了。我们会写一个`Model`，一个`View Model`，一个`View`。*

*我们将使用的文章包括为我们的架构编写测试，我将在这篇文章中省略。如果你第一次见到 MVVM 和 Objective-C，它们足以让你头晕目眩。*

*我们开始吧！*

# *入门指南*

*我们从一个简单的`Person`模型开始:*

*让我们把这段代码拆开。*

## *接口和实现*

*我们首先看到的是`@interface`注释。要创建一个 Objective-C 类，我们必须提供两段代码，分别叫做*接口*和*实现*。*

*它们被存储在单独的文件中:头具有`.h`扩展名，实现具有`.m`扩展名(要知道为什么，请阅读这个精彩的[堆栈溢出答案](https://stackoverflow.com/a/652266/10706839))。*

*`@interface`用于*声明*一个类。我们声明类中将包含哪些变量和方法。*接口*描述了我们的类将会是什么样子。*

*实现部分将是构造实际方法的地方。实现描述了我们的类将做什么。*

## *n 对象*

*在这之后，我们写一个冒号(`:`)后面跟着`NSObject`。这类似于 Swift，我们简单地声明我们的类继承自`NSObject`。*

> *大多数 Objective-C 类层次结构的根类，子类从该类继承运行时系统的基本接口以及作为 Objective-C 对象的能力— [文档](https://developer.apple.com/documentation/objectivec/nsobject)*

# *实例和类方法*

*现在，这段文字是怎么回事？*

```
*- (instancetype)initwithSalutation:(NSString *)salutation firstName:(NSString *)firstName lastName:(NSString *)lastName birthdate:(NSDate *)birthdate;*
```

*`-`符号意味着我们正在编写一个*实例方法*，而不是一个*类方法*(类方法由`+`符号表示)。*

*   *实例方法是一种只在类的特定实例上可用的方法，只有在类的所述实例已经被创建之后。*
*   *类方法是可以在类本身上调用的方法，不需要实例。*

*参见[这篇文章](https://teamtreehouse.com/community/in-objective-c-whats-the-difference-between-instance-and-class-methods-and-how-do-you-switch-between-the-two)了解更多关于实例和类方法的信息。现在，知道我们通常使用*实例*方法就足够了。*

*`(instancetype)`部分是函数将返回的内容。你可以把你的方法返回的变量的类型放在这里，或者简单的说`(void)`如果它不返回任何东西。*

*我们返回一个所谓的`instancetype` ，其中基本上意味着我们正在返回一个调用该方法的类的实例。*

*然后是我们函数的名字`initWithSalutation`。*

*之后，我们初始化变量。`NSString`是`String`的 Objective-C 版本。`*`标志是做什么的？这是一个指针，指示一个*引用类型*。这不像 Swift，在 Swift 中字符串有*值语义*。*

# *性能*

```
*@property (nonatomic, readonly) NSString *salutation;
@property (nonatomic, readonly) NSString *firstName;
@property (nonatomic, readonly) NSString *lastName;
@property (nonatomic, readonly) NSDate *birthdate;*
```

*标签提供了一种方法来定义一个类想要封装的信息。导入我们类的其他类也可以访问这个属性。*

*您可以在属性上定义各种属性。这里的第一个是`nonatomic`,它是我们大多数时候在属性上定义的。*

*这意味着多个线程可以同时访问这个变量，所以它不是线程安全的。我们几乎总是使用它的原因是它在性能上更快，而且大多数时候`atomic`并没有真正提供实质性的额外好处。在 Swift 中，变量默认为`nonatomic`。*

*我们用`readonly`来表示一个设定后不想改变的值。*

*我们用`@end`标签标记我们接口的结尾。*

# *履行*

*这是我们的`Person`模型的接口，我们仍然需要创建实现。*

*我们从导入我们的`Person`头文件开始。*

*在那之后，唯一新的是`[super init]`部分，它代表了你如何在 Objective-C 中调用一个函数。*

*这段代码调用了我们继承的类`NSObject`的`init`方法。然后，我们检查`self`是否被正确初始化，然后我们才给变量赋值。*

*我们在 Swift 中的模型如下所示:*

*又短又甜，对吧？*

*我们的模型对象准备好了！如果你还在读这封信，那真是太棒了。我们已经谈了这么多了！*

# *构建视图模型*

*现在是时候创建我们的视图模型了。我们已经完成了基础部分，所以从现在开始会更容易。*

*这里没有什么新东西，这是一个简单的接口，有一个`Person`对象和两个字符串。*

*我们的视图模型的实现看起来有点复杂。*

*我们导入`Person`和`PersonViewModel`头文件。*

*就像之前一样，我们调用`NSObject`的`init`方法，检查`self`是否被正确初始化。*

*我们初始化我们的`person`对象，然后调用`[stringWithFormat:](https://developer.apple.com/documentation/foundation/nsstring/1497275-stringwithformat?language=objc)`函数，该函数接受一个格式字符串和一个逗号分隔的参数列表，并将其代入输出字符串。*

*在我们的`nameText`被设置后，我们创建一个`[NSDateFormatter](https://developer.apple.com/documentation/foundation/nsdateformatter)`实例，它是`[DateFormatter](https://developer.apple.com/documentation/foundation/dateformatter)`的 Objective-C 版本。*

```
*NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];*
```

*我们的对象被称为 *dateFormatter* ，它是一个*引用类型*，它的类型是 *NSDateFormatter* 。我们用`alloc`告诉`NSDateFormatter`类为这个实例分配一块内存。然后我们用`init`初始化它。*

*我们在`dateFormatter`上调用`setDateFormat`函数。然后我们使用`stringFromDate`方法为我们的`birthdateText`属性设置文本。*

*哼！我们已经完成了视图模型。让我们看看这在 Swift 中会是什么样子。*

*以下是我们迄今为止所做的工作:*

*   *创建了一个人的模型，带有自定义的称呼、名字、姓氏和生日。*
*   *我们为此人创建了一个视图模型。这将是视图和模型之间的唯一中介。*
*   *视图模型用于将数据从我们的模型转换成可以呈现给用户的格式。我们将出生日期从`Date`转换为`String`。*
*   *我们正在熟悉 Objective-C 的基础知识。*

# *景色*

*在现实世界的项目中，我们需要使用类似于 Objective-C 的 [ReactiveObjC](https://github.com/ReactiveCocoa/ReactiveObjC) 或 Swift 的 [RxSwift](https://github.com/ReactiveX/RxSwift) 或类似于[键值观察](https://www.appcoda.com/understanding-key-value-observing-coding/)的框架来绑定我们的数据。*

*对于本文来说，深入研究这些细节就有些多余了，所以，现在，我们只是假设数据绑定的问题已经解决了。*

*如果我们的视图控制器中有一个`nameLabel`和一个`birthdateLabel`,我们就应该这样做，这应该很清楚:*

```
*- (void)viewDidLoad { [super viewDidLoad]; self.nameLabel.text = self.viewModel.nameText;           
    self.birthdateLabel.text = self.viewModel.birthdateText;
}*
```

# *最后的想法*

*我们在这篇文章中讨论了很多。如果你已经做到了这一步，那么现在你应该已经很好的理解了 MVVM 和 Objective-C*

*如果还没有完全理解，不要担心，这些都是很难的话题。您可能想重读这篇文章，或者看看下面的一些参考资料。*

*另外，不要忘记 MVVM 和 Objective-C 还有很多内容，本文只是一个介绍。*

*在这篇文章中，我们开玩笑说我最初不喜欢 Objective-C，一方面是因为我想让阅读变得更有趣。另一方面，我知道学习 Objective-C 可能会令人沮丧。你并不孤单。*

*与往常一样，情况比这更复杂，这种语言有积极的方面，但可读性不是其中之一。*

*感谢您花时间阅读我的文章。如果你有任何想法、问题或建议，请在下面的评论中留下。*

# *资源*

*   *[MVVM 简介](https://www.objc.io/issues/13-architecture/mvvm/)*
*   *[MVVM 简介:使用 MVVM 设计模式重构 MVC 应用](https://www.appcoda.com/mvvm-vs-mvc/)*
*   *[通过教程设计模式:MVVM](https://www.raywenderlich.com/34-design-patterns-by-tutorials-mvvm)*
*   *【Swift 开发人员的目标-C*
*   *[为什么 Objective-C 文件使用。m 分机？](https://stackoverflow.com/questions/652186/why-do-objective-c-files-use-the-m-extension/652266#652266)*
*   *[接口和实现](https://www.oreilly.com/library/view/programming-ios-6/9781449365783/ch04s02.html)*
*   *[实例和类方法的区别](https://teamtreehouse.com/community/in-objective-c-whats-the-difference-between-instance-and-class-methods-and-how-do-you-switch-between-the-two)*