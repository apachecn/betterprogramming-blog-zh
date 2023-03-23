# 将 Kotlin 暂停功能转换为 Swift 的 async/await With Adapter 模式

> 原文：<https://betterprogramming.pub/kotlin-suspending-functions-as-swift-async-await-with-adapter-pattern-a79aacaa5b1c>

## 从 Swift 的角度来看，在使用 Kotlin 的共享代码时，充分利用最新的 API

![](img/ae9a225b893940b1cfe0033ce18e769f.png)

丹·丹尼斯在 [Unsplash](https://unsplash.com/s/photos/ropes-knot?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

最近，我不得不参与一个 Kotlin 多平台移动项目，在这个项目中，表示逻辑是特定于平台的，只有业务逻辑是共享的。

该应用程序是在固定(并且很少)数量的用例上实现的——大多数用例在每个屏幕上都可用，但是在某种不同的调用上下文中。

Kotlin/Native 与 Swift/Objective-C 的互操作性将 Kotlin 挂起函数呈现为 iOS 端带有完成处理程序的*函数。*

生成的完成处理程序 API 不是最令人愉快的 API，尤其是当需要在 iOS 端以特定的顺序执行许多挂起函数(作为完成处理程序可用)时。这可能很快导致回调地狱。

# 什么是完成处理程序？

完成处理程序是在未来某个时间调用的回调。它们可以交付已完成操作的结果，通知错误，或者只是在满足特定条件时被调用。

以下代码片段包含一个演示示例:

完成处理程序的示例

关于完成处理器的更多信息可以在[这里](https://medium.com/@dhavalkansara51/swift-closures-with-completion-handler-de2d9f2fdd4f)找到。

# 回调地狱的解决方案

在某些情况下，回调地狱的一个解决方案被认为是足够好的，那就是实现一个适配器模式，使完成处理程序 API 适应异步/等待 Swift API。

该解决方案提供了一种在 iOS 端有效使用挂起功能的简单方法。值得考虑的是，这种模式增加了一些样板文件，并且通常必须为每个功能手工实现。考虑到缺点，这个解决方案仍然值得一试。

以下部分将描述如何实现 async/await 适配器的完成处理程序的最基本版本。

*本文中的示例只是一个演示性示例，展示了如何使用 Swift continuation API 将挂起函数的完成处理程序转换为 async/await，这对处理 Kotlin 多平台代码的 iOS 开发人员非常有用。*

# 什么是适配器设计模式？

一个[适配器设计模式](https://refactoring.guru/design-patterns/adapter)是一个流行的结构设计模式，它允许具有不兼容接口的对象一起工作。Android 世界中流行的适配器模式实现之一是`[RecyclerView](https://developer.android.com/guide/topics/ui/layout/recyclerview)` adapter。

async/await adapter 的完成处理程序的以下实现并没有严格遵循 adapter 模式的实现，但它可以被视为 adapter 模式的实现，因为它解决了设计模式旨在解决的问题。

# 简单实现

将下面的例子看作是一个位于共享 Kotlin 多平台模块中的用例，它模拟了一些需要花费时间来完成的工作(比如一个 API 请求):

示例使用案例

Swift 语言在很多方面与 Kotlin 不同。

一个是[检查异常](https://kotlinlang.org/docs/exceptions.html#checked-exceptions)的概念 Swift 有而 Kotlin 没有。

挂起的[函数必须用](https://kotlinlang.org/docs/native-objc-interop.html#errors-and-exceptions) `[@Throws](https://kotlinlang.org/docs/native-objc-interop.html#errors-and-exceptions)` 进行注释，后跟预期会发生的异常(它们的类)(其中一个当然是`CancellationException`)。

得益于此，它们在完成处理程序的调用中作为`NSError`传播，并且可以在 iOS 端轻松处理。

`ExampleUseCase`的 API 将作为下面的完成处理程序在 iOS 端可用。

通过完成处理器 API 使用 Swift 代码的暂停功能

为了使它与 async/await 兼容，让我们创建一个作为适配器的类。

示例用例适配器

现在创建一个函数来适应`ExampleUseCaseAdapter`中`ExampleUseCase`的行为。

简单的代表平台模型的`struct`和一个示例错误`enum`被添加来显示处理错误和执行一些映射的可能性(这里的映射不违反 SRP 原则吗？把你的想法写在评论区)。

示例 UseCaseAdapter 实现

在`getSomeExampleData` —标有`async throws` —函数`[withCheckedThrowingContinuatio](https://developer.apple.com/documentation/swift/withcheckedthrowingcontinuation(function:_:))n`(可以认为是类似于`[suspendCancelableCoroutine](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/suspend-cancellable-coroutine.html))`的东西)提供了`continuation`，它根据完成处理程序中提供的结果恢复特定的数据。

1.  如果提供给 closure 的`data`不是`nil`，代码将恢复，并将`data`的值映射到预期的返回类型。
2.  如果来自`@Throws`的异常发生(不是`nil`)，代码将失败恢复。这里是处理错误的地方。控制可以被传递给一些通用的解决方案，例如，将区分`CancelationException`和其他异常(其中一个是原因)，这例如将导致更多信息的失败原因。
3.  如果数据为`nil`且错误也为`nil`，则代码继续，并出现 Swift 错误(将由`withCheckedThrowingContinuation`抛出)，在本例中，该错误是为该示例定义的`ExampleAdapterError.errorWithAsync`。这种行为应该根据您的要求进行调整。

由于上面的实现，在适配器的帮助下，用例代码可以使用 async/await API 来执行。

考虑以下发布某种数据元素的视图模型实现的示例:

示例视图模型实现

使用 async/await API，当调用`fetchData`时，元素很容易被强制地和优雅地提供非初始值。

上面的例子可能看起来微不足道，但是想象一下有许多[完成处理程序](https://levelup.gitconnected.com/from-callbacks-to-async-await-in-swift-aebd38ab0f13)——一个适配器可以很容易地避免平台表示中的回调地狱。

# 所有的工作都值得争论吗？

如上所述，现在使用适配器包装的共享代码允许使用现代的`async/await` API 来处理 iOS 端的挂起功能。

上面的手动实现可以被认为是混合了样板文件的过度工程化。从另一个角度来看，当一个用例(或任何具有挂起功能的东西)在应用程序中的许多地方被广泛使用时，应用程序的其他部分以某种方式依赖于它的数据，或者有许多异步函数要同时或以特定顺序调用，那么手动适配器就值得考虑。

适用于`async/await`的作为完成处理程序的挂起函数更容易使用，更容易测试，并且降低了源代码的复杂性。

处理可空结果和错误的样板代码可以提取到其他一些通用函数中，以便更好地处理它。此外，特定的适配器也可以重构为更通用的实现。由于这一点，样板文件可以减少。

# 取消怎么办？

正如 Michał Klimczak 在评论部分提到的，通常在处理暂停函数时，注意取消以防止内存泄漏和节省资源是很重要的。

表示挂起函数的完成处理程序不支持取消，并且不能被取消。

然而，在许多情况下，完成处理程序到`async/await`的简单手动适配器已经足够好了。正确处理取消是一个好的做法，在特殊情况下，这是一个必须具备的机制。

一个典型的问题是挂起函数的自适应，它执行一些耗时的操作，同时使用回调无限地通知结果。以下面的代码为例:

暂停函数定期调用回调

在 Android 端，可以简单地取消启动它的协程来停止回调，而 iOS 端的完成处理程序无法做到这一点。

这里的一个解决方案是重构代码，将回调表示为将发出结果的`Flow`。

流量周期性发射值

可以很容易地调整`Flow`来提供一个 API，知道它是在哪个协程中启动的，并且还简化了 iOS 端的值收集。这里最大的问题是，`Flow`是 Kotlin 中的一个接口，在转换为 Swift 后，它失去了它的泛型类型。这可以通过使用一个类作为 adapter⁴来解决，该类保存由`Flow`发出的一般类型的项目。考虑下面的例子:

简单的流量适配器实施

上面的代码位于一个共享模块中。subscribe 函数为 iOS 提供了一个简单的基于回调的 API 和一个 scope 参数来启动其中的`Flow`。当满足特定条件时，可以在以后取消该范围。

现在，它可以很容易地适应 iOS 端，类似于以前的 async/await 适配器示例。

# **如何在 iOS 上取消流量？**

第一件事是提供一个作用域，它可以是一个实用程序类，根据特定的用例用足够的`CoroutineContext`元素实现一个`CoroutineScope`接口。请考虑下面的例子:

代码片段 above⁴添加了一个关闭作用域的函数，这实际上取消了内部的`job`。

现在，我们可以在平台的`Flow`适配器中添加范围取消逻辑，例如在 iOS 端使用`[withTaskCancellationHandler](https://developer.apple.com/documentation/swift/withtaskcancellationhandler(handler:operation:))`。

Swift 中的异步流量适配器

当运行异步函数的任务将被取消时，作用域将被取消。

*交付更新的逻辑只是一个信息示例，应根据需要进行调整。*

需要注意的一件重要事情是，取消逻辑不需要这样做。

根据用例的不同，用于收集`Flow`的范围可以与其他挂起操作共享。因此，取消可以在进行调用的对象(如平台`[ViewModel](https://developer.android.com/topic/libraries/architecture/viewmodel)`)的反初始化时被调用，而不是单个任务的取消。这些只是手动改编 Kotlin `Flow`时必须考虑的一些实现细节。

现在实现如下:

AsyncFlowAdapter 的用法

在 ExampleViewModel 内部，函数更新可以像下面这样使用:

ExampleUseCaseAdapter 的用法

在前面的片段中给出的例子是一个无限冷流的适应过程，但是适配器实现对于热流也是类似的。

将适配器移至共享代码，以便在运行于作为适配器参数公开的作用域中的协程主体内执行一些挂起操作，这是取消问题的解决方案。

对于`Flow`来说，编写一个通用的适配器很容易，但是并不是所有的东西都可以成为`Flow`。仍然需要使用暂停功能以及提供取消机制。这些情况下的适配器需要为共享代码中的每个单个挂起函数手动编写，以便稍后在平台端进行调整。这使得在 iOS 中使用挂起功能所需的样板文件增加了一倍。记住所有的要求(易用性，取消处理)，这基本上是一个没有解决方案。

# 样板文件的解决方案

好消息是，有一些库可以帮助减少需要手工编写的代码量。

其中之一是一个名为 [Koru](https://github.com/FutureMind/koru) 的库，由 Michał Klimczak 创建，它使用 [KSP](https://kotlinlang.org/docs/ksp-overview.html) 来生成包装器，用于暂停某些类和接口中的函数，这些类和接口用注释标记，然后允许代码生成。所生成的包装器包含具有 API 的函数，这些函数具有作用域参数以及针对它们执行的操作的成功和失败的回调。这个 API 可以很容易地适应异步/等待。

在未来，Koru 可能会为 iOS 提供基础适配器(包装器),这将使适配过程更加顺畅。

另一个不基于注释处理器生成源代码的解决方案是[KMP-本地例程](https://github.com/rickclephas/KMP-NativeCoroutines)。这个库使用所谓的[“编译器方法”](https://kotlinlang.slack.com/archives/C3PQML5NU/p1659266993874829?thread_ts=1659099843.729999&cid=C3PQML5NU)并利用 Kotlin-ObjC 互操作。该解决方案提供了通用类型的流实现，解决了取消支持的问题，并且不需要用注释来标记代码就可以正常工作(这里的注释用于禁止某些函数的生成)。

然而，这种方法在编译过程中有一些递归错误的问题，但是，根据库作者 Rick Clephas 的说法，库中有很多改进的计划。

当需要从 iOS 调用的挂起函数数量增加时，这两种解决方案都值得一试。同样的情况也适用于无法使用 API 的情况，API 很容易手动调整，同时仍然能够感知取消。要记住的重要一点是，这两个库都是开源解决方案。这意味着它们需要由维护者和社区来更新，以便跟上快速发展的 Kotlin 环境和可能出现的潜在错误。

对于那些仍然对使用第三方库持怀疑态度的人来说，还有另一个主要适用于绿地项目的解决方案。那就是考虑需要调用的挂起函数的量是不是不能通过一些架构上的改变来减少。

其中一个变化可能是将表示逻辑(在许多情况下是使用基于挂起功能的共享业务逻辑)推到共享代码的重构。这可能导致暂停功能的数量减少到只有表示状态/副作用的可适应的流集合(MVVM，MVI ),或者导致在平台侧(MVP)使用完全没有暂停功能的同步视图接口(协议)实现。

# 结论

使用适配器模式将完成处理程序转换为 async/await 使得代码可读性更好，使用起来也更愉快。

好处包括对运行代码的更多控制、简单的测试和通用模式的封装(如错误处理)。

现在还没有在 iOS 端使用挂起功能的灵丹妙药。

手动适配器实现的缺点主要包括样板文件和缺少取消功能，因此在手动实现之前，总是值得三思，以确保手动实现符合预期。本文中描述的替代解决方案和规避可能有助于提供满足预期需求的解决方案。

Kotlin/Native interoperability 还提供了一个从挂起函数生成的 async/await API，但是正如文档中提到的，[它们只是为了好玩](https://kotlinlang.org/docs/native-objc-interop.html#suspending-functions)。将来，可能不再需要适配器，但是现在，手动实现可能是一条出路。

这可能不是处理数据的最佳逻辑。

用完成处理程序测试异步代码处理在操作的回调被触发后切换回同步上下文的问题。更多关于测试异步/等待代码[的信息，请点击这里](https://www.swiftbysundell.com/articles/unit-testing-code-that-uses-async-await/)。

⁴I 在约翰·奥莱利的《人类空间》中首次发现了这种模式