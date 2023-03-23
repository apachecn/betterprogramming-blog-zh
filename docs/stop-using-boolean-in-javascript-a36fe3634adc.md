# 停止在 JavaScript 中使用 Boolean()

> 原文：<https://betterprogramming.pub/stop-using-boolean-in-javascript-a36fe3634adc>

## 请使用双重否定技巧

![](img/a44d24268aa3a88d00fb41873e9f55af.png)

帕特里克·福尔在 [Unsplash](https://unsplash.com/s/photos/maths?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

布尔值——以英国数学家乔治·布尔的名字命名——不是*真*就是*假*。

你会看到布尔这个术语出现在数学的分支中，比如布尔代数。在编程中，我们依靠布尔值来决定控制结构的流程，比如`if`语句和`while`循环。

当我们需要将一个值转换成`true`或`false`时，称为布尔强制，JavaScript 有内置的`Boolean()`函数。

如果您需要快速、一次性的布尔强制，有一种更好的方法。

# 逻辑否定运算符

首先，我们需要引入逻辑否定运算符，也称为感叹号。该运算符的名称源自布尔代数的“非”门，它有效地将一个值从真翻转为假，反之亦然。

```
console.log(!true);  // false
console.log(!false); // truelet x = 5;
console.log(x != 3); // true
```

但是如果`!`不在一个布尔值之前会发生什么呢？

如果运算符后跟一个非布尔值，该值将被强制转换为布尔值，然后求反。

```
console.log(!0);    // true
console.log(!5);    // false
console.log(!"");   // true
console.log(!null); // true
```

这引发了一场关于真假价值观的对话。TL；dr 是指在布尔强制期间，某些值(如空字符串、整数零和 null)将计算为 false。因此，它们是虚假的价值。

# 双重否定算子？

既然我们知道逻辑求反运算符能够执行布尔强制，那么我们需要做的就是重新求反布尔值，以保留其原始强制值。

```
console.log(!!5);    // true
console.log(!!"");   // false
console.log(!!null); // false
```

`!!`是运营商吗？不。从技术上讲，这只是一个双重否定运算符。但这没关系，因为我们得到了一个只有两个字符的`Boolean()`的函数替代。

现在，我知道有些人会抱怨`!!`很难读懂——我能理解。但是现在你有选择了。

你认为双重否定技巧更容易理解还是你更喜欢用 `*Boolean()*` *？在下面分享你的想法和评论。感谢阅读！*