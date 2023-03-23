# 检查数组中值的三种方法

> 原文：<https://betterprogramming.pub/three-ways-to-check-for-a-value-in-an-array-5976b8fe030d>

## 学习使用内置数组方法或自己的循环

![](img/f391af963c6daa6ce90f474caea86b81.png)

照片由 [Pavan Trikutam](https://unsplash.com/@ptrikutam?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/three?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

有时你需要找出一个数组中是否存在一个特定的值。实现这一点有许多选择。我整理了三个选项供你选择。各有利弊；最终，了解这三种策略并学会何时使用每种策略对你最有利。

# 的。includes()方法

对于简单的是或否，`.includes()`方法是一种简单而有效的方法来确定数组中的值。在将有问题的值作为唯一参数传递后，将返回布尔值 true 或 false。

```
let names = ["Jim","Sarah","Tychus"];
if(names.includes("Tychus")) {
   console.log("Value found");
}
```

# 的。indexOf()方法

如果您想知道除了*之外的*值存在于数组中的哪里*，如果*值存在，那么`.indexOf()`方法是一个健康的选择。

```
let names = ["Jim","Sarah","Tychus"];
if(names.indexOf("Tychus") >= 0) {
   console.log("Value found");
}
```

注意对于`.indexOf()`，表达式正在检查`>= 0`。这是因为如果找不到该值，该方法将返回`-1`。否则，它将返回索引，即 iterable 中的位置。

# 编写手动循环

最后，第三种方法是简单地遍历数组并查找值是否存在。

您可能已经这样做了，并且正在寻找更有效的方法，但是您会惊讶地发现——我也是——手动循环在性能上与上述方法相当。

```
function findValue(haystack,needle) {
   for(const item of haystack) {
      if(item === needle) {
         return true;
      }
   }
   return false;
}let names = ["A","B","C","D","E"];
console.log(findValue(names,"D"));
```

有多种方法可以构建循环。上面的代码片段使用了技术中的[for …,但是还有各种其他方式。`.forEach()`数组方法、while 循环和传统的 for 循环只是您的一些选择。](https://medium.com/better-programming/use-for-of-to-loop-through-your-javascript-arrays-57ebb900ab5a)