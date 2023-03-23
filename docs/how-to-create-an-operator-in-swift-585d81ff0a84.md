# 如何在 Swift 中创建操作员

> 原文：<https://betterprogramming.pub/how-to-create-an-operator-in-swift-585d81ff0a84>

## 在 Swift 中构建您自己的自定义操作员

![](img/736732eb709623888c3bb7d6259ab81a.png)

克莱顿·罗宾斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

自定义操作符是 iOS 开发者中的热门话题。一些人认为这是个坏主意，而另一些人喜欢。

今天，我将向您展示创建自己的运算符的基础。您将了解 Swift 中的五种操作员类型，以及如何从中创建自定义操作员。

例如，你将学习如何使用表情符号计算阶乘:

```
5❗
```

# Swift 中的操作员类型

Swift 中有五种类型的操作员:

*   中缀运算符—两个值之间的运算符(如`10 + 5`)。
*   前缀运算符—值前的运算符(如`!true`)。
*   后缀运算符—出现在一个值之后(如[阶乘](https://www.cuemath.com/numbers/factorial/#:~:text=the%20factorial%20of%20a%20number%20is%20the%20function%20that%20multiplies%20the%20number%20by%20every%20natural%20number%20below%20it) `4!`)。
*   赋值运算符——更新值的运算符(例如`num += 1`)。
*   三元运算符——具有两个符号和三个表达式的运算符(如`condition **?** true_expression **:** false_expression`)。

在 Swift 中，除三元运算符之外的所有运算符类型都是可定制的。这意味着您可以创建一个新的自定义运算符。

让我们看一些例子。

# 如何创建自定义前缀运算符

让我们实现一个表情符号平方根运算符，这样您就可以用`✔️25.0`替换`sqrt(25.0)`。

因为`✔️`出现在数字之前，所以运算符类型是一个*前缀运算符。*

让我们用实现一个自定义平方根运算符:

```
**prefix** **operator** ✔️**prefix** **func** ✔️(num: Double) -> Double {
    **return** sqrt(num)
}print(✔️36.0)
```

输出:

```
6.0
```

为了理解发生了什么，让我们检查一下代码:

*   在第一行，您定义了一个新的前缀运算符。
*   然后创建一个定义新的`✔️`操作符行为的`prefix func`。在这种情况下，它接受 number 参数的平方根并返回结果。

这就是 Swift 的第一个客户运营商。让我们看一些其他的例子。

# 如何创建自定义中缀运算符

让我们使用表情符号➕.创建一个新的加法运算符

加号运算符需要放在两个数字之间。因此，操作员的类型需要为`infix` **:**

```
**infix** **operator** ➕**func** ➕(lhs: Int, rhs: Int) -> Int {
    **return** lhs + rhs
}print(3 ➕ 6)
```

输出:

```
9
```

让我们检查一下代码:

*   首先，您声明一个由`➕`表情符号代表的新的`infix operator`。
*   然后创建操作符函数。它需要两个参数，`lhs`和`rhs`(左侧和右侧)。它将它们相加并返回结果(这里，Swift 让您省略函数前面的`infix`关键字)。

# 如何创建后缀运算符

后缀运算符的一个例子是阶乘`!`。

比如五的阶乘是`5! = 5 * 4 * 3 * 2 * 1 = 120`。

在 Swift 中，没有内置的阶乘运算符。要计算一个`factorial()`，使用此函数:

```
**func** factorial(_ n: Int) -> Double {
  **return** (1...n).map(Double.init).reduce(1.0, *)
}
```

让我们使用 emoji❗.创建一个自定义阶乘运算符

由于阶乘运算符出现在数字之后，该运算符是一个*后缀运算符*:

```
**postfix** **operator** ❗**postfix** **func** ❗(num: Int) -> Double {
    **return** factorial(num)
}print(5❗)
```

输出:

```
120.0
```

运算符函数`❗`调用`factorial()`来获得一个数的阶乘。

# 如何创建赋值运算符

让我们创建一个类似于`+=`的赋值运算符。但是这个操作符除以一个数并使用`➗=`更新原始值:

输出:

```
7.0
```

让我们检查一下代码:

*   赋值运算符没有关键字。相反，您必须声明一个中缀运算符:`➗=`。
*   然后创建运算符函数`➗=`，通过将它除以右边的值来更新左边的值。
*   `➗=`操作员需要修改左侧的值。但是函数不允许你修改它的参数。为了克服这一点，将`lhs`参数标记为`inout`。这使得函数可以修改它。

太好了，现在您知道如何在 Swift 中创建自己的自定义操作员了。在结束之前，您需要了解一件关于操作顺序的事情。

# 优先级和结合性

要链接操作符(例如`16.0 / 2.0 / 4.0`，你需要指定操作符的优先级和结合性。否则，编译器不知道如何分组和计算操作。

根据 [Swift 文档](https://docs.swift.org/swift-book/LanguageGuide/AdvancedOperators.html#ID41):

> "**运算符优先级**赋予某些运算符比其他运算符更高的优先级；首先应用这些运算符。
> 
> **操作符结合性**定义了相同优先级的操作符如何组合在一起——从左边组合或者从右边组合。"

Swift 中的关联性类型有:

*   `left`
*   `right`
*   `none`

## 例子

我们来创建一个表情符号除法运算符:`➗`。与您已经看到的示例类似，您可以定义一个新的中缀运算符并创建运算符函数:

```
**infix** **operator** ➗**func** ➗(lhs: Double, rhs: Double) -> Double {
    **return** lhs / rhs
}
```

现在可以用运算符将两个数相除:

```
print(3.0 ➗ 3.0)
```

但是一旦将更多的操作符连接在一起，就会出现一个错误:

```
print(3.0 ➗ 3.0 ➗ 6.0)
```

结果:

```
**adjacent operators are in non-associative precedence group 'DefaultPrecedence'**
```

这是因为当括号丢失时，编译器不知道优先处理什么操作。

为了克服这个问题(不加括号)，你需要定义一个带有`left`结合性的*优先组*。这样，编译器知道从左到右将`3.0 ➗ 3.0 ➗ 6.0`分组为`(3.0 ➗ 3.0) ➗ 6.0`:

输出:

```
0.166
```

# 结论

在 Swift 中，您可以创建自定义操作员。可自定义的运算符有中缀、前缀、后缀和赋值运算符。

例如，您可以使用表情后缀运算符计算数字的阶乘:

```
6❗
```

感谢阅读。编码快乐！

# 更多文章

[](/50-swift-interview-questions-and-answers-for-2021-8911c2768a51) [## 2021 年 50 个 Swift 面试问题和答案

### 通过了解这些基本问题的答案，赢得 iOS 开发人员的面试

better 编程. pub](/50-swift-interview-questions-and-answers-for-2021-8911c2768a51) [](/5-useful-swift-one-liners-to-write-concise-code-e63f75337a53) [## 编写简洁代码的 5 个有用的 Swift 一行程序

### 学习一些一行程序来提高代码的可读性

better 编程. pub](/5-useful-swift-one-liners-to-write-concise-code-e63f75337a53) 

# 参考

 [## Swift.org

### 您可以将此页面上的资源用作 Swift 语言的文档。苹果公司拥有额外的资源…

swift.org](https://swift.org/documentation/)