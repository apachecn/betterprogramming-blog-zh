# 像写 Python 代码一样写 Swift

> 原文：<https://betterprogramming.pub/3-amazing-ways-of-using-python-syntax-in-swift-9528dca09b2f>

## 在 Swift 中使用 Python 语法的 3 种方式

![](img/8af5cbe6373a1871de43ad5ffda21239.png)

Muhammad Haikal Sjukri 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

虽然 Python 继续引领机器学习和数据科学的进步，但 Swift 也在迅速发展。谷歌备受关注的 [Swift For Tensorflow](https://www.tensorflow.org/swift) 项目清楚地表明了 Python 与 Swift 的互操作性将如何在人工智能领域发挥更大的作用。

无论如何，我不会深入探究 [PythonKit](https://github.com/pvieito/PythonKit) ，而是向您展示一些很酷的 Python 技巧，您可以将它们应用到您的 Swift 代码库中。现在，你可能是一名希望开始使用 Python 的 iOS 开发人员，或者反之亦然。

在这两种情况下，下面的方法只会向您展示尽管 Swift 和 Python 语言有着截然不同的差异，但它们却有着一些奇怪的相似之处。用 Pythonic 的方式编写 Swift 代码很容易。

# 导入子模块

Python 有几种不同的方式来编写重要的语句。执行`import abc`在`sys.modules`中查找`abc`，如果不可用，Python 将继续搜索预安装的内置模块列表。

当模块或子模块属于另一个包时导入模块或子模块的另一种语法:`from xyz import abc`。

虽然使用简单的`import`语句是在 Swift 代码库中使用框架的最常见方式，但您也可以导入子模块或类或结构，就像我们在 Python 中所做的那样。Swift 导入语句支持以下语法:

```
//Swiftimport [module] 
import [module].[submodule] 
import [import kind] [module].[symbol name]
```

以下是 Swift 中导入子模块的示例:

```
import UIKit.UITableViewController let tableVC = UITableViewController() 
let vc = UIViewController() //works//orimport class UIKit.UITableViewController let tableVC = UITableViewController() 
let vc = UIViewController() //error
```

请注意，当使用`UIViewController`时，第二个导入语句会抛出一个错误。这是因为我们只导入了符号名为`UITableViewController`的类。

Swift 中另一件有趣的事情是，您不能像在 Python 中那样重命名导入的资源:`import abc as a`。作为一种变通方法，您可以在 Swift 中的 import 语句后使用`typealias`。

# 使用 not 运算符

Python 为我们提供了一个`not`操作符来否定一个布尔值，它类似于 Swift 和其他语言中的补码(`!`)符号。对于我们中的一些人来说，`not`操作符似乎可读性更强。

很高兴，在 Swift 中，函数是一等公民，您可以将`!`变成一个函数，使它看起来像 Python `not`操作符。这里有一个 Swift 片段展示了如何做到这一点。

```
let **not** = (!)let isUserValid = falseif **not**(isUserValid) {
  print("User is valid.")
}
```

# 解构

析构允许我们在一行中一次给多个变量赋值。这在 Python 中很常用，如下所示:

```
#Pythonx, y = (5, 11)
a, b = b, a
a,b = b, a + b
```

在 Swift 中做同样的事情在代码库中并不常见。然而，将元组分成多个值非常方便。甚至像 Python 一样交换变量。下面的 Swift 片段展示了几个析构示例:

```
//Swiftfunc getUserDetails() -> (name: String, password: String) {
    return ("Swift", "beta5.3")
}let (username, password) = getUserDetails()var a = 10
var b = 20
(a, b) = (b, a)
```

进行就地交换的最后一段代码比在 Swift 中使用第三个变量更容易理解。请注意，与 Python 语法不同，在 Swift 析构中使用括号是强制性的。

我们看到了如何在 Swift 中复制一些最常见的 Python 语法。我希望这对您有所启发，并在互用性到来时使您在两种语言之间的转换更加顺利。

这一次到此为止。感谢阅读。