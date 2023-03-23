# 函数式编程:Currying 与部分应用

> 原文：<https://betterprogramming.pub/functional-programming-currying-vs-partial-application-53b8b05c73e3>

## 它们是什么，我们如何使用它们？

![](img/bf0e92b7cd04f8bace5f2d46c1ae22a2.png)

[Florian Klauer](https://unsplash.com/@florianklauer?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在学习函数式编程时，许多初学者对函数式编程的一些概念感到困惑。刚接触 FP 的时候，对*curry*和 *partial application 很迷茫。*

在本帖中，我将举例说明它们。

# 什么是 Currying？

嗯，大多数情况下，如果人们对此感到困惑，那么很可能是因为术语*咖喱*，它与印度的食物名称相同。

然而，在计算机科学中，currying 是一种技术的术语。它起源于一个数学函数。

假设有一个 JavaScript 函数，`add`。

```
const add = (x, y) => x + y;
```

因此，`add`接受两个参数— `x`和`y`，并返回一个合计值。这个函数`add`，可以用数学方式表示如下:

```
f(x,y) = x + y
```

那么，函数`f(x,y)`可以这样表示:

```
f(x,y) = h(x)(y) = x + y
```

`h`是一个采用单个参数的翻译函数，输出一个采用单个参数的函数。有了`x = 2`，那么函数`f`就会是这样的，如下图:

```
f(2,y) = 2 + y
```

`f(2,y)`仍然需要让`y`返回一个合计值。然后，可以表示为另一个取`y`的函数，这个等于这个。

```
f(2,y) = h(2)(y) = 2 + y
```

`h(2)(y)`也指等待`y`的函数，因为它已经知道`x`指的是什么。

让我们回到 JavaScript 中的`add`函数。

如果`add`是带两个参数的`f(x,y)`，那么如何用`add`来表示`h(x)(y)`？就像`h(2)(y)`最终返回另一个需要`y`的函数一样，`add`也应该返回一个函数，就像这样:

```
// Arrow function
const add = x => y => x + y;// Normal function
function add(x) {
  return function(y) {
    return x + y;
  }
}
```

然后，我们可以这样实现这个函数:

```
const addFive = add(5);addFive(7); // 12
```

所以，currying 函数是一个返回另一个函数的函数，这个函数一次只接受一个参数，就像`add`。

# 什么是局部应用？

那么，什么是局部应用函数呢？

就一秒钟，让我们再来看看 currying。我们处理了带两个参数的函数`add`。你觉得有了三个参数应该是什么样子？

```
const add = (x, y, z) => x + y + z;
```

我解释过 currying 是一个函数，它返回另一个函数，一次接受一个参数。

那么，这一次，`add`取三个参数，它应该被分解成三个不同的取一个参数的函数。

```
const add = x => (y, z) => x + y + z;
```

这将返回一个函数，但它仍然同时接受两个参数。然后，必须再次更改返回的函数。

```
// Arrow function
const add = x => y => z => x + y + z;// Normal function
function add(x) {
  return function(y) {
    return function(z) {
      return x + y + z;
    }
  }
}const addFive = add(5);
const addFourAgain = addFive(4);
const nineTeen = addFourAgain(10);
nineTeen === 19 // true
```

好的，我以为我们要讨论部分应用，但是为什么我们又要讨论奉承呢？因为他们彼此非常非常相似。

```
const add = x => (y, z) => x + y + z;
```

所以，这不是一个奉承函数，因为`add`返回了一个带两个参数的函数，而不是一个。但这是部分应用功能。

…什么？

# 共同点和不同点

所以，这就是它们之间的区别。

Currying:返回另一个函数的函数可能会返回另一个函数，但每个返回的函数一次只能接受一个参数。

部分应用:一个函数返回另一个函数，这个函数可能返回另一个函数，但是每个返回的函数可以有几个参数。

```
// Currying function
const add = x => y => z => x + y + z;// Partial Application function
const add = x => (y, z) => x + y + z;
```

共同点:**currying 和 partial application 功能都是*而不是*和原来一样的功能。它们是新返回的函数，参数较少。**

**不同之处:Currying 只有一个参数，不像 partial application 可以有多个参数。**

# **我为什么需要它们？**

**两者都允许你利用*懒评*。这里有一个简单的例子。**

**所以，每当我们点击`#myDiv`，它的背景颜色将被设置为不同十六进制的蓝色。我们甚至不必在每次需要的时候都将整个字符串放入代码中。**

**就像上面的例子一样，currying 或局部应用使得*惰性评估*在需要的地方更加有效。**

# **结论**

**Currying 和 partial application 可能会让开发人员对它们的名字感到困惑，因为他们无法真正猜测它们的名字是用来做什么的。**

**但实际上，它们都是关于一次接受多少个参数，更重要的是要记住，你可以有效地利用*懒惰评估*。**

**你可能想读我的另一篇文章，讨论函数式编程中的`pipe`，你也会理解这个流程。感谢您的阅读！**

```
const add = x => y => x + y;
const minus = x => y => x - y;pipe(
 add(5),
 minus(10)
)(10); // -5
```

**[](https://medium.com/better-programming/functional-programming-and-the-pipe-function-in-javascript-c92833052057) [## JavaScript 中的函数式编程和管道函数

### 什么是管道，我们如何使用它？

medium.com](https://medium.com/better-programming/functional-programming-and-the-pipe-function-in-javascript-c92833052057)** 

# **资源**

*   **[维基中的阿谀奉承](https://en.wikipedia.org/wiki/Currying)**
*   **[维基中的部分应用](https://en.wikipedia.org/wiki/Partial_application)**
*   **[在 JavaScript 中获取随机 RGB 堆栈溢出](https://stackoverflow.com/questions/23095637/how-do-you-get-random-rgb-in-javascript)**