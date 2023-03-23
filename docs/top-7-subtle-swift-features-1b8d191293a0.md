# 7 大微妙的 Swift 特性

> 原文：<https://betterprogramming.pub/top-7-subtle-swift-features-1b8d191293a0>

## 枚举作为命名空间，可动态调用，等等

![](img/ed6f3a17ca705c93036c604c7d3d5d9c.png)

# 1.关键词`indirect`

它只和枚举一起使用。如你所知，枚举是值类型，存储在堆栈中。因此，编译器需要知道每个枚举占用多少内存。

由于在任何时候只有一个选项是可能的，enum 占用了最大 case 的内存加上一些操作信息。

```
// Just a general enum, nothing fancy
enum Foo {
    case bizz(String)
    case fizz(Int)
}
```

但是如果我们让 enum 依赖于它自己呢？

```
// Infinite size??
enum Foo {
    case bizz(Foo)
    case fizz
}
```

这个定义会产生编译器错误。

> 递归枚举`Foo`未标记`indirect`

这个错误是有意义的:编译器不能计算`Foo`的大小，因为它趋向于无穷大。`indirect`关键词来了。

```
// Oh, fine
enum Foo {
    indirect case bizz(Foo)
    case fizz
}
```

*   **简单:**修改 enum 内存结构，解决递归问题。
*   **详细:**不再内联存储在内存中。实际上，使用`indirect`修饰符，数据现在被存储在一个指针后面(间接)。

问题解决了！此外，我们可以将整个枚举修改为间接枚举

```
// Every case is indirect now
indirect enum Foo {
    case bizz(Foo?)
    case fizz(Foo?)
}
```

# 2.属性`@autoclosure`

Swift 的`@autoclosure`属性使您能够定义一个自动包装在闭包中的参数。它主要用于将表达式的执行推迟到真正需要的时候。

然后，计算可以这样调用:

所以，在这种情况下，当`zero: true`时，`calculate`的调用根本不计算表达式，提高代码性能。

# 3.懒惰的

`lazy`存储属性是直到第一次使用时才计算初始值的属性。惰性属性必须始终声明为变量。注意，如果在`struct`中使用`lazy`，那么使用它的函数必须标记为`mutating`。

我们已经讨论过`@autoclosure`，它也有助于推迟表达式求值。那个可以和`lazy`一起用！考虑一下依赖注入这个常见的例子。

# 4.枚举作为命名空间

Swift 没有名称空间，这在大项目中可能是个问题。这很容易用枚举来解决。

# 5.动态成员查找

本节描述了`@dynamicMemberLookup`属性。它可以与结构和类一起使用。

仅仅将`@dynamicMemberLookup`添加到定义中会产生一个错误

> `@dynamicMemberLookup`属性要求`Foo`有一个接受`ExpressibleByStringLiteral`或`key path`的`subscript(dynamicMember:)`方法

因此，需要定义这样的下标

在`subscript`中，您可以实现更复杂的逻辑来检索数据。但是您可以看到这种实现仅限于字符串，并不真正安全。这可以用`key path`来修改。

即使您知道这个特性，也不意味着它应该在任何地方使用。什么更具可读性和表现力，就看你了:`a.himself.age`还是`a.age`。

# 6.可动态调用

还有一个编译器特性，允许你调用对象。可应用于`struct`、`enum`和`class`。

添加属性后，会生成错误:

> `@dynamicCallable`属性要求`RangeGenerator`具有有效的`dynamicallyCall(withArguments:)`方法或`dynamicallyCall(withKeywordArguments:)`方法

方法签名与`@dynamicMemberLookup`类似。

# 7.内嵌

有时你想给出关于编译器可以使用的优化的附加信息。内联代码是最重要的优化特性之一。那么，`‌@inlinable`、`@inline(__always)`、`@usableFromInline`怎么用呢？

属性将函数体作为模块接口的一部分导出，使它在被其他模块引用时对优化器可用。

因此，`@inlinable`使得该方法的实现成为公共的，并且能够被内联到调用者中。其次，它强迫你做出它所谓的一切`@usableFromInline`。

`@inline(__always)`告诉编译器忽略内联试探法，总是(几乎)内联函数。

一个是`@inline(__always)`而不是`@inlinable`的函数将不可用于在其模块外的内联，因为该函数的代码不可用。

> `@inline(__always)`可能对性能有益，但是由于代码大小的增加，它也可能对宏性能产生灾难性的影响。

这对实现有更多的影响，如果你想深入了解这个问题，请查看这个[论坛](https://forums.swift.org/t/when-should-both-inlinable-and-inline-always-be-used/37375)上的讨论:

```
**Want to Connect?**This post was originally published on my website. [Check it out!](https://alexdremov.me/quick-guide-to-async-await-in-swift/) [*Subscribe to my newsletter and don’t miss other useful Swift tips*](https://alexdremov.me/#/portal/signup)
```

# 参考

1.  [https://www . swiftbysundell . com/articles/using-auto closure-when-design-swift-APIs/](https://www.swiftbysundell.com/articles/using-autoclosure-when-designing-swift-apis/)
2.  [https://www . swiftbysundell . com/articles/powerful-ways-to-use-swift-enum/](https://www.swiftbysundell.com/articles/powerful-ways-to-use-swift-enums/)
3.  [https://www . hacking with swift . com/articles/134/how-to-use-dynamic call-in-swift](https://www.hackingwithswift.com/articles/134/how-to-use-dynamiccallable-in-swift)
4.  [https://www . hacking with swift . com/example-code/language/what-are-lazy-variables](https://www.hackingwithswift.com/example-code/language/what-are-lazy-variables)
5.  [https://forums . swift . org/t/who-benefits-from-the-indirect-keyword/20167](https://forums.swift.org/t/who-benefits-from-the-indirect-keyword/20167)
6.  [https://www . tothenew . com/blog/recursive-enumerations-in-swift/](https://www.tothenew.com/blog/recursive-enumerations-in-swift/)
7.  [https://www.avanderlee.com/swift/dynamic-member-lookup/](https://www.avanderlee.com/swift/dynamic-member-lookup/)