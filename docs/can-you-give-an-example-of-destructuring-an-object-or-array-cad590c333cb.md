# JavaScript 访谈:你能举一个析构对象或数组的例子吗？

> 原文：<https://betterprogramming.pub/can-you-give-an-example-of-destructuring-an-object-or-array-cad590c333cb>

## 一些快速简单的代码示例

![](img/b9067689d54c77c1b6de96b915e99c22.png)

照片由[巴雷特沃德](https://unsplash.com/@barrettward?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/disassemble?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 在我们解构之前，什么是结构？

在计算机科学中，我们经常构建和使用的结构类型被称为*数据结构*。

数据结构是为存储和组织数据而创建的存储格式，通常优先考虑数据的可访问性和定制。换句话说:

> 更准确地说，数据结构是数据值、数据值之间的关系以及可应用于数据的功能或操作的集合——[维基百科](https://en.wikipedia.org/wiki/Data_structure)

JavaScript 有两种内置的数据结构:数组和对象。

# “解构”一个数据结构意味着什么？

> "**析构赋值**语法是一个 JavaScript 表达式，它可以将数组中的值或对象中的属性解包到不同的变量中。"— MDN

这就是 MDN 的定义。就个人而言，这个定义最有帮助的部分是单词 *unpack* ，在我们前进的时候请记住这个术语。

让我们用一个析构数组的代码示例来深入分析一下:

在第一行中，我们声明了一个新变量`studentsArr`，并给它分配了一个学生姓名数组的值。第二行是我们析构的地方。

在第 2 行中，我们声明了三个变量；`first`、`second`和`third`。通过在左边声明它们，我们开始了析构。通过这样编写语法，在第 2 行中，我们对 JavaScript 说:

“我要你解构，或者说“解开”右边的数组。一旦你解构或“解包”了数组，我要你把它的值赋给左边的变量。

在这样做的时候，我们在一行代码中声明了三个新变量，并给它们分配了三个不同的值。如果不进行析构，这需要三行代码:

# `…’`其余参数

如果我们希望将数组的前几个值赋给特定的变量，但我们还希望捕获数组的其余值，我们可以使用 rest 语法:

剩余的数组值被分配给`rest`。您可以使用任何名称来分配剩余的值，只要它前面有`…` rest 语法。

# 解构对象

析构对于从对象中解包数据特别有用。例如，假设我们有一个充满电影对象的电影应用程序，如下所示:

如果我们想从这个对象中提取单个值，我们可以这样做:

```
const title = movie.title
const country = movie.country
```

或者，使用析构语法，我们可以这样做:

```
const {title, country} = movie
console.log(title, country) // *'Star Wars', 'United States'*
```

JavaScript 将获取左侧声明的变量，并将它们与所选析构对象中的相应值进行匹配！

# 嵌套解构

假设我们有嵌套的数据，我们希望使用析构来访问这些数据。这听起来可能很复杂，但实际上很简单！

请注意，与通常的链接不同，这种语法非常简单。

# 默认值

析构也可以用来分配默认值。以我们的电影为例，偶尔会有不公开预算的电影。如果我们想在析构中考虑到这一点，我们可以指定一个缺省值:

# 这就是所有的人！

我希望我能够以一种容易理解的方式为你“解开”这个概念。只要稍加练习，我保证这个概念会很快被接受。

感谢阅读！