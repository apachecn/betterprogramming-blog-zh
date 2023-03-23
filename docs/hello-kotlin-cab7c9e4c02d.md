# 你好，科特林！

> 原文：<https://betterprogramming.pub/hello-kotlin-cab7c9e4c02d>

## 构建一个移动应用程序或编写第一行代码。下面是对 Kotlin 的简短介绍，以及为什么它应该是你学习的下一门语言

![](img/546bf99f26e9126872e3efaa625a7252.png)

[Danial RiCaRoS](https://unsplash.com/@ricaros?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

# 科特林长什么样？

让我给你介绍几行 Kotlin 代码:

看起来像是 Python 和 JavaScript 与 Java 类型的混合，对吗？

上面的介绍性代码有几个突出的特性，并将 Kotlin 与 Java 和 Python 等其他语言进行了比较。如果要测试并运行示例代码，请选择以下一项或两项:

1.  下载 [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) 并跟随[这篇简短的教程](https://kotlinlang.org/docs/tutorials/getting-started.html)。它将指导您如何在 IDE 中创建新项目，并向您展示如何创建和运行 Kotlin 文件。
2.  如果你对你的操作系统命令行很熟悉，那么[下载 Kotlin 命令行编译器](https://kotlinlang.org/docs/tutorials/command-line.html)。

让我们开始吧。

## 1.用关键字`fun`定义函数

开始编写 Kotlin 函数既快速又简单。关键字`fun`开始函数定义。我们的函数名为`main`，它有一个名为`args` *的参数。*`args`*的数据类型是`Array<String>` *，*这是一个由字符串组成的数组[如果使用 JVM 运行你的代码](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-array/)就是 Java 数组*。*如果您不期望任何命令行输入，则不需要`args`参数。*

*当你编译并运行一个 Kotlin 文件时，解释器会寻找`main()`函数作为程序的入口点。*

## *2.关键字`var`和`val`用于定义变量*

*如果使用`val`关键字声明一个变量，那么这个变量的值以后就不能修改了。它是一个只读的局部变量。*

```
*val x = 1      // Kotlin will infer the Int type 
val y: Int = 2 // declare and assign a variable
val z: Int     // must specify data type if not assigning value
z = 3          // assign value at a later point in program*
```

*如果需要重新分配变量值，使用`var`关键字:*

```
*var sum = 0    // Kotlin will infer the Int type
sum += 5*
```

## *3.变量可以为空*

*您可以允许变量保存`null`值，但是在定义变量时必须明确指定可空类型。*

*在类型声明后添加符号`?`告诉 Kotlin 变量可以为空。*

*下面是一个在逻辑中使用可空类型来安全执行操作的函数示例:*

*参数的`Double?`数据类型表明函数的输入可以是一个`Double`值或`null`。我们首先验证`args1`和`args2`都不为空，然后执行运算。函数的返回类型也是`Double?`(在函数头的最后一个冒号后声明)，这意味着函数可能返回一个`Double`值或一个`null`值。我们返回一个`null`值来表示`args1`或`args 2`不是一个数字。*

## *4.使用$符号可以快速简单地打印数值*

*Kotlin print 语句在内部调用 Java 打印方法`System.out.print`并打印到标准输出。*

*以下几个场景展示了如何使用`$`符号打印变量值和函数值:*

*第 5 行展示了如何进行函数调用并立即打印其返回值。*

*在 Java 中，相同的 print 语句如下所示:*

```
*System.out.println("The product of " + a + " and " + b + " is " + (product(a, b)))*
```

*程序员不得不做大量的手工字符串连接。*

*Kotlin 的 print 语句在语法上与 Python 相似。它们简洁易读，输入速度快，并且很容易打印任何数据类型的变量。*

## *5.Kotlin 可以访问所有 Java 虚拟机(JVM)库*

*可以访问数组、散列表、列表等类。使 Kotlin 能够利用 Java 强大的数据结构。Kotlin 文档说 Kotlin 是用 Java [互操作性](https://kotlinlang.org/docs/reference/java-interop.html)设计的。*

*只需在 Kotlin 文件的顶部导入 Java Util 包:*

*使用`ArrayList`方法与在 Java 中使用它们几乎是一样的。唯一的区别是`size()`方法变成了 Kotlin 中的一个属性。因此，我们用`items.size`代替了 Java 中的`items.size()`。*

*现在，让我们看看 Kotlin 是如何引用 Java 类并访问它们的方法的:*

*上面的类在结构上代表了一个典型的 Java 类。它包含单个属性和一个`getter`和`setter`方法，其他 Java 类包含用于操作和计算的附加方法。*

*您可以在 Kotlin 文件中使用 Dog 类，而不使用 import 语句(只要 Java 文件和 Kotlin 文件在同一个文件夹中):*

*`Getter`和`setter`函数在 Kotlin 中被表示为属性。这意味着 Java 类中以`get`或`set`开头的方法可以通过引用属性名在 Kotlin 中使用。当我们将“德国牧羊犬”赋给`dog.breed`时，我们正在调用 Java `setBreed()`方法。当我们打印`dog.breed`的值时，我们正在调用`getBreed()`方法。*

*下面是 Java 中相同操作的样子:*

*当构造一个新对象并调用它的方法时，Kotlin 语法比 Java 更简洁，看起来也更干净。*

# *我在哪里用过 Kotlin？*

*我第一次接触 Kotlin 是在我的 Android 移动应用程序开发课上。学习曲线很低，因为我非常熟悉 Java，互操作性使得从 Java 到 Kotlin 的过渡非常容易管理。很快，我发现自己编写了更少的样板代码，维护了易于阅读的文件，并使用可空类型来保护我的应用程序免受可能的错误。我在不牺牲代码质量的情况下开发得更快。*

*上完课，我了解到很多大应用(Pinterest、Square、Lyft、Trello、 [Zomato](https://www.youtube.com/watch?v=ao9QvtpszOU&feature=youtu.be) 等等)都把自己的 app 代码库转换成了 Kotlin。结果是大幅减少了代码行，提高了程序员的性能，并在编译时检测到了应用程序中的错误。*

*几个月后，在我的编译器课程中，我们被要求选择一种语言来编写编译器。我和我的搭档选择了科特林。其他大部分同学选择了 Python。我们觉得使用 Python 有很多好处。我们能够快速编写[数据类](https://kotlinlang.org/docs/reference/data-classes.html)(在 Java 中，单行数据类声明需要 50 行或更多行)。我们的对象和变量可以有数据类型，这使得我们的数千行代码更可读，并且我们允许我们的许多对象可以为空。Kotlin 允许我们使用函数式设计，编写许多小方法来构建我们的编译器。最重要的是，我们利用了大家熟悉的 Java 类，比如 ArrayList 和 HashMap。*

# *结论*

*Kotlin 被证明在语法上比 Java 简单，但比 Python 复杂一点。Kotlin 是一种类型化语言，这是 Python 的一大优势(当然，这取决于您正在从事的项目)。Kotlin 中提供的安全检查，如检查可空性或验证对象的数据类型，有助于编写更安全的代码，更不容易出现运行时错误。最后，它是一种熟悉的语言，学习难度很低，因为它的许多组件都可以在 Java、JavaScript 和 Python 中找到。*

*概括一下:*

*   *简明代码*
*   *可读语法*
*   *易于检查和维护*
*   *运行时错误不再令人头疼*
*   *Java 友好的*

*我希望这能让你写出你的第一行 Kotlin 代码！如果你已经在使用 Kotlin，我希望听到更多关于你的经历。*