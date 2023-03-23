# 停止使用 For 循环迭代数组

> 原文：<https://betterprogramming.pub/stop-using-for-loops-to-iterate-over-arrays-5c46940e79d1>

## 开始用。forEach()方法

![](img/0d349381b5e78419c0194c3d62bf992d.png)

何塞·阿拉贡内塞斯在 [Unsplash](https://unsplash.com/s/photos/stop?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

因为循环在我心中有着特殊的位置。很长一段时间，我依靠它们来远离 while loops。不可避免地，这导致忘记增加我的计数器变量。使用 for 循环迭代一个数组的标准语法已经被写入内存:For i 等于零，I 小于 length，I+plus…

当我教编程时，我总是将 while 和 for 循环区分为使用后者进行已知次数的迭代。原因是所有的循环语法都被提前处理了——从而减少了错误(尤其是对学习者而言),并且在六个月后重新访问代码时，扫描代码的时间也变得更容易了。

我已经开始远离我钟爱的迭代结构，转而在 JavaScript 中使用`.forEach()`方法，你也应该这样做。与循环的样板相比，`.forEach()`方法进一步减轻了开销，并且在传达代码意图时更加清晰。见鬼，这个方法的名字出现在描述这个块的口语句子中:对于数组中的每一项，做一些事情。

# 是什么？forEach()方法？

作为数组类的内置方法，`.forEach()`是在 ECMAScript 2015(也称为 ES6)中引入的。

根据 [Mozilla 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)，该方法将回调作为参数。不必深入讨论，回调是一个将对数组中的每一项执行的函数。这一点很关键，使用`.forEach()`最适合于对每个项目的*执行相同操作的情况。该函数有一个必需的参数，即值，还有三个可选参数，分别用于索引、基数组和分配的`this`值。*

# 一个基本例子

为了演示`.forEach()`方法的基本用法，让我们定义一个整数数组并将每个值的平方打印到控制台。

```
let nums = [1,2,3,4,5];nums.forEach(function(n) {
   console.log(n ** 2); // 1, 4, 9, 16, 25
});
```

# 包括索引

如果我们还想打印索引值呢？我们将在函数定义中包含第二个可选参数。

```
let nums = [1,2,3,4,5];nums.forEach(function(n,i) {
   console.log("i: " + n ** 2);
});
/*
  0: 1
  1: 4
  2: 9
  3: 16
  4: 25
*/
```

# 用存储值。forEach()

如果您不想打印所有内容，而是将结果存储在一个新变量中，该怎么办？

```
let nums = [1,2,3,4,5];
let result = nums.forEach(function(n) {
   return(n ** 2);
});
console.log(result); // undefined
```

正如您在上面的代码片段中看到的，`.forEach()`方法没有返回。现实是使用`.map()`方法是正确的答案；但是，如果必须通过`.forEach()`，那么可以手动对新数组进行分配。

```
let nums = [1,2,3,4,5];
let result = [];
nums.forEach(function(n) {
   result.push(n**2);
});
console.log(result); // [1,4,9,16,25];
```

# 浓缩回调函数

如果你想进一步压缩你的代码，考虑用一个箭头函数代替回调函数。

```
let nums = [1,2,3,4,5];

nums.forEach((n) => {
   console.log(n**2);
});
```

事实上，如果您只打算使用值(没有可选参数)，您可以跳过`n`周围的括号，如果您的循环只有一条语句，您可以完全跳过花括号`{}`。

```
let nums = [1,2,3,4,5];nums.forEach(n => console.log(n**2));
```

# 有否定的吗？

简而言之，答案是肯定的，在选择编写代码的方式时，总会有一些权衡。我遇到的一个潜在的缺点——它实际上不是缺点，只是鼓励更好的实践——是您不能像在 for 循环中那样修改每个索引中的原始值:

```
let nums = [1,2,3,4,5];for(let i=0; i<nums.length; i++) {
   nums[i] = nums[i] ** 2;
}console.log(nums); // [1,4,9,16,25]
```

***更新:ES6 中引入的*** `***for...of***` ***循环旨在将*** `***.forEach()***` ***替换为*** [***更简单的约定，也就是更具执行性的***](https://medium.com/better-programming/use-for-of-to-loop-through-your-javascript-arrays-57ebb900ab5a) ***。感谢分享这些信息的读者，非常感谢！***