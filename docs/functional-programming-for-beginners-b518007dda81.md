# 面向初学者的函数式编程

> 原文：<https://betterprogramming.pub/functional-programming-for-beginners-b518007dda81>

*用于过滤数组的无压力编程范例及更多内容*

![](img/3b0b705d634e92c3c8f6a15572f7e788.png)

照片由 [Rima Kruciene](https://unsplash.com/@rimakruciene?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/@rimakruciene?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

坦白地说，我很震惊有这么多程序员对[函数式编程](https://en.wikipedia.org/wiki/Functional_programming)一无所知。这是编写安全、定义良好的代码的最实用的方法之一，而不需要理解它的基本编程原则。

理解基本原则很重要，但本文只是一个基本介绍。它展示了函数式编程如何提高代码的可读性，然后以附加资源的链接结束。

让我们从用 for 循环过滤一组住宅开始。

```
class Home {
  constructor(
    public type: "Condo" | "House",
    public price: number,
  ) { }
}const homes = [ 
  new Home("Condo", 100), 
  new Home("House", 200),
]
```

# 使用 For 循环过滤

下面的代码是大多数初学者如何过滤数组的。

```
function filterCondos(homes: Home[]): Home[] {
  const condos = []
  for (let i = 0; i < homes.length; ++i) {
    const home = homes[i]
    if (home.type === "Condo") {
      condos.push(home)
    }
  }
  return condos
}const condos = filterCondos(homes)
```

函数`filterCondos`循环遍历并将 homes 推入一个数组，其中`home.type === “Condo"`。

基本上，我不喜欢`filterCondos`,因为它对于一个简单的任务来说有很多代码。

检查类型，`home.type === "Condo"`是唯一重要的一行代码。那么，为什么`filterCondos`如此臃肿？怎样才能减少过剩？

# 使用 Array.prototype.filter 过滤

函数式编程范式允许代码一目了然。

```
function byCondo(home: Home): boolean {
  return home.type === "Condo"
}const condos = homes.filter(byCondo)
```

函数`byCondo`检查一个家是否是“公寓”当与 [Array.prototype.filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 一起使用时，我们得到一个数组`condos`。

结果和使用`filterCondos`一样，没有多余的代码。

# 用咖喱函数过滤

我想按任何类型进行过滤，而不仅仅是“公寓”这可以通过创建一个函数来实现，在这个函数中，类型被传入，新的函数被返回。

```
const condos = homes.filter(byType("Condo"))
const houses = homes.filter(byType("House"))
```

这种技术叫做[奉承](https://en.wikipedia.org/wiki/Currying)。如果娃娃是函数，就像一个俄罗斯娃娃——函数返回函数。

```
function byType(type: "Condo" | "House") {
  return function (home: Home): boolean {
    return home.type === type
  }
}
```

为了简单起见，使用像 [lodash](https://lodash.com) 这样的库来生成 curry 函数。

```
import _ from "lodash"function filterByType(type: "Condo" | "House", home: Home) {
  return home.type === type
}const byType = _.curry(filterByType)const condos = homes.filter(byType("Condo"))
const houses = homes.filter(byType("House"))
```

表达式`_.curry(filterByType)`构建了`byType`函数。

# 结论

[命令式编程](https://en.wikipedia.org/wiki/Imperative_programming)，使用 for 循环，产生不必要的代码，使代码更难阅读。函数式编程是解决方案，因为它关注逻辑——它是[声明式编程](https://en.wikipedia.org/wiki/Declarative_programming)范例的一部分。

使用函数式编程还有很多上面没有讨论的原因。查看下面的附加资源，以获得对函数式编程更完整的理解。

1.  Mozilla 开发者网络(MDN)上的数组对象 API
2.  [Sumit Kumar prad Han](https://www.skptricks.com/2018/11/learn-to-chain-map-filter-and-reduce-in-javascript.html)[撰写的关于链接](https://www.blogger.com/profile/13441435149900194825)的短文
3.  [关于连锁的长文](https://medium.com/poka-techblog/simplify-your-javascript-use-map-reduce-and-filter-bd02c593cc2d)作者[艾蒂安·塔尔博特](https://medium.com/@etiennetalbot)
4.  FunFunFunction 的[功能编程播放列表](https://www.youtube.com/watch?v=BMUiFMZr7vk&list=PL0zVEGEvSaeEd9hlmCXrk5yUyqUag-n84&index=1)
5.  [为什么函数式编程如此重要](https://medium.com/@navneet.sahota/why-functional-programming-matters-e13e910a0152)作者 [Navneet Singh](https://medium.com/@navneet.sahota)
6.  [c#中的函数式编程](https://blog.submain.com/csharp-functional-programming)Carlos Schults