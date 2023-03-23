# 为什么应该在 JavaScript 中使用枚举

> 原文：<https://betterprogramming.pub/why-you-should-use-enumerations-in-javascript-3cbb0d03b8de>

## 枚举有许多经常被忽略的好处

![](img/f748dc2448302fb79cd5f42d9ca46032.png)

由 [Brooke Campbell](https://unsplash.com/@bcampbell?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

JavaScript 中的枚举有很多好处。它们有助于减少字符串的拼写错误，同时也为一些数值提供了英文名称。这些值也被认为是常量，在运行时不会发生变化——所有这些都使得代码更容易重构，因为这些值只有一个真实的来源。不喜欢什么？

让我们看一些例子以及如何实现枚举。

# 定义我们的枚举

尽管 JavaScript 没有枚举的本机类型，但我们总是可以使用对象创建自己的类型。在定义枚举时，有几个常用的约定——尽管您不必坚持它们。

*   枚举名称是大写字母。
*   避免使用缩写，除非它们是常用的。
*   属性是大写的。
*   属性用下划线分隔。

考虑到以上内容，我们来看看以下内容:

我们这里有一个枚举，用于一些我们可能在代码库中经常使用的时间值。我们在枚举中定义了一个永远不会改变的常数，而不是让原始数字到处浮动，我们可能会不小心给它加一个 0。

在这里使用枚举的好处是:

*   IDE/编辑器中的自动完成
*   真理的单一来源
*   一致性
*   突出显示错误——我们可能会遗漏值的 0，但不会遗漏拼写错误的常量名称
*   可按名称搜索

注意:一个经常被忽视的好处是，在枚举中给这个值一个特定的名字将允许我们在代码中搜索这个值的所有使用。数字 7 可能会出现一千次，但`DAYS_PER_WEEK`出现的次数会少得多。

# 幻数被高估了

在代码中遇到幻数从来都不好玩。这个看似武断的 2 是什么意思？

如果这是一个枚举，那么该值可能已经被赋予了一个揭示意图的名称。

在枚举中给这个值一个合适的名称后，我们可以立即看到这个值的用途。在代码中准确地命名是很重要的。它使一切更容易阅读、推理和理解。

也就是说，在我们的代码中有原始数字并不总是难以理解的。一些数字如此常见，以至于我们不需要用名字来突出它们的意图，只要代码是不言自明的。请考虑以下情况:

```
const dailyPay = hourlyRate * 8
```

我们可以清楚地看到，数字 8 代表一天工作的小时数。这个值不一定要放在枚举中。

# 确保它是冷冻的

创建枚举时要考虑的事情是确保对象是冻结的。调用 native `Object.freeze(value)`将防止我们定义的常量发生突变。我们不希望这些值在运行时被改变。

当我们调用这个函数时，我们需要传入我们的新枚举，它的当前状态将被冻结并返回。对象的原型也被冻结。现在不能修改、添加或删除任何属性。

```
Choices.ROCK = 'paper' // Returns 'paper'console.log(Choices.ROCK) // Logs 'rock'
```

如果我们现在试图改变对象，我们试图分配的值将被返回，但是对象的状态不会被改变。当我们尝试注销 enum 属性时，我们可以看到这一点。如我们所料，`ROCK`属性保留了它的初始值`rock`。

注意:冻结只适用于顶层属性。嵌套对象仍然是可变的。

# 注意使用的值

值得注意的是，有时我们可能会遇到一个微妙的问题。我们应该警惕在枚举中使用 JavaScript falsy 值作为值。它们会引起不必要的头痛。

上面的问题是我们的枚举使用 0 作为一月的值。在 JavaScript 中这是一个错误的值，意味着我们的条件将计算为`true`。所有 falsy 值都将导致上述记录。这也是因为`==`的宽松对比`===`的严格对比，这本身就是另一个话题。

相反，尽量避免在枚举中分配虚假的值。

![](img/332a9539ca216d70d31091ac4df815d7.png)

西蒙·马特辛格在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 最后一个结论！

在 JavaScript 中使用枚举的概念有很多好处，但缺点很少。虽然它可能不适合每一个用例，但它肯定会影响大多数用例。那么为什么不利用它们呢？

这里有一些参考资料可能会引起你的兴趣，如果你想了解更多或者开始在你自己的代码库中实现枚举，如果你还没有这样做的话。

[MDN object . freeze()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)
[Tutorialspoint 枚举](https://www.tutorialspoint.com/what-is-the-syntax-to-define-enums-in-javascript)
[JavaScript Falsy 值](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)

感谢您的阅读。我希望你喜欢这篇文章，并学到了一些东西。如果你碰巧有任何反馈、批评或贡献，请随意写在下面的评论区。

再见了。