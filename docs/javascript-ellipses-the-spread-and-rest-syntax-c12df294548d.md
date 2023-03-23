# JavaScript 省略号:Spread 和 Rest 语法

> 原文：<https://betterprogramming.pub/javascript-ellipses-the-spread-and-rest-syntax-c12df294548d>

## 现代 JavaScript 的实用简写

![](img/5be25385760c85a4f632ce42cb53c252.png)

照片由 [Claudel Rheault](https://unsplash.com/@claudelrheault?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 输入 Javascript 省略号(…)

现代 JavaScript 允许我们使用三个点，`…`，也称为省略号，来执行某些转换和速记方法。省略号的使用分为两类:[休止语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)和[展开语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)。我将依次解释这些。

# Rest 语法

Rest 语法允许程序员检索集合中程序员没有使用过的剩余项——项的*rest*——例如对象、数组或函数参数。

让我们来看几个例子。

## 带有对象解构的 Rest 语法

现代 JavaScript 允许我们解构对象。也就是说，我们可以从一个现有的对象中快速定义变量并给它们赋值。这里有一个简单的例子:

在这个例子中，我们首先定义一个对象。然后，使用对象解构，我们从我们的对象中取出定义变量`c`和`d`，并分别给它们赋值。然后可以使用变量`c`和`d`，正如我们在解构后的日志行中看到的。

使用“rest”语法，我们可以获得对象中未被显式解构的剩余项，如下例所示:

正如你所看到的，在从`obj`中解构`c`和`d`之后，我们解构了`…restOfObj`，它被赋予了一个新对象的值，该对象具有来自`obj`的所有键，除了那些被显式使用的键。需要注意的是，`rest`语法必须用在一个解构的结尾，并且由前面的变量名来表示，在这个例子中是`restOfObj`，带有一个省略号(`…`)。

# 带有数组解构的 Rest 语法

现代 JavaScript 也允许我们解构数组。这里有一个简单的例子:

这里，我们首先定义一个数组，并将其赋给变量`arr`。然后，我们解构数组，取出前三项，并将它们分配给变量`firstItem`、`secondItem`和`thirdItem`。如您所见，数组的第一个值赋给了解构中定义的第一个变量，第二个值赋给了第二个变量，依此类推。

使用`rest`语法，我们可以取出数组中的前几项，然后将剩余的项分配给一个新数组:

如您所见，通过使用`rest`语法，我们已经创建了一个名为`restOfArr`的变量，并为其分配了一个新数组，其中包含来自`arr`的未被显式解构的剩余项。如同`rest`对象解构的语法一样，`rest`数组的语法必须在解构的结尾使用。结尾由前面的变量名表示，在本例中是带省略号(`…`)的`restOfArr`。

## 带有函数参数的 Rest 语法

函数定义也接受定义参数的`rest`语法，如下所示:

如您所见，我们已经定义了函数参数`a`、`b`和`c`。接下来，我们使用一个带有参数名`…restOfProvidedParameters`的省略号来分配剩余的参数，并将它们填充到一个数组中。由于第一、第二和第三个参数在函数定义中被显式地使用为`a`、`b`和`c`，所以它们不包含在`restOfProvidedParameters`数组中。

## Rest 语法陷阱

顾名思义，`rest`语法*不能*用于获取对象、数组或参数定义的开始元素。例如，这是不可能的:

```
function (...firstParametersExceptTheLast, lastParameter) {}// SyntaxError: Rest parameter must be last formal parameter
```

同样，it *也不能*在中间元素，就像这样:

```
function (firstParameter, ...middleParameters, lastParameter) {}// SyntaxError: Rest parameter must be last formal parameter
```

# 扩展语法

Spread 语法允许集合(如对象或数组)以一种简化的方式与其他集合或函数进行交互。让我们来看看不同的组合。

## 将数组语法扩展到函数调用

这里我们定义了一个函数和一个长度为`2`的数组。然后，我们用字符串`a`、展开数组`...arr`和字符串`d`调用函数:

当`fun`被调用时，字符串`a`首先被提供给`aParam`。接下来，`arr`就展开了。数组中有两个项目，因此`arr`、`b`、`c`、`bParam`和`cParam`将获得这些值。当扩展完成时，剩余的字符串值`d`被提供给函数的`dParam`。

## 将数组语法扩展到其他数组

扩展语法也可用于组合数组:

```
const arr1 = [ 'b', 'c' ];
const arr2 = [ 'e', 'f' ];const combinedArr = [ 'a', ...arr1, 'd', ...arr2, 'g' ];console.log(combinedArr); // [ 'a', 'b', 'c', 'd', 'e', 'f', 'g' ];
```

这里我们看到两个数组，`arr1`和`arr2`被定义。我们创建了一个新的数组`combinedArr`，在它的创建过程中，我们在其中“传播”`arr1`和`arr2`。结果是新数组`combinedArr`包含了展开数组的值。

## 将对象的语法扩展到其他对象

扩展语法可用于添加和覆盖对象中的键值。规则是最后提供的键值优先。这里有一个例子:

如您所见，我们已经定义了三个对象:`obj1`、`obj2`和`obj3`。然后我们定义了一个新的对象，`combinedObj`，并将之前定义的三个对象分布在其中。给定键的最后一个提供的值会覆盖以前提供的值。以下是一些解释:

*   关键`a`是`1`因为虽然在`combinedObj`中明确定义为`0`，但是`obj1`是在明确定义之后展开并覆盖的。
*   键`d`是`0`,因为虽然`obj2`被展开并定义为`2`,但它随后在`combinedObj`内被明确设置为`0`。
*   Key `f`之所以是`0`，是因为虽然`obj1`、`obj2`、`obj3`被展开并分别定义和覆盖为`1`、`2`、`3`，但最终还是从`combinedObj`内部明确设置为`0`。

## 传播语法附加内容

在 JavaScript 中，数组也是一个对象，因此它也可以扩展到一个对象:

```
const arr = [ 'a', 'b', 'c' ];const obj = { ...arr };console.log(obj);
// { 0: 'a', 1: 'b', 2: 'c' }
```

将一个数组扩展到另一个数组可用于制作数组的浅层副本:

```
const originalArr = [ 'a', 'b', 'c' ];const arrayShallowCopy = [ ...originalArr ];arrayShallowCopy[1] = 'overrideShallowCopyValueOnly';console.log(originalArr); 
// [ 'a', 'b', 'c' ]
// (The value is unchanged)console.log(arrayShallowCopy); 
// [ 'a', 'overrideShallowCopyValueOnly', 'c' ]
```

# 传播语法陷阱

Spread 语法不适用于集合到数组、对象和函数的所有组合。这里有一些*不*工作:

*   对象不能传播到函数调用
*   对象不能扩展到数组

# 摘要

在 Javascript 中，省略号(`…`)用于两个独立的缩写——rest 语法和 spread 语法。Rest 语法将集合的剩余元素设置为一个已定义的变量。扩展语法允许集合中的元素以某种方式与其他对象、数组和函数进行交互，通常是为了提供或覆盖值。

我希望这对你有所帮助！

# 参考

*   [https://www . stadler software . com/coding/JavaScript-three-dots-rest-parameters-and-spread-syntax/](https://www.stadlersoftware.com/coding/javascript-three-dots-rest-parameters-and-spread-syntax/)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Operators/Spread _ syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)