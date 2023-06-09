# 如何在 Swift 中编写解耦代码

> 原文：<https://betterprogramming.pub/how-to-write-decoupled-code-in-swift-515cfd3fd8eb>

## 松散耦合的代码是可测试的代码

![](img/e237068f88d92f9e81324ac2079f7a42.png)

照片由 [Kilian Kremer](https://unsplash.com/@eezkimo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/rope?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在本文中，我将讨论如何更实际地编写解耦代码。这些例子将在 Swift，但他们适用于所有其他语言。这篇文章是我上一篇文章的延续， [*为什么要写单元测试*](https://medium.com/@ronen.harati/why-write-unit-tests-260fb199af84) *，***我讨论了写单元测试的重要性。**

**让我们从什么是解耦代码开始，以及为什么它在一般情况下很重要，尤其是在编写单元测试时。为了理解什么是解耦代码，我们先来谈谈耦合代码。**

**耦合代码是类的依赖关系不能改变的地方，这意味着依赖关系是硬编码的，并且使用特定的实现。让我们看一个例子**

**`NaughtyClass`创建`sessionManager`的实例，调用`btnLogoutTapped`时，在`sessionManager`对象上调用 logout。**

**乍一看，这似乎很好，所有的功能都可以工作，但是，这段代码是紧密耦合的。试着想想为什么`NaughtyClass`其实很调皮，我等着…**

**`SessionManager`是一个不可替换的依赖项，意味着注销实现在运行时不能改变。总的来说，这不是一个好的设计，因为如果在未来，我们有几种不同类型的注销，例如脸书注销，谷歌注销等等。**

**我们将不能轻易地添加这些情况，我们将需要改变主注销方法，这将决定使用哪个注销。这一改变将直接影响`NaughtyClass`，并可能引入一个 bug。**

**这对单元测试也是不利的，因为我们将无法模仿`SessionManager`。模仿是一种创建假对象的方法，它可以代替`SessionManager`来完全控制所有的`NaughtyClass`依赖项。**

**这对于单元测试来说是至关重要的，因为我们需要将被测试的类与其所有的依赖项隔离开来。**

**在我们的例子中，意识到我们的代码是紧密耦合的并不困难，然而，在更大的项目中，这并不总是那么容易。当试图判断一个`class`是否耦合时，我们可以看看在不同的项目中重用它有多容易。**

**如果您需要导入许多文件来使用一个类，这很好地表明了该类是紧密耦合的。如果您可以重用一个类而不导入太多或任何文件，这很好地表明了这个类的解耦程度。**

# **严重程度**

**让我们列出耦合和解耦代码中的四个不同的严重级别:**

*   **紧密耦合的是最强类型的耦合代码。在这种情况下，就像上面的例子一样，一个类拥有它的依赖项，它们不能被改变。这强制该类使用特定的实现，意味着该类使用不可替换的特定对象。**
*   **耦合比紧耦合好一点，但是仍然被认为是耦合的。在这种情况下，一个类的依赖关系可以被替换，但属于一个特定的类。例如，如果依赖项被设置为一个`var`实例，它可以被同一个类的不同对象替换。这使得我们能够在一定程度上设置子类和覆盖实现，这比紧密耦合要好。**
*   **松散耦合是指一个类不依赖于某个对象或类，而是依赖于一个协议/接口。在这种情况下，依赖关系没有特定的实现，而是符合一个协议/接口。这允许我们创建多个实现，并在运行时使用不同的实现，包括单元测试的模仿对象和类。**
*   **解耦是指一个类不依赖于某个对象、类或者协议/接口。这听起来像是这个类没有依赖关系，但是我们稍后会看到事实并非如此。在这种情况下，类可以在其他项目中重用，而不需要任何其他文件。**

**注意，并不是每个人都以这种方式划分不同的严重程度。**

**紧耦合和耦合可以组合成紧耦合。松耦合和解耦也可以组合成任何一种。然而，我认为这样划分更有意义，我们可以沿着解耦的阶梯向上(对象>类>协议>闭包)。**

# **脱钩阶梯**

**现在，让我们看一下前面的例子，沿着去耦阶梯向上走。正如我们提到的，`NaughtyClass` 是紧密耦合的，试着花一点时间来思考我们如何才能使它刚好耦合，我会等…**

**好的，所以，为了使它耦合，我们需要依赖关系来使用一个特定的类，而不是一个特定的对象。最简单的方法是将`sessionManager` 实例改为`var`实例，而不是`let`实例。**

**既然`sessionManager`是一个`var`，那么它可以在运行时被同一个类的不同对象或者`SessionManager`的子类替换。**

**这是可以的，但是有一个更好的方法来初始化`sessionManager`实例。我们可以从外界注入这种依赖，这令人惊讶地被称为*依赖注入*。**

**让我们看看那会是什么样子。**

**依赖注入使我们能够用正确的对象初始化我们的类，而不是在类中创建对象并用另一个替换它。**

**这样更好，因为`sessionManager`可以是一个注入的共享实例，每次都会创建一个新对象。即使我们注入了依赖，我们的代码仍然是耦合的。**

**原因是我们的类依赖于一个特定的类，但它是从依赖于一个特定的对象升级而来的。下一步是编写松散耦合的代码，花点时间想想我们如何实现这一点…**

**好的，所以，为了让我们的类松散耦合，我们需要它依赖于一个协议/接口，而不是一个对象或类。让我们首先创建我们的`protocol`。**

**既然已经声明了我们的`protocol`，那就用它来代替一个类吧。**

**现在我们已经使用了一个`protocol`作为我们的依赖，我们的`class`是松散耦合的！这使我们能够使用任何符合`SessionManagerProtocol`的对象或类。**

**我们现在可以创建模拟对象，并在编写单元测试时注入它们。然而，我们的类仍然依赖于一个协议，我们怎样才能使它完全解耦呢？花点时间思考一下…**

**好，所以，我们需要尝试用一种不依赖于特定对象，类，或者协议的方式来写我们的类，嗯。让我们看看那会是什么样子。**

**我们可以注入一个实现注销逻辑的`closure`。这使得任何人都可以实现注销逻辑，只要闭包的输入和输出与我们的声明匹配。**

**我们的类现在是完全解耦的，因为它不依赖于特定的对象、类或协议。**

**我不一定推荐用这种方式编写我们的类，而且我确信还有其他方法可以编写完全解耦的类，但是，知道创建完全解耦的类是可能的还是很好的。**

**希望你喜欢我们的脱钩之旅。看几个例子，我会让你自己去试着弄清楚它们是什么级别的耦合。欢迎在评论中发表你的答案。**

**在我的下一篇关于[依赖注入容器](https://medium.com/@ronen.harati/dependency-injection-container-in-swift-b4d7e139338c)的文章中，我将讨论一种使用容器的更高级的注入方法。**

# **我的应用**

**[](https://apps.apple.com/us/app/koala-nap/id1478157024?ls=1) [## 考拉午睡

### 你或你的伴侣打鼾吗？现在你可以从第一天开始积极减少打鼾，使用考拉午睡。除了展示如何…

apps.apple.com](https://apps.apple.com/us/app/koala-nap/id1478157024?ls=1) [](https://apps.apple.com/us/app/tile-color-match/id1294483076?ls=1) [## 瓷砖颜色匹配

### 彩色瓷砖匹配是一个令人兴奋的新游戏。目的是通过移动底部的瓷砖来匹配上面的棋盘布局…

apps.apple.com](https://apps.apple.com/us/app/tile-color-match/id1294483076?ls=1) [](https://apps.apple.com/us/app/last-flower/id1112524681?ls=1) [## 最后一朵花

### 你能拯救最后一朵花吗？iPad 上也有-小行星正在撞击地球，所有的…

apps.apple.com](https://apps.apple.com/us/app/last-flower/id1112524681?ls=1) 

www.koalanap.com**