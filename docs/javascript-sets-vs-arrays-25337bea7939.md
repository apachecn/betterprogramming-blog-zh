# JavaScript:集合与数组

> 原文：<https://betterprogramming.pub/javascript-sets-vs-arrays-25337bea7939>

## 介绍集合，您不知道自己需要的 ES6 数据类型

![](img/c4a56fe2175498b501953b703550f2d5.png)

照片由[马腾·范登·霍维尔](https://unsplash.com/@mvdheuvel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

`Set`对象类型是在 2015 ECMAScript 规范中引入的，并准备好在 [Node.js](https://nodejs.org/) 和大多数浏览器中使用。

集合很像数组，但又有点不同。本文探讨了这些差异，并解释了什么时候应该使用其中一个而不是另一个。让我们来看看。

# Sets，街区的新成员

集合是 ES6 中可用的特殊对象类型。您可以像这样创建一个空集:

```
const characters = new Set()
```

或者，您可以将一个 *iterable* 传递给集合的构造函数来填充它。iterable 只是可以循环的东西，比如数组或字符串。

```
const characters = new Set(['Rod', 'Todd', 'Sherri', 'Terri'])
```

# 阵列，可靠的主力

数组是大多数 JavaScript 应用程序的主要构件，不管是旧的还是新的。如果你以前写过 JavaScript，你可能已经很熟悉它们了。您可以像这样创建一个数组:

```
const characters = ['Rod', 'Todd', 'Sherri', 'Terri']
```

# 那么这有什么意义呢？

这两种数据类型相似，但做的事情略有不同。一个`set`被设计用来表示一个*唯一的*项的集合，而一个数组则更通用一些。

可以用`Set`来表示的一个很好的例子是一个大学生在一个学期中所学的课程。他们可以选修一门或多门课程，但一学期内同一门课程不能选修超过一次。

```
const courses = new Set(['English', 'Science', 'Lego Robotics'])
```

另一方面，口袋妖怪卡片的集合对于`Set`来说不是一个好的用例，因为它可能包含重复的卡片。在这种情况下，数组是表示数据的更好方式。

```
const cards = [
  'Machop',
  'Diglett',
  'Charmeleon',
  'Machop',
  'Squirtle'
]
```

副本可以被传递到一个`Set`，但是它们不会被保留。将以下代码复制到您的浏览器控制台中，亲自查看:

```
new Set([
  'Machop',
  'Diglett',
  'Charmeleon',
  'Machop',
  'Squirtle'
])// Set(4) {"Machop", "Diglett", "Charmeleon", "Squirtle"}
```

传入集合的数组包含两个 Machops，但集合只保留一个副本。这种行为很微妙，但是非常有用。

# 这个怎么用？

假设您正在开发一个博客，并希望创建一个允许访问者搜索与一个或多个类别相匹配的帖子的功能。每个类别只能应用一次。

如果您使用数组来表示活动类别的列表，您需要注意避免重复。这可以通过检查列表是否已经包含正在添加的类别来完成。

可以使用`indexOf`或`includes`方法来做到这一点:

```
// If our list does not include the category
if (!list.includes(category)) {
  // Then add the new category to the list
  list.push(category)
}
```

我曾经发现自己一直在编写这种代码，但是可以使用 Sets 来自动处理这个问题。您可以简单地使用`add`方法，该集合将始终保持唯一。

```
// Just add the category to the list
// No need to perform any checks in advance!
list.add(category)
```

# 将集合转换回数组

我们已经看到，通过将数组传递给集合的构造函数，可以将数组转换为集合，但是如何将集合转换为数组呢？

一种选择是静态调用数组`from`方法:

```
const set = new Set(['Casablanca', 'The Wizard of Oz', 'Jaws'])
const arr = Array.from(set)
console.log(arr)// (3) ["Casablanca", "The Wizard of Oz", "Jaws"]
```

ES6 扩展运算符是另一个选项:

```
const set = new Set(['Casablanca', 'The Wizard of Oz', 'Jaws'])
const arr = [...set]
console.log(arr)// (3) ["Casablanca", "The Wizard of Oz", "Jaws"]
```

集合不支持函数式编程方法，如 map、filter 和 reduce，因此通常将它们转换为数组进行处理会很方便。

# 使用集合从数组中删除重复项

即使您更喜欢将数据保存在数组中，集合仍然很有帮助。从数组中删除重复项的一个简便方法是将其转换为集合，然后再转换回来。

```
const cards = [
  'Machop',
  'Diglett',
  'Charmeleon',
  'Machop',
  'Squirtle'
]
const uniqueCards = [...new Set(cards)]
console.log(uniqueCards)// (4) ["Machop", "Diglett", "Charmeleon", "Squirtle"]
```

# 集合如何知道哪些值是唯一的？

到目前为止，我们已经看到了集合如何只保存唯一的值，但是唯一的值是如何确定的呢？让我们试一试，找出答案。

首先，让我们把值`3`加到一个集合中两次:

```
new Set([1, 2, 3, 4, 3])// Set(4) {1, 2, 3, 4}
```

第二个 3 消失了。这与我们目前所学的一致，但是如果最后的 3 作为一个字符串添加呢？

```
new Set([1, 2, 3, 4, '3'])// Set(5) {1, 2, 3, 4, "3"}
```

有意思。布景师认为`3`与`'3'`不同。如果我们给一个集合添加匹配的数组会怎么样？

```
new Set([['Jesse Pinkman'], ['Jesse Pinkman']])// Set(4) {['Jesse Pinkman'], ['Jesse Pinkman']}
```

在这种情况下，集合保留了两个具有相同内容的数组…那么对象呢？

```
new Set([{name: 'Ron Burgundy'}, {name: 'Ron Burgundy'}])// Set(2) {{name: 'Ron Burgundy'}, {name: 'Ron Burgundy'}}
```

具有匹配键和值的对象文字也被认为是不同的…

# 这一切怎么解释？

集合使用*严格相等* ( `===`)来确定哪些值是唯一的。这解释了为什么集合同时维护了`3`(数字)和`'3'`(字符串)的副本。

它还解释了为什么具有相同内容的数组和对象文字被认为是唯一的。JavaScript 通过引用而不是内容来比较对象，数组只是一种特殊的对象。

# 摘要

集合为 JavaScript 开发人员提供了一种新的表示数据的方式。虽然数组仍然是 JavaScript 应用程序的通用工具，但是集合旨在表示唯一的值集合。

集合和数组之间的转换很容易。您可以使用集合来确保您的数据保持唯一，然后将其转换为数组，以利用函数方法，如 map、filter 和 reduce。

集合使用*严格相等* 来比较值并确定什么是唯一的。因为 JavaScript 通过引用来比较对象，所以数组和对象文字可以被认为是唯一的，即使它们包含相同的内容。

本文主要关注集合的概念方面。您现在应该很清楚什么时候应该使用集合，什么时候最好使用数组。

要了解如何将 set 付诸行动的更多信息，请查看 MDN 的文档。