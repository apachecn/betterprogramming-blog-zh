# 创建一个自定义 SwiftUI 属性包装器来组合存储和发布值

> 原文：<https://betterprogramming.pub/how-to-create-a-property-wrapper-to-combine-storing-and-publishing-values-578c1c6bee88>

## 为您自己的用例构建一个属性包装

![](img/cae500f1e65735a39e754352c691bd6d.png)

格伦·卡斯滕斯-彼得斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

2019 年引入了属性包装器。从那以后，我们学会了如何使用它们，如何创建它们，苹果也在不断地发布它们。

然而，它们不能轻易结合。每当我们应用属性包装时，属性的实际类型都会改变。组合包装器将导致难以使用的奇怪类型。

在今天的文章中，我们将尝试通过创建一个自定义属性包装器来克服这个限制。

# 使用案例

将引入的`[Publisher](https://developer.apple.com/documentation/combine/publisher)`通知其他组件的变化。这种机制在 SwiftUI 中广泛使用，通过`@[Published](https://developer.apple.com/documentation/combine/published)`属性包装器通知 UI 状态的变化。

除了 SwiftUI 之外,`@Published`属性包装器可以用在许多上下文中。例如，我们可以有一个`UserPreferencesManager`来跟踪用户偏好。我们可以使用`@Published`将偏好的改变传播到应用程序的其他部分。

当处理用户首选项时，我们希望将它们存储在本地。我们想到的第一个存储选择是`[UserDefaults](https://developer.apple.com/documentation/foundation/userdefaults)`:一种存储键值对的简单方法。

SwiftUI 引入了`@[AppStorage](https://developer.apple.com/documentation/swiftui/appstorage)`属性包装器来简化来自`UserDefaults`的信息的存储和检索。通过这个属性包装器，变量被自动存储在`UserDefaults`的一个实例中，并从该实例中检索。

在这个代码片段中，我们指定了一个键，这个键将被`@AppStorage`属性包装器用来存储和检索`name`首选项。

然而，并不是所有闪光的东西都是金子。通过研究`@AppStorage`属性包装器的签名，我们可以看到它依赖于`UserDefaults`的具体实现，而不是它的接口，因此它违反了[依赖倒置](https://en.wikipedia.org/wiki/Dependency_inversion_principle)原则。

让我们看看如何将这两个属性包装器结合起来。我们将从一个手动解决方案开始，我们将实现一个基于自定义属性包装的可扩展解决方案。

# 基本场景

我们来刷新一下需求。我们希望:

1.  从存储器中存储和检索一段数据。
2.  当数据更新时，发布新值。

作为软需求，我们希望避免代码重复，允许依赖注入和可测试性。

一个好的心态是开始实现一个可行的解决方案，然后重构它以实现我们所有的目标。下面是一个基本的手动工作解决方案。

在这个片段中，我们定义了`Storage`协议:一个用于任何类型存储的接口。为了简单起见，我们将开始只存储`String`类型，但是我们可以将解决方案扩展为通用的。这个协议使我们能够使用任何符合它的`Storage`，而不仅仅是`UserDefaults`。有了这个协议，我们已经克服了`@AppStorage`的一个限制。

然后，我们上`UserPreferenceManager`课。在那个类中，我们为键创建了一个抽象，以避免输入错误，并且我们定义了一个`name`属性。`name`属性是`@Published`，所以 app 的其他部分可以在它发生变化时做出反应。

然后，我们需要存储和检索它。我们不能用一个以上的属性包装器来注释同一个变量。理论上，这是可能的，但是每个属性包装器都会改变变量的实际类型。例如，像这样的属性的发布者

```
@Published @AppStorage("name") var name: String 
```

不是一个`Publisher<String>`而是一个`Publisher<AppStorage<String>>`。这将使使用`name`变量变得更加困难。

因此，我们开始通过使用`willSet`属性观察器和保存`UserDefaults`中的值来解决存储问题。

我们必须做出的决定是关于使用`willSet`还是`didSet`属性观察器。`@AppStorage`属性包装器文档没有指定值何时存储在磁盘上。我们更喜欢在更新内存之前更新存储，以便为用户提供一致的体验。如果我们在磁盘之前更新内存，我们可能会达到不一致的状态，其中:

*   用户看到更新的版本，
*   应用程序在存储值之前崩溃，
*   用户重新启动应用程序，
*   用户会看到旧值。

如果我们使用`willSet`，这种情况就不会发生。在最坏的情况下，应用程序崩溃，用户看到新的值，这是他们必须看到的正确值。

存储问题的最后一步是何时从磁盘中检索值。初始化器为我们做了这项工作。

这种方法很好，但是扩展性不好。如果我们需要添加多个`@Published`值，我们需要复制并粘贴属性观察器，我们还需要一个接一个地初始化`init`中的所有属性。

# 统一的解决方案

这个问题的正确解决方案是将两个属性包装器结合在一起。然而，我们没有任何 API 可以让我们真正地将它们结合在一起。

另一种方法是创建我们自己的属性包装器。使用定制的属性包装器，我们可以实现我们需要支持的用例，并且可以避免代码重复。

为了安全地重构代码，我们需要一个测试套件来确保实现的行为不会改变。一旦我们有了单元测试，我们就可以创建属性包装器并在实现中使用它。

## 添加测试套件

我们想要实施的行为是:

1.  当`name`改变时发布一个值。
2.  验证当我们更新`name`时，新值是否被保存。
3.  在启动时，验证`name`包含正确的值。

这些测试将帮助我们保留我们想要的接口。事实上，如果我们不得不更新一些测试代码，我们就会知道有问题。

感谢**依赖注入**，我们可以从实际的存储实现中抽象出来。首先，让我们为我们的测试创建一个`MockedStorage`。

我们有一个用来存储所有值的字典。它使用一些`String`键。它用下标实现`Storage`协议方法。

然后，我们可以设置测试:

该片段概述了测试的结构。我们在`setUpWithError`方法中初始化了几个强制展开的属性。注意，`storage`是`Storage`类型的，所以我们可以使用我们想要的任何实现。

在`tearDownWithError`中，我们释放这些值的内存。

我在编写测试时学到的一个小技巧是，开始只编写签名:这有助于我在更高的抽象层次上思考，专注于我想要测试的东西。这有助于我了解我是否涵盖了所有的边缘案例。

当我对测试用例的签名感到满意时，我开始填充实现。

花几分钟时间阅读测试。它们并不复杂:

1.  在第一个测试中，我们创建了`sut`，我们订阅了`@Published`属性，然后我们断言属性如我们预期的那样发生了变化。我们需要删除在`init`之后发出的第一个发布值。
2.  在第二个测试中，我们创建了`sut`，并检查存储器中的值是否从`nil`传递到给定值。
3.  在最后一个测试中，我们通过设置`sut`的初始值来准备测试。然后我们创建`sut`，并确保第一个值是正确的。

如果我们按下`⌘+U`，我们应该会看到测试通过。我们有一个坚实的工作基础。

# `Create the Custom Property Wrapper`

接下来，我们创建属性包装器。为此，我们创建了一个用`@propertyWrapper`关键字注释的类型。

该关键字要求该类型具有一个`wrappedValue`属性。它可以是任何类型，可以是计算的或存储的属性。

在这种情况下，我们希望有一个存储在`Storage`中并从中检索的`wrappedValue`。我们将把`Storage`注入到`propertyWrapper`中。为了存储和检索对象，我们需要一个`key`属性。

通过这个片段，我们重新实现了`@AppStorage`属性包装器。属性`storage`被隐式展开以支持**属性注入**。在编写的时候，属性包装器语法不能访问`self`实例，即使在闭包或惰性变量中也不能。这意味着尚不支持以下语法:

```
class AClass {
  let storage: Storage // invalid  
  @StoredPublished(key: "a-key", storage: self.storage) var name: String // also this is invalid
  @StoredPublished(key: "a-key", storage: { self.storage }) var name: String}
```

所以我们不能在注释语法中注入依赖关系。将依赖项注入到由属性包装器支持的变量中的唯一方法是使用包装的属性将其注入到类型的`init`中。

为了实现这一点，我们需要使用一个隐式展开的属性。请注意，如果我们忘记设置它，这可能会导致运行时崩溃。我们可以通过为`storage`属性提供默认值来防止这种情况。尽管如此，我更喜欢崩溃:在这种情况下，这是一个系统化的开发错误，测试会立即捕捉到，并且修复非常快。

现在让我们来实现`publisher`。有许多方法可以实现这一点，但最简单的方法是使用一个`PassthroughSubject`。一个`Subject`支持一个`send`方法将它的值传播给所有的订阅者。

这个代码片段只包含了我们需要的`Publisher`的代码行以及清晰的上下文。

在第 12 行，我们声明了私有的`publisher`，并用`PassthroughSubject`初始化它。在第 7 行，我们发布了新值，在我们把它存储在`storage`中之后。

在第 16 行，我们声明了一个名为`projectedValue`的计算属性。这个属性启用了我们在绑定变量和观察`@Published`变量时使用的`$`语法。

# 连接属性包装

我们现在已经在属性包装器中实现了我们需要的所有行为，让我们用它来更新代码。

改变真的很容易。

1.  用`@StoredPublished`替换`@Published`属性包装器。新的属性包装器需要一个键，我们可以像以前一样使用`StorageKeys.name.rawValue`键。
2.  移除`willSet`属性观察器。
3.  用第 12 行替换对初始值的检索，我们在这里设置存储。

我们现在可以按`⌘+U`来查看测试是否通过。

## 可量测性

这种解决方案比基本解决方案的伸缩性更好。当我们添加一个新变量时，我们只需要添加一个键，一个属性，并且我们需要设置属性包装器的`storage`属性。

这最后一步是令人讨厌的部分:由于有许多`@StoredPublished`属性，我们的`init`最终会有许多这样的行:

```
**self**._property1.storage = **self**.storage**self**._property2.storage = **self**.storage...**self**._propertyN.storage = **self**.storage
```

这不是最糟糕的，但也不好。

我们可以通过使用`[MirrorAPI](https://developer.apple.com/documentation/swift/mirror)`做得更好。这些 API 让我们检查一个类型来探究它的属性。在我们的例子中，我们可以搜索所有用`@StoredPublished`属性包装器标注的属性。这是可能的，因为属性包装隐式地改变了属性的实际类型。

在我们的例子中，所有标注了`@StoredPublished`的属性都变成了`StoredPublished`类型。有了这些知识，我们可以编写这个助手函数:

该函数带有一个我们需要设置的`Storage`，以及一个我们需要检查的实例。然后，它创建实例的`Mirror`并检查其内容。每当有类型为`StoredPublished`的属性时，我们就设置它的`storage`属性

最后，我们需要在`init`中调用这个函数，替换`self._name.storage = self.storage`行。

如果我们运行测试，我们会看到他们崩溃。发生这种情况是因为`StoredPublished`属性包装器是一个`struct`。对于值类型，当我们用镜像检查包装时，我们会得到它的一个副本。我们需要更新它，因此我们宁愿有一个参考。

所以我们把`StoredPublished`的类型从`struct`改成`class`。让我们再次运行测试，看看他们是否通过。

我们现在解决了所有的问题:每次我们添加一个新的`@StoredPublished`属性时，我们不再需要更新`init`。

# **结论**

在今天的文章中，我们探索了几个非常有用的属性包装器，我们看到了它们在可组合性和可测试性方面的局限性。

然后，我们实现了一个定制的属性包装器，来处理发布一个值并将其存储在抽象中的复杂用例`Storage`。

最后，我们解决了可伸缩性问题，使用`MirrorAPI`将存储设置为用`StoredPublished`属性包装器注释的所有类型。

对读者来说，一个很好的练习是将`StoredPublished`转换成一个通用的，并相应地更新代码。