# Swift 中的客户操作员

> 原文：<https://betterprogramming.pub/custom-operators-in-swift-5cf596d27849>

## 对运算符类型、如何使用它们以及它们的缺点的简短介绍

![](img/7a5bf3d35dd65a6989bb3bdeb50fad62.png)

布雷特·赛勒斯的照片

Swift 是一种非常灵活的语言，为您提供了许多工具来修改和扩充它。这些增强之一是对自定义或重载运算符的支持。

# 什么是自定义运算符？

除了预定义的运算符集合，Swift 还允许您定义自己的运算符，如`==`、`+`、`-`等。有三种不同的类型:*前缀*、*后缀*和*中缀*运算符。

## 前缀和后缀

顾名思义，这些操作符要么在它们各自的目标之前，要么在它们各自的目标之后，比如递增/递减操作符`++` / `--`。因此，它们是“T21”一元运算符，影响单个值。

要声明前缀或后缀操作符，我们需要在目标类型的扩展中创建一个带有正确修饰符`prefix`或`postfix`的`static`函数:

首先，我们需要定义操作符。操作符的可能字符被限制为不干扰其他事物的标识符，如类型、函数等。完整列表可在“[Swift 编程语言](https://docs.swift.org/swift-book/ReferenceManual/LexicalStructure.html#ID418)中找到。

实际上并不需要将操作符逻辑放入扩展中。全球发售的`func`也可以。但是在扩展中为它们各自的类型添加操作符是一种对它们进行逻辑分组的好方法。

## 中缀

作为一个“[二元](https://en.wikipedia.org/wiki/Binary_operation)操作符，第三种在它的目标之间工作，可以访问两者，像算术操作符`+`、`-`、`*`、`/`等。

它们的定义非常类似于*中缀* / *前缀*运算符:

已经存在的`infix operator -`不需要重新定义，但是编译器也没有抱怨。相关的类型不必完全相同，就像我们可以返回我们喜欢的任何类型一样。这允许非常灵活的操作符，比如创建中间类型来创建类似构建器的特定于领域的语言。

# 声明运算符

要定义操作符的功能，首先必须定义它本身。如果我们不想覆盖或重用现有的修饰符，我们必须自己定义它的 [*优先级*](https://en.wikipedia.org/wiki/Order_of_operations) 和 [*结合性*](https://en.wikipedia.org/wiki/Associative_property) 。

运算的顺序，也称为“运算符优先级”，定义了表达式中不同运算符之间的规则。例如，表达式`2 + 3 * 4`将导致`14`，因为由于运算符优先级，该表达式等效于`2 + (3 * 4)`。

结合性定义了相同优先级的操作符在一起使用时应该如何表现。根据我们的逻辑，当表达式`a !! b !! c`被赋值`(a !! b) !! c`和`a !! (b !! c)`时，可能会有不同。这就是为什么我们可以定义如何以及是否可以将多个操作符链接在一起。

已经定义了多个 precede 组，如果我们的操作符不使用现有的组，它默认为`DefaultPrecedenceGroup`。

这些组按“从高到低”排序。所有组名都以`Precedence`结尾，为便于阅读，此处省略。

```
| Group              | Assoc | Example operators   
| ------------------ | ----- | --------------------------- 
| BitwiseShift       | none  | <<  >>                   
| Multiplication     | left  | *  /                     
| Addition           | left  | +  -                     
| RangeFormation     | none  | ...  ..<                 
| Casting            | none  | is  as  as?  as!       
| NilCoalescing      | right | ??                        
| Comparison         | none  | !=  ==  <  >  <=  >= 
| LogicalConjunction | left  | &&                        
| LogicalDisjunction | left  | ||                      
| Default            | none  |                             
| Ternary            | right | ?  :                       
| Assignment         | right | =  +=  -=` 
```

自定义优先组允许我们将运算符放在现有运算符之间:

文章中已经提到了优先位置和结合性。但是什么是`assignment`属性呢？

如 [Swift 提案 SE-0077](https://github.com/apple/swift-evolution/blob/master/proposals/0077-operator-precedence.md#assignment) 中所写，`assignment: true`将把操作器折叠成一个可选的链，允许`foo?.bar += 2`作为`foo?(.bar += 2)`工作，而不是作为`(foo?.bar) += 2`没有进行类型检查。

获得这些操作符属性可能需要一些“试错法”，或者更好，一组单元测试来定义确切的行为，特别是如果它们是共享框架的一部分。

# 重载运算符

另一个要注意的陷阱是重载现有的操作符。没有什么能阻止你为`Int`重新定义像`+`这样的基本操作符:

我不认为有必要解释为什么这是一个可怕的想法。如果你重载现有的操作符，或者用不同的类型重用同一个操作符，确保它的目的是清楚的。

# 自定义操作符的缺点

尽管自定义操作符很容易创建，并且自定义操作符的可能用例是多种多样的，但是我强烈建议您不要(过度)使用它们！

与扩展一样，我们以新的非标准操作符的形式将技术债务引入到我们的项目中。更糟糕的是，它可能是在新的上下文中重用的熟悉的操作符。Onboarding 开发人员可能不知道发生了什么，需要搜索文档或操作符声明本身。这将需要一定的心智能力来理解 Swift 本身之上的“迷你语言”。你不能像用扩展方法一样，用 CMD 键点击操作符来找到它的定义。

例如，我创建了[库](https://github.com/netzgut/integral-swift-autolayout)来简化创建带有扩展和自定义操作符的 AutoLayout 代码:

起初，我创建了一些扩展来避免我一遍又一遍地输入`constraint(`或`constant:`。代码更加易读和简洁，不需要解释发生了什么。所有需要的信息都还在。

我还为本文创建了自定义操作符，以创建比扩展方法更直观的“特定于领域的语言”方法。尽管在处理代码时可能会立即提高生产率，但从长远来看，这可能会成为一个问题。

苹果并没有真的努力在每个版本中破坏 Xcode、Swift 或 iOS SDK 即使没有自定义操作符或扩展，我们的代码也可能会意外破坏。但是通过引入这样的增强，我们增加了破损的可能性。因此，除了我们的“正常”代码，我们还必须为项目的生命周期维护“支持”代码。恢复到“未扩充”代码可能不像“搜索&替换”那么简单。

# 结论

与您可以使用的其他工具和依赖项一样，您需要知道何时值得包含它，并意识到它对您的项目的长期影响。这并不意味着您不应该使用自定义操作符。它们是简化表达式的好工具。但不要为了短期收益把自己画到墙角。

扩展方法是一个很好的选择，提供了一个更详细的表面和可发现性来解释它们的用例，即使有人不熟悉“幕后”发生的事情。但是它们不会为您提供对它们的使用的控制，比如设置特定的优先级或结合性。

# 资源

*   [Swift 提案 SE-0077:改进运营商声明](https://github.com/apple/swift-evolution/blob/master/proposals/0077-operator-precedence.md)
*   [Swift 编程语言:高级操作员](https://docs.swift.org/swift-book/LanguageGuide/AdvancedOperators.html)(Swift 编程语言)
*   [操作符允许字符](https://docs.swift.org/swift-book/ReferenceManual/LexicalStructure.html#ID418)(Swift 编程语言)
*   [操作顺序](https://en.wikipedia.org/wiki/Order_of_operations)(维基百科)
*   [关联属性](https://en.wikipedia.org/wiki/Associative_property)(维基百科)