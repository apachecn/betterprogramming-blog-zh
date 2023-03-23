# 5 个 JavaScript 数组方法和应该注意的陷阱

> 原文：<https://betterprogramming.pub/5-array-methods-to-know-by-heart-d298c7009999>

## 了解 map、reduce、sort、foreach 等的复杂性

![](img/937e05b0306846b4524733540e6bedae.png)

布莱克·康纳利在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

我们都经历过这种情况:我们处在编码流程中，代码像闪电一样从我们的手指中弹出。然后我们需要一个方法来操作一个`array`，但是我们是空白的！所以我们做了一些快速的谷歌搜索，甚至更快的复制粘贴，运行我们的代码，就在那里:一个大红色的错误*出现在我们的控制台上。这都是因为我们的代码与我们复制粘贴的代码略有不同。*

本文旨在通过提高您对五种常用的`array`方法、它们的来龙去脉(包括示例用例)以及需要注意的陷阱的理解来防止这种情况再次发生！无论您是最近才开始编码，还是已经编码好几年了，这些提示和技巧既包括易于理解的例子，也包括高级用例。

这是五个需要用心学习的数组方法(已排序),它们将使你的编码技巧更上一层楼！

![](img/6118c989fb3935bbe4ef483bd094688e.png)

# 过滤器

*   返回一个新的`array`。
*   不会改变调用它的`array`。

这一条非常简单明了，不需要任何记忆技巧就能记住。想过滤你的数组吗？继续使用`filter`方法吧！`filter`方法遍历数组中的每个元素，只返回通过由提供的函数实现的测试的项目。

`filter`方法接受三个参数:

1.  元素的`currentValue`
2.  数组中元素的`index`
3.  `array`本身

## 使用

```
let oldBand = ["Scary", "Baby", "Ginger", "Sporty", "Posh"]let newBand = oldBand.filter(currentElement => currentElement.length > 4)console.log(newBand)
// Outputs: Array ["Scary", "Ginger", "Sporty"]
```

在上面的例子中，我们在`oldBand`变量上使用了`filter`方法来创建一个`newBand`变量，其中我们只保留了名称超过四个字母的乐队成员。

`filter`方法的一个典型用例是允许用户过滤新闻网站上的文章、博客上的帖子或社交平台上的用户。在我们希望允许用户过滤一系列文章的情况下，我们的代码可能如下所示:

我们定义了一个`searchTerm`变量(通常会绑定到一个文本输入)，并将其指向“React”(即我们只想找到标题中有“React”的文章)。然后，我们将变量`articles`绑定到文章的`array`(通常从数据库中获取)。最后，我们在`filter`方法的帮助下创建一个新的`array`，并传递给它一个函数，该函数利用`includes`方法在我们的`articles` `array`中包含的对象的`titles`中寻找我们的`searchTerm`。`includes`方法区分大写和小写字母。因此，我们需要在`toLowerCase`方法的帮助下改变传递函数中的字符串。

## 逮到你了

除了必须提供一个函数之外，这个函数并没有任何其他的问题(也就是说，只调用`array.filter()`而不调用函数会抛出一个`TypeError`)。

![](img/8ff43bec5c6e0f9e95dd0725a8bc6e1d.png)

# 为每一个

*   不返回任何内容，但执行为`array`中的每个元素提供的函数。
*   不会改变调用它的`array`。

这个也很明显——它将为数组中的每个元素执行一次我们提供的函数。然而，只有在没有更好的工具时，才应使用`forEach`方法。

乍一看，它可能看起来像一个简单的循环，但重要的是要记住方法不是循环，如果你在编码前不思考，这个坏男孩会有一些令人讨厌的问题！`forEach`方法接受一个函数，并为`array.`中的每个元素执行该函数

`forEach`方法接受三个参数:

1.  元素的`currentValue`
2.  数组中元素的`index`
3.  `array`本身

## 使用

```
let fruits = ["apple", "banana", "lemon"]fruits.forEach(element => console.log(element))
// Outputs: "apple"
// Outputs: "banana"
// Outputs: "lemon"
```

上面的例子只是将数组中的每个值记录到我们的控制台中。您可能已经知道，`forEach`是在没有更好的选择时使用的通用工具。

尽管`forEach`本身不会改变调用它的数组，我们还是可以通过提供的函数改变数组，尽管最佳实践是使用适合这个目的的方法(例如`filter`或`map` 方法)。为了让您了解我们如何使用`forEach`实现数组操作，以及为什么应该避免使用它，请看下面的代码:

```
let countDown = [4, 3, 2, 1, 0]countDown.forEach((element, index, array)=> {
  element > 1 ? array.splice(index, 1) : ""
})console.log(countDown)
// Outputs: [3, 1, 0]
```

写上面的内容时，我们期望我们的函数移除所有值大于`1`的元素。因为我们在迭代过程中操作数组，所以实际发生的是函数只在每隔一个索引上运行，因为当我们在迭代结束前移除元素时，我们的数组一直在收缩。当从我们的`forEach`函数中记录元素和索引时变得很清楚，就像这样:

## 逮到你了

你不会想到像这样的基本函数会有任何问题，但是我们已经在上面讨论过了！另一个容易被忽略的问题是，如果在调用`forEach`方法的`array`中有一个空索引或`undefined`索引(即`myArr = [1, 2, , 4, 5]`，则不会在空索引上调用该函数(在本例中为`myArr[2]`)。

最终，我们会遇到需要使用外部引用(如数据库)的情况，这反过来要求我们异步工作。

`forEach`方法的一个缺点是它期望一个同步函数，不会等待承诺。因此，如果我们需要在一个已经异步运行的函数中遍历一个`array`，我们就失去了对异步函数执行顺序的控制。在这些情况下，最好使用一个`[for...of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of)` [块](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of)，它是一个循环而不是一个函数，因此本身不必异步执行。

![](img/04b8ceb2cc89ce3630ff617b24b5e03e.png)

# 地图

*   返回新的`array`。
*   不会改变调用它的`array`。

`map`方法接受一个函数并返回一个新的`array`,其中填充了所提供函数的结果。这可能是新开发人员遇到的第一个方法，因为它是在 React 中构建元素集合时使用的方法。记住它的名字可能有点棘手，因为名字并没有真正描述它的功能。

在数学中，*映射*和*函数*可以互换使用，如果你是数学家，这可能会让你更容易记住。试着想想主动动词，映射。我们正在将旧的`array`中的每个元素映射到新的`array`。

`map`方法接受三个参数:

1.  元素的`currentValue`
2.  数组中元素的`index`
3.  `array`本身

## 使用

```
let oldSalaries = [3000, 1500, 7000]newSalaries = oldSalaries.map((element) => element * 1.02)console.log(newSalaries)
// Outputs: Array [3060, 1530, 7140]
```

上面的代码展示了一个如何使用`map`方法的简单例子。我们有一份每年到期的工资清单。我们`map`覆盖我们的旧数组，给每个人加薪 2%，并将其保存在我们的`newSalaries`变量中。

`map`方法的一个典型用例是，当我们有一个`array`时，我们希望基于初始`array`中包含的值创建一个新的`array`。如果你已经在 React 中编写了一些代码，当[嵌入内联 JSX](https://reactjs.org/docs/lists-and-keys.html#embedding-map-in-jsx) 并遍历存储在数组中的数据来创建 UI 组件时，你可能会不假思索地使用它:

## 逮到你了

就像使用`forEach`方法一样，`map`不会迭代空索引或`array`中的`undefined`索引。尽管可以操纵源`array`并忽略返回的`array`，但这是一种反模式。在这种情况下，应使用`forEach`或`for...of`块。

`map`与`forEach`的另一个共同点是，当在迭代过程中操作源`array`时，它会导致不可预见的问题。不过，这应该不是什么大问题，因为`map`已经返回了一个新的`array`。

![](img/d7973c2f8cb3391a9f7e79c061d546ae.png)

# 减少

*   回报取决于实现了什么功能，以及作为`initialValue`提供了什么。
*   总是提供一个`initialValue`来避免陷阱！
*   不会改变调用它的数组。

记住`reduce`的诀窍是把它想象成一种把数组简化为一个新变量的方法——可以是`number`、`string`、`array`或其他什么。在它最简单的形式中，它将把你的由数字组成的数组简化为一个数字，但是它有更多的用途！

`reduce`方法接受四个参数:

1.  `accumulator`
2.  元素的`currentValue`
3.  数组中元素的`index`
4.  `array`本身

## 使用

起初，用例似乎只限于处理数字，但是在其他情况下，`reduce`方法可以证明是有益的(例如，当通过属性对存储在数组中的对象进行分组时[)。](https://javascript.plainenglish.io/5-use-cases-for-reduce-in-javascript-61ed243b8fef#08bb)

## 逮到你了

如果数组为空并且没有提供`initialValue`，将抛出`TypeError`。如果数组只有一个元素(不考虑位置)并且没有提供`initialValue`——或者如果提供了`initialValue`，但是数组是空的——将返回 solo 值，而不调用提供的函数*。*

![](img/e1c9355c1442cbe54f87e2c2b05e4b54.png)

# 分类

*   返回相同的数组。
*   在地方可称为(即`array.sort()`)。
*   通过排序来改变调用它的数组。

这个程序做了它所说的事情:它对我们的`array`进行排序。然而，我们*应该*总是提供一个比较函数来避免陷阱。如果没有提供函数，`sort`方法会将每个元素转换成字符串，比较它们的 UTF-16 代码单元值序列，并按升序排序。因此“angular”在“bootstrap”之前，由于转换为字符串，700 在 9 之前。

`sort`方法接受两个参数:

1.  用于比较的`firstElement`
2.  `secondElement`进行比较

当提供比较功能时，这是`sort`解释返回值的方式:

*   如果提供的函数返回小于`0`的值，则`firstElement`和`secondElement`保持不变。
*   如果所提供的函数返回`0`，则`firstElement`和`secondElement`相对于彼此保持不变，但相对于所有不同的元素进行排序。
*   如果提供的函数返回大于`0`的值，则将`secondElement`排在`firstElement`之前。

## 使用

```
let numbersArray = [46, 2000, 12, 58, 300]numbersArray.sort((a, b) => a - b)console.log(numbersArray)
// Outputs: [12, 46, 58, 300, 2000]
```

上面的例子显示了一个简单的比较函数，用于对包含数字的`array`进行升序排序。

稍微调整一下，可以使用类似的比较函数对日期进行排序:

最后，如果我们有一个包含大写和小写混合的`array`，我们可以像这样按字母顺序排序:

## 逮到你了

始终提供比较功能来控制输出。还记得如果我们不提供 compare 函数，`sort`方法会将元素转换成字符串并进行比较吗？这导致大写单词出现在小写单词之前(即“Bootstrap”出现在“angular”之前)，这很容易忘记，因此可能会导致问题。

如果在我们提供的`array`中有值是`undefined`，它们将被排序到数组的末尾。因此，您应该始终为`sort`提供一个由相同的`[primitive values](https://developer.mozilla.org/en-US/docs/Glossary/Primitive)`或`objects`组成的`array`。

# 结论

这就是我们列出的让你在编码中更加成功和高效的方法清单。感谢您花时间阅读这篇文章。我希望你学到了新的东西！