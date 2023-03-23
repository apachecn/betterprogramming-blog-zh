# 用 Kotlin 编写条件语句的新方法

> 原文：<https://betterprogramming.pub/a-new-way-to-write-conditional-statements-in-kotlin-a3c029e417dc>

## 在 Kotlin 中使用“when”使代码更具可读性

![](img/30cff42796af2f653d3668aa5ffa63d4.png)

[斯科特·格雷厄姆](https://unsplash.com/@sctgrhm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

自从 Kotlin 诞生以来，它一直是全球 Android 开发者开发应用程序的首选语言之一。人们喜欢 Kotlin 有各种原因:扩展功能、做事方便、样板代码少、跨平台支持等等。

然而，我们中有许多人忽视了科特林的小事情的潜力。比如我还是偶尔看到很多队友用`if`和`else if`嵌套语句。

有了 Kotlin，我们可以有一个独特而简单的解决方案，可以处理多种问题。例如，`when`语句可以替换任何 Kotlin 项目中的所有条件语句，只要你足够聪明地使用它。

不再拖延，让我们学习如何有效地使用 Kotlin 中的`when`语句。

# 将 If/Else 替换为 When

`If` / `Else`语句自编程出现以来就一直存在。不管平台/语言如何，开发人员都倾向于将它们用于条件执行。

感谢科特林，我们不再需要使用旧的风格。`when`语句处理所有类型的条件语句，包括`if` / `else`，它们的嵌套语句也可以用`when`替换。

让我们考虑一个简单的例子，我们基于输入字符串返回表情符号。如果你考虑按常规写作，我们可以这样做:

传统的 if/else 执行

我们可以使用 Kotlin 中的`when`语句重写上面的代码。它提供了比`if` / `else`更好的可读性:

用 when 语句替换 if/else。

# 将开关盒替换为 When

与`if` / `else`语句类似，`switch` 格是古代处理附条件执行的方式之一。在 Kotlin 中，为了在实现条件执行时保持一致性，我们可以用类似的方式将`switch` case 和`if` / `else`替换为`when`。

首先，让我们看看如何以传统方式用`switch`实现上面的例子:

开关案例示例

`Switch` case 可以替换为`when`语句，类似于`if` / `else`。这将使代码更具可读性、一致性，并为新开发人员提供快速适应性。

# 用 When 块返回值

到目前为止，您已经看到了 Kotlin 中的`when`块替换条件语句的能力。但它能做的不止这些。例如，它可以在执行必要的步骤后从条件块中返回值。看一看:

When 块返回值。

这种类型的代码实现为开发人员节省了大量时间。

# When 的任意条件

我们可以使用一个或多个检查来用`when`操作符执行一个块:

使用 when 执行任意语句。

只有一种情况必须匹配要执行的相应代码块，所以逗号充当了一个`OR`操作符。除此之外，`when`块执行类似 range 的内部条件(如上所示)。

# 使用不带参数的 When

到目前为止，我们已经在一个论点中使用了`when`。但是`when`真正的潜力在于我们可以不带任何参数的使用它，这是我在使用 Kotlin 之前从未见过的。看一看:

When block 不带参数。

它作为一个简单的`if` / `else`块，具有类级别的可变条件。

# 将实例与 When 进行比较

我们还可以在运行时使用`is`操作符检查给定的操作符是否是特定类型的实例。`is`操作符类似于 Java 中的`instanceof`操作符。我们可以在`when`块中使用`is`和`!is`操作符。看一看:

实例检查与科特林的时候。

# 使用 When 的优势

*   在 Java 中，像`switch`这样的一些条件语句只能用原始数据类型、枚举和字符串类来操作。但是 Kotlin 允许将`when`用于用户定义的类型。这使得它对开发人员更加友好。
*   与 Java 不同，Kotlin 的`when`可以用于动态值，比如函数的返回类型。
*   我们还可以使用充当`contains`的`in`操作符来检查给定的参数是否存在于集合中。

# 奖金

要了解更多关于 Kotlin 的信息，请阅读 Kotlin 高级编程系列的前几部分:

*   [“使用 Kotlin 进行高级编程](https://medium.com/better-programming/advanced-android-programming-with-kotlin-5e40b1be22bb)”
*   [“用 Kotlin 进行高级编程—第二部分](https://medium.com/android-dev-hacks/advanced-android-programming-with-kotlin-part-2-aae2a15258b0)”
*   [“用 Kotlin 进行高级编程—第三部分](https://medium.com/better-programming/advanced-programming-in-kotlin-2e01fbc39134)”
*   [“kot Lin 中的高级 Android 编程—第四部分”](https://medium.com/better-programming/advanced-android-programing-in-kotlin-part-4-187b88fea048)

# 结论

目前就这些。希望你学到了有用的东西。