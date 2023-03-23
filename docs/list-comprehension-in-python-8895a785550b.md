# Python 中的列表理解

> 原文：<https://betterprogramming.pub/list-comprehension-in-python-8895a785550b>

## 从其他可重复项创建新列表

列表理解用于从其他可重复项创建新列表。

当列表综合返回列表时，它们由包含表达式的括号组成，对每个元素执行表达式，并使用 for 循环迭代每个元素。

这是基本语法:

```
new_list = [expression for_loop_one_or_more conditions]
```

例如，使用 For 循环查找一个数字的平方:

使用列表理解寻找方块:

这里，方括号表示输出是一个`list`。`n**2` 是对每个元素执行的表达式，`for n in numbers` 用于迭代每个元素。换句话说，**对** `**numbers**`中的每个元素执行 `**n**2**` **(表达式)。**

列表理解的一个更复杂的例子:

使用 for 循环从两个列表中找出公共数字。

在列表理解中使用条件:

使用列表理解从两个列表中找出公共数字:

从列表中返回作为元组不相等的数字:

给你。当我们返回一个元组列表时，元组必须在括号中以避免错误。在上面的例子中，带有`a`和`b`的元组将被打印，使得`a`和`b`不相同。

列表理解也可以用于迭代字符串，如下所示:

这个例子将每个字符串从`list_a`转换成更小的大小写。

像元组一样，列表理解可以用来产生列表的列表，如下所示:

这里我们使用`list_a` 来创建`square_cube_list`*，这是 Python 中的一个`list of list` ，包含来自`list_a`的数字的正方形和立方体*

*如果你喜欢我的文章，觉得它们很有用，请给我买杯咖啡。谢谢！*

*![](img/3542eddd0ca1f504cf9becec4747c334.png)*

***要获得我的新故事的更新，请关注我的** [**媒体**](https://medium.com/@happymishra66) **和** [**推特**](https://twitter.com/happyrupesh123)*

# ***其他文章:***

1.  *[react js 初学者指南](https://medium.com/free-code-camp/a-beginners-guide-to-getting-started-with-react-c7f34354279e)*
2.  *[JavaScript 之旅:从下载脚本到执行](https://medium.com/@amasand23/the-journey-of-javascript-from-downloading-scripts-to-execution-part-i-967cc1112b4f)*
3.  *[为什么渐进式网络应用很棒，如何构建一个](https://medium.com/free-code-camp/benefits-of-progressive-web-applications-pwas-and-how-to-build-one-a763e6424717)*
4.  *[让我们一劳永逸地得到这个‘这个’](https://hackernoon.com/lets-get-this-this-once-and-for-all-f59d76438d34)*
5.  *[服务人员](https://hackernoon.com/service-workers-62a7b14aa63a)*
6.  *[服务工作者实现](https://hackernoon.com/building-pokemon-app-to-evaluate-the-power-of-berries-service-worker-176d7c4e70e3)*
7.  *[JavaScript 中的执行上下文](https://medium.com/@happymishra66/execution-context-in-javascript-319dd72e8e2c)*
8.  *[ReactJS 中的虚拟 DOM](https://medium.com/@happymishra66/virtual-dom-in-reactjs-43a3fdb1d130)*
9.  *[JavaScript 中的原型](https://medium.com/@happymishra66/prototypes-in-javascript-5bba2990e04b)*
10.  *[JavaScript 中的‘this’](https://medium.com/@happymishra66/this-in-javascript-8e8d4cd3930)*
11.  *[JavaScript 中的 object . create](https://medium.com/@happymishra66/object-create-in-javascript-fa8674df6ed2)*
12.  *JavaScript 中的继承*
13.  *[用 JavaScript 创建对象](https://medium.com/@happymishra66/create-objects-in-javascript-10924cfa9fc7)*
14.  *[JavaScript 中的对象](https://medium.com/@happymishra66/objects-in-javascript-2980a15e9e71)*
15.  *[Python 中的 Zip](https://medium.com/@happymishra66/zip-in-python-48cb4f70d013)*
16.  *[Python 中的装饰者](https://medium.com/@happymishra66/decorators-in-python-8fd0dce93c08)*
17.  *[在 Python 中连接两个列表](https://medium.com/@happymishra66/concatenating-two-lists-in-python-3cf9051da17f)*
18.  *[Python 中的 lambda、map 和 filter](https://medium.com/@happymishra66/lambda-map-and-filter-in-python-4935f248593)*
19.  *[用 Python 列出理解](https://medium.com/@happymishra66/list-comprehension-in-python-8895a785550b)*