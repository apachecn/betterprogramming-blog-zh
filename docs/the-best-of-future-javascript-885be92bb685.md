# 未来最好的 JavaScript

> 原文：<https://betterprogramming.pub/the-best-of-future-javascript-885be92bb685>

## Do 表达式、可选链接和 nullish 合并运算符。让我们来看看每一个

![](img/76c929df22bed9cb238df8b878636922.png)

布鲁诺·伯格尔在 [Unsplash](https://unsplash.com/s/photos/road?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

**📕看看我的新书** [**干净代码原则和模式:软件从业者手册**](https://www.amazon.com/Clean-Code-Principles-Patterns-Practitioners-ebook/dp/B0BSDJKYQJ/ref=sr_1_1?crid=8M0CMV4CP6UQ&keywords=clean+code+principles&qid=1674980636&sprefix=clean+code+principle%2Caps%2C175&sr=8-1) ！

以下是未来 JavaScript 中我最喜欢的部分。

# 做表情

`do { .. }`表达式执行一个块(块内有一条或多条语句)，块内最终的语句完成值成为`do`表达式的完成值。你看这里的[规格](https://github.com/tc39/proposal-do-expressions)。

有了`do`表达式，你就可以摆脱烦人的`let`变量，并且能够随心所欲地使用`const` 。

我在我的一个附带项目中搜索了`let` ，其中包含大量代码，大多数找到的事件都遵循逻辑，我声明了一个变量，并将其值设置为基于哪个条件评估为真的值之一:

但是在初始设置后，我不会改变`opacity`或`color` 的值。它们基本上是常量，但我不得不用`let`来声明它们。

使用`do`表达式，我可以如下重写上面的代码，并用`const`替换`let` :

# 可选的链接和无效合并运算符

我们都写过成百上千次类似的表达方式:

```
const creditCardType = creditCardTypes && creditCardTypes[0] && creditCardTypes[0].type ? creditCardTypes[0].type : ‘Visa’;
```

它很长，我希望它能写得更短。它可以！

感谢两个新的实验性 JavaScript 特性:[可选链接](https://github.com/tc39/proposal-optional-chaining)和 [nullish 合并操作符](https://github.com/tc39/proposal-nullish-coalescing)。利用这两个新的实验特性，我可以将上面的语句写成如下:

```
const creditCardType = creditCardTypes?.[0]?.type ?? ‘Visa’;
```

`??`操作符是`||`操作符的绝佳替代品。`||`操作符检查真值，而`??`操作符检查空值:`undefined`和`null`。

如果`??`运算符左侧的表达式计算结果为 undefined 或 null，则返回其右侧的表达式。使用`||`操作符可能会导致细微的错误，例如在计算数值时。

当使用`||`操作符时，数值零计算为一个假值，可能会产生意外的结果，但是当使用`??`操作符时应该可以正常工作。

下面是一个潜在错误的例子:

```
const minValue = suppliedMinValue || -100;
```

当你提供一个 min 时会发生什么。值为零？您得到的是-100 的`minValue`，而不是零，这可能是意料之外的。使用`??`，我们可以解决这个问题:

```
const minValue = suppliedMinValue ?? -100;
```

# 好消息！

你可以使用 [Babel](https://babeljs.io/) 插件获得这些新的实验特性:

*   [巴别塔可选链接插件](https://babeljs.io/docs/en/babel-plugin-proposal-optional-chaining)。
*   Babel nullish 合并运算符插件。

如果使用 TypeScript，它支持可选链接和 nullish 合并操作符，但不支持`do`表达式。

类似地，如果您使用[流](https://flow.org/)静态类型检查器，您可以通过在`[.flowconfig](https://flow.org/en/docs/config/options/)`中启用它们来使用可选的链接和 nullish 合并操作符。

**📕看看我的新书** [**干净代码原则和模式:软件从业者手册**](https://www.amazon.com/Clean-Code-Principles-Patterns-Practitioners-ebook/dp/B0BSDJKYQJ/ref=sr_1_1?crid=8M0CMV4CP6UQ&keywords=clean+code+principles&qid=1674980636&sprefix=clean+code+principle%2Caps%2C175&sr=8-1) ！