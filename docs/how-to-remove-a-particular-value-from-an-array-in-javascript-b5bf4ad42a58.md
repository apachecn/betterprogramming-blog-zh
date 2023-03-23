# 如何在 JavaScript 中从数组中移除值

> 原文：<https://betterprogramming.pub/how-to-remove-a-particular-value-from-an-array-in-javascript-b5bf4ad42a58>

## 有比简单地使用删除更好的方法

![](img/4d48a6f8350b53810649d95be682eade.png)

Giovanna Gomes 在 [Unsplash](http://unsplash.com) 上拍摄的照片

从数组中删除值是一项常见的任务，但必须小心执行。

有多种方法可以完成这项任务。我们会掩护两个。第一个很简单，也是你作为自学者应该尝试的。不幸的是，它也伴随着可能的陷阱。第二种使用了一种不太为人所知的方法，但是产生了更加一致的结果。

# 使用“删除”关键字

从数组中删除特定值的最简单、最直接的方法是使用`delete`关键字。

假设我们想从下面的数组中删除数字 3；我们可以使用 delete。

```
let numbers = [1,2,3,4,5];
delete numbers[2];
```

方括号内的数字 2 不是我们要删除的值，而是数组中的索引。索引是标识符，指定了我们在数组中引用的位置。

虽然使用`delete`很简单，但它也伴随着风险和隐藏的复杂性。

**我们如何知道删除哪个索引？**最有可能的是，你需要使用额外的技术，比如`.indexOf()`来识别要删除的值的索引。

我们将如何处理那个洞？打印出我们修改过的数组给了我们一个潜在问题的预览。

```
let numbers = [1,2,3,4,5];
delete numbers[2];console.log(numbers); // [1,2,undefined,4,5]
```

这个`undefined`值意味着我们的索引序列中有一个缺口。如果您对数组中的每个索引或每个值执行一组操作，这可能会是个问题。

# 使用拼接()

幸运的是，有一个解决方案，并且是 JavaScript 自带的——不需要额外的库或框架。

`splice()`方法采用一个起始索引、许多要删除的项目和可变数量的要添加的项目。要删除的项目数和要添加的项目数是可选的。

为了从上面的例子中删除数字 3，我们指定了特定的索引并只删除了一个值。

```
let numbers = [1,2,3,4,5];
numbers.splice(2,1);console.log(numbers); // [1, 2, 4, 5]
```

我们不再拥有索引二的值。此外，该数组将被重新索引，这意味着该孔将被关闭，索引将被重新编号，再次成为连续的。

到目前为止，我们已经使用了一个静态索引来删除一个值…这在现实世界中并不经常发生。让我们使用`indexOf()`方法返回我们搜索的索引。

```
let numbers = [1,2,3,4,5];
const search_value = 3;
const search_index = numbers.indexOf(search_value);if(search_index >= 0) {
   numbers.splice(search_index,1);
}console.log(numbers); // [1, 2, 4, 5]
```

我们首先检查以确保搜索值存在于数组中。`indexOf()`方法返回搜索值的索引，如果没有找到，则返回`-1`。然后，我们获取该索引并使用`splice()`方法。