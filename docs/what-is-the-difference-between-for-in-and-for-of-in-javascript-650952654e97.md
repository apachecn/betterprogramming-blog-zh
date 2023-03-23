# JavaScript 中的' for…in '和' for…of '有什么区别？

> 原文：<https://betterprogramming.pub/what-is-the-difference-between-for-in-and-for-of-in-javascript-650952654e97>

## 一个用于可枚举的，一个用于可迭代的…你应该如何使用每一个？

![](img/55039d82eacd4cf6bd3da7e27521adf4.png)

照片由[菲利普·姆罗兹](https://unsplash.com/@mroz?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/loop?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

循环已经走过了漫长的道路。从`while`循环开始，发展到普通的`for`循环，都不迭代实际的数据结构。相反，它们迭代一个序列，该序列反映了用户的标识符。

```
let nums = [1,2,3,4,5];for(let i=0; i < nums.length; i++) {
   console.log(nums[i]);
}
```

变量`i`实际上并不是`nums`的一部分，只是碰巧类似于`nums`的索引。

现代 JavaScript 有更好的方法来循环数据——特别是`for...in`和`for...of`循环。了解这两者之间的区别，以及指导我们何时使用它们的基本原则。

# 可枚举与可迭代

为了理解`for...in`和`for...of`之间的区别，定义可枚举和可迭代之间的区别是很重要的。这可能很棘手，因为有一些重叠，而且这些术语经常互换使用。

我喜欢把可枚举数想象成一个长方形，把可迭代数想象成一个正方形。因此，所有的**可重复项都是可枚举的**；但是，并不是所有的可枚举数都是可迭代的。

记住这一点，让我们更深入地挖掘它们的定义。

我们将首先定义一个可枚举的。动词 *enumerate* 被定义为一次一个项目的提及或计数。将它应用到编程中，可枚举就变成了一个实体，它的各个部分可以被清楚地识别和引用。

现在，我们来看一个**可迭代的**。从词根动词 *iterate* ，定义为重复执行，iterable 是一个实体，其中对每个项目执行相同的一组操作。

那么有什么区别呢？在我们强调是什么将可枚举提升为可迭代之前，下面是每种方法的一些示例:

*   一袋 M & Ms 巧克力豆是可数的
*   车管所的线是可重复的
*   一堆书是可数的
*   书架上的一排书是可重复的
*   JSON 对象是可枚举的
*   JSON 数组是可迭代的

你认为这是关键品质吗？这是命令。iterable 有一个内部的顺序，而 enumerable 有不同的部分，但是它们是无序的。

# 使用“for…in”

我们从`for...in`开始，它用于循环遍历一个可枚举对象。这非常适合对对象中的键值对进行计数，如下例所示。

```
let person = {
   "first_name": "Jonathan",
   "last_name": "Hsu",
   "medium-handle": "[@jhsu98](http://twitter.com/jhsu98)"
}for(const key in person) {
   console.log(key + ": " + person[key]);
}/*
"first_name: Jonathan"
"last_name: Hsu"
"medium-handle: @jhsu98"
*/
```

如你所见，`for...in`循环将为对象的每个键设置临时变量。因此，在这种情况下，被计数的是标识符。

# 使用“for…of”

好，现在我们过渡到`for...of`循环。这个循环有一个非常相似的语法。然而，在关键字`of`之后需要一个 iterable 与使用`in`的可枚举的要求相反。

```
let scores = [43,58,28,69,38];for(const item of scores) {
   console.log(item);
}/*
43
58
28
69
38
*/
```

如果我们尝试将`for...of`与一个可枚举对象一起使用，会出现以下错误:

```
let person = {
   "first_name": "Jonathan",
   "last_name": "Hsu",
   "medium-handle": "[@jhsu98](http://twitter.com/jhsu98)"
}for(const item of person) {
   console.log(item);
}/*
"TypeError: person is not iterable
    at gewuhimaza.js:6:84
    at [https://static.jsbin.com/js/prod/runner-4.1.7.min.js:1:13924](https://static.jsbin.com/js/prod/runner-4.1.7.min.js:1:13924)
    at [https://static.jsbin.com/js/prod/runner-4.1.7.min.js:1:10866](https://static.jsbin.com/js/prod/runner-4.1.7.min.js:1:10866)"
*/
```

# 我认为数组也是可枚举的

你说得对！我们看到向`for...of`传递一个可枚举数会导致错误，但是向`for...in`传递一个可迭代数是可以接受的。

```
let scores = [43,58,28,69,38];for(const item in scores) {
   console.log(item);
}/*
"0"
"1"
"2"
"3"
"4"
*/
```

类似于`for...in`对对象的标识符进行计数，`for...in`将对索引进行计数——将它们视为位置标识符。

*这就对了。Iterables 是一种可枚举类型，额外的特性是一个顺序。在描述您的数据结构时，尽量有意识地传播* `for...in` *和* `for...of` *之间的差异。*