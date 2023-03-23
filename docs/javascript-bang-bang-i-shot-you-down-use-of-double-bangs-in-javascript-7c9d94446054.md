# Javascript“砰，砰。我把你打趴下了”——使用双刘海(！！)在 Javascript 中。

> 原文：<https://betterprogramming.pub/javascript-bang-bang-i-shot-you-down-use-of-double-bangs-in-javascript-7c9d94446054>

## 如何在使用双刘海时不被击落

![](img/013ac9ccf04cd406f97cccf744ad261d.png)

# TLDR；

的！！(双击)逻辑运算符返回一个值的真值。

# Javascript“真”值

在 Javascript 中，每个值都有一个相关联的布尔值， *true* 或 *false* 。例如，`null`值有一个相关联的布尔值`false`。一个字符串值，比如`abc`，有一个相关联的布尔值`true`。

与布尔`true`相关联的值被称为*真值*。与布尔值`false`相关联的值被称为*假值*。

真实值的完整列表可在此处找到:

[](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) [## 真理

### 在 JavaScript 中，真值是在布尔上下文中计算时被认为是真的值。所有值都是…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) 

falsy 值的完整列表可在此处找到:

[](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) [## 福尔西

### falsy 值是在布尔上下文中计算时转换为 false 的值。

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) 

# 布尔运算

某些操作，比如条件`if`语句，会选择使用相关的布尔`true/false`值，而不是值本身。在这些情况下，我们可以说该值正在布尔上下文中使用，并将被强制或强制为其关联的`true/false`值。

# 第一枪——砰的一声！

在 Javascript 中，感叹号("！")符号，称为“砰”，是逻辑“非”运算符。放在一个布尔值的前面，它将反转该值，返回相反的值。

```
!*true*; // Returns *false*.
!*false*; // Returns *true*.
```

就像一个条件`if`语句，砰的一声("！")创建布尔上下文。因此，例如，与`true`相关联的字符串`abc`，如果在其前面放置一个爆炸，将计算为`false`。

所以，单次爆炸做了两件事:

*   确定与真/假值相关的值。
*   返回相关真/假值的相反值。

逻辑“非”运算符的参考:

[](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators) [## 逻辑运算符

### 逻辑运算符通常与布尔(逻辑)值一起使用。当它们是时，它们返回一个布尔值。然而…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators) 

# 第二枪——双爆炸！

如果砰的一声("！")返回相反的真值，对一个值执行两次 bang 会做什么？

所以，运行 bang 两次确定值的反义词，然后返回值的反义词。

不是很有用吧？但是对于非布尔值，这有点酷:

因此，对一个值运行两次 bang 会首先将该值更改为相反的布尔值，然后获取返回的布尔值并再次翻转到相反的值。

简而言之，如 TL 中所述；DR，**双击返回一个值的布尔真/假关联**。很酷，对吧？

# 如何使用双爆炸

如果您想快速将一个值简化为布尔值，了解任何给定值的相关布尔值会很有帮助:

# 逮到你了。哦，不！我把你击落了！

最后一点，小心双响。它有可能用几个陷阱把你打下来。

例如，这个字符串的计算结果是什么？

```
const x = '';
!!x;
```

它是一个类似于`abc`的字符串，所以它可能与一个布尔值`true`相关联。但是，它也是一个空字符串，所以它可能与一个布尔值`false`相关联。

```
const x = '';
!!x; // Evaluates to false.
```

是的，空字符串值与布尔`false`值相关联。他们是假的。

有一些这种不直观的真假任务。所以，有机会的话，看看这些快速列表吧。

[](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) [## 真理

### 在 JavaScript 中，真值是在布尔上下文中计算时被认为是真的值。所有值都是…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) [](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) [## 福尔西

### falsy 值是在布尔上下文中计算时转换为 false 的值。

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) 

# 砰，砰。希望我没有把你打趴下！

感谢阅读，希望这篇文章对你有帮助！

直到下一次，

轻拍