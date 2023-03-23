# 如何在 JavaScript 中将对象转换成数组，反之亦然

> 原文：<https://betterprogramming.pub/how-to-convert-objects-to-arrays-and-vice-versa-in-javascript-7776b5843948>

## 学习使用 Object.entries()和 Object.fromEntries()

![](img/30f746d7fe359a540853a677dfcebfda.png)

照片由 [Adri Tormo](https://unsplash.com/@tormius?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/arrows?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

如果您曾经使用过数据集，尤其是基于 web 的 API，那么我相信您一定遇到过 JavaScript Object Notation (JSON)。作为 XML 和 MongoDB 等基于文档的数据库中使用的符号的轻量级替代，JSON 无处不在。

本教程将演示如何使用`Object`类中的内置方法将一个对象转换成数组，反之亦然。在我们开始之前，让我们回顾一下 JSON，以确保我们清楚基本语法以及使用这两种转换技术时会发生什么。

# JSON 概述

使 JSON 如此灵活的两种数据类型是对象和数组。这些是保存多个值的复杂数据类型，与保存单个值的原始数据类型相反。

*   对象是一组无序的键值对。键是一个文本标识符，它在对象中必须是唯一的，值可以是原始数据值，也可以是复杂数据值。
*   数组是值的有序列表，不需要唯一，可以是各种数据类型。列表中的位置称为索引，从零开始，这意味着数组中的第一项的索引为`0`。

复杂的数据集可能会看到对象的数组或值是数组的对象(甚至是另一个对象)。每当你在一个数据结构中包含一个复杂的数据结构时，我们称之为嵌套。

好了，现在我们已经对 JSON 做了一分钟的介绍，接下来是解决方案！

# 将对象转换为数组

当将一个对象转换成数组时，我们将使用来自`Object`类的`.entries()`方法。这将把我们的对象转换成数组的数组。每个嵌套数组都是一个双值列表，其中第一项是键，第二项是值。

```
let character = {
   "first_name": "Locke",
   "last_name": "Cole",
   "job": "Treasure Hunter"
}let arr = Object.entries(character);
console.log(arr);
/*
[
  ["first_name","Locke"],
  ["last_name","Cole"],
  ["job","Treasure Hunter"]
]
*/
```

有嵌套对象和/或数组的复杂对象怎么办？找出答案的唯一方法就是测试！

```
let obj = {
  "name": "Rean Schwarzer",
  "occupations": [
    "Student (graduated)",
    "Resistance Leader",
    "New Class VII Instructor"
  ],
  "parents": {
    "father": "Teo",
    "mother": "Lucia"
  }
}let entries = Object.entries(obj);
console.log(entries);/*
[
  ["name", "Rean Schwarzer"],
  [
    "occupations", [
      "Student (graduated)",
      "Resistance Leader",
      "New Class VII Instructor"
    ]
  ],
  [
    "parents", {
      father: "Teo",
      mother: "Lucia"
    }
  ]
]
*/
```

请注意，嵌套的数组或对象只是成为适当的两项列表中的第二个值。该方法不会将嵌套对象递归转换为数组。仅转换顶层。

# 将数组转换为对象

以前，从对象到数组的转换是`Object`类内置方法的唯一方向。然而，从 ES2019/ES10 开始，引入了`.fromEntries()`方法，该方法将两项数组转换为一个对象——实际上是`.entries()`方法的逆过程。

要了解这一点，让我们从一个简单的例子开始。结果应该是可以预见的。

```
let scores = [
  ["Tim", 90],
  ["Joe", 88],
  ["Kelly", 95],
  ["Jesse", 77]
]let obj = Object.fromEntries(scores);
console.log(obj);
/*
{
  Jesse: 77,
  Joe: 88,
  Kelly: 95,
  Tim: 90
}
*/
```

好了，那么复杂数据呢？

```
let arr = [
  ["name", "Rean Schwarzer"],
  [
    "occupations", [
      "Student (graduated)",
      "Resistance Leader",
      "New Class VII Instructor"
    ]
  ],
  [
    "parents", {
      father: "Teo",
      mother: "Lucia"
    }
  ]
];let obj = Object.fromEntries(arr);
console.log(obj);/*
{
  name: "Rean Schwarzer",
  occupations: [
    "Student (graduated)",
    "Resistance Leader",
    "New Class VII Instructor"
  ],
  parents: {
    father: "Teo",
    mother: "Lucia"
  }
}
*/
```

与`entries()`方法一样，这只转换顶级数组。更深的数组和对象被传递。

如果我们的数组没有正确格式化会发生什么？如果数组有一个原始值或者一个对象呢？如果嵌套数组中的元素少于两个怎么办？如果一个嵌套的数组有两个以上的条目呢？

让我们尝试几个场景…

```
// Non-array value
let arr = [
  ["first_name","Jonathan"],
  "Arthur",
  ["last_name","Hsu"]
]let obj = Object.fromEntries(arr);
console.log(obj);
// "TypeError: Iterator value Arthur is not an entry object
```

当我们传入一个被描述为*非入口对象*时，该方法将返回一个 TypeError。

```
// Nested array item has less than two items
let arr = [
  ["first_name","Jonathan"],
  ["middle_name"],
  ["last_name","Hsu"],
  []
]let obj = Object.fromEntries(arr);
console.log(obj);
/*
{
  first_name: "Jonathan",
  last_name: "Hsu",
  middle_name: undefined,
  undefined: undefined
}
*/
```

如果一个嵌套数组是空的(没有条目)，那么得到的键值对是`undefined: undefined`，而只有一个条目的嵌套数组将被视为一个没有定义值的键值对。

```
// Nested array item has more than two items
let arr = [
  ["first_name","Jonathan"],
  ["middle_name","Arthur","Jones"],
  ["last_name","Hsu"]
]let obj = Object.fromEntries(arr);
console.log(obj);
/*
{
  first_name: "Jonathan",
  last_name: "Hsu",
  middle_name: "Arthur"
}
*/
```

当嵌套数组有两个以上的项时，多余的项将被忽略。

你以前用过`.entries()`把对象转换成数组吗？你看到自己用`.fromEntries()`转换到另一个方向了吗？在下面分享你的评论和问题吧！