# JavaScript 中在数组循环完成前退出的三种方法

> 原文：<https://betterprogramming.pub/three-ways-to-exit-an-array-loop-before-completion-in-javascript-56cc6ffff820>

## 学会使用 break、some()和 every()

![](img/6da16d3403209908ca164240374ac6e6.png)

[罗马卡夫](https://unsplash.com/@romankraft?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/loop?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

使用循环——我经常称之为迭代结构——是编程中的一项基本技能。对数据结构进行迭代是如此常见的任务，以至于它变得像肌肉记忆一样，反射性地反复使用相同的技术。

有时候，当我们在一个循环中，我们想早点退出。当在一定次数的尝试中搜索东西或建立退出标准时，这种要求很常见。

我们将讨论退出 JavaScript 循环最常用的方法——或者任何控制结构——以及两个鲜为人知的方法来扩展您的工具包。

# Break 关键字

关键字`break`是提前退出的经典技巧。我们到处都能看到 break 语句，从`switch`语句到`while`和`for`循环。该命令将退出其直接结构。

举个例子，让我们写一个循环来打印出一个数组中的所有数字。作为一个附加要求，我们希望在遇到大于 4 的值时停止。我们将使用一个 [for…of](https://medium.com/better-programming/use-for-of-to-loop-through-your-javascript-arrays-57ebb900ab5a?) 循环来迭代我们的数组。

```
const nums = [1,2,3,4,5,6];for(const n of nums) {
   console.log(n);
   if(n > 4) {
      break;
   }
}// 1, 2, 3, 4, 5
```

Break 语句灵活、简单，在编程世界中几乎无处不在。这是一种你想在快速拨号上使用的技术，当涉及到退出结构时，这是最好的开始。

接下来的两种方法与方法`map`、`filter`和`reduce`一起引入。

这三种方法都挺受欢迎的；然而，它们的两个不太为人所知的兄弟，`.some()`和`.every()`，可以用在需要提前结束数组循环的情况下。

# 的。some()方法

所有的迭代 JavaScript 方法都接受回调，这是一个为数组中的每一项调用的函数。使用`.some()`方法，该函数将被执行，直到返回一个`true`结果。

```
const nums = [1,2,3,4,5,6];nums.some(n => {
   console.log(n);
   return n > 4;
});// 1, 2, 3, 4, 5
```

如果您被示例中的语法吓住了，这里有一个简短的纲要:

*   `n`是我们的参数表。在这个回调示例中，它是数组中的每一项。由于只有一个参数，我们可以省略括号。
*   `=>`是一个箭头函数符号，另一种定义函数的方式。它取代了传统的`function`关键字。
*   花括号以同样的方式工作，定义我们函数的边界
*   最后，`});`是我们关闭函数体的关闭语法，然后是方法调用，最后是整个语句。

# 的。every()方法

`.every()`方法实际上与`.some()`方法相反。在这个方法中，数组被迭代，直到返回`true`。

```
const nums = [1,2,3,4,5,6];
nums.every(n => {
   console.log(n);
   return n < 5;
});// 1, 2, 3, 4, 5
```

你用`.some()`还是`.every()`？这些功能是否值得取代现有的实践或收集灰尘的工具？请在下面的评论中告诉我们。