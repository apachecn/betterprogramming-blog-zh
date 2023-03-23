# JavaScript 最佳实践

> 原文：<https://betterprogramming.pub/javascript-best-practices-returns-scopes-and-ifs-5d321aa5a75a>

## 返回、范围和 if 语句

![](img/d7de18b09503057c7869fd2bf6639589.png)

[肖恩贝尔](https://unsplash.com/@shauncolinbell?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在本文中，我们将看看在某些回调中返回值，使用块范围的变量，清理`if`语句，并清楚地返回。

# 在传入数组方法的回调中返回值

很多 JavaScript 数组方法，像`map`、`from`、`every`、`some`、`reduce`、`find`、`findIndex`、`reduceRight`、`filter`、`sort`，都是采取回调的。

传入的回调应该返回一些值，以便这些方法可以返回一个正确的值，允许我们做其他事情。例如，下面的代码可能是无用的和错误的:

```
const arr = [1, 2, 3];
const mapped = arr.map(() => {});
```

然后我们看到`mapped`的值是`[undefined, undefined, undefined]`。很可能不是我们想要的。因此，我们应该确保这些数组方法接受的回调返回一个值，这样我们就不会得到意想不到的东西。

我们不应该有不返回任何值的回调，除非我们 100%确定我们想这样做。

# 使用块范围的变量

如果我们使用`var`来声明变量，我们应该像对待块范围的变量一样对待它们，这样我们就不会给自己和其他开发人员造成混淆。

例如，我们可以这样写:

`foo`如我们所料记录了`true`，但令人困惑的是我们在一个`if`块中声明了`var foo`两次。

我们应该像对待块范围的变量一样对待`var`以减少混淆，所以我们应该改为:

更好的是，我们应该对块范围的变量使用`let`:

`let`总是块范围的，所以我们不能像`var`那样把变量放在任何我们想要的地方。

# 类方法应该引用这个

实例方法应该引用 JavaScript 类中的`this`。否则，它作为实例方法就没有意义了。如果我们不需要在一个类方法中引用`this`，那么它应该是一个静态方法。

例如，如果我们有:

`bar`不应该是实例方法，因为它不引用`this`。相反，我们应该使它成为静态方法，如下所示:

# 通过一段代码限制线性独立的路径

我们不应该在任何`if`语句中有太多的`else if`块来降低复杂度。为了使代码易于阅读，我们应该减少一条`if`语句可以采用的路径数量。

例如，我们可以写:

我们应该考虑减少`if`语句的情况。

![](img/92fa1d6d4e4b3f9ad31625bb370b222b.png)

[杆长](https://unsplash.com/@rodlong?utm_source=medium&utm_medium=referral)在[防溅板](https://unsplash.com?utm_source=medium&utm_medium=referral)上拍照。

# Return 语句应该指定一个值或不指定值

在 JavaScript 中，`return`语句可以以多种方式返回`undefined`。`return`返回`undefined`后无任何内容。后面带有`void`的`return`和后面带有`void`的表达式也返回`undefined`。

`return`同`undefined`之后它也返回`undefined`。

我们应该考虑什么都不返回，或者显式地返回`undefined`或一个表达式，以明确我们试图返回或不返回什么。

所以我们要么写:

```
const bar = () => {
  return;
}
```

或者:

```
const bar = () => {
  return 1;
}
```

或者:

```
const bar = () => {
  return undefined;
}
```

很少使用`void`操作符，所以大多数开发人员不熟悉它。它的用途也是有限的。因此，不常使用。

# 结论

许多数组方法接受一个应该返回值的回调。像`map`、`reduce`、`filter`等实例方法。所有的回调都应该返回值。

类实例方法应该引用`this`。如果没有，那么它们应该是静态方法。

陈述不应该太复杂。保持最小的路径数量。

最后，`return`应该返回值或不返回值，而不是使用`void`操作符。