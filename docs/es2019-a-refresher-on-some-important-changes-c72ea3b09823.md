# ES2019:一些重要变化的回顾

> 原文：<https://betterprogramming.pub/es2019-a-refresher-on-some-important-changes-c72ea3b09823>

## ES10 中更改或添加的内容

![](img/91af638e2487fc2152d6b94cb16a575d.png)

[Adrià Tormo](https://unsplash.com/@tormius?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

如果 ECMAScript 过去的发布时间表为将来的发布提供了任何线索，那么。Next 将于 2020 年 6 月左右发布。随着这个即将发布的版本，我认为重温一下 ES10 中的一些变化或添加会很好。

# Array.flat()

`Array.flat()`是一个数组方法，当应用该方法时，返回一个嵌套数组被展平的新数组。该方法接受一个选项来指定要展平的数组的深度。如果未指定金额，则默认为 1。如果指定的数量大于数组的深度，则数组会一直展平。`Array.flat()`也可以连续调用。在这里，我们可以看到`array.flat()`在使用:

# Array.flatMap()

`Array.flatMap()`做的和它听起来的一样:它获得了 ES6 map 方法的功能，并将其与前面提到的`Array.flat()`方法的功能相结合。实际上，`flatMap()`首先运行我们指定的 map 函数，然后返回一个包含指定函数输出的扁平数组。在操作中，`flatMap()`看起来像这样:

# Object.fromEntries()

此方法从键值对列表中创建一个对象:

```
let myEntries = new Map([[“name”, “zach”], [“coffee”, “yes”]]);console.log(Object.fromEntries(myEntries));// { name: ‘zach’, coffee: ‘yes’ }
```

# String.trimStart()和 String.trimEnd()

`String.trimStart()`和`String.trimEnd()`是新的方法，可以分别用来删除字符串开头和结尾的任何空白:

# 数组。Sort()稳定性

如果一个数组先排序，然后再排序，如果两种排序不冲突，那么得到的数组将保留这两种排序:

# Function.toString()

`.toString()`现在返回函数的精确文本，包括注释和空格:

# JSON 超集

这一改变允许段落分隔符(`(U+2029)`)和行分隔符(`(U+2028)`)位于字符串中。在旧版本中，它们会导致抛出语法异常。

```
// Not valid in before ES2019eval(‘“\u2028”’);
```

# 符号.描述()

返回符号对象的只读可选描述属性:

```
let sym = “foo”let symObject = Symbol(sym)console.log(symObject.description);//Returns ‘foo’
```

# 可选 Catch 绑定

在以前的规范中，catch 子句需要向其传递一个参数。ES10 改变了这一点。现在，catch 子句可以选择接受参数:

# 结论

我认为看到 JavaScript 不断获得新特性并随着每个新版本变得更加高效是令人兴奋的。现在，在成为标准的过程的第三阶段有几个提议。你可以在 GitHub 上了解最新的提议[。](https://github.com/tc39/proposals)