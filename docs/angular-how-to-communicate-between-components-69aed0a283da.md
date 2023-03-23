# 如何在 Angular 组件之间进行通信

> 原文：<https://betterprogramming.pub/angular-how-to-communicate-between-components-69aed0a283da>

多个组件使我们的应用程序更具可重用性，但是我们如何在它们之间进行通信呢？

![](img/b2c01a27b149f20deede1b71ba467908.png)

来源[https://unsplash.com/photos/6bKpHAun4d8](https://unsplash.com/photos/6bKpHAun4d8)

# 将数据传递给组件

我们想使用来自`@angular/core`的`@Input()`装饰器将数据从父组件传递给组件。让我们回顾一个简单的例子，看看这是如何实现的。

首先，我们需要创建一个新组件`ng g c name_of_your_component`，我将我的组件命名为`first`，然后导入`@Input()`装饰器并创建一个`name`变量:

我们的`first.component.html`应该是这样的:

我们可以在`app.component.html`中使用我们的组件，但是对于`name`变量呢？我们可以通过使用`class="btn"`将数据传递给组件。不清楚？请参见下面的示例。

当您执行`ng serve -o`时，您应该看到`Hello, Asu!`这是一种从父组件向组件传递数据的简单方法。

# 从组件获取数据

有一个`@Output()`装饰器，就像`@Input()`一样，你可以从`@angular/core`导入它:

我们将`@Input()`装饰器改为`@Output()`，并用`constructor`中的`Asu`初始化`name`变量。

在`app.component.ts`中，我们需要像读取对象属性一样读取`name`变量，但首先让我们将角度`id`添加到`app.component.html`中:

我们可以通过使用来自`@angular/core`的`@ViewChild`装饰器来使用`app.component.ts`中的 id `#firstComponent`:

在第 10 行你可以看到我们使用了`@ViewChild('id')`。在这种情况下，id 是`firstComponent`。更远的部分是标准的`accessor name_of_variable: type`。在由`OnInit`接口实现的功能`ngOnInit`中，我们使用基本的警告功能来显示来自第一个组件的`name`。您可以尝试在`constructor`中做同样的事情，但是在控制台中，您应该会看到类似于`Can't get name of undefined`的错误。这是因为在构造函数级别，我们的第一个组件直到我们的父组件激活后才激活。

# 组件间通信的终极方式

服务！是的，当使用服务在组件之间进行通信时，不需要父子或父子关系。

生成服务`ng g s services/Store --module=app`。我在`app.module`中添加了一个`--module`标志来提供服务。

我想给你们看一个不好的例子，为此，我们需要在我们的组件中做一些改变:

在第 14 行我们注入`StoreService`。它可以从`store`属性中获得。让我们生成名为`second`的第二个组件:

并将其添加到`app.component.html`:

在这个简单的例子中，我们更改了`second`组件中的`name`，然后在`first`组件中刷新它。仅仅更改名称就需要做大量的工作，而刷新并不是我们想要的。相反，我们可以使用名为`Subject`和`Observable`的`rxjs`库中的工具。使用这些有什么好处？`name`将在`first`组件中自行刷新，无需我们的帮助。

让我们告诉你如何做到这一点。

我们可以保持`second`组件现在的样子，但是我们要改变`first`组件和`StoreService`。

在构造函数中，我们订阅了来自`StoreService`的名为`getName()`的函数；我们可以订阅这个，因为它返回`Observable`。Subscribe 意味着每当可观察到的变化时，它将做`subscribe`中的所有事情。

我们需要在 html 中添加`async`管道。管道本身将处理诸如在组件被破坏后显示新值或取消订阅之类的事情(它防止内存泄漏)。

每当我们在主题属性上调用`next`函数时，可观察值就会改变，在我们的例子中，这个函数叫做`name`。

在我看来，这是组件间通信的最佳方式，但这取决于我们想要共享数据的组件树中有多少组件。