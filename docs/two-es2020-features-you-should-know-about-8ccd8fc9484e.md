# 您应该了解的两个 ES2020 特性

> 原文：<https://betterprogramming.pub/two-es2020-features-you-should-know-about-8ccd8fc9484e>

## 深入研究可选链接和无效合并操作符

![](img/3c6c1b0e328a3d4c8c543fe2a14863b5.png)

照片由[郭佳欣·阿维蒂斯扬](https://unsplash.com/@kar111?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

在作为 ES2020(又名 ES11)规范的一部分发布的所有[新特性](/javascript-es2020-features-with-simple-examples-d301dbef2c37)中，我发现可选链接和 nullish 合并操作符是最有用的。在本文中，我将向您展示它们如何使您的项目受益。

# 零融合算子

在 JavaScript 中，`undefined`和`null`被视为“falsy”值。这意味着在真正的假布尔值周围执行空值检查可能很棘手——例如，`0`或`false`——这有时可能是函数的合法响应。

下面是一个(非常做作的)例子:

如果来自`decisionMaker`的响应是`undefined`，我们可以使用 nullish 合并操作符(`??`)将值设置为`"Undecided"`——否则，我们打印`true`或`false`。

这可以与逻辑“或”运算符(`||`)形成对比，如果值为`false`或“falsy”，则逻辑“或”运算符失败在这个例子中，如果我们使用`||`操作符，当`decisionMaker`函数同时返回`false`和`undefined`时，我们会看到`false`，这不是我们想要的行为。

在使用 nullish 合并操作符之前，我们应该完成如下工作:

```
const decision = decisionMaker();if (typeof decision === "undefined") {
  console.log("Should I go to the ball?", "Undecided");
} else {
  console.log("Should I go to the ball?", decision);
}
```

使用 nullish 合并操作符给我们带来了更干净、可读性更强的代码，并减少了出错的空间。

# 可选链接

可选链接允许您从嵌套属性中选择一个值，而无需显式检查链中的每个属性是否都已定义。如果链中的任何属性都没有定义，您只会得到`undefined`而不是运行时错误。

这意味着您可以像这样重构代码:

```
let result;if (foo && foo.bar && foo.bar.baz) {
  result = foo.bar.baz;
}
```

对此:

```
const result = foo?.bar?.baz;
```

这对于可读性来说是一个巨大的胜利，让你的代码更加简洁。这也意味着您很少会错过空检查和在代码中引入不必要的 bug。

这是可选链接的主要用例——然而，在其他一些情况下，它会非常有用。例如，如果函数已定义，它允许您选择性地执行该函数:

这里，我们只执行从 actions 对象中选择的值(如果它已定义)——这允许我们在函数未定义的情况下使用 nullish 合并操作符返回回退消息。以前，您可能需要这样做:

```
if (typeof actions[action] === "function") {
  return actions[action](num1, num2);
}return "Calculation is not recognised";
```

这比使用可选链接的版本代码要多得多，在我看来也不够清晰——当然这是个人偏好！

如果定义了数组，还可以使用可选的链接从数组中选择一项。例如:

```
const first = people.filter((person) => person.name === name)?.[0];
```

我们按姓名过滤一组人员，如果过滤器返回了一些值，则只选择第一个选项。同样，简洁明了，在我看来，比大量的空值检查更具可读性。

掌握最新 JavaScript 规范的最新内容意味着您可以利用新特性来编写更简洁、更易维护的代码。可选链接和 nullish 合并操作符是我特别喜欢的，希望这些例子能给你一些如何在自己的项目中使用它们的想法。