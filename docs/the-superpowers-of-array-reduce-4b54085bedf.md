# 数组的超能力减少()

> 原文：<https://betterprogramming.pub/the-superpowers-of-array-reduce-4b54085bedf>

## 最强大的高阶函数之一

![](img/cc4a405bf6107a8b1a132acd9ce255d3.png)

埃斯特万·洛佩兹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

ECMAScript 5 在 2009 年引入了许多令人敬畏的[特性](https://www.w3schools.com/js/js_es5.asp)，其中大部分是数组方法，如`isArray`、`forEach`、`map`、`filter`、`every`、`some`。不过还是说说我最喜欢的一个:`reduce`。

# 该简化方法

`reduce`方法对数组的每个元素执行一个`reducer`回调函数(由用户提供),产生一个输出值。

## 还原剂

`reducer`函数有四个参数:

*   蓄能器(acc)
*   当前值(当前)
*   当前索引(idx)
*   源阵列(src)

您的`reducer`函数的返回值被分配给累加器，累加器的值在整个数组的每次迭代中被记住，并最终成为最终的单个结果值。

重要提示:在每次迭代中，您必须返回下一次迭代的累加器值(最终将是最终的返回值)，否则累加器的下一个(也是最终的)值将是`undefined`。

## 初始值

`reduce`方法接受第二个可选参数:`initialValue`。如果不提供，累加器的初始值将是数组的第一个元素，第一次迭代将指向第二个元素。如果提供了`initialValue` ，则为累加器的初始值，第一次迭代指向数组的第一个元素。

# 例子

## 对有/没有初始值的数字求和

对有/没有初始值的数字求和

如果没有初始值，第一次迭代将有`accumulator`指向数组的第一个元素(1)，有`currentValue`指向数组的第二个元素(2)。

给定初始值，第一次迭代将有一个带有给定初始值的值的`accumulator`(3)，`currentValue`将指向数组的第一个元素(1)。

## 计算数组中出现的次数

让我们统计以下单词的出现次数，并将结果存储在一个映射中:

如果土拨鼠会扔木头，它会扔多少木头？如果土拨鼠会扔木头的话，他会尽可能多地扔木头。

多少只土拨鼠？

我们初始化一个空的 map 并使用它作为累加器的初始值，当我们迭代句子中的单词时，初始化或更新每个单词的出现次数。

这只是两个例子，但是现在你一定已经意识到`reduce`有多棒了，对吧？

它允许你取一个数组，并把它的值简化为基本上任何可以从它保存的数据中导出的值。它还允许您返回任何类型的数据，而不管数组元素的类型。

# 一种方法来统治他们？

再看看其他的 ES5 数组方法，我们可以看到每个方法都在数组上使用给定的回调函数，并返回某种结果。

例如:

*   `map`转换数组的每个元素，返回一个新的数组。
*   `every`检查给定的条件是否适用于数组中的每个元素，返回相应的布尔值。

看起来很眼熟，对吧？

利用我们已经知道的，让我们试着用`reduce`来实现其他的 ES5 数组方法。

注意:在每个例子中，我们都会将新方法添加到`Array`的原型中，其中`this`将指向我们正在操作的数组。

# 地图

`[map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)`方法创建一个新数组，其中每个原始元素都由给定的`transformer`回调函数转换。

## 使用

使用将数组中的每个数字加倍的 transformer 回调，我们得到一个新数组，其中每个元素都是其原始值的两倍。

## 用`reduce`

`map`对一个数组进行操作，返回一个新的数组，所以累加器必须是一个数组。

使用 reduce 绘制地图

使用`reduce`，我们从一个空的数组累加器开始，迭代数组。然后，我们对每个元素应用 transformer 回调，并将其推送到累加数组。

# 过滤器

方法`[filter()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)`创建了一个新的数组，其中所有元素都通过了由所提供的函数实现的测试。

## 使用

使用一个过滤掉所有奇数的测试回调，我们得到一个新的数组，其中包含原始数组的所有偶数元素。

## 用`reduce`

就像前面的例子一样，`filter`也操作一个数组并返回一个新的数组，所以累加器必须是一个数组。

使用 reduce 过滤

使用`reduce`，我们从一个空的数组累加器开始，遍历数组。然后，我们使用 tester 回调来检查每个元素是否应该被推送到累积数组中。

# 每个

`[every()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every)`方法测试数组中的所有元素是否都通过了由提供的函数实现的测试。它返回一个布尔值。

## 使用

使用一个测试数组中每个元素的回调函数，我们得到一个布尔值，表明是否所有元素都通过了测试。在这种情况下，所有元素都小于 10，因此`every`返回`true`。

## 用`reduce`

`every`对数组进行操作并返回布尔值，因此累加器必须是布尔值。

使用`reduce`，我们从一个布尔累加器值`true`开始(我们将在后面讨论原因)并迭代数组。然后，我们使用逻辑 AND ( `&&`)将测试器回调的结果链接到累加器，如果所有元素都通过测试，最终返回`true`，否则返回`false`。

为什么从`true`开始？

如果数组为空，`every`返回`true`而不管测试回调(即使回调返回`false`)。

否则，如果所有元素都满足条件，使用逻辑 AND 的初始`true`值的链接将最终解析为`true`。否则，链接将最终解析为`false`。

# 一些

`[some()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some)`方法测试数组中是否至少有一个元素通过了由提供的函数实现的测试。它返回一个布尔值。

## 使用

使用一个测试数组中每个元素的回调函数，我们得到一个布尔值，表明是否有任何元素通过了测试。在这种情况下，第四个元素大于 3，因此`some`返回`true`。

## 用`reduce`

`some`对一个数组进行操作并返回一个布尔值，所以累加器必须是布尔值。

使用`reduce`，我们从布尔累加器值`false`开始(稍后我们将讨论原因)并迭代数组。然后，我们使用逻辑 OR ( `||`)将测试器回调的结果链接到累加器，如果有任何元素通过测试，最终返回`true`，否则返回`false`。

为什么从`*false*`开始？

如果数组为空，则`*some*`返回`*false*`而不管测试回调(即使回调返回`*true*`)。

否则，如果任何元素满足条件，使用逻辑 OR 的初始`*false*`值的链接将最终解析为`*true*`。否则，链接将最终解析为`*false*`。

# 免责声明(每个和一些)

`every`方法为数组中的每个元素执行一次回调函数，直到找到回调函数返回一个 falsy 值(当转换为布尔值时，该值变为`false`)的元素。如果找到这样的元素，`every`立即返回`false`。

类似地，`some`方法对数组中的每个元素执行一次回调函数，直到找到回调函数返回一个 *truthy* 值(转换为布尔值时变成`true`的值)的元素。如果找到这样的元素，`some`立即返回`true`。

然而，没有什么好办法让[终止](https://stackoverflow.com/questions/36144406/how-to-break-on-reduce-method) `[reduce](https://stackoverflow.com/questions/36144406/how-to-break-on-reduce-method)` [中间循环](https://stackoverflow.com/questions/36144406/how-to-break-on-reduce-method)。
这意味着尽管两个实现(最初的`every/some`方法和使用`reduce`的相应实现)都有一个运行时`O(n)`，但是最初的实现很可能会终止，而不必遍历整个数组，从而使它们更加高效。

# 过滤器+地图

给定一个数字数组，过滤掉所有偶数元素并平方剩余元素的最有效方法是什么(使用 ES5 方法)？

让我们试试`filter`然后是`map`:

我们创建了一个只保留奇数元素的测试函数和一个平方给定元素的转换函数。然后我们在链接`filter`和`map`方法时使用这两个回调函数，并返回期望的数组。

让我们使用我们所知道的关于用`reduce`实现`filter`和`map`的知识，只是这一次让我们将它们结合在一起。

我们使用相同的测试器和转换函数来测试每个元素是否应该保留在数组中，如果应该的话就转换它。

这种方法节省了我们创建过滤值的中间数组的需要，并且我们得到了一个稍微更有效的算法，因为我们不必迭代两个不同的数组(原始数组和中间数组)。

# 结论

这些是如何使用强大的`reduce`方法实现其他 ES5 方法的几个例子。

你如何在日常编码中使用它？分享在评论里！

# 来源

*   w3schools 的 [ES5 特性](https://www.w3schools.com/js/js_es5.asp)
*   [MDN web docs 的 Javascript 数组方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)。
    本文中的每个方法文档都来自这里。