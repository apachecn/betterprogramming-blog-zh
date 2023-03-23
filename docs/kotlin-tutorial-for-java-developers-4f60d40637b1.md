# 面向 Java 开发人员的 Kotlin 教程

> 原文：<https://betterprogramming.pub/kotlin-tutorial-for-java-developers-4f60d40637b1>

## Android 开发人员进行语言转换所需要知道的一切

![](img/1ccd4be5c3d8c094104cfeba02e79902.png)

图片来源:作者

Kotlin 是一种新兴的编程语言，旨在解决 Java 的缺陷，并与现代世界的适应性需求更加紧密地联系在一起。虽然 Kotlin 曾经是一种小众语言，但它正在迅速发展，许多公司都从 Java 转向了 Kotlin。

事实上，谷歌在 2017 年就宣布正式支持 Kotlin，而不是 Java，作为 Android 开发的主要语言。

现在越来越多的公司在寻找 Kotlin 开发者。今天，我们将通过强调每个基本 Kotlin 概念的差异和语法来帮助您过渡到这种新语言。

**下面是我们今天要讲的内容:**

*   科特林 vs. Java
*   科特林的《你好，世界》
*   Kotlin 变量和数据类型
*   科特林条件句和循环
*   科特林收藏
*   科特林函数
*   用 Kotlin 进行函数式编程
*   下一步跟科特林学什么

# 科特林 vs. Java

Kotlin 是一种开源的静态类型编程语言，由 Jetbrains 开发，以创建 IntelliJ IDEA IDE 而闻名。Kotlin 的目标是成为一种更易用、更简洁的语言，适用于任何类型的项目。

Kotlin 主要用于 Android 应用程序开发或使用 React 之类的框架进行前端开发。

Kotlin 是独一无二的，因为它完全可以与 Java 代码互操作，这意味着您可以使用现有的 Java 库或框架。Kotlin 代码可以转换成 JavaScript 或 Java 字节码，用于 Java 虚拟机(JVM)或 Android 开发。

现代公司对 Kotlin 的多功能性特别感兴趣，因为开发人员可以对公司需要的任何项目使用同一种语言。

随着敏捷开发变得越来越流行，像谷歌、亚马逊、网飞、优步这样的公司已经开始雇佣 Kotlin 开发人员。事实上，在 GitHub 的年度报告中，它被列为 2020 年增长第四快的语言，报告了更多 Kotlin 开发者的实现。

## 静态打字

像 Java 一样，变量只能被赋予与其类型相匹配的值。这使您可以在执行前轻松捕捉编译器发现的错误。

```
var m : Int = 12m = 10          // okm = "twelve" // error!m = 10.0        // error!
```

## 非局部跳跃

Kotlin 允许你在任何地方退出一个函数。

## 集合过滤

Kotlin 允许您在集合中搜索任何符合传递标准的数据。

## **扩展功能**

扩展函数允许您扩展现有组件，而无需在其中编写方法，从而使代码更加简洁。

## 高阶函数

Kotlin 函数被视为一级函数，这意味着它们可以从其他函数传递或返回。

```
people.filter { person -> person.headCount == 1 }
```

## 惰性装载

Kotlin 通过只加载它立即需要的资源来减少加载时间。

# Kotlin 和 Java 的主要区别

## 科特林

*   允许从 Kotlin 跨平台传输到 Android、JVM 等等。
*   为函数式编程和面向对象编程从头开始构建。
*   为简洁而设计。
*   内置智能强制转换和隐式强制转换。
*   无法分配`null`值以确保空值安全。
*   对协程、扩展函数和数据类等现代特性的现成支持。

## Java 语言(一种计算机语言，尤用于创建网站)

*   不跨平台，不能传输。
*   为面向对象编程而构建，功能支持极其有限。
*   专为特异性而设计。
*   严格的显式类型规则。
*   允许空值，但抛出`nullPointerException`，这必须通过异常处理来捕捉。
*   通过复杂的变通方法对现代功能的有限支持。

# 科特林的《你好，世界》

让我们看看 Java 和 Kotlin 中的一个`Hello World`程序，看看它们的区别。

您可以使用 Kotlin 插件在任何 Java IDE 中编写 Kotlin，甚至可以直接在命令行中编写。

显然，Kotlin 更简洁，只使用了两行代码。这是因为 Kotlin 允许函数存在于类之外，这意味着我们不必声明一个`HelloWorld`类。

*   在 Kotlin 代码的第 1 行上，我们定义了`main`方法。和 Java 一样，每个程序都需要一个`main`函数。Kotlin 版本 1.3+从`main`中移除了输入和输出，以使语法更简单，视觉上更清晰。
*   在**第 2 行**上，我们使用内置的`println()`函数打印字符串`Hello, World!`。这与 Java 的打印功能工作方式相同，但不需要从外部`System`类中提取。
*   在第 2 行的结尾，注意没有分号来标记该行的结尾。Kotlin 逐行检测语句的结尾，不需要分号。

# Kotlin 变量和数据类型

您可以使用`var`或`val`关键字来定义 Kotlin 变量。

```
var <variableName> = <value> //mutableval <variableName> = <value> //read-onlyval <variableName>: <dataType> = <value> // explicit type casting
```

Kotlin 中的变量与 Java 中的变量非常相似，除了:

*   Kotlin 为函数式编程提供了不可变(只读)的数据类型。
*   Kotlin 允许智能类型，有时也称为隐式类型，它自动检测变量的数据类型，而无需显式标记。
*   默认情况下，Kotlin 不允许将`null`值赋给变量或返回值。使用 Java，您可以赋值`null`，但是如果您试图访问那个值，它将抛出一个`nullPointerException`。

让我们看看这些差异在实践中是怎样的。

# 只读变量与可变变量

Kotlin 中的变量可以是只读的或者可变的。只读变量的值在最初赋值后就不能更改了。在使用函数式编程时，只读变量对于减少副作用特别有帮助。

可变变量就像 Java 中的大多数变量一样，可以在初始化后更改。

## 只读初始化

```
val number = 17println("number = $number")number = 42  // Not allowed, throws an exception
```

## 可变初始化

```
var number = 17println("number = $number")number = 42  // var can be reassignedprintln("number = $number")
```

最佳实践是尽可能使用只读的`val`变量，并且只在你特别需要的时候使用可变的`var`。这使程序的整体复杂性最小化，并且更容易理解它们的数据流。

# Kotlin 数据类型

与 Java 不同，Kotlin 没有原始数据类型。以下所有类型在运行时都是对象，但在 Java 字节码中会转换成相应的 Java 原语类型。

## 整数类型

Kotlin 包括所有标准整数类型。下面是显式强制转换的只读整数的示例，这些整数被初始化为其最大可能值。

```
val byte: Byte = 127val short: Short = 32767val int: Int = 2147483647val long: Long = 9223372036854775807
```

## 浮点数

Kotlin 支持标准的十进制和指数表示法。`Float`和`Double`的行为相同，但是`Double`比`Float`能容纳更多的数字。Kotlin 将任何浮点数智能转换为`Double`，这意味着您必须在参数末尾包含`f`，以将变量设置为`Float`。

```
val float: Float = 3.4028235e38fval double: Double = 1.7976931348623157e308
```

## 文本类型

Kotlin 支持类似 Java 的`Char`和`String`类型来表示文本。用单引号表示单个字符(如`'c'`)，用双引号表示字符串(如`"this string"`)。

```
val character: Char = '#'val text: String = "Learning about Kotlin's data types"
```

与 Java 不同，Kotlin 允许您使用三组双引号轻松创建多行字符串。Java 只为多行字符串提供了`concat`和`line.separator`，不够简洁，需要特殊实现。

## 布尔型

Kotlin 的布尔类型在功能上与 Java 的完全相同。

```
val yes: Boolean = trueval no: Boolean = false
```

注意:您可以使用`as`关键字将一种类型转换成另一种类型。

## 类型推理

类型推断是编译器的一项功能，当编译器可以为您推断类型时，它允许您忽略代码中的类型。这有时也被称为*智能打字*或*隐式打字*。

如果一个变量可以被声明为两种不同大小的类型，它将选择默认值:`Double`表示浮点数，`Int`表示整数。

要隐式声明非默认类型，可以在值的末尾添加类型键，如下面的类型`Long`的`L`:

Kotlin 的类型推理系统非常健壮，允许文字、对象和复杂结构的隐式类型化，如 lambda 表达式。

# 科特林条件句和循环

Kotlin 包括两个条件语句`if`和`when`，以及两个循环`for`和`while`。

所有的条件都是用以下方式定义的:

```
<conditional> (<desiredCondition>) <code><conditional> (<desiredCondition>) { <codeBlock>}
```

## 如果语句

Kotlin 的`if`语句就像 Java 一样，它包含一个代码块，如果列出的条件变为`true`，它就会执行。您还可以添加一个在跳过`if`语句时执行的`else`语句。

## When 语句

Kotlin 还有一个与 C 的`switch`语句类似的`when`语句。`when`语句创建多个检查分支，每个分支评估一个特定的条件。

`when`既可以做表达式，也可以做陈述句。如果用作表达式，第一个匹配分支的值将成为整个表达式的值。如果用作语句，则忽略各个分支的值。

你可以把`when`想象成多个`if-else`语句组合成一种更简洁的格式。

## For 循环

Kotlin `for`循环的工作方式类似于 C `for each`循环，而不是 Java 的`for`循环。它接受一个集合，就像一个数组，并对集合中的每个元素完成相同的操作。

1 `for`循环的语法是:

```
for (item in collection) print(item)for (i in 1..3) { println(i)}
```

## While 循环

只要满足列出的条件，循环就会重复执行代码体。和 Java 一样，有两种类型的`while`循环。标准的`while`循环在代码执行前检查条件，而`do-while`循环在代码执行后检查。

# 科特林收藏

Kotlin 包括三个基本集合:列表、集合和数组。像 Java 一样，它们都作为一个框架以不同的方式存储数据。集合像任何其他值一样被赋给变量。

您可以使用 Kotlin 中唯一的构造函数来创建所有集合。所有构造函数都使用相同的命名约定`<collection>Of(<value1, value2, …>)`。比如可以用`listOf(1, 2)`做两个整数的列表。

## 目录

列表是一种索引的线性数据结构，类似于 Java 数组。索引从`0`开始，一直到`(list.size - 1)`。与数组不同，列表是动态的，可以调整大小以容纳添加或删除的元素。列表可以包含任意数量的重复元素。

## 设置

集合是一种无序的线性数据结构，不能包含重复的元素。集合没有索引。如果两个集合都包含所有相同的唯一元素，则认为这两个集合相等。

尽管上述集合在初始化时以相反的顺序出现，但它们是相等的。

## 排列

该数组不是 Kotlin 中的本机数据类型。它以`Array`级为代表。这些数组的行为与 Java 数组相同:都包含可变值，但大小是固定的。

您可以使用函数`arrayOf(<element1, element2, …>)`创建一个数组。

例如:

```
val priorities = arrayOf("HIGH", "MEDIUM", "LOW")println(priorities[1])priorities[1] = "NORMAL"println(priorities[1])println(priorities.size)
```

# 科特林函数

Kotlin 中的函数要求您定义函数的名称、输入和输出。所有的函数定义都以关键字`fun`开始，并包含一个调用函数时执行的代码块。与 Java 不同，您不需要将方法定义为`static`，因为 Kotlin 与 OOP 的联系不那么紧密。

下面是科特林函数语法的一个例子:

```
fun <variableName>(<inputName>: <inputType>): <returnType>fun fibonacci(index: Int): Long
```

注意:为了模仿 Java 的`void`返回类型的行为，您可以将返回类型设置为`Nothing`。

Kotlin 和 Java 最大的区别之一是，在 Kotlin 中，函数可以存在于类之外。另一方面，Java 被严格地绑定到面向对象的编程上，要求一切都在类定义中。

包级函数(任何特定类之外的函数)用于保存对程序中任何对象都同样有用的任务。例如，一个将千克转换为磅以及将磅转换为千克的`convert`函数将是一个很好的包级函数，因为程序中的任何类都可能需要转换度量。

Kotlin 还允许高阶函数，这意味着函数可以像其他值一样被传递或返回。这对函数式编程设计是非常宝贵的。

# 扩展功能

Kotlin 采用了带有扩展函数的标准 OOP 公式，这允许您扩展现有类的功能，而无需继承它们。当试图使用不能修改但可以扩展的第三方库的功能时，扩展函数很有用。与继承不同，您可以扩展一个类，而无需对它进行完全访问。

例如，您可以向外部 API 添加额外的行为，这将有助于它与您的解决方案更紧密地集成。

要声明一个扩展函数，我们需要在它的名字前面加上一个接收者类型，即被扩展的类型。下面给`MutableList`增加一个`swap`功能:

```
fun MutableList < Int > .swap(index1: Int, index2: Int) {val tmp = this[index1]this[index1] = this[index2]this[index2] = tmp}
```

注意:`this`关键字用于引用扩展类型的值和方法。

# 用 Kotlin 进行函数式编程

Kotlin 和 Java 最显著的区别在于，Kotlin 是为函数式编程量身定制的，而不仅仅是面向对象编程。Java 中的函数式编程是有限的。虽然您可以在 Java 中模仿许多函数行为，但大多数开发人员都认为这在现实世界中并不适用。

Kotlin 是一种全功能的函数式语言，具有高阶函数、lambda 表达式、运算符重载、惰性求值、运算符重载等等。

# 下一步跟科特林学什么

恭喜你迈出了与科特林的第一步！这种新兴的语言对于现代开发人员来说是完美的，对于现有的 Java 开发人员来说也很容易掌握。

现在，您可以转到中级主题，例如:

*   可空类型
*   作为表达式的条件
*   地图收藏
*   速记函数符号
*   操作员功能

*快乐学习！*