# 如何以更干净、不可变的方式对对象进行更改

> 原文：<https://betterprogramming.pub/how-to-make-changes-to-objects-in-a-cleaner-immutable-way-3f8a2691e1f0>

## 在不更改数据的情况下添加和删除属性

![](img/7f2fad0bf0f5105aae2863526ad9fdc0.png)

由[尤里察·科莱蒂](https://unsplash.com/@juricakoletic?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/transformation?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

本文将为您提供一些关于如何在 JavaScript 程序中使用 ES6 语法以更简洁的方式改变状态的技巧。

我假设您对 JavaScript 及其 ES6 语法有基本的了解，或者有一点使用 JavaScript 的经验。在您的应用程序*中使状态不可变总是一个好主意。*不可变数据* 表示不变的数据。JavaScript 中的字符串就是一个例子。一旦你创建了一个字符串，它永远不会改变。*

不可变的数据更简单。通过这样做，您可以确保您的数据不会在代码库中的其他地方被更改。这是件好事。你更有信心不会发生意想不到的事情。

# **什么是状态？**

是你的程序记住的事情，还是你的程序跟踪的随时间发生的事情。以为例，在一个待办事项列表中，app 需要记住的事情有:什么时候添加新的待办事项，什么时候更新待办事项，什么时候从列表中删除。跟踪这些变化就是所谓的在应用程序中维护状态。

那么，我们如何在不改变数据的情况下以不可变的方式处理数据呢？每个程序都需要一种跟踪这些变化的方法，如果你不能修改状态，那么我们如何在程序中维护状态呢？

我会告诉你怎么做。

下面是一个具有两个属性的对象:

```
const person = {
     id: 1,
     name: 'Adjoa'
};
console.log(person); // returns { id:1, name: 'Adjoa'};
```

现在，我该如何获取`person`中的所有属性并添加另一个属性，比如性别，而不改变它呢？让我给你介绍一下对象传播操作符。

# **对象传播算子**

对象扩展操作符允许我们将一个对象的属性扩展或展开到一个新对象中。它将所有属性从一个对象复制到另一个对象。这在很多方面都很有用！

***举例:***

```
const newPerson = {
      ...person,
      gender: 'female'
};console.log(newPerson); //{ id:1, name: 'Adjoa', gender: 'female'};// Includes all properties from the person object.
```

我们不需要手动创建一个新对象，将第一个对象中的所有属性写入第二个对象，然后添加新属性。这种方式超级简单容易！

你知道同样的操作符也可以用来覆盖对象的属性吗？让我展示给你看。

示例:

```
const updatedPerson = {
      ...newPerson,
      gender: 'male'
};

console.log(updatedPerson); // returns { id: 1, name: 'Adjoa', gender: 'male' };
```

性别的价值从男性变成了女性。

我们已经看到了如何通过添加新属性而不修改其内容来更新对象。有人可能会问，那么我如何以不可变的方式移除元素呢？毁灭和休息来了。

# 用 destructing+Rest 从对象中移除字段

## 什么是解构？

析构只是允许我们给变量赋值。

示例:

```
// using our earlier exampleconst { name } = updatedPerson;console.log(name); // returns 'Adjoa';
```

从上面的例子中可以看出，我们对变量使用了与对`updatedPerson`属性相同的名称。但是您也可以用不同于对象属性的名称替换变量。

示例:

```
const { name: title } = updatePerson;console.log(title); // returns 'Adjoa';
```

## 什么是休息？

Rest 允许我们将对象的所有剩余参数或属性解包到一个变量中。

示例:

```
// using the earlier person object
const {id, ...rest} = person;console.log(id); // 1
console.log(rest); // {name: 'Adjoa')
```

## 如何使用 destructing+rest 移除字段:

```
const { id, ...updatedPersonWithoutId } = updatedPerson;
```

上面发生了什么？我们把*`id`*抽出来变成它自己的常数，然后把`updatedPerson`中剩余的任何属性都收集到`updatedPersonWithoutId` 常数中。换句话说，我们正在将除了 T5 之外的 T4 的所有属性复制到 T6 常量中。**

```
 **console.log(updatedPersonWithoutId); // returns { name: 'Adjoa', gender: 'male' }
//id was not included in the object returned.console.log(updatedPerson); // { id:1, name: 'Adjoa', gender: 'male' }// original object hasn't mutated but retains its old properties.**
```

**因此，我们以一种更干净、不可变的方式有效地添加和删除了对象的属性！**

**接下来，我将写在 JavaScript 中修改数组。**

**感谢您的阅读。如果你觉得这篇文章有帮助，请留下评论。**