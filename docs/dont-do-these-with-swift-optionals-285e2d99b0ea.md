# 不要用 Swift 选项做这些

> 原文：<https://betterprogramming.pub/dont-do-these-with-swift-optionals-285e2d99b0ea>

## 在 Swift 中使用选项时的注意事项列表

![](img/fab04ac07bc99d44bbaca530ca337d1a.png)

由[凯利·西克玛](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

可选变量是 Swift 中的一个重要概念。在较老的语言中，如 C++或 Java，所有指针都是可选的，但所有基本类型都不是——除非它们被包装在某种结构中。

在内部，*可选*是一个有两个变量的结构:

*   指示变量是否有值的标志，以及
*   一个值。

如果标志为 false，则忽略该值。如果标志为真，则可以使用该值。

逻辑上可以表示为一个盒子，里面可以有一些内容，也可以什么都没有。

Swift 的一个优势是可选性被集成到语言本身中。如果你做错了什么，编译器会显示一个编译时错误。但也有例外。在这里，我将尝试回顾如何正确地使用选项，以及如何不使用它们。

# 选择权声明

有两种方法可以声明选项:

```
**var** a: Int?
**var** b: Int!
```

两种方法都声明了可选的整数变量，但是有一个很大的区别。问号(`?`)不允许我们使用变量，除非我们确定它有一个值。感叹号(`!`)允许创建一个未初始化的变量，但它不需要进行零检查。如果你把它作为一个参数或者赋给一个非可选的变量，程序会崩溃。

```
***// Do:***
**var** a: Int?      // Declare an optional variable, initialised to nil
**var** b: Int? = 1  // Declare an optional variable, initialised to 1
**var** c: Int       // Declare a non-optional variable, must be initialised in constructor. If it's not initialised in constructor, code won't compile
**var** d: Int!      // Declare an optional variable, not initialised. Do it only if you're 100% sure variable will be initialised. For example, in **viewDidLoad** or in **@IBOutlet*****// Don't:***
**var** e: Int! = 1   // It's just useless and can create bugs. You should either make it non-optional, or initialise later
**var** f: Int! = nil // When we use exclamation mark, we assume that variable will always have a value
```

正如您所看到的，用感叹号声明可选选项并不是一个好主意。只要有可能，就用问号代替。

# 可选类型

任何类型都可以是可选的。但是混合不同的“不存在”的值不是一个好主意。例如，类型`Void?`是一个有效的类型，但是它没有任何意义。

```
***// Don't:***
**var** t: Void? = **nil** print(t)   // Prints "nil"t = Void()
print(t)   // Prints "Optional(())"
```

# 打印选项

你不应该直接打印期权。如果它没有任何值，你将得到“nil”，否则它将是“可选的(值)”。

```
***// Don't:***
**var** a: Int? = 1
print(a)   // Prints "Optional(1)"
print(a!)  // Will crash if a is **nil*****// Do:***
print(a ?? "No value")  // Prints value of a or "No value" for **nil**
print(a ?? "nil")       // Same as print(a), but without "Optional"
print(a ?? "")          // Prints empty line if value is **nil**
```

字符串插值也是如此。例如:

```
**let** str = "\(a)" // str = "Optional(1)"
```

# `Optional Constants`

将非零选项定义为常量是没有用的。不要这样做——这让人困惑。

```
***// Don't:* let**c: Int? = 1***// Do:***
**let** d: Int = 1
```

非可选变量(或常量)总是可以分配给可选变量。例如:

```
**let** a: Int = 1
**var** b: Int? = 2***// Do:***
b = a***// Don't***
a = b     // Compiler will show an error
```

# 打开

如果我们需要把一个可选变量赋给一个非可选变量，我们该怎么做？答案是:解开它。正如我前面说过的，可选结构是泛型结构之王。该值被包装到一个结构中。从那里获取值是展开一个可选的。

有四种方法打开它。第一种选择:

```
***// Do:***
**var** a: Int? = 1
**var** b: Int = 2b = a ?? 0
```

在上面的例子中，我们使用了最常见的展开方式。我们提供一个默认值。如果变量`a`是`nil`，我们将`0`赋值给`b`。

第二种选择:

```
***// Do:***
**var** a: Int? = 1
**var** b: Int = 2**guard** **let** c = a **else** { **return** }
b = c
```

这里，我们检查`a` 是否不是`nil`，如果是，我们将其分配给`c`。否则，我们就从当前函数中选择`return`。如果我们在一个循环中，我们也可以使用`break`或`continue`，或者如果`nil`值指示一个错误，我们也可以使用`throw`。换句话说，我们需要在到达任务之前终止当前的作用域。

第三种选择:

```
**// Do:**
**var** a: Int? = 1
**var** b: Int = 2**if** **let** c = a {
    b = c
}
```

和第二个一样，只是倒置了。如果值是`nil`，我们不终止当前函数，而是只在值不是`nil`时执行`if`范围。

第四种选择:

```
***// Don't:***
**var** a: Int? = 1
**var** b: Int = 2b = a!
```

这被称为“强制解包”。如果你想做，再考虑一下。如果你还想做，你可能需要复习你的 Swift 知识。你正在做的事情很可能有问题。当然，这个功能是存在的。这意味着在某些情况下你可以使用它，但这种情况非常罕见。

例如，这将起作用:

```
***// Don't:***
**var** a: Int? = 1
**var** b: Int = 2**if** a != nil {
    b = a!
}
```

但是很丑。如果`a`不是`nil`时需要将`a`赋值给`b`，按以下方式操作:

```
***// Do:* var** a: Int? = **nil
var** b: Int = 2b = a ?? b
```

# 链接

我想在这里讨论的下一个主题是链接。

假设我们有一个可选的`struct`或`class`，它们有可选的属性或方法。

```
***// Do:*****struct** C {
    **var** a: Int?
    **func** getA() -> Int? { a }
}**struct** D {
    **var** c: C?
}**var** d: D? = D()
**var** a = d?.c?.a
```

如你所见，我们有一个作业，一行有两个问号。工作原理:

1.  检查`d`的值是否为`nil`。如果是`nil`，它将`nil`分配给`a`。
2.  否则，检查`d` ( `struct D`)的属性`c`。同样的逻辑。如果是`nil`，则`a`变为`nil`，代码结束。
3.  如果`d?.c`不是`nil`，它最终将变量`d`的属性`c`的属性`a`的值赋给变量`a`。

有一个重要的细节:如果你在开始的时候得到一个可选的，你不需要在每个点之前用一个问号。即使值是可选的，只有当变量或属性有可选类型时，才需要使用问号。

例如:

```
***// Do:***
**struct** C {
    **var** a: Int?
    **func** getA() -> Int? { a }
}**struct** D {
    **var** c: C = C()
}**var** d: D? = D()
**var** a = d?.c.getA()
```

***如果一个链中至少有一个可选组件，结果将是可选的。***

# 期权和回拨

我想讨论的最后一个主题是混合了异步工作和转义回调的选项。这听起来可能很奇怪，但让我解释一下这里的危险在哪里。

假设我们有一个方法为`getUser(...)`的类`API`。我们不会深入研究实现，但该方法是异步的，并返回一个可选的用户值和一个可选的错误。

```
**class** API {
    ... **func** getUser(delegate: **@escaping**(**_** user: **Any**?, **_** error: Error?) -> Void) {
        DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
            delegate(**nil**, **nil**)
        }
    }
}
```

该函数仅在一秒钟后返回`nil`值。但足以说明想法。

```
***// Don't:*****var** api: API? = API()**func** login(delegate: **@escaping** () -> Void) {
    .... api?.getUser { (**_**, **_**) **in** delegate()
    }
}
```

这个代码有什么问题？变量`api`是可选的。这意味着如果`api`是`nil`，方法`getUser`将永远不会被调用。并且`login`方法的`delegate`函数也不会被调用。大概你的 app 的 UI 会显示一些 spinner，会旋转，会旋转，永远不会结束。

也许可选的 API 并不常见，但是可选的`user`却很常见。如果登录失败，可以返回`nil`。如果`User`是一个有更多方法的复杂类(例如，向服务器发送推送通知令牌)，在某些时候，你的回调可能在可选代码中。

如果你使用这种方法，你应该总是检查变量是否是`nil`，如果是，调用代码的*替代* *分支*，它调用`delegate`函数。

例如:

```
***// Do:*****var** api: API? = API()
**func** login(delegate: **@escaping** () -> Void) {
    **guard** **let** safeApi = api **else** {
        delegate()
        **return** } safeApi.getUser { (**_**, **_**) **in** delegate()
    }
}
```

Swift 的另一个令人惊叹的特性是扩展。如果你喜欢有用的扩展，看看我的文章:

[](https://medium.com/better-programming/10-useful-swift-string-extensions-e4280e55a554) [## 10 个有用的 Swift 字符串扩展

### 电子邮件验证、类型转换等等

medium.com](https://medium.com/better-programming/10-useful-swift-string-extensions-e4280e55a554) [](https://medium.com/better-programming/24-swift-extensions-for-cleaner-code-41e250c9c4c3) [## 24 个 Swift 扩展，代码更简洁

### 更高效地构建您的移动应用

medium.com](https://medium.com/better-programming/24-swift-extensions-for-cleaner-code-41e250c9c4c3) 

# 结论

选项是一个非常强大的工具，它有助于避免许多与`nil`值有关的问题。使用期权有安全和不安全的两种方式。尽可能选择安全的。使用感叹号(`!`)很少是个好主意。

编码快乐，下次见！