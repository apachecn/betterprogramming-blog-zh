# JavaScript 中什么是对象析构？

> 原文：<https://betterprogramming.pub/destructuring-5909dd48dff0>

## 析构、扩展操作符，以及为什么应该使用它们

![](img/91fa0ac8cd154fb00c54dafa6193bc13.png)

[李·艾德伍德](https://unsplash.com/@lee2d2?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/combustion?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

在担心世界可能会被失控的神经网络摧毁之前，让我们想想完全不同的事情。*破坏*到底是什么？

[destructing-assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)语法是一个 JavaScript 表达式，允许我们将数组或对象属性中的值解包到不同的变量中。

# 数组析构与对象析构

JavaScript 中有两种类型的析构:

首先是*数组析构。* 数组析构允许我们将数组中的每一个值赋给它的变量。自从引入了 spread 操作符之后，将数组拆分成与用户相关的值就变得非常简单了。

让我们举一个数组析构的例子:

```
var **numbers** = [1,6,9,8,2];
[**number1**, **number2**, ...**otherNumbers**] = numbers;console.log(**number1**) // -> 1
console.log(**number2**) // -> 6
console.log(**otherNumbers**) // -> (3) [9, 8, 2]
```

其次是*对象析构。我发现这个特性比数组析构更强大。数组析构很简单，并且是基于位置的——正如你在上面的例子中看到的。*

但是对象析构更进一步。让我们通过一些例子来发现这一点。

```
var **dog** = {age: 6, name: 'Lizzy', paws: 4};
var {**name**, ...**other**} = **dog**;console.log(**name**) // -> Lizzy
console.log(**other**) // -> {age: 6, paws: 4}
```

让我简单解释一下这里发生了什么。`dog`对象被析构为一个变量`name`，它收集了`other`**(rest 参数)中的其余属性。它不是基于位置的，而是基于对象的属性。在这种情况下，`name`只是一个简写。这不是很棒吗？我们也可以写:**

```
var {'name': **nameProperty**, ...**other**} = **dog**;console.log(**nameProperty**) // -> Lizzy
```

# **用例**

**由于人类倾向于通过例子来学习，我将会给你一些析构的例子和一个非常相似的特性 spread 操作符。**

## **创建对象的副本**

**当你在 JavaScript 中将一个变量赋给一个没有析构的`other`对象，并且你改变了一个属性时，两个对象都会改变——如下所示:**

```
var **dog** = {name: 'Lizzy', age: 4};
var **otherDog** = **dog**; // no destructuring**otherDog**.age = 3;console.log(**otherDog**.age) // -> 3
console.log(**dog**.age) // -> 3 (I don't want this!)
```

**这并不总是你想要的。您可以使用 spread 运算符来防止这种情况发生，并从对象创建副本。下面的例子将从`dog`对象中获取每个属性(使用 spread 运算符),并且只改变`age`。**

```
var **dog** = {name: 'Lizzy', age: 4};
var **otherDog** = {...dog, age: 3}; // spread operator**otherDog**.age = 3;console.log(**otherDog**.age) // -> 3
console.log(**dog**.age) // -> 4 (Happy age, happy life)
```

**当使用 spread 操作符时，对象可以彼此独立地改变，我们实际上已经创建了一个新的对象。这是创建一个新对象的简单方法，只需做一些小的改动。**

**在一些框架中，比如 Angular，有很多用例。例如，我们可以用一种非常易读的方式在一个对象上发出一个新值。**

```
this.**articleUpdated**.emit({
  ...this.**article**,
  **status**: value
});
```

**spread 运算符不会创建对象的深层副本。**

**它从对象中提取的值仍然是引用，它不会创建新的实例。但是在极少数你需要的情况下，lodash 的克隆 Deep 可以做到这一点。**

## **函数参数析构**

**函数参数析构建立在我们在本文中讨论过的东西的基础上，对于处理大型对象非常有用。**

**您可以直接使用它，而不必创建一个新变量来创建属性的简写。用一个例子会更清楚:**

```
var **dog** = {age: 5, paws: 4};function ***printAge***({**age**}) {
  console.log(**age**); // instead of repeating dog.age everywhere
}printAge(**dog**);
```

**对于大型对象，这非常有用。**

# **结论**

**析构和 spread 操作符从 ES6 开始就有了，但是大多数程序员不使用它们。**

**有些语言，比如 Java，甚至没有这个特性。它们是对 JavaScript 语言的很好的补充，我总是很高兴看到它们发挥作用。**