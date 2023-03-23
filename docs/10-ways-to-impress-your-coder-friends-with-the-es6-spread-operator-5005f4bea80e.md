# 用 ES6 Spread Operator 给你的(编码)朋友留下深刻印象的 10 种方法

> 原文：<https://betterprogramming.pub/10-ways-to-impress-your-coder-friends-with-the-es6-spread-operator-5005f4bea80e>

## 让我们想象一下…

![](img/d343cbe83b052769d3a9393368cfc457.png)

Artem Sapegin 在 [Unsplash](https://unsplash.com/photos/b18TRXc8UPQ) 上拍摄的照片

学习 React 凸显了我最近获得的 JavaScript 知识中的一个巨大缺口:嵌套对象和数组的非破坏性变异。这在使用 React 状态时变得特别重要，因为您需要在不改变先前状态的情况下从大型对象中添加、更新和删除元素。

当我第一次开始阅读 spread 操作符时，在使用 React state 的上下文中，我很快意识到这种看起来奇怪的语法有多种用途。但是首先…

# 什么是扩展操作符？

扩展操作符或扩展“语法”是在 ES6 中引入 Javascript 的。官方 [Mozilla Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) 对 spread 语法的定义如下:

> **Spread syntax** 允许在需要零个或多个参数(对于函数调用)或元素(对于数组文本)的地方扩展一个可迭代对象，例如一个数组表达式或字符串，或者在需要零个或多个键值对(对于对象文本)的地方扩展一个对象表达式。

换句话说，扩展操作符是**三个点(…)** ，后面跟一个‘可迭代’的数据类型，比如数组、对象，甚至是字符串。我喜欢把这理解为在一个函数或一个新值中“分散”值。

嗯嗯…..

## 太酷了…我该怎么用？

很高兴你问了！幸运的是，我在网上找到了这三个小圆点的 10 个我最喜欢的用法。准备好大吃一惊吧。

## 1.制作数组的副本

```
const fruit = ["Apple", "Banana", "Pear"]const moreFruit = [...fruit] console.log(moreFruit) // ["Apple", "Banana", "Pear"]
```

## 2.串联两个(或更多)数组

而不改变任何一个现有的数组！

```
const dogs = ["Biscuit", "Buddy", "Duke"]const cats = ["Lola", "Mia"]const pets = [...dogs, ...cats] console.log(pets) // ["Biscuit", "Buddy", "Duke", "Lola", "Bailey", "Mia"]
```

## 3.向函数传递参数

```
const sum = (a, b) => { return a + b; }const numbers = [2, 3];console.log(sum(...numbers)); // 5
```

## 4.向 Javascript 数学对象方法传递参数

```
const numbers = [2, 5, 7, 80, 6]Math.max(numbers) // NaNMath.max(...numbers) // 80
```

## 5.基本对象析构

```
const user = {
   id: 101,
   email: "ilovepuppies@hotmail.com"
}const {id, email} = {...user}console.log(id) // 101console.log(email) // "ilovepuppies@hotmail.com"
```

## 6.稍微不太基本的对象析构

```
const user = {
   id: 101,
   email: 'ilovepuppies@hotmail.com',
   addressLine1: "42 Wallaby Way",
   addressLine2: "Sydney",
   addressLine3: "Australia"
}const {id, email, ...fullAddress} = {...user}console.log(id) // 101console.log(email) // "ilovepuppies@hotmail.com"console.log(fullAddress) // {address_line_1: "42 Wallaby Way", address_line_2: "Sydney", address_line_3: "Australia"
```

## **7。拆分字符串**

```
const greeting = "Hello";const greetingChars = [...greeting]console.log(greetingChars) // ["H", "e", "l", "l", "o"]
```

## 8.控制台将数组记录为完整的句子

```
const sentence = ['This', 'is', 'a', 'sentence'];console.log(...sentence) // This is a sentence
```

## 9.向现有对象添加新的键值对

```
let meal = {
   starter: "soup",
   main: "pasta"
}meal = {...meal, dessert: "chocolate cake"}console.log(meal) // {starter: "soup", main: "pasta", dessert: "chocolate cake"}
```

最后…

## 10.无损更新反应状态

```
state = {
   pizzas: [
      { 
         type: "cheese",
         price: 10
      },
      { 
         type: "pepperoni",
         price: 12
      }
   ]
}this.setState({
   pizzas: [
      ...this.state.pizzas,
      {type: "veggie deluxe", price: 15}
   ]
})console.log(this.state) // {
   pizzas: [
      { 
         type: "cheese",
         price: 10
      },
      { 
         type: "pepperoni",
         price: 12
      },
      {
         type: "veggie deluxe", 
         price: 15
      }
   ]
}
```

感谢阅读！我希望您对此感兴趣，并考虑在您的 Javascript 词汇表中添加 spread 运算符。另外，非常感谢以下资源，它们帮助我发现了 spread operator 的强大功能:

[](https://davidwalsh.name/spread-operator) [## Spread 运算符的 6 大用途

### 感谢 ES6 和 Babel 之类的东西，编写 JavaScript 已经变得非常动态，从新的语言语法到…

davidwalsh.name](https://davidwalsh.name/spread-operator) [](https://lucybain.com/blog/2018/js-es6-spread-operator/) [## Lucy | JS: ES6 的对象扩展操作符

### 两年前我写过关于析构赋值的文章，这是我最喜欢的 ES6 特性之一。自从…

lucybain.com](https://lucybain.com/blog/2018/js-es6-spread-operator/) [](https://codeburst.io/a-simple-guide-to-destructuring-and-es6-spread-operator-e02212af5831) [## 析构和 ES6 扩展运算符的简单指南

### JavaScript 到 ES6 版本的发展带来了一系列新的工具和实用程序。这些工具允许我们…

codeburst.io](https://codeburst.io/a-simple-guide-to-destructuring-and-es6-spread-operator-e02212af5831)