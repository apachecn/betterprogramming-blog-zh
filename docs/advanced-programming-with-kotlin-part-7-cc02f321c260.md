# 用 Kotlin 进行高级编程(第 7 部分)

> 原文：<https://betterprogramming.pub/advanced-programming-with-kotlin-part-7-cc02f321c260>

## 从 Java 调用 Kotlin

![](img/900b790f84cb812926155fd5eddf8e4c.png)

[Levi Lei](https://unsplash.com/@levilei?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

与 Java 不同，在 Java 中我们需要编写所有内容，Kotlin 编译器可以理解代码，并在幕后编写样板代码。例如，它可以推断变量声明中的类型。这提高了生产率并节省了时间。

如果你在网上搜索，你会发现 Kotlin 解决了 Java 的许多痛点，以及 Kotlin 的特性如何让 Android 开发更有趣。

正如[为你开源](https://opensourceforu.com/2018/07/kotlin-a-language-for-modern-multi-platform-applications/)所描述的:

> “Kotlin 是一种多平台编程语言，简洁、安全、可互操作且工具友好。它是一种静态类型的编程语言，运行在 Java 虚拟机上，也可以编译成 JavaScript 源代码，或者可以使用 LLVM 编译器基础设施。”

毫无疑问，Kotlin 有助于开发人员比使用 Java 更高效，但我们不能一夜之间就将多年的编码转换成 Kotlin。将 Java 项目转换成 Kotlin 的最大优势之一是我们可以增量地完成，这意味着我们可以在同一个项目中同时拥有 Kotlin 和 Java。

这在理论上很棒，但在现实中，我们需要学习一些东西，帮助我们从 Java 中调用 Kotlin 代码。在本文中，我们将了解三个重要的注释，它们将有助于从 Java 中调用 Kotlin 代码。

# @JvmStatic

Kotlin 更像是一个函数式程序。它把功能放在第一位。与 Java 不同，Kotlin 中的包级函数是静态函数。创建静态函数的另一种方法是在伴随对象中声明函数。

为了更好地理解它，让我们创建一个简单的具有单一功能的 Kotlin 对象。看一看:

现在让我们看看如何从 Kotlin 和 Java 代码中调用`readyTheBride` 函数。

我们可以使用类名和函数调用直接从 Kotlin 中完成:

```
MarriageUtils.readyTheBride() //Kotlin
```

但是当我们在 Java 代码中做同样的事情时，它会抛出一个编译器错误。我们不能从 Java 代码中直接访问伴随对象函数。为此，我们需要一个变通方法，就是使用`Companion` 作为到达函数的桥梁:

```
MarriageUtils.Companion.readyTheBride() //Java
```

这是最常用的过程，但这个问题的真正解决方案是使用`JvmStatic`注释，这将向编译器传达这是一个在 Kotlin 和 Java 中都可以访问的静态函数。看一看:

现在，我们可以从 Kotlin 和 Java 中直接调用`readyTheBride`函数，无需任何伴随。

```
MarriageUtils.readyTheBride() // Kotlin
MarriageUtils.readyTheBride() // Java
```

如文档中的[所示，`JvmStatic`注释:](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-static/)

> "指定如果该元素是一个函数，则需要从该元素生成一个额外的静态方法。如果此元素是一个属性，则应生成附加的静态 getter/setter 方法。

# @JvmOverloads

通常，为了建模一个只保存数据的类，我们使用一个`data class`。这种方法将自动生成所有的样板文件。我们也可以给变量赋予默认值。看一看:

```
data class Marriage(val place : String = "London", val bride : String, val groom : String)
```

`Marriage`是保存三个属性数据的数据类: `place`、`bride`和`groom`。`bride`和`groom`是正常参数，但`place`是有默认值的参数。当我们在 Kotlin 代码中使用缺省值参数时，给它们赋值是可选的。看一看:

```
val a = Marriage( bride = "Monica", groom = "Chandler" )
val b = Marriage( place = "USA", bride = "Monica", groom = "Chandler" )
```

Kotlin 代码中有两种方法可以完美地工作。让我们看看我们能从 Java 代码中做些什么:

```
Marriage a = new Marriage( "Monica", "Chandler"); //Compiler errorMarriage b = new Marriage( "USA", "Monica", "Chandler"); 
```

第一种方法抛出一个编译器错误，因为 Java 没有缺省值参数。这个问题的解决方案是将`JvmOverloads`注释与`constructor`一起使用:

```
data class  Marriage @JvmOverloads constructor(val place : String =   "London", val bride : String, val groom : String)
```

如果一个方法有 A 参数和 B 参数(它们有默认值)，就会生成 B 重载。第一个使用 A-1 参数(除了最后一个使用默认值)，第二个使用-2 参数，依此类推。由于有多个构造函数，当我们从 Java 中调用这些类时，这将完美地工作。

# @JvmField

为了理解这个注释是如何有用的，我们需要回顾一下我们用 Java 创建 POJO 类的方式。手动创建一个包含所有 getters、setters 和其他属性的 POJO 类需要做大量的工作。看一看:

然而，当我们试图在 Kotlin 中实现相同的类时，它看起来如下:

```
data class  Marriage (val place : String , val bride : String, val groom : String)
```

所以当我们试图访问 Kotlin 数据类变量时，从 Kotlin 方法到 Java 方法都会有所不同。

首先，让我们从科特林代码中获取:

接下来，让我们从 Java 代码中访问:

```
Marriage marriage = new Marriage( "USA", "Monica", "Chandler");
String place = marriage.getPlace();
```

这里，我们需要使用 getters 和 setters，而不是直接从变量中访问。但是有一个直接访问变量的变通方法。我们需要对您想要直接访问的变量使用`JvmField`注释。看一看:

```
data class Marriage (@JvmField val place : String, val bride : String, val groom : String)
```

现在，我们可以从 Java 访问数据类中的变量，如下所示:

```
Marriage marraige = new Marriage( "USA", "Monica", "Chandler");
String place = marraige.place; // Variable access
String groom = marraige.getGroom(); // Method access
```

# 结论

要了解更多关于 Kotlin 的信息，请阅读 Kotlin 高级编程系列的前几部分:

*   [“使用 Kotlin 进行高级编程](https://medium.com/better-programming/advanced-android-programming-with-kotlin-5e40b1be22bb)”
*   [“用 Kotlin 进行高级编程—第二部分](https://medium.com/android-dev-hacks/advanced-android-programming-with-kotlin-part-2-aae2a15258b0)”
*   [“用 Kotlin 进行高级编程—第三部分](https://medium.com/better-programming/advanced-programming-in-kotlin-2e01fbc39134)”
*   [“使用 Kotlin 进行高级编程—第 4 部分”](https://medium.com/better-programming/advanced-android-programing-in-kotlin-part-4-187b88fea048)
*   [“使用 Kotlin 进行高级编程—第 5 部分”](https://medium.com/better-programming/advanced-programming-in-kotlin-part-5-b674ce9e692f)
*   [“使用 Kotlin 进行高级编程—第 6 部分”](https://medium.com/better-programming/advanced-programming-with-kotlin-part-6-3f33290d8aad)

要了解更多关于 Kotlin 协同例程和 Kotlin 的其他高级特性，请阅读以下文章:

*   [“科特林协程，从基础到高级](https://medium.com/better-programming/kotlin-coroutines-from-basics-to-advanced-ad3eb1421006)
*   [“如何使用 Kotlin 密封类进行状态管理”](https://medium.com/better-programming/how-to-use-kotlin-sealed-classes-for-state-management-c1cfb81abc6a)
*   [“使用新 Kotlin 流异步加载数据](https://medium.com/better-programming/asynchronous-data-loading-with-new-kotlin-flow-233f85ae1d8b)
*   [“探索 Kotlin 中的集合和序列”](https://medium.com/better-programming/exploring-collections-and-sequences-in-kotlin-3a324ea08fb9)
*   [“为什么以及如何使用 Kotlin 的本地序列化库](https://medium.com/better-programming/why-and-how-to-use-kotlins-native-serialization-library-c88c0f14f93d)”
*   [“学习如何组合科特林流](https://medium.com/@sgkantamani/learn-how-to-combine-kotlin-flows-317849a71d3e)”

目前就这些。希望你学到了有用的东西。感谢阅读。