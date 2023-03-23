# 如何在 JavaScript 中使用 ForEach()

> 原文：<https://betterprogramming.pub/how-to-use-foreach-in-javascript-bce289252035>

## 因为循环曾经是我的一切。不再是了

![](img/ced734b8e5b319ec2b026048ceb687f1.png)

*照片由* [*托马斯·金托*](https://unsplash.com/@thomaskinto?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) *上*[*Unsplash*](https://unsplash.com/s/photos/for-each?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)*。*

我不知道你是怎么想的，但是当我开始学习如何编写 JavaScript 时，`forEach()`并不在我的考虑范围之内。如果我需要迭代一个数组，`for`循环就是我的一切。

几年前，当我参加我的编码训练营时，没有人教我们 JavaScript 数组方法。我不确定编码训练营现在是否考虑得更周到，把它们包含在课程中，但是如果不是，那么这篇文章是给你的！

# forEach()是做什么的？

`forEach()`以干净简洁的方式迭代数组。与它的前身(`for` loop)不同，你不必指明循环应该在数组长度的持续时间内运行。它就是这么做的！

`forEach()`数组方法对数组中的每个元素执行一次函数。它不会改变您正在迭代的原始数组。这意味着数组将*而不是*改变。

# 告诉我有关正在执行的此功能的更多信息…

对数组中每个元素执行的函数是一个[回调函数](https://www.freecodecamp.org/news/javascript-callback-functions-what-are-callbacks-in-js-and-how-to-use-them/)。

1.  `currentValue`(必选):代表正在迭代的数组中的每个值。
2.  `index`:如正在循环的`currentValue`的索引。
3.  `array`:就像你正在循环的数组一样。

该函数接受三个参数:

```
function(currentValue, index, array)
```

它还接受一个`thisArg`作为可选参数。事实上，我很少使用索引和数组参数。

如果您想了解关于这些可选参数的更多信息，请查阅 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)。

# 句法

```
array.forEach(function(currentValue, index, array))
```

现在让我们考虑一个使用一系列水果的简单例子:

```
const fruits = ["apple" , "banana" , "orange"]
```

现在，我们只是要迭代它们，并安慰出值:

```
fruits.forEach((element) => { console.log(element)}) //"apple" 
//"banana" 
//"orange"
```

哇！比`for`循环简单多了，对吧？您甚至可以使用 ES6 语法更简洁地编写:

```
fruits.forEach(element => console.log(element))
```

# “元素”一词的使用

你不必在你的回调函数中使用单词`element`来表示`currentValue`。您可以给这个参数起任何对您有意义的名字，但是您通常会看到它被命名为`element`或`el`。在我们的水果例子中，你可以用单词`fruit`代替`element`。

# 什么时候应该使用 forEach()？

当我第一次被介绍给`forEach()`时，它是变革性的。到那时，我会使用一个`for`循环来遍历一个数组，然后根据特定的标准，将元素推入一个新的数组。大概是这样的:

我不得不谷歌语法，因为它已经太长了！

您可以使用`forEach()`执行相同的过程:

虽然您仍然可以使用`forEach()`来做类似的事情，但是随着 JavaScript 数组方法`filter()`的引入，这已经不再是必要的或者最佳实践了。

# forEach()最适合在什么时候使用

*   您需要在不中断流程的情况下迭代整个数组。
*   没有其他的数组方法可以解决你的问题。

我知道，我知道。让我解释一下。

随着其他数组方法的引入，比如`filter()`、`every()`、`map()`等等，使用`forEach()`的必要性就没那么大了。老实说，我不能告诉你我最后一次在代码中使用它是什么时候。这并不是说它不重要。值得了解和理解。

`forEach()`总结到这里！

你在代码中使用它了吗？有哪些例子？我错过什么了吗？评论一下，让我知道！