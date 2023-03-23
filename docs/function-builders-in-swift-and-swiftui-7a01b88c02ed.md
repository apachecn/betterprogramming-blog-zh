# Swift 和 SwiftUI 中的函数构建器

> 原文：<https://betterprogramming.pub/function-builders-in-swift-and-swiftui-7a01b88c02ed>

## 了解 Swift 的最新功能之一

![](img/b9d7a0b6d56cfb5e79d24d3b7cef81d5.png)

[由哎呦插画. pics](https://icons8.com)

*函数构建器*是 Swift 5.1 中首次引入的语言功能。它支持 SwiftUI 声明式 DSL，允许我们以可读和简洁的方式构建异构用户界面层次结构。在本文中，我们将学习如何在我们的代码中使用它们，涵盖以下主题:

*   什么是函数构造器？
*   函数构建器如何在 Swift 编译器层面上工作？
*   如何实施定制的 Swift 函数生成器

# 了解 Swift 函数构建器

函数生成器是一种实现嵌入式 DSL 的类型，用于从函数的表达式语句中收集部分结果，并将它们组合成一个返回值[【1】](https://github.com/apple/swift-evolution/blob/9992cf3c11c2d5e0ea20bee98657d93902d5b174/proposals/XXXX-function-builders.md)。

最小的*函数构建器类型*实现如下:

```
**@_functionBuilder** **struct** **Builder** {
    **static** **func** **buildBlock**(_ partialResults: **String...**) **->** **String** {
        partialResults**.reduce**("", **+**)
    }
}
```

一个*函数构建器类型*必须用`@functionBuilder`属性进行注释，这允许它单独作为一个定制属性使用。

请注意，function builder 属性有一个下划线，表示该特性目前正在开发中。

方法`buildBlock()`是强制性的。它必须是静态的，并且必须有准确的名称。否则，您会在使用时看到一个编译错误。

自定义的*函数生成器属性*可以应用于[【1】](https://github.com/apple/swift-evolution/blob/9992cf3c11c2d5e0ea20bee98657d93902d5b174/proposals/XXXX-function-builders.md):

1.  不属于协议要求的一部分的`func`、`var`或`subscript`声明。它使*函数构建器转换*应用于函数体。
2.  函数的闭包参数，包括协议要求。它导致*函数构建器转换*被应用于任何显式闭包的主体，这些闭包作为相应的参数传递，除非闭包包含 return 语句。

让我们继续我们的`@Builder`例子，回顾一下这两种使用场景。

通过用属性`@Builder`标记 then，我们可以在声明中使用特殊的语法:

```
**@Builder** **func** **abc**() **->** **String** {
    "Method: "
    "ABC"
}

**struct** **Foo** {
    **@Builder** **var** abc: **String** {
        "Getter: "
        "ABC"
    }

    **subscript**(_ anything: **String**) **->** **String** {
        **@Builder** **get** {
            "Subscript: "
            "ABC"
        }
        **set** { */* nothing */* }
    }
}
```

如果我们调用声明:

```
**print**(**abc**())
**print**(**Foo**()**.**abc)
**print**(**Foo**()[""])
```

它将打印:

```
Method: ABC
Getter: ABC
Subscript: ABC
```

转到第二个场景，下面是我们如何将一个函数生成器注释的闭包作为参数传递:

```
**func** **acceptBuilder**(**@Builder** build: () **->** **String**) {
    **print**(**build**())
}
```

然后调用启用 DSL 语法的`acceptBuilder()`函数:

```
acceptBuilder {
    "Closure argument: "
    "ABC"
}
```

上面的代码将打印出来:

```
Closure argument: ABC
```

# Swift 函数生成器的目的

> 允许创建优秀的库一直是 Swift 的核心目标。[【1】](https://github.com/apple/swift-evolution/blob/9992cf3c11c2d5e0ea20bee98657d93902d5b174/proposals/XXXX-function-builders.md#function-builder-types)

Swift 函数构建器解决的一类问题是分层异构数据结构的构建。一些例子是:

*   生成结构化数据，例如 XML、JSON。
*   生成 GUI 层次结构，例如 SwiftUI、HTML。

这就是它的作用。它是如何工作的？

# Swift 函数构造器剖析

如果我们转储从`abc()`方法生成的 AST:

```
(func_decl range=[builder.swift:10:10 - line:13:1] "abc()" interface type='() -> String' access=internal
...
  (declref_expr implicit type='(Builder.Type) -> (String...) -> String' location=builder.swift:10:31 range=[builder.swift:10:31 - line:10:31] decl=builder.(file).Builder.buildBlock@builder.swift:5:17 function_ref=single)
  ...
    (string_literal_expr type='String' location=builder.swift:11:5 range=[builder.swift:11:5 - line:11:5] encoding=utf8 value="Method: " builtin_initializer=Swift.(file).String extension.init(_builtinStringLiteral:utf8CodeUnitCount:isASCII:) initializer=**NULL**)
    (string_literal_expr type='String' location=builder.swift:12:5 range=[builder.swift:12:5 - line:12:5] encoding=utf8 value="ABC" builtin_initializer=Swift.(file).String extension.init(_builtinStringLiteral:utf8CodeUnitCount:isASCII:) initializer=**NULL**)
...
```

我们会发现它转化为对`Builder.buildBlock("Method: ", "ABC")`的调用。

在[语义分析](https://swift.org/swift-compiler/#compiler-architecture)阶段，Swift 编译器[将](https://github.com/apple/swift/blob/51267754fc35ca17d6bbb8f9066390925cbb5c02/lib/Sema/CSApply.cpp#L5059)[函数生成器将](https://github.com/apple/swift/blob/78880ffc1a6bdbb01610990ba395e2cbef5d14a3/lib/Sema/BuilderTransform.cpp)转换为解析后的 AST，就好像我们已经编写了`Builder.buildBlock(<arguments>)`[【1】](https://github.com/apple/swift-evolution/blob/9992cf3c11c2d5e0ea20bee98657d93902d5b174/proposals/XXXX-function-builders.md)[【2】](https://github.com/apple/swift/blob/724f8c23dbd1a52b09e5d4ba97e328d87ac03021/include/swift/AST/KnownIdentifiers.def#L34)。

另一个使用案例是将函数生成器应用于闭包参数。在这种情况下，Swift 编译器将[将闭包](https://github.com/apple/swift/blob/51267754fc35ca17d6bbb8f9066390925cbb5c02/lib/Sema/CSApply.cpp#L7713)重写为带有包含构建器调用的[单个表达式体](https://github.com/apple/swift/blob/78880ffc1a6bdbb01610990ba395e2cbef5d14a3/lib/Sema/BuilderTransform.cpp#L784)的闭包。

为了有所用处，函数构建器必须提供七个构建方法的子集，这些方法实现不同种类的转换[【1】](https://github.com/apple/swift-evolution/blob/9992cf3c11c2d5e0ea20bee98657d93902d5b174/proposals/XXXX-function-builders.md#function-building-methods)，[【2】](https://github.com/apple/swift/blob/724f8c23dbd1a52b09e5d4ba97e328d87ac03021/include/swift/AST/KnownIdentifiers.def#L34):

*   `buildBlock(_ parts: PartialResult...) -> PartialResult`将多个部分结果合并为一个。
*   `buildDo(_ parts: PartialResult...) -> PartialResult`与`buildBlock()`相同，但针对`do`条款。
*   `buildIf(_ parts: PartialResult...) -> PartialResult`与`buildBlock()`相同，但用于`if`语句。
*   `buildEither(first: PartialResult) -> PartialResult`和`buildEither(second: PartialResult) -> PartialResult`从两个可选执行的子块的结果中创建部分结果。您必须实现这两种方法，并且它们必须是彼此相反的。
*   `buildExpression(_ expression: Expression) -> PartialResult`从单个表达式创建部分结果。
*   `buildOptional(_ part: PartialResult?) -> PartialResult`从可选执行的子块的结果中创建部分结果。
*   `buildFinalResult(_ parts: PartialResult...) -> Result`从多个部分结果中产生一个最终结果。

所有方法都支持基于其参数类型的重载。

在使用时，Swift 编译器将尝试使用提供的方法子集重写 DSL 语法。在编译器找不到匹配的情况下，它将发出一个编译错误。

# 实现自定义函数生成器

让我们通过实现一个`NSAttributedString`函数生成器来提高我们的知识。

构建器从子字符串中创建一个 final `NSAttributedString`:

```
**@_functionBuilder**
**struct** **AttributedStringBuilder** {
    **static** **func** **buildBlock**(_ components: **NSAttributedString...**) **->** **NSAttributedString** {
        **let** result **=** **NSMutableAttributedString**(string: "")
        components**.forEach**(result**.**append)
        **return** result
    }
}
```

接下来，我们需要考虑构建器支持什么类型的表达式。在我们的例子中，我们将接受字符串和图像，并将它们提升到属性化的子字符串:

```
**@_functionBuilder**
**struct** **AttributedStringBuilder** {
    **...**

    **static** **func** **buildExpression**(_ text: **String**) **->** **NSAttributedString** {
        **NSAttributedString**(string: text, attributes: [:])
    }

    **static** **func** **buildExpression**(_ image: **UIImage**) **->** **NSAttributedString** {
        **let** attachment **=** **NSTextAttachment**()
        attachment**.**image **=** image
        **return** **NSAttributedString**(attachment: attachment)
    }

    **static** **func** **buildExpression**(_ attr: **NSAttributedString**) **->** **NSAttributedString** {
        attr
    }
}
```

注意，我们也接受类型为`NSAttributedString`的表达式，并不加修改地返回它们。

我们还需要一个助手方法，允许我们添加额外的属性:

```
**extension** **NSAttributedString** {
    **func** **withAttributes**(_ attrs: [**NSAttributedString.Key**: **Any**]) **->** **NSAttributedString** {
        **let** copy **=** **NSMutableAttributedString**(attributedString: **self**)
        copy**.addAttributes**(attrs, range: **NSRange**(location: 0, length: string**.**count))
        **return** copy
    }
}
```

然后添加一个基于构建器的便利初始化器:

```
**extension** **NSAttributedString** {
    convenience **init**(**@AttributedStringBuilder** builder: () **->** **NSAttributedString**) {
        **self.init**(attributedString: **builder**())
    }
}
```

最后，让我们测试一下构建器。注意，SwiftUI 不直接支持`NSAttributedString` s，因此我们将求助于传统的 UIKit:

```
**class** **ViewController**: **UIViewController** {
    **@IBOutlet** **weak** **var** label: **UILabel!**

    **override** **func** **viewDidLoad**() {
        **super.viewDidLoad**()

        label**.**attributedText **=** **NSAttributedString** {
            *// 1.*
            **NSAttributedString** {
                *// 2.*
                "Folder "
                *// 3.*
                **UIImage**(systemName: "folder")**!**
                *// 4.*
            }
            "\n"
            **NSAttributedString** {
                "Document "
                **UIImage**(systemName: "doc")**!**
                    **.withRenderingMode**(**.**alwaysTemplate)
            }
            **.withAttributes**([
                **.**font: **UIFont.systemFont**(ofSize: 32),
                **.**foregroundColor: **UIColor.**red
            ])
        }
    }
}
```

Swift 编译器将上述代码翻译成以下`AttributedStringBuilder`方法调用:

1.  `buildExpression()`同`NSAttributedString`争论。
2.  `buildExpression()`同`String`论证。
3.  `buildExpression()`带`Image`自变量。
4.  在构建了所有部分结果之后，调用方法`buildBlock()`，将所有中间子字符串作为参数。

结果如下:

![](img/21318b15ed478b7277466891b7f64b69.png)

# 源代码

你可以在这里找到[的源代码](https://github.com/V8tr/AttributedStringBuilder)。它是在“未授权”下发布的，这允许你用它做任何你想做的事情。

# 参考

*   [拉取请求#25221:函数生成器的初步支持](https://github.com/apple/swift/pull/25221)
*   [功能构建器提案](https://github.com/apple/swift-evolution/blob/9992cf3c11c2d5e0ea20bee98657d93902d5b174/proposals/XXXX-function-builders.md)
*   [Swift 论坛主题](https://forums.swift.org/t/function-builders/25167)
*   [牛逼的函数构建器](https://github.com/carson-katri/awesome-function-builders)
*   [view builder 上的苹果文档](https://developer.apple.com/documentation/swiftui/viewbuilder)