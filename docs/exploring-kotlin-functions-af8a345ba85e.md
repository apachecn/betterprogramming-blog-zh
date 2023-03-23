# 探索科特林函数

> 原文：<https://betterprogramming.pub/exploring-kotlin-functions-af8a345ba85e>

## 中缀、内联等等

![](img/ed0cd521f6c56fa5bd65a259774dac3b.png)

马克·赖歇尔在 Unsplash[上的照片](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 介绍

随着 Kotlin 在 Android 开发中的引入，我们最终都会得到那些包含我们在 Android 应用程序开发中使用的各种函数的 Util 文件。

Util files 并不是一个新的产品，但是 Kotlin 的功能性使它比以前更加精致和优化。我们都知道 Kotlin 是一种面向对象的函数式编程语言。

根据我的经验，我已经使用 Kotlin 的面向对象功能来创建基类和实现复杂的架构。但说到功能性能力，Kotlin 要方便得多。它减少了样板代码，提高了代码的可读性和重用性。

因此，我决定写一篇文章来记录我使用的所有函数类型。我们开始吧！

# 扩展功能

通过扩展函数，Kotlin 提供了用新功能扩展类的能力，而不必继承类或使用设计模式。作为一名迁移到 Kotlin 的 Java 开发人员，这个特性似乎好得难以置信——但它是真实的。

在我迄今为止使用的所有扩展函数中，我喜欢显示、移除和隐藏函数，如下所示:

```
fun View.remove() {
    if (visibility != View.GONE) visibility = View.GONE
}

fun View.show() {
    if (visibility != View.VISIBLE) visibility = View.VISIBLE
}

fun View.hide() {
    if (visibility != View.INVISIBLE) visibility = View.INVISIBLE
}
```

扩展函数不仅提高了代码的可重用性，还使 Kotlin 更加易读和简洁。强烈推荐你阅读这两篇文章，了解 Android 开发中更有用的扩展函数:

*   [5 个 Kotlin 扩展，让您的 Android 代码更具表现力](/5-kotlin-extensions-to-make-your-android-code-more-expressive-4c9243cb9466)
*   来自 KTX 库的 5 个强大的 Kotlin 扩展

# 在一条直线上的

在 Kotlin 中，扩展函数、lambads 等作为对象存储。所以它们消耗内存，虚拟调用会引入运行时开销。我们有时可以通过内联函数代码来消除内存开销。

内联函数是用关键字`inline`声明的。内联函数的使用增强了 Kotlin 的性能。*内联函数告诉编译器将参数和函数复制到调用点。*

很少有人认为在上面使用的扩展函数上使用 inline 是有意义的，这样它们就可以被移动到调用站点而不是存储为对象。看一看:

```
inline fun View.remove() {
    if (*visibility* != View.*GONE*) *visibility* = View.*GONE* }

inline fun View.show() {
    if (*visibility* != View.*VISIBLE*) *visibility* = View.*VISIBLE* }

inline fun View.hide() {
    if (*visibility* != View.*INVISIBLE*) *visibility* = View.*INVISIBLE* }
```

# 跨线

`Crossinline`允许你标记一个 lambda，禁止它使用非本地返回。实际上，`inline` lambdas 将`return`解释为非本地返回，而`return`在非内联的 lambdas 上是不允许的。看一个简单的例子:

下面是上述程序的输出:

```
Hello, Main!! 
Hello
```

不打印`main finished`的原因是 inline lambdas 将`return`解释为非本地返回。现在当你改成 crossinline 的时候，就不允许在里面返回了，这就允许编译器执行最后一条语句。

# 中缀

`infix`符号允许我们快速调用单参数函数。为了创建一个`infix`函数，需要两个参数。第一个参数是目标对象，第二个参数是传递给函数的实际参数。

创建`infix`函数就像创建`inline`函数一样。语法上唯一的区别是我们使用了`infix`关键字而不是`inline`。现在，让我们看看如何调用`infix`函数。

```
val fruit = Fruit()                       
fruit type "apple"
```

在中缀函数的帮助下，我们可以使代码更加易读和简洁。

# 高阶的

可作为参数传递或可从函数返回的函数称为*高阶函数。*换句话说，高阶函数就是以函数为参数或者返回函数的函数。

不管您在哪个平台上开发，这些类型的函数都非常方便。作为一名 Android 开发人员，我发现高阶函数可以非常方便地代替点击监听器并将函数作为参数传递。

# 嵌套函数

我们可以在另一个函数中编写一个函数，类似于 Python 中的嵌套函数。嵌套函数的作用域是外部函数，只有外部函数可以包含嵌套函数。内部函数不受函数外部发生的任何事情的影响:

# 具有默认值和命名参数的函数

Kotlin 有许多有用和被低估的功能，我敢肯定这是其中之一。

假设我们要写一个计算两个值之和的函数。这里棘手的部分是用户可以传递两个值或其中任何一个值，或者什么都不传递。我确信 Java 可以处理它，但不像 Kotlin 那样优雅和简单。看一看:

```
fun add(a : Int = 0, b : Int = 0) =  a + b
```

这不是很棒吗——一个参数有默认值的函数！您可以根据自己的需要只传递`a` 或`b` 值。要传递特定的参数，我们可以使用 Kotlin 中的命名参数特性:

# λ表达式

Lambda 是函数的最简单形式，只有一行表达式(大多数情况下)。在 Kotlin 中，lambda 函数用花括号`{}`括起来。Lambda 函数不像普通函数那样有`fun`关键字或任何访问修饰符(public、private 和 protected)。看一看:

Kotlin 中的简单 lambda 函数

Kotlin 中的 lambda 函数没有返回类型，但我们可以传递参数，如下所示:

# 通用函数

泛型是 Kotlin 中最强大的特性之一。我们可以创建一个泛型类、函数或属性，并用不同的数据类型访问它们，同时确保编译时的类型安全。

```
fun <T> genericFunc(objects: T) {
    // Do whatever
}
```

我最常用的一个通用函数包括从包中检索数据:

这看起来很简单，但它减少了开发人员的大量工作，提高了代码的可读性和简洁性，所有这些都是通过几个函数实现的。

# 奖金

如果你对科特林感兴趣，我强烈推荐你阅读以下文章:

*   [“使用 Kotlin 进行高级编程](https://medium.com/better-programming/advanced-android-programming-with-kotlin-5e40b1be22bb)”
*   [“使用 Kotlin 进行高级编程—第 3 部分](https://medium.com/better-programming/advanced-programming-in-kotlin-2e01fbc39134)”
*   [科特林](https://medium.com/@sgkantamani/sealed-classes-in-kotlin-e48e072daca8)中的密封类

**我最近看到了一篇由 [Satya Pavan Kantamani](https://medium.com/u/d255c4fc9d76?source=post_page-----4c9f44d04e68--------------------------------) 发表的关于 Android 中的 ExoPlayer 的优秀文章，强烈推荐:**

**[](https://levelup.gitconnected.com/customize-exoplayer-overlay-look-like-youtube-player-14fdd6d4583d) [## 自定义 ExoPlayer 覆盖看起来像 Youtube 播放器

### 为 ExoPlayer 创建自定义 UI 元素

levelup.gitconnected.com](https://levelup.gitconnected.com/customize-exoplayer-overlay-look-like-youtube-player-14fdd6d4583d) 

目前就这些。希望你学到了有用的东西。感谢阅读。**