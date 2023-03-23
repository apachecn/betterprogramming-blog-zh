# 如何在 JavaScript 中合并两个数组

> 原文：<https://betterprogramming.pub/how-to-merge-two-arrays-in-javascript-fee01b3e0ed0>

## 使用。concat()方法或扩展运算符

![](img/b9d7676ba529ecd2e812342ccd248c02.png)

照片由[克莱顿·卡迪纳利](https://unsplash.com/@clayton_cardinalli?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/merge?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

合并两个数组，无论是已经存在的数组还是尚未定义的第二个数组，都是一项常见的任务。此外，如果您不小心，那么合并可能不会像您预期的那样运行。

让我们来看看两个数组`[1,2,3]`和`[4,5,6]`，我们希望将它们合并成`[1,2,3,4,5,6]`。你的第一直觉可能是使用`+`符号或`.push()`方法，但这两种方法都不会产生正确的结果。

```
a = [1,2,3]
b = [4,5,6]console.log(a + b);     // [1,2,34,5,6]
console.log(a.push(b)); // [1,2,3,[4,5,6]
```

在第一个例子中，连接操作符`+`将每个条目视为一个字符串。因此，`a`中的最后一个项目和`b`中的第一个项目被连接在一起，产生`34`。

在第二个示例中，`b`被视为单个值，并被添加到`a`的末尾，从而生成一个嵌套数组。

为了生成我们正在寻找的数组，我们需要使用`.concat()`方法或者 spread `...`操作符。

# 的。concat()方法

这一直是连接数组的实际技术。该方法是从父数组(按顺序排在第一位的数组)中调用的，此后接受任意数量的数组。

```
let a = [1,2,3];
let b = [4,5,6];let c = a.concat(b);
console.log(c);  // [1,2,3,4,5,6]
```

# 扩展运算符

在 ES6+中受支持，扩展运算符`...`将在与数组一起使用时“解包”项目。我使用的视觉效果是通过拆开多个盒子来创建一个多米诺骨牌序列。

```
let a = [1,2,3];
let b = [4,5,6];let c = [...a, ...b];
console.log(c);
```

# 我应该使用哪个？

这是一个有趣的问题。有些根据阵列的[状态](https://stackoverflow.com/questions/48865710/spread-operator-vs-array-concat)进行区分。其他人注意到当[将多个方法链接在一起](https://www.freecodecamp.org/forum/t/concatenation-vs-spread-operators/234234)时可能会有差异。

我喜欢记住尽可能少的东西，所以我采取了默认使用 spread 的立场，只在必要时使用`.concat()`。传播更加灵活，尤其是在进行更改时。

给定我们在本教程中使用的例子，如果我们现在需要将`[0,0,0]`添加到序列的前面，会发生什么？对扩展代码的修改要容易得多。

```
let a = [1,2,3];
let b = [4,5,6];let c = [0,0,0].concat(a,b);
let d = [0,0,0,...a,...b];
```

使用`.concat()`，我们用一个数组文字包装了前面的语句，或者将两个参数传递给方法，或者将它们一个接一个地链接起来。

使用 spread 操作符，变化很小，在解包`a`和`b`之前添加元素。