# 学习 JavaScript:箭头函数

> 原文：<https://betterprogramming.pub/learning-javascript-arrow-functions-36cce13351c2>

## 用粗箭头函数浓缩你的函数定义

![](img/c8e5e84a12de4608b5f3ad5716b6913b.png)

照片由[布伦丹·丘奇](https://unsplash.com/@bdchu614?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/arrow?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

如果你涉足 JavaScript 已经有一段时间了，你可能会在一些代码中遇到粗箭头符号`=>`,并想知道它是什么意思。

仔细看一些例子，不难发现有输入、语句和输出。考虑到它被称为箭头*函数*，你可以进行逻辑跳跃。

```
const convertString = (s) => {
   return s.replace(" ","_").toLowerCase();
}console.log(convertString("Hello World"));
// prints hello_world// convertString is the name
// (s)is the parameter list
// the curly braces define the body of the function
```

但是箭头函数实际上是如何工作的呢？它们是如何产生的？为什么要改变正常的函数定义？最重要的是，我如何开始使用它们？

为了回答这些问题，让我们不要像对 JavaScript 一无所知一样谈论它们，而是像我们已经有了想要转换成最新最好的 ES6 的代码一样。

# 什么是箭头函数？

箭头函数是匿名函数的精简表示，匿名函数是没有独立声明的函数。

您可能已经养成了定义函数的习惯，但是如果您像我一样，并且您不是日常工作中的开发人员，您可能不会意识到在编写有组织且简洁的代码的同时还有效率可言。

所以，我们一起来学习吧！我们的下一步是理解匿名函数是如何工作的，然后我们将着手把我们的传统函数转换成匿名函数，最后转换成箭头符号。

# 匿名函数是如何工作的？

当一个方法接受一个函数作为参数，并且该函数不打算重复使用时，匿名函数是最常用的。

因此，最有效和最易读的方法是内联定义函数，并且不命名，因为它可以在其后被丢弃。在接受函数作为参数的方法中，这种情况经常发生。

```
// the map method performs a function on each element in the arrayconst numbers = [1,2,3,4]
const squares = numbers.map((n) => {
   return n ** 2;
});console.log(squares); // 1,4,9,16
```

如果此代码导致眯眼、重复或抓头，以下是传统定义函数的相同示例:

```
function calcSquare(n) {
   return n ** 2;
}// the map method performs a function on each element in the arrayconst numbers = [1,2,3,4]
const squares = numbers.map(calcSquare);console.log(squares); // 1,4,9,16
```

那么，如果传统功能仍然有效，为什么要改变呢？

# 箭头函数语法更简洁

用这种语法编写函数需要更少的开销(键入文字字符),并且适应了 JS 中的函数不再整齐地写在每个脚本的顶部并在下面调用的现实。

除了不赞成使用`function`关键字之外，arrow 函数还有其他语法效率，这些效率一开始可能会令人困惑，但一旦解释清楚，就会变得非常酷。

以下是语法变化的列表，供您参考:

## **无功能关键字**

如果你的代码块有输入、语句和(大概)输出，那么它听起来很像一个函数。

没有必要进一步说明发生了什么。上述情况出现的唯一其他时间是在类方法中，但那是在一个类中，所以有明显的区别。

## **没有函数名，除非你想要它**

匿名函数和命名函数有什么区别？嗯，就是这个名字。

我们可以将传统函数吸收到 arrow 语法中，减少必要的编码约定，而不是将 arrow 语法和传统语法混为一谈。只需将匿名函数分配给一个变量，该变量的名称将用于调用该函数。

## **跳过单个参数的括号**

如果你的函数只有一个参数，那么你可以选择跳过括号。这进一步压缩了代码，可能使新手阅读起来更困难，但是一旦理解了就更容易编写和浏览。

## **跳过花括号，返回一条语句**

如果函数体只有一条语句，那么你可以跳过花括号，甚至是返回语句。

就像省略单个参数的圆括号一样，这一开始可能会令人难以置信地精简，但是一旦精通，就很难进行语法输入和阅读了。

# 如何开始使用箭头功能？

如果你是一个全新的 JavaScript 初学者，那么你只需使用箭头符号来定义函数。落入这个阵营，如果你正在挣扎，那么你需要问的真正问题是:“什么是功能，我如何使用它们？”

如果你以前写过 JS，并试图转换你的代码实践，那么这就是一个新旧转换的问题。

让我们通过使用之前的`calcSquare()`函数并通过箭头符号对其进行优化来演示这种转换。

```
function calcSquare(n) {
   let result = n ** 2;
   return result;
}let numbers = [1,2,3,4];
console.log(numbers.map(calcSquare));
```

所以，我们的函数只使用了一次来计算数组中每个值的平方。我们将移除独立定义，并在`.map()`方法中创建一个匿名函数。

```
let numbers = [1,2,3,4];
console.log(numbers.map((n) => {
   let result = n ** 2;
   return result;
}));
```

但是，如果有两个数据集，意味着函数必须多次使用，该怎么办呢？我们将从`.map()`方法中取出函数；然而，这一次，它将用箭头符号来定义。

```
const calcSquares = (n) => {
   result = n ** 2;
   return result;
};let numbers = [1,2,3,4];
console.log(numbers.map(calcSquares));let numbers2 = [5,6,7,8];
console.log(numbers2.map(calcSquares));
```

在这一点上，我们已经完全升级到了箭头符号，但是还没有到结束的时候。我们可以去掉括号，因为我们的函数只有一个参数。

```
const calcSquares = n => {
   result = n ** 2;
   return result;
}
```

此外，由于我们可以通过直接返回求值结果将主体压缩成一条语句，因此我们可以去掉花括号和`return`关键字。

```
const calcSquares = n => n ** 2;
```

暂停一下，看看我们压缩了多少函数。哇…

我们已经将一个现有的函数转换为箭头符号，记住样板文件，以此作为所有未来函数的起点:

```
const functionName = () => {};
```

*   如果你的函数是匿名的，跳过变量赋值。
*   如果您的函数只有一个参数，请删除括号。
*   如果你的函数体中只有一条语句，去掉花括号，不要用`return`关键字。

# 结论

最终，我认为作为一个全新的学习者，箭头函数可能更容易学习，但是对于我们这些在思想上改写了以前的编码惯例的人来说，请记住箭头符号是一种简化的、精简的、优化的 JavaScript 函数定义方法。