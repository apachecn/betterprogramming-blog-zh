# JavaScript 异步迭代

> 原文：<https://betterprogramming.pub/async-iteration-with-javascript-420b6d67c372>

## JavaScript 中的异步迭代器和生成器

![](img/6aa6a6f25ae1ba2c3f00a772c9c4166d.png)

照片由[亚历山德罗·德桑蒂斯](https://unsplash.com/@alessandrodesantis?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

异步代码是 JavaScript 非常重要的一部分。我们需要编写大量的异步代码，因为 JavaScript 是单线程语言。如果我们一行一行地运行所有的东西，那么需要更长时间运行的代码将会阻碍程序，导致它冻结。

为了容易地编写异步代码，JavaScript 有承诺，这些承诺是可链接的，并且可以按顺序运行，无需嵌套。

ES2017 引入了用于链接承诺的`async`和`await`语法，这使得一切变得更容易。然而，仍然没有办法按顺序运行承诺，按顺序迭代它们。这意味着在一个序列中运行大量的承诺仍然是一个无解的问题。

幸运的是，在 ES2018 中，我们终于有了一个可以与 ES2017 中引入的`async`函数一起使用的`for-await-of`循环。它适用于任何可迭代对象，比如同步`for...of`循环。这意味着我们可以用它迭代对象，比如映射、集合、节点列表和`arguments`对象。

此外，它可以使用`Symbol.iterator`方法处理任何对象，该方法返回一个生成器让我们进行迭代。

# for-await-of 循环

我们可以如下使用`for-await-of`循环:

[https://gist . github . com/better programming/a6c 34421 c 67 BD 84 ab 894861 e 3256 e 449 . js](https://gist.github.com/BetterProgramming/a6c34421c67bd84ab894861e3256e449.js)

在上面的例子中，我们添加了十个承诺，在`for`循环中定义它们，然后将它们推到`promises`数组中。然后，我们通过定义一个`async`函数来运行承诺，然后使用`for-await-of`循环来遍历每个承诺，一次运行一个。

`for-await-of`循环中的`await`将从承诺中获取解析值，而`console.log`将记录解析值。

和其他`async`函数一样，只能返回承诺。

`for-await-of`循环的语法与任何其他`for...of`循环相同。`let p`是变量声明，`promises`是我们正在迭代的`promises`数组。

上面的代码像`Promise.all`一样工作，代码是并行运行的，所以一秒钟后我们看到一到十。

# 异步迭代

或者，如果我们用`Symbol.asyncIterator`方法定义一个对象，我们可以定义我们自己的异步可迭代对象。这是一个特殊的方法，它返回一个生成器，让我们用`for-await-of`循环遍历它。

例如，我们可以写:

上面的代码将返回一个生成器，它在`for-await-of`循环运行时返回一个新的承诺。

在`Symbol.asyncIterator`方法中，我们有一个`next`函数，如果我们想创建一个异步可迭代对象，就需要这个函数。此外，我们有`i`属性来跟踪我们迭代的内容。

在我们返回的承诺中，我们有一个带有回调函数的`setTimeout`，它解析为一个带有我们想要解析的`value`和属性`done`的对象。如果迭代器应该继续为`for-await-of`循环返回新值，则`done`属性为`false`，否则为`true`。

![](img/abc6a1f91bc0be9c428553683f4d86dd.png)

由[杰森·黑眼](https://unsplash.com/@jeisblack?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 异步发电机

我们可以用`yield`关键字来缩短代码，使生成器函数返回一个生成器。生成器是另一种可迭代对象，可以与`for-await-of`循环一起使用。

例如，我们可以写:

关键字`function*`表示我们的函数只返回一个生成器。`async`表示发电机异步运行。在`asyncGenerator`函数中，我们有`yield`关键字。当我们用`for-await-of`循环遍历生成器时，`yield`关键字从生成器中获取下一个项目。

`yield`关键字只适用于顶层代码。它在回调函数中不起作用。

如果我们运行上面的代码，我们应该得到一到十个输出。

如果我们这样写:

```
async function* asyncGenerator() {
  for (let i = 1; i <= 10; i++) {
    setTimeout(() => {
      yield i;
    }, 1000);
  }
}
```

我们会得到一个`SyntaxError`。

如果我们定义一个被设置为生成器函数的`Symbol.asyncIterator`方法，那么一个对象就可以成为可迭代的。

例如，我们可以写:

然后我们在`console.log`输出中记录了一到十个。

它像我们上面定义的`asyncIterable`对象一样工作，除了代码更短。同样，我们不能在回调中使用`yield`关键字，所以我们必须像上面那样写。

最后，我们可以编写以下代码来直接循环遍历承诺:

`await`关键字实际上等待每个承诺的解析，直到运行下一个，所以当我们运行代码时，一秒钟后就会显示每个数字。

异步迭代解决了顺序运行许多承诺的承诺。随着`for-await-of`循环和异步迭代器和生成器的引入，该功能在 ES2018 或更高版本中可用。有了这些，迭代异步代码的问题就解决了。