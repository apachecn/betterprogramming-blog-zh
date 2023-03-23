# JavaScript 中反转字符串的 3 种方法

> 原文：<https://betterprogramming.pub/3-ways-to-reverse-a-string-in-javascript-85db33abe8e5>

## 为害怕的人介绍算法

![](img/bff9b5501bbc8a298138275e028ce5a2.png)

[Joanna Kosinska](https://unsplash.com/@joannakosinska?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/string-light?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片。

对于所有害怕算法的人来说，你不必害怕。是的，我去过那里。不，我还是对其中一些不太满意。但是所需要的只是学习和练习，练习，练习。

让我们从反转一根弦开始练习。我将向你展示三种方法来做到这一点。

# 1.**反向()方法**

第一种方法非常简单，几乎是作弊。想一想:有没有内置的逆向方法可以做到这一点？的确，没有内建的方法来反转一个字符串，但是数组呢？是的，有一个！很奇怪，它叫`reverse()`。所以我们要做的就是:

1.  通过调用`.split('')`将字符串转换成数组。
2.  给`.reverse()`打电话。
3.  通过调用数组上的`.join('')`将数组转换回字符串。

就这样:

我们可以更进一步，通过链接方法。现在这一切都在一行中！

# 2.使用 for 循环

正如我所说，第一种方法几乎就像作弊，所以让我们准备好另一种解决方案以防万一。如果你能以多种方式解决一个问题，这总是好的。

对于这个解决方案，我们将使用一个`for…of`循环。与常规的 for 循环相比，我更喜欢它精简的语法，因为它包含了更多的东西。这是我们要做的:

1.  创建一个名为`reversed`的变量，并使其成为一个空字符串。
2.  编写`for…of` 循环的主体。它将遍历字符串中的每个元素，我们将该元素添加到`reversed`字符串的开头，然后是`reversed`字符串的其余部分。
3.  返回`reversed`字符串。

# 3.reduce()方法

最后一种方法有点复杂，但值得一提。它将涉及一个名为`reduce`的数组助手。

[根据 MDN web docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce),“`reduce()`方法对数组的每个元素执行一个 reducer 函数(您提供的)，产生一个输出值。”基本上，它接受数组中的所有值，并将它们压缩成一个奇异值。

`reduce()`方法有两个参数——一个箭头函数和函数的初始值(在我们的例子中，是一个空字符串)——看起来像这样:`reduce(() => {}, '')`。

每当`reduce()`运行时，它将获取起始值，并将其作为第一个参数传递给 arrow 函数。然后，无论从内部函数返回什么，都将被用作该函数下一次运行的起始参数。

让我们来看看它的实际应用:

1.  用`.split('')`把我们的字符串转换成一个数组。
2.  写出 reduce 函数的结构:`reduce(() => {}, '')`。
3.  让我们给箭头函数添加参数。第一个参数将是一个反向字符串。姑且称之为`reversed`。第二个参数是我们当前在数组中操作的`element`。其机制类似于上面的第二种方法——我们将把该元素添加到开头，然后是字符串的其余部分。
4.  该函数通过数组的每个元素运行一次，执行该函数。

现在您知道如何在 JavaScript 中反转字符串了！

# 资源:

1.  没有斯蒂芬·格里德我会在哪里？这里有他在 Udemy 上的精彩课程的链接，[编码面试训练营:算法+数据结构](https://www.udemy.com/course/coding-interview-bootcamp-algorithms-and-data-structure/)。
2.  [MDN 上的 Array.prototype.reverse()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reverse) 。
3.  [用于 MDN 上](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of)循环的…。
4.  [MDN 上的 Array.prototype.reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) 。