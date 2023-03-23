# 带有非空断言运算符的更清晰的类型脚本

> 原文：<https://betterprogramming.pub/cleaner-typescript-with-the-non-null-assertion-operator-300789388376>

![](img/7a39b756010fc8a182098a1ccd631888.png)

图片由[杰洛特](http://pixabay.com)在 [Needpix](https://www.needpix.com/) 上拍摄

最近，我了解了一个有用的类型脚本操作符:[非空断言操作符](https://github.com/Microsoft/TypeScript/wiki/What's-new-in-TypeScript#non-null-assertion-operator)。它否定变量中的空类型和未定义类型。

在这篇文章中，我将解释如何以及何时使用这个操作符，并给出一些例子来说明它可以在什么地方帮助你。

TL；DR:在变量后添加感叹号会忽略未定义或空类型。

# 如何使用非空断言运算符

非空断言操作符接受一个有类型的变量，并从中删除未定义的和空的类型。

使用操作符就像添加一个感叹号一样简单。

1.  忽略变量的`undefined | null`类型:

2.执行函数时忽略`undefined`类型:

3.当操作符的断言在运行时失败时，代码将充当常规的 JavaScript 代码。这可能会导致意外的行为。操作员不安全使用的例子:

请注意:该操作符只有在`[strictNullChecks](https://www.typescriptlang.org/docs/handbook/compiler-options.html)` [标志](https://www.typescriptlang.org/docs/handbook/compiler-options.html)开启时才会生效。当标志关闭时，编译器不检查`undefined`和`null`类型赋值。

现在，我知道你在想什么…

# 我为什么要这么做？

![](img/8ed3c285d57780d218962136eefb5f0d.png)

持怀疑态度的吉娃娃由[陈熠](https://www.flickr.com/photos/yiie/4865201576)在 [Flickr](https://www.flickr.com/photos/yiie/4865201576) 上

让我们看看非空断言操作符可以帮助解决的一些实际情况:

## 带有 React 引用的事件处理程序

[React refs](https://reactjs.org/docs/refs-and-the-dom.html) 用于访问一个元素的当前 HTML 节点。使用 refs 时，当前值可能为 null(在被引用的元素被卸载的情况下)。

在许多情况下，我们可以确定当前元件已安装，因此`null`类型无关紧要。

在下面的示例中，当单击按钮时，输入元素被滚动到视图中:

我们知道当`goToInput`被执行时，输入元素被挂载。我们可以有把握地假设`ref.current`不为空:

不使用断言操作符也可以解决编译错误。我们可以用`[Logical AND](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators#Logical_AND)`:

这有点冗长。

当用于链接的可选属性时，这变得特别麻烦。请看这个极端的例子:

## 使用 React 进行正确注射测试

下面的例子包含了一个用于 prop 测试的通用模式。我们有两个组成部分。

第一个是可重用组件，可以是第三方库组件。它接收一个回调并在输入值改变时调用它。回调接收一个表单事件作为参数。

第二个组件叫做`SpecificField`，它包含了`SpecificInput`。`SpecificField`知道如何提取当前值并将其传递给回调函数:

我们想测试一下`SpecificField`是否正确地调用了`onChange`回调。
这意味着`SpecificField`得到一个`onFieldChanged`回调，该回调打开事件值:

由于`onFieldChanged`属性是可选的并且可能是未定义的，所以会出现编译错误。

然而，我们知道`injectedCallback`是在错误发生的那一行定义的——我们已经测试过了。

这就是非空断言操作符可以提供帮助的地方:

作为比较，不使用运算符的一个可能的解决方案是使用 if-else 语句:

这要冗长得多。

在这两个选项中，我更喜欢使用非空断言操作符。它更短，更简洁，样板文件更少。

# 编辑:可选链接呢？

有时我会被问到 Null 断言操作符和 Javascript 的可选链接之间的区别。
虽然可选链接是一个非常有用的工具，但是它的用法和目的有点不同。考虑使用`Array.find`函数的情况，其中**知道**将找到一个项目。让我们举一个过于简单的例子:

```
const arr = [{value: 1}, {value: 2}];const item = arr.find(el => el.value === 2);const result = item.value + 1; //TS error, since item is (allegedly) possibly undefined
```

使用可选的链接，将引发相同的错误— `item?.value`仍然可能是`undefined`，并且将发生编译错误。但是，如果您使用空断言操作符，错误将被否定。你可以[在这里](https://www.typescriptlang.org/play?#code/MYewdgzgLgBAhgJwTAvDA2gbwG5wDYCuApgFwwCMAvgDQw77FkBMlAugNwBQoksAllCIBbVPCQA6AGZ8wAEwAURPKgB8MJeNyEiqFGiYBKLj2gwERCATyw0A4ZoY6A1BXYwA9O4AqAZXVIQBFoIGWAdOxE+CBh5fDwiAHMiWTwATwMYAAcQCBCAIzSYAjkiaTBkzm5wU3NLawB1AQALAHlMqD5wfFEIgH4HbRgXcjdPHw68ZTgwfwRAgEIYPoHiGCXo6D5JrJz8wuLZUpkKqt4zCysoRqhWzKIEOChAnsEheZXnVw93AEEm2hujwA5NEbjpwER5pwgA)看到完整的例子。

# 结论

非空断言操作符是一个方便的小工具。使用时，切记小心轻放。误用会导致意外行为。

然而，好处是很多的:它减少了在运行时无法发生的某些场景中的认知负荷。此外，与其他替代方案相比，它使您的代码不那么冗长。另外，它会让你觉得你在对编译器大喊大叫，这总是很有趣。

接线员已经帮我几次了。我希望你会和我一样觉得它很有用。

🕵️‍♂️ *Psst…* 如果你喜欢这个*，*在 Twitter 上关注我([@ omril 321](https://twitter.com/omril321))——我定期在 Twitter 上发布我学到的很酷的东西。