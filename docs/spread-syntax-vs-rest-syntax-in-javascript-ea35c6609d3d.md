# JavaScript 中的 Spread 语法(…)与 Rest 语法(…)

> 原文：<https://betterprogramming.pub/spread-syntax-vs-rest-syntax-in-javascript-ea35c6609d3d>

## 用两个例子来理解两者的区别

![](img/c1537ea4ce73585af4eb2106d9dac8ab.png)

图片由 [Andreea Popescu](https://unsplash.com/@andreeazpopescu?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

S *pread* 语法和 *rest* 语法可以让你的编码更加整洁。两者语法相同(即`…`)，但省略号用在不同的地方，做的事情就不一样。

不同之处在于，spread 语法*将数组或对象*的元素*展开到单个变量*中，而 rest 语法*将单个变量*压缩并将其*放入数组或对象*中。

# 使用扩展语法的示例

## 1.在函数调用中传播变量

如下面的代码所示，`myArray`的元素作为函数调用中的参数传播。呼叫`printNum(…myArray, ‘x’);`相当于呼叫`printNum(4, 2, 9, ‘x’);`。这使得代码更加简洁，并允许您动态地更改函数调用的参数。

```
function printNum(a, b, c, d) {
  console.log(a, b, c, d); // 4, 2, 9, 'x'
}const myArray = [4, 2, 9];printNum(...myArray, 'x');
```

## 2.克隆或连接(连接)一个对象或数组

您可以使用 spread 语法合并两个对象或数组，如下所示。

但是，要知道克隆一个对象或数组只会很肤浅。这意味着你不能复制一个有嵌套数组的数组(例如`[[‘innerArray’, ‘name’], ‘another_element’]`)。这同样适用于对象—不能克隆具有嵌套内部对象的对象。

# 使用 Rest 语法的示例

## 1.在函数定义中接受不同数量的参数

rest 语法的主要用途是接受函数中不同数量的参数([可变函数](https://en.wikipedia.org/wiki/Variadic_function))。

如下图，`functionArgsArray`可以在函数中浓缩到单个数组，不需要事先知道参数的个数。在函数参数中只能使用一次 rest 语法，并且它必须是函数定义中的最后一个参数。

您还可以使用 spread 语法将数组扩展到各个参数中，然后用 rest 参数将它们压缩，如下所示:

## 2.使用析构创建新的数组/对象

结合[析构](https://javascript.plainenglish.io/the-top-6-javascript-operators-to-code-faster-and-cleaner-3cb13cc8c6f)，可以使用 rest 操作符创建一个新数组，如下所示。Rest 语法将`myArray` 的剩余元素压缩成一个新数组。这就是它得名的原因。

```
Split myArray into a local variable and another array (remainderArray) by using the rest syntax … .const myArray = [5, 7, 'ten', 25, 46];
const [one, , ...remainderArray] = myArray;
console.log(one, remainderArray); // 5, ['ten', 25, 46]
```

# 最后的想法

我希望你喜欢这篇短文。使用这两种语法可以使代码更加简洁。

感谢阅读。如果你喜欢这篇文章，可以考虑在这里[订阅](https://craftedwebpages.medium.com/membership)成为中级会员，以获得数以千计的其他文章。

# 学习资源

*   [传播语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)
*   [Rest 语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters) ( [参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters))