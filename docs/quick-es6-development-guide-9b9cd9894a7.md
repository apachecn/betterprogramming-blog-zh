# ES6 快速开发指南—第 1 部分

> 原文：<https://betterprogramming.pub/quick-es6-development-guide-9b9cd9894a7>

## 作用域、循环、数据结构等等

![](img/12c61f27e7d545df3a7d1ecfc2496924.png)

照片由[普里西拉·杜·普里兹](https://unsplash.com/@priscilladupreez?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/loops?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在这篇文章中，我们将研究 ES6 中有哪些新特性，以及如何使用它们，并评估一些快速示例来了解相对于 ES5 的改进。新功能的列表很大，但这里我们将重点关注一些常用的功能。我们还将探索如何在日常开发任务中使用最佳实践。

ES9 正在讨论中，但是 ES6 仍然不被所有浏览器完全支持。可以查看 GitHub 上的[详细兼容性表](https://kangax.github.io/compat-table/es6/)进行参考。ECMAScript(ES)是标准，而 JavaScript 是用来实现这一标准并在此基础上构建的最流行的语言。

# 辖域

ES6 引入了一些受欢迎的变化来解决 JavaScript 作用域的常见问题。

我们举个例子(ES5):

```
function myFunction() {
 var myValue = "I am value";
 if (1) {
   let myValue = "I am another value";
   console.log(myValue)
 }
 console.log(myValue)
}
```

输出:
*我是另一个值*
*我是另一个值*

然而，我不希望我的外部`console.log`语句(如上例)打印内部变量的值。这通常不是我们想要的行为。

ES5 中有两个作用域:全局和函数。

## **范围**

它只是一个边界，在这个边界内变量是存在的，或者说是可以被使用的。

例如:

```
function myFunction(){
    var myValue = "Don't touch, I'm a value"
}
console.log(myValue)
```

输出:
*引用错误:未定义 myValue。*

这是因为我们试图在变量`myValue`的作用域之外访问它的值。虽然在 ES5 中，你可以通过使用生命来实现它，但这并不容易。

ES6 中引入了一个关键字`let`，这个关键字有局部范围(没错，下一节还有一个)。

`let`不是 var 的替代品，但据说它是 ES6 和 TypeScript 中的一个很好的编程标准，因为它使变量范围可预测并且更容易推理。

## 以打字打的文件

ES6 标准语言，扩展名. ts

让我们看看它如何解决早期的问题(ES6)。

```
function myFunction() {
 var myValue = "I am value";
 if (1) {
   let myValue = "I am another value";
   console.log(myValue)
 }
 console.log(myValue)
}
```

输出:
*我是另一个值*
*我是值*

你应该感到有成就感！！现在试着用`let`为自己循环一次。

## 常数

另一个令人兴奋的新增功能是`const`。`const`提供了不变性机制，这意味着变量不会随时间而改变。

例如:

```
const myConst = "my const value";
myConst = "my another const value";Output:
VM47:1 Uncaught SyntaxError: Identifier 'myConst' has already been declared
    at <anonymous>:1:1
```

在任何参数之前，我要补充的是，变量是不可变的，但它所赋的值是不可变的。这意味着如果我们声明一个对象为`const`，我们可以改变这个对象的属性。

例如:

```
const myConstObject = {};
myConstObject.constProp = "Const Value";
console.log(myConstObject)

Output:
	{constProp: "Const Value"}
```

# 环

如果你经常使用`for…in` `foreach`循环，你可能会怀疑`for…of`的引入，但是相信我，你会喜欢它的！

那么，为什么`for…of`已经到来？肯定`for…of`有比`for…in`更好的东西。我们去看看。

## 在(ES5)中为……

```
var myCoins = [1,2,5,10];
for( let coin in myCoins){
	console.log(typeof(coin));
}

Output:
	string
	string
	string
	string
```

是的，你没看错输出。`for…in`循环返回一个数字数组作为字符串值。这显然会混淆并产生难以跟踪的错误。

## For 和 forEach (ES5)

让我们看一个基本的例子来说明`forEach`是如何工作的:

```
var myCoins = [1,2,5,10,20]
myCoins.forEach(function(value){
console.log(value);
});Output:
1,2,5,10,20
```

是的，没错，没有数字到字符串的转换问题。但是有一个问题。您不能使用 break 语句跳出循环，并使用 continue 语句移至下一个迭代。

## 为了…的

在 ES6 中引入的`for…of`避免了`for…in`循环的问题，它与 break 和 continue 语句一起工作，甚至允许从封闭块返回。

```
var myCoins = [1,2,5,10];
for( let coin of myCoins){
	console.log(typeof(coin));
}

Output:
	number
	number
	number
	number
```

# 数据结构

先说一些常用的，比如`Set`和`Map`。

## 一组

让我们快速介绍一下关于`Set`的一些重要要点。

*   您不像在`Set`中的对象那样存储键值对，只存储键。
*   没有重复值包含不同的元素/对象。

示例:

```
let myCoinSet = new Set();
myCoinSet.add('foo');
myCointSet.size
// 1myCoinSet.add('foo');
myCoinSet.size
//1
```

`Set`接收可迭代参数作为其输入。示例:

```
let myCoinSet = new Set([1, 2, 3]) - correct
let myCoinSet = new Set(1,2,3) -wrong
```

或者你可以这样做:

```
Let myCoinSet = new Set();
myCoinset.add(1) -correct
myCoinSet.add(1,2,4) -wrong
myCoinSet.add([1,4,5]) -wrong
```

`Set`不像数组那样支持元素的随机顺序访问。例如:

```
console.log(myCoinSet[1]) -wrong
```

`Set`的一些常见操作:

*   移除元素:`myCoinSet.delete(2)`。
*   清空器械包:`myCoinSet.clear();`

循环遍历`Set`非常简单。例如:

```
let myCoinSet = new Set([1, 2, 5, 10])		
for (let coin in myCoinSet){
console.log(coin)
}Output:
	1, 2, 5, 10
```

## 地图

让我们快速介绍一下地图的一些重要要点。

*   映射是一个键值对。
*   保存时会保持一个顺序，所以当我们放置一个循环时，它会以相同的顺序打印。

让我们用`Map`做一些基本操作。

正在创建`Map`:

```
let myMap = new Map();
```

添加对:

```
let myMap = new Map();
myMap.set("Name", "Carlos")
myMap.set("Occupation", "Engineer" )
```

另一种补充方式是:

```
let myMap = new Map([
 [ "Name", "Alex" ],
 [ "Occupation", "Test Engineer" ],
 [ "Company", "Modus Create" ]
]);
```

通过键获取元素:

```
map.get("Name")
// Carlos
```

正在检查密钥是否存在:

```
map.has(“Name”)
//true
```

按键删除:

```
map.delete(“Name”)
```

检查大小:

```
map.size()
```

清空整个地图:

```
map.clear()
```

像我们对集合所做的那样在地图上循环是非常容易的，但是这里我们有键值，所以这取决于你想要循环什么。示例:

```
let m = new Map([
 ["Name", "Chris"],
 ["Occupation", "Technical Manager"],
 ["Company", "Modus Create"],
 ["Country", "USA"]
]);1-	for(let keys of m.keys()){
	console.log(keys)
	}
```

输出:

价值:姓名关键字:克里斯
价值:职业关键字:技术经理
价值:公司关键字:作案手法
价值:国家关键字:美国

# 对象与地图有何不同

在 Objects 中，您必须使用简单的键作为字符串、数字或符号，但是在`Map`中，您可以使用偶数数组和对象。

地图是对象的实例，而不是相反。

函数作为值在`Map`中是不可能的，但在 Object 中是可能的。示例:

```
var person = {
 name: "Carlos",
 workingDays() {
   console.log("25 days average")
 }
}
```

如果你的应用程序有大量的 JSON 操作，使用对象比`Map`更好。

你使用物件和`Maps`是什么体验？请分享你认为其中一种比另一种更有效的情景。什么问题是你不能用一种方法解决而用另一种方法不能解决的？

ES6 中有很多更酷更高级的特性，比如胖箭头函数、析构、模块、类、接口、承诺和类型。这不是这个话题的结尾。在下一篇文章中寻找这个主题的第二部分。

请留下您对这件作品的宝贵意见和建议。