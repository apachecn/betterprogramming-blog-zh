# 充分利用 JavaScript 中析构的 5 种方法来编写更简洁的代码

> 原文：<https://betterprogramming.pub/5-ways-to-make-the-most-of-destructuring-in-javascript-to-write-cleaner-code-d674c00da9c7>

## 析构是 ES6 最激动人心的特性之一

![](img/7945577335efc45814beb858c1ccecd1.png)

照片由[劳伦提乌·约尔达切](https://unsplash.com/@jordachelr?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

析构使我的代码更短更简洁。尤其是涉及到 Redux 的时候，我不得不处理不变性的概念，有了 destructuring 的帮助就更容易了。

在本文中，我将向您展示析构的五个用例。让我们开始吧。

# 1.从对象获取属性值

析构一个对象是一种常见的情况。请看下面的例子:

在上面的例子中，我们创建了一个变量名，并将`person.name`的值赋给它。所以变量名的值是`Amy`。

您可能想知道左边的变量名是否应该与对象的属性名相同。答案是肯定的。

```
let { name, age } = person; // the same as: let { age, name } = person;console.log(name); // Amyconsole.log(age); // 28
```

那么如果你想使用不同的变量名呢？动态析构为你而生。让我们看看它是如何工作的:

最佳做法是，如果属性不存在，我们将添加默认值:

```
let { 
  [‘name’]: myName = ‘default name’, 
  [‘age’]: myAge = 0 
} = person;
```

明白了吗？

# 2.访问数组中的项目

假设你想访问一个数组的一个特定项，并把它的值赋给一个变量。如果该项不存在，则分配一个默认值。对于这种任务，您曾经检查过数组的长度。例如，您想要访问第四项:

通过使用析构:

```
let fruits = [‘banana’, ‘apple’, ‘orange’];let [ , , , selectedFruit = ‘mango’] = fruits;console.log(selectedFruit); // mango
```

你可能会注意到在`selectedFruit`变量之前有三个逗号。他们拆分了第一个、第二个和第三个元素，但是我在这里没有使用任何变量，所以它们被忽略了。你在这里关心的只有`selectedFruit`。它被赋给数组的第四个元素(如果有的话)或者默认值`mango`。

# 3.交换东西

要以传统方式交换两个变量的值，可以使用一个临时变量:

多亏了析构，你可以只用一行代码就完成同样的任务:

你看，你不需要一个临时变量。

为了更有趣，你甚至可以交换两个以上的变量:

# 4.处理不可变操作

你知道不变性的概念吗？它指的是创建后不能修改的对象。乍一看你可能会觉得很难理解，但是鉴于[提供的所有好处](https://hackernoon.com/5-benefits-of-immutable-objects-worth-considering-for-your-next-project-f98e7e85b6ac)，这值得一试。

假设您有一个`fruits`数组，并且您想要修改第一项的值。但是因为不变性规则，你不能直接在`fruits`数组上改变它。您必须创建一个新的数组:

```
let fruits = [‘mango’, ‘banana’, ‘apple’];let copiedFruits = fruits;copiedFruits[0] = ‘orange’;
```

`copiedFruits[0]`现在是橙色，但问题是`fruits[0]`也是`orange` 因为引用类型。

那你是做什么的？正确的代码应该是:

```
let fruits = [‘mango’, ‘banana’, ‘apple’];let [...copiedFruits] = fruits;copiedFruits[0] = ‘orange’;console.log(copiedFruits[0]); // orangeconsole.log(fruits[0]); // mango
```

# 5.向函数传递参数

用参数作为对象定义函数的老方法是:

新的更清晰的方法是:

# 结论

在本文中，我向您展示了在 JavaScript 中使用析构的五种方法。我错过了什么吗？如果你能填补空白就太好了。