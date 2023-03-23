# 使用“for…of”循环访问 JavaScript 数组

> 原文：<https://betterprogramming.pub/use-for-of-to-loop-through-your-javascript-arrays-57ebb900ab5a>

## 迭代数组的更简单方法

![](img/36513619e61c0a9ea68df66b0781cfbf.png)

约书亚·阿拉贡在 [Unsplash](https://unsplash.com/) 上拍摄的照片

跟上所有最新的编码惯例可能是一项挑战。更难的是带着以前(过时的)编程经验进入一门语言，并学习正确的做事方法——而不仅仅是不同的或更好的方法。

我最初学习使用 for 循环来迭代数组。然后我发现了`.forEach()`数组方法，它为数组中的每个元素执行一个函数。我还了解到这不是迭代数组的最佳方法，因此我的搜索导致了`for...of`循环。

# 什么是 for…of 循环？

`for...of`循环与`for...in`非常相似，你可能见过它在其他语言中迭代对象或可迭代对象。`for...of`循环将数组中的每一项设置为一个已定义的变量(将其设为常量)。

下面是代码片段(traditional、forEach、for…of ),用于遍历一个五项数组并打印每个值。

```
let my_array = [1,2,3,4,5];// traditional for loop
for(let i=0; i < my_array.length; i++) {
   console.log(my_array[i]);
}// .forEach() method
my_array.forEach((element) => {
   console.log(element);
});// for...of method
for(const element of my_array) {
   console.log(element);
}
```

# 为什么要使用 for…of 循环？

最初，我采用了`.forEach()`方法，因为我觉得它打字更少，更容易阅读，但是`for...of`方法对这两者都更好。此外，`.forEach()`方法的性能较低，并且无法在周期中期停止。此外，`.forEach()`与异步任务斗争。

`for...of`循环是迭代数组的一系列选项中的最新技术。对于一个比`.forEach()`灵活且性能更好的低语法、易读的方法，使用`for...of`就可以了。