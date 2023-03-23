# 取代旧方法的 ES6 字符串和数组方法

> 原文：<https://betterprogramming.pub/es6-string-and-array-methods-that-replace-older-methods-b0224766d255>

## 请不要再使用 indexOf

![](img/d8faea897e7272c35b61ff02dd9c2460.png)

苏珊·莫尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

JavaScript 是世界上最流行的编程语言之一。

这是因为，自 2015 年以来，JavaScript 增加了许多伟大的功能，旨在取代早期版本的旧功能。

在本文中，我们将研究取代早期方法的 ES6 字符串和数组方法。

# 从索引 Of 到开始于

ES6 引入了字符串的`startsWith`实例方法，可以用来替代`indexOf`方法。

例如，给定以下字符串:

```
const str = 'foobar';
```

然后我们可以替换:

```
str.indexOf('foo') === 0
```

使用:

```
str.startsWith('foo')
```

如果我们想检查字符串`str`是否以`'foo'`开始。

`startsWith`比`indexOf`短得多，我们也不用按索引查。

# 从索引到结束

`endsWith`方法比`startsWith`方法更有用，因为没有简单的方法来检查一个字符串是否以给定的后缀结尾。

没有`endsWith`，我们不得不写一个如下的函数:

```
const endsWith = (str, suffix) => {
  const index = str.indexOf(suffix);
  return index >= 0 &&
    index === str.length - suffix.length;
}
```

我们必须通过检查索引是否为 0 或更大来检查后缀是否位于带有`str.indexOf(suffix)`的字符串中。然后我们必须通过检查`index`是否在`str.length — suffix.length`中来检查`index`是否位于字符串的末端。

然后给出以下字符串:

```
const str = 'foobar';
```

我们会写:

```
endsWith(str, 'bar')
```

对于`endsWith`，我们只需写:

```
str.endsWith('bar')
```

这很方便，我们不必定义自己的函数来检查一个字符串是否以给定的后缀字符串结尾。

# 从索引到包含

ES6 引入的`includes`方法检查一个字符串是否位于给定的字符串中。

因此，给定以下字符串:

```
const str = 'foobarbaz';
```

我们可以通过编写以下代码来检查子字符串是否存在于字符串中:

```
str.indexOf('bar') >= 0
```

对于 ES6，我们可以改为编写:

```
str.includes('bar')
```

这个比`indexOf`短。

# 从 Array.prototype.indexOf 到 Array.prototype.findIndex

ES6 引入了`findIndex`数组实例方法。它对查找任何东西的索引都很有用。它需要一个回调函数来返回我们想要搜索的内容的条件。

`indexOf`不能很好地处理对象，因为它只找到与给定对象引用相同的对象。

例如，`indexOf`非常适用于像数字这样的原始值:

```
arr.indexOf(1)
```

它比使用`findIndex`短，如下所示:

```
arr.findIndex(a => a === 1)
```

它们都返回索引 1，也就是 0。

然而，当我们搜索一个对象的索引时，`indexOf`是行不通的。给定以下数组:

如果我们写:

```
arr.indexOf({
  name: 'Alex'
})
```

那么我们将得到-1，因为`{ name: ‘Alex’ }`与数组中的引用不同。

因此，`findIndex`在这里将是有用的:

```
arr.findIndex(a => a.name === 'Alex')
```

这将返回 1，这是正确的索引，因为我们返回一个比较`name`属性的值的条件。

# 从 Array.prototype.slice()到 Array.from()或 Spread 运算符

在大多数情况下，`slice`和`from`实例都可以用 spread 操作符替换。

例如，我们不再需要`slice`将类似数组的对象转换成数组。

如果我们想把`arguments`对象转换成一个数组，而不是写:

```
const arr = Array.prototype.slice.call(arguments);
```

我们写道:

```
const arr = [...arguments];
```

它要短得多，我们不必调用`call`并将`arguments`作为`this`值传入。

在这两个例子中，当我们有给定的函数时，我们将`[1, 2, 3]`赋值给`arr`:

```
function foo() {
  const arr = [...arguments]; 
}
```

并写道:

```
foo(1, 2, 3);
```

我们也可以用 spread 操作符来替换`Array.from`方法的大多数用例。

只要我们传递给`Array.from`的对象是一个数组或另一种可迭代对象，我们就可以使用 spread 操作符。

因此，与其写:

```
function foo() {
  const arr = Array.from(arguments); 
}
```

我们可以写:

```
function foo() {
  const arr = [...arguments]; 
}
```

`Array.from`唯一有用的情况是将整数作为属性标识符的对象和`length`属性转换成数组。

例如，我们可以写:

要转换:

至`[“foo”, “bar”, “baz”]`。

我们不能使用 spread 操作符将它转换成数组，因为它不是一个可迭代的对象。

# 结论

除了一些边缘情况，新的 ES6 字符串和数组函数是很好的补充，可以用来在许多地方缩短代码。

字符串实例方法节省了最多的代码。更短、自我解释的代码对每个人来说都更容易理解。