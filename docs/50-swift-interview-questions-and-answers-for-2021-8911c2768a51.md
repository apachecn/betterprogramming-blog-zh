# 50 个 Swift 面试问题和答案

> 原文：<https://betterprogramming.pub/50-swift-interview-questions-and-answers-for-2021-8911c2768a51>

## 通过了解这些基本问题的答案，赢得 iOS 开发人员的面试

![](img/560cd6055fb025d91daaa490f06839e7.png)

原图:[查尔斯·德鲁维奥](https://unsplash.com/@charlesdeluvio)

这里列出了 50 个 Swift 面试问题和答案。这些问题集中在 Swift 编程和 iOS 应用程序开发。在你有机会在面试官面前展示你的技能之前，你需要知道这些问题的答案。

这些问题没有特定的顺序，让我们开始吧！

# 1.*什么导致了错误？你能如何修理它？*

```
**let** n1: Int = 1
**let** n2: Float = 2.0
**let** n3: Double = 3.34**var** result = n1 + n2 + n3
```

## 回答:

在 Swift 中，两种数据类型之间的隐式类型转换是不可能的。

在上面的代码中，您试图将三个元素加在一起，每个元素代表一种不同的数据类型。

要解决这个问题，您需要将每个值转换为相同的数据类型。例如:

```
**var** result = Double(n1) + Double(n2) + n3
```

# 2.*L*`*en*`*的值是多少？为什么？*

```
**var** arr1 = [1, 2, 3]
**var** arr2 = arr1arr2.append(4)**var** len = arr1.count
```

## 回答:

`len`的值为 3，即`arr1`中的元素个数为 3。这是因为将`arr1`分配给`arr2`实际上意味着将`arr1`的副本分配给`arr2`，因此`arr1`不受影响。

在 Swift 中，所有的基本数据类型(布尔值、整数等。)，枚举和结构本质上都是*值类型*。

数组也是值类型。当在 Swift 中移动值类型时，您实际上是在使用它的副本。例如，当将一个*值类型*作为参数传递给一个函数时，会创建一个它的副本，因此无论该函数做什么都不会反映在原始值中。

# 3.有什么问题，你如何解决？

考虑这段试图从 iOS 设备的本地存储中检索主题颜色的代码:

```
**var** color = UserDefaults.standard.string(forKey: "themeColor")!
print(color)
```

你能发现错误并改正它吗？

## 回答:

第一行从*用户默认值*中检索主题颜色。然而，这个方法返回一个可选的(因为`themeColor`可能没有被定义)。如果没有找到密钥，返回一个`nil`，上面的代码崩溃:

`fatal error: unexpectedly found nil while unwrapping an Optional value`

这发生在第一行使用`!`到*强制展开*可选的，现在是一个`nil`。*只有当您 100%确定该值不是* `*nil*`时，才应使用强制解包。

要解决这个问题，您可以使用*可选绑定*来检查是否找到了键的值:

```
**if** **let** color = defaults.stringForKey("themeColor") {
    print(color)
}
```

# 4.*你能看到哪些潜在的改进？*

您正在查看一个拉取请求，遇到了这种方法:

你能向代码的作者提出什么改进建议？

## 回答:

尽管这段代码可能有效，但有两件事需要考虑。

*   使用硬编码的字符串如(例如`"West"`)不是一个好主意。如果有人拼错了怎么办？为了解决这个问题，应该放弃硬编码的字符串，而应该创建一个枚举。
*   还有，用一个 *switch* 语句代替冗长的 if-else 语句怎么样？

有了这些改进，代码变得类型更安全，可读性更好:

# 5.*Swift 中有哪些枚举？*

## 回答:

枚举是一组相关的值。

枚举使得编写类型安全的代码成为可能。

```
**enum** Direction {
    **case** North
    **case** East
    **case** South
    **case** West
}
```

现在，在您的代码中，例如，您可以调用`Direction.North`，而不是使用神秘的字符串`"North"`(这很容易被拼错并导致令人讨厌的错误)。

# 6.*Swift 中的可选项目是什么？你怎么能创造一个呢？*

## 回答:

一个*可选的*是可以存储一个值或者一个`nil`的类型。您可以通过在任何类型后添加问号`?`来创建可选:

```
**var** number: Int? = 10
```

# 7.*Swift 中的* **typealias** *是什么？你如何创造一个？*

## 回答:

`[Typealias](https://www.codingem.com/typealias-in-swift/)`顾名思义，是现有数据类型的别名。

您可以像这样创建一个:

```
**typealias** Weight = Float
```

现在你可以用`Weight`代替`Float`:

```
**let** mass1: Weight = 150.0
**let** mass2: Weight = 220.0**let** total: Weight = mass1 + mass2
```

# 8.*说出使用 Swift 的一些优势*

## 回答:

仅举几个例子:

*   Swift 是一种类型安全语言
*   它有闭合支持
*   它有可选的类型支持
*   它有内置的错误处理功能
*   它支持模式匹配

# 9.*Swift 中有 5 种控制转移语句——说出它们的名称并解释使用它们的原因。*

## 回答:

五种控制转移语句是:

*   `Break`
*   `Continue`
*   `Fallthrough`
*   `Throw`
*   `Return`

控制转移语句更改代码运行的顺序。

例如，当认为不必继续循环时，可以使用控制转移语句`**break**` 来结束 For 循环的执行:

```
**for** choice **in** choices:
    **if** isCorrect(choice):
        print("Correct choice found!")
        **break**
```

# 10.建议对这段代码进行一个小的重构

```
**if** age < 18 {
    driveCar()
} **else** {
    doNotDrive()
}
```

这个表达式很简洁，运行良好——但是你能建议一个小的重构改进来使它更好吗？

## 回答:

您可以使用[三元条件操作符](https://www.codingem.com/swift-one-liner-if-else-statements/)将该表达式转换成一行代码，在这种情况下，这不会影响可读性，但会提高可读性。

```
age < 18 ? driveCar() : doNotDrive()
```

# 11.提高代码可读性

在我们公司，我们有 20 名开发人员和 20 种独特的编码风格。我们如何实施一些常见的编码风格/最佳实践？

## 回答:

开始使用棉绒，例如 [Swiftlint](https://medium.com/codex/swift-automatic-code-styling-tool-in-2021-971531595651) 。linter 是一个易于安装的工具，它可以检查和修复您的错误，并代表您执行最佳实践和惯例。

您可以使用 linter 的默认指南，但是您也可以配置 linter 来匹配您公司的偏好。

# 12.*Swift 中的完成处理程序是什么？*

## 回答:

[完成处理程序](https://medium.com/codex/swift-closures-made-simple-cb81dd15b543#:~:text=completion handlers—closures in action)是正在运行的闭包。

假设您执行一个耗时的任务，比如一个网络请求，并且您想在请求完成后立即做一些事情。但是你肯定*不想*因为多次检查流程是否正在进行而浪费资源。

这就是使用完成处理程序的地方。完成处理程序是一个闭包，一旦耗时的流程完成，它就会“把你叫回来”。

# 13.*如果没有物理的 Ios 设备，如何测试一个 Ios 应用？*

## 回答:

如果你没有 iOS 设备，你可以使用苹果的 iOS 设备模拟器在 Mac 上测试你的应用。

# 14.*init()在 Swift 中是做什么的？*

## 回答:

`init()`方法用于初始化一个实例。

初始化意味着准备一个(类、结构或枚举的)实例以供使用。

在初始化过程中，为实例的每个属性设置初始值。在实例可以使用之前，您还可以执行一些其他准备工作。

# 15.*解释 Swift* 中 `*Let*` *与* `*Var*` *的区别*

## 回答:

在 Swift 中，您可以使用`let`创建一个常量(一个不能更改的值)，使用`var`创建一个变量(一个以后可以修改的值)。

# 16.*什么是* `*PLIST*` *？*

## 回答:

PLIST 或属性列表是用于在项目文件系统中存储数据的键值对的字典。比如`info.plist`。

# 17.*Swift 中有哪些协议？举个例子*

## 回答:

[协议](https://medium.com/codex/swift-how-protocols-work-5d0d43a961e7)是一个代码契约。它充当属性、方法等的蓝图。它描述了符合的类型*必须如何表现。*

您不能创建协议的实例。相反，你可以让一个类*符合*一个协议。

以下是描述动物的示例协议:

让我们创建符合`Animal`协议的`Cat`和`Dog`类。因此，要求它们都实现`Animal`协议中描述的行为，即变量`name`、`color`和`makeSound()`方法:

# 18.*什么是双问号运算符？*

## 回答:

双问号运算符`??`被称为零合并运算符。如果不是`nil`，则返回左侧的值。如果左边是`nil`，那么它返回右边的值。

*零合并*可用作检查可选值是否为`nil`的简写。例如，您可以替换为:

```
**var** name: String?**if** name != **nil** {
    print(name)
} **else** {
    print("N/A")
}
```

有了这个:

```
print(name ?? "N/A")
```

# 19.*什么是守卫声明？举个例子*

## 回答:

guard 语句用于将程序控制转移到范围之外。Guard 语句类似于 if 语句，但它仅在某些条件不满足时运行。

例如，用于退出函数的 guard 语句:

```
**func** myFun() {
    **guard** **false** **else** {
         print("This block is run")
         **return**
    }
    print("This is never run")
}myFun()
```

输出:

```
This block is run
```

# 20.*Swift 中有哪三种主要的托收类型？*

## 回答:

*   数组:数组是值的有序集合。
*   集合:集合是值的无序集合。
*   字典:字典是键值对的无序集合。

# 21.*Swift 中的 Defer 是什么？*

## 回答:

您可以使用 [defer](https://medium.com/codex/swift-cheat-sheet-statement-keywords-cf6520f3de12#:~:text=use defer to execute code right before exiting the scope where you are using the keyword. for example:) 方法在退出作用域之前执行代码。例如，让我们在函数执行完成之前打印一些内容:

延迟通常在打开和关闭某个范围内的上下文时使用，例如在访问文件时。

# 22.没有第三个辅助变量可以交换两个变量吗？

## 回答:

是的，这是可能的。使用[元组析构](https://www.codingem.com/swift-destructuring-tuples/)，可以这样解决问题:

```
**var** a = 1
**var** b = 2(a, b) = (b, a)
```

# *23。结构和类有什么区别？*

## 回答:

*   结构是值类型，而类是引用类型。
*   结构不支持继承，类支持。
*   在类中，我们可以用`let`关键字创建一个实例，并尝试改变它的属性，而在结构中没有可变性。
*   结构不支持类型转换，但类支持。

# 24.什么是可选链接？

## 回答:

可选链接意味着您可以安全地调用可能是`nil`的东西的属性。

可选链接的工作方式，顾名思义，就是用问号运算符`?`链接一个或多个可选值，如下所示:

```
something?.someValue?.someMethod()
```

如果在上述链中的任何一点遇到了`nil`，应用程序不会崩溃——相反，会返回一个`nil`。

# 25.*什么是可选绑定？*

## 回答:

可选绑定检查可选的是否包含值。如果可选的有一个值，可选绑定使该值暂时可用:

作为一个例子，下面的代码检查名字是否是`nil`。如果不是，则创建一个临时常数`realName`并为其赋值`name`。

```
**var** name: String? = "Charles"**if** **let** realName = name {
    print (realName)
}
```

输出:

```
Charles
```

# 26.*解释 MVC 架构*

## 回答:

MVC(模型-视图-控制器)，是一个开发 iOS 应用的软件架构。这是 iOS 应用程序开发的基本概念之一。

多个 iOS 框架使用 MVC。

MVC 的思想是将数据从一个地方传递到另一个地方。这意味着中的任何对象都属于以下三个类别之一:

*   **模型:**模型代表 app 的数据。它存储信息，如商店中的产品。模型管理应用程序的状态。
*   视图:视图负责显示用户界面并与之交互。例如，一个视图为应用程序的用户呈现一个产品表。
*   控制器:控制器是将模型和视图粘合在一起的东西。它负责控制两者之间的逻辑。

# 27.*Swift 中的输入输出参数是什么？*

## 回答:

一个 [inout](https://www.codingem.com/2021/04/07/swift-what-is-inout/) 参数允许你改变函数内部参数的值。

要使参数进出，在参数类型前使用`inout`关键字。

要传递一个变量作为输入输出，在它的名字前面使用`&`。

例如:

# 28.*什么是元组？如何创建一个，如何从中读取值？*

## 回答:

元组是可用于将多个值捆绑在一起的值，例如，作为一对。

元组的值不需要属于同一类型。

您可以通过在括号内用逗号分隔值来创建元组。

例如:

```
**var** coordinates3D = (1.0, 2.0, 5.0)
```

要访问元组中的值，请使用点符号和索引:

```
**let** xPos = coordinates3D.0
```

也可以创建元组，以便每个值都有一个名称:

```
var coordinates3D = (x: 1.0, y: 2.0, z: 5.0)
```

在这种情况下，您可以通过名称访问元组的特定值:

```
let xPos = coordinates3D.x
```

# 29.*什么是 Swift 报文？*

## 回答:

Swift Messages 是一个库，用于将消息显示为 iOS 设备屏幕顶部或底部的状态栏。

# 30.*可以给函数参数一个默认值吗？怎么会？*

## 回答:

可以给参数一个默认值:

```
**func** eat(food: String = "spaghetti") {
    print("Yum! I ate some good \(food).")
}
```

# 31.什么是仿制药？举一个使用泛型的例子

## 回答:

泛型允许您编写灵活且可重用的代码，可以处理任何数据类型。

假设您正在编写一个 3D 向量结构，但是您希望能够使用整数、浮点数和双精度数来创建向量。您肯定不希望为每种数据类型分别编写相同的代码。

这就是你可以使用泛型的地方。

例如，您可以为参数创建一个通用类型(代表任何类型),使用字母如`T`,如下所示:

# 32.英镑财产被称为什么？什么是 Getters 和 Setters？

## 回答:

`pounds`属性也被称为 [*一个计算属性*](https://medium.com/codex/getters-and-setters-in-swift-79a46e9401a0) *。*

在 Swift 中，计算的属性*不存储在对象*中。计算属性意味着只有当试图访问它时，它的值才“按需”计算。您可以使用`get`和(可选)`set`方法创建计算属性。

*   当调用`weight.pounds`时，`get`方法执行“按需”*计算*。
*   `set`方法在`pounds`更新时更新`kilograms`。(注意`set`方法是可选的，你不需要这样的方法来创建一个计算属性。)

# 33.==和===运算符有什么区别？

## 回答:

*   `==`是相等运算符。
*   `===`是恒等运算符。

相等运算符`==`用于检查两个`Equatable`类型是否相等:

```
"Hello" == "Hello"
10.0 == 5.0 + 5.0
```

恒等运算符`===`可用于检查两个类是否相同，即它们是否指向同一个内存地址。让我们看一个例子:

```
**class** Fruit {
    **var** name = "Banana"
}**let** fruit1 = Fruit()
**let** fruit2 = fruit1 // fruit2 now points to same address as fruit1fruit1 === fruit2 // true
```

# 34.*什么是扩展？*

## 回答:

在 Swift 中，您可以使用扩展向现有类型添加功能。

在 Swift 中，您可以使用`**extension**`关键字创建扩展:

```
**extension** SomeExistingType {
    // add new functionality to SomeExistingType here
}
```

# 35.*什么是嵌套函数？*

## 回答:

嵌套函数是函数内部函数的组合:

```
**func** outer() {
    **func** inner() {
       // Do something here
    }
}
```

# 36.*如何在 Swift 中创建基类？*

## 回答:

您可以通过定义没有超类的类来创建基类。

# 37.*什么是强制解包？你应该什么时候使用它？*

## 回答:

强制展开尝试将可选转换为值，不管它是否包含值。

强制解包是不安全的，因为如果可选选项是`nil`，你试图解开它，它会触发一个错误，使应用程序崩溃。因此，应该避免使用它，除非你 100%确定这个选项不是`nil`。

# 38.列举使用高阶函数的一些好处。

## 回答:

*   它们提供了灵活性。
*   它们在不能使用常规函数的异步调用中很有用。
*   他们有时会提高代码的质量，使代码更短、更简洁。

# 39.*F*`**ileprivate**`*和 P* `**rivate**` *有什么区别？*

## 回答:

*   可以在同一个 Swift 文件中的任何地方读取`fileprivate`属性，但不能在文件之外读取。
*   一个`private`属性只能在声明它的类型中读取(以及在同一文件中该类型的扩展中)。

# 40.*您如何解释 Swift 的功能？*

## 回答:

函数使得定义可重用的代码块成为可能。函数可以执行属于程序一部分的任务。

通常，函数接受一些它们可以使用的值。

# 41.*N*`**il**`*和 N* `one` *有什么区别？*

## 回答:

两者没有区别:

```
nil == .none // returns true
```

唯一的区别是使用`nil`比使用`none`更常见。

# 42.*Swift 中的字典是什么？*

## 回答:

字典是 Swift 中的基本集合类型。它可以用来存储键值对。

通过知道一个键，可以很容易地访问一个值:

```
**let** dict = ["a": 1, "b": 2]**let** valueOfA = dict["a"]
```

# 43.*M*`***utating***`*关键字是做什么的？*

## 回答:

您可以使用`mutating`关键字通过标记特定的方法`mutating`来允许改变方法中结构的属性。

例如:

默认情况下，这对于[值类型](https://medium.com/codex/structs-vs-classes-in-swift-c37ef7e7d960#:~:text=a%20value%20type%20is%20a%20type%20whose%20value%20is%20copied%20when%20it%E2%80%99s%20assigned%20to%20a%20variable%20or%20constant%2C%20or%20when%20it%E2%80%99s%20passed%20to%20a%20function.)(结构和枚举)是不可能的，但是对于[引用类型](https://medium.com/codex/structs-vs-classes-in-swift-c37ef7e7d960#:~:text=in%20swift%2C%20classes%20are%20reference%20types%20by%20their%20nature.%20when%20we%20create%20an%20instance%20of%20a%20class%20and%20assign%20it%20to%20a%20variable%2C%20the%20instance%20is%20not%20copied%20to%20the%20new%20variable.%20a%20reference%20to%20it%20is%20used%20instead.)(类)是可能的。

# 44.你能解决这个代码中的问题吗？

下面的代码抛出了一个编译器错误。怎么了？你能如何修理它？

## 回答:

一个`[guard](https://www.codingem.com/the-guard-keyword-in-swift/)`语句的`else`块需要一个退出路径。

例如，您可以使用`return`为其提供一个:

# 45.*什么是反初始化器？你怎么能创造一个呢？*

## 回答:

在释放类实例之前运行反初始化程序。

您可以使用`deinit`关键字创建一个反初始化器。

只有当您需要在释放类实例之前做一些整理工作时，此方法才是有用的。大多数情况下，让 Swift 代表您自动处理就足够了。

下面是一个反初始化器的例子，当`Example`的一个实例被释放时，它将`number`设置回 0。

```
**var** number = 15**class** Example {
    **init**() {
       number *= 10
    }

    **deinit** {
        number = 0
    }
}
```

# 46.函数和方法有什么区别？

## 回答:

函数和方法之间有一点小小的区别。两者都是可重用的代码块，然而，方法属于类、结构或枚举，而函数不属于。

# 47.你怎么能禁止一个类被继承呢？

## 回答:

通过使用`final`关键字使该类成为最终类。例如:

```
**final** **class** Animal {
    let name = "I'm a furry animal"
}
```

# 48.*什么是懒惰变量？什么时候应该使用？*

## 回答:

惰性变量的初始值是在第一次调用它时计算的。惰性变量可以通过在需要之前不做不必要的工作来优化代码。

例如:

```
**lazy** **var** tallest: Person? = {
    **return** people.max(by: { $0.height < $1.height })
}()
```

# 49.*Swift 中的自动关闭是什么？您应该如何以及何时使用它？*

## 回答:

一个自动闭包将一个函数参数包装到一个闭包里。当调用 autoclosure 时，它返回包装在里面的表达式的值。

有时，在使用带有闭包参数的函数时，使用 autoclosures 在语法上很方便。这是因为 autoclosure 允许您省略花括号`{}`。这可以使代码更具可读性。

然而，请记住[苹果对使用自动关闭](https://docs.swift.org/swift-book/LanguageGuide/Closures.html)的说法:

> 调用采用自动闭包的函数很常见，但实现这种函数并不常见。

下面是一个简化代码的自动关闭的例子。第一个代码片段使用常规闭包，第二个代码片段使用 autoclosure。看看第二个例子中的`I_will`函数调用是如何变得更具可读性的:

# 50.这段代码中缺少了什么？

```
**enum** Example {
  **case** something(Int, Example)
}
```

## 回答:

可以在 Swift 中创建类似上面的递归枚举。下面是一个抽象的例子——然而，使用递归枚举是默认禁用的，你需要使用`indirect`关键字来启用它:

```
**enum** Example {
  indirect **case** something(Int, Example)
}
```

# 51.奖金——在结束中[软弱的自己]做了什么

在一个类中，闭包与 **self** 创建了一个强引用循环。

强大的引用周期会导致内存泄漏，从而导致应用程序出现意外行为。

例如，被删除的视图控制器可以继续在后台执行，因为 ARC 不能释放它。

为了防止这种情况，您需要使对**自身**的引用变弱。这是通过在闭包中使用**【弱自我】**来实现的。

阅读更多关于[这个话题](https://www.codingem.com/weak-self-in-swift/)。

# 结论

感谢阅读。我希望这些对你有用，并能帮助你找到理想的工作！

我很想加入你的 LinkedIn 网络。随意连接 [Artturi Jalli](https://www.linkedin.com/in/artturi-jalli-29619413a) 。

# 资源

 [## Swift.org

### 您可以将此页面上的资源用作 Swift 语言的文档。苹果公司拥有额外的资源…

swift.org](https://swift.org/documentation/)  [## Apple 开发者文档

### 编辑描述

developer.apple.com](https://developer.apple.com/documentation/)