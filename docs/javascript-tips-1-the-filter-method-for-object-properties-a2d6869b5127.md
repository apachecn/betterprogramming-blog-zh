# JavaScript 技巧#1:对象属性的过滤方法。

> 原文：<https://betterprogramming.pub/javascript-tips-1-the-filter-method-for-object-properties-a2d6869b5127>

## 应用于对象属性的过滤器数组方法的简单 JavaScript 聚合填充

![](img/6848beba9581ab98917c4b806fde25a0.png)

照片由[布雷克·理查德·弗道恩](https://unsplash.com/@blakeverdoorn)在 [Unsplash](https://unsplash.com/photos/gM-RfQsZK98) 上拍摄

# 问题是

您发现自己管理从第三方 API 接收的大规模对象或从 JSON 文件读取数据的频率有多高？在 JavaScript 中，对象无处不在，很多时候我们需要添加、删除或编辑它们的属性。我们还想生成一个新的对象，这样我们就不会改变原来的对象。

ECMAScript 5.1 为我们提供了一个极好的`Array.prototype.filter` 方法来过滤数组，并且只返回由我们的条件过滤的值的列表。但是对于对象上的相同操作，我们总是不得不采用功能较少的方法。

# 解决方案

一种选择是过滤我们的对象属性。如果这是一个循环操作，我们可以为`Object`原型填充一个方法，它会为我们完成:

![](img/268592500e8a82a7d07bdac5710ba86f.png)

现在，您可以轻松地通过谓词回调来过滤对象属性，谓词回调返回“true”或“false”来过滤对象属性。

> 这个实现只迭代对象的`own`和`enumerable`属性。