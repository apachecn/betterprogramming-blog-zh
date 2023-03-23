# JavaScript:从数组中移除值的四种方法

> 原文：<https://betterprogramming.pub/javascript-four-ways-to-remove-a-value-from-an-array-12992980eea1>

## 删除数组值的四种本机方法

![](img/f224169d073508d44726eb4240cd1d27.png)

[菠萝供应公司](https://unsplash.com/@pineapple?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/four?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

数组是存储多个值的有序数据类型。这意味着数组中的每一项都有一个数字标识符——一个索引——用来引用它在行中的位置。

数组从索引`0`开始，每个值递增 1。要引用数组中的值，请使用方括号`[]`中的适当索引。

```
let numbers = [10,20,30,40];// print a value with an explicit index
console.log(numbers[0]); # 10
console.log(numbers[1]); # 20
console.log(numbers[2]); # 30
console.log(numbers[3]); # 40// alternatively, use a for loop
for(let i=0; i<numbers.length; i++) {
   console.log(numbers[i]);
}
```

现在，如果我们想删除数组中的一个值呢？这里有四种从数组中删除值的技术。每种技术都有一个时间和地点，这将在下面描述。

**注意:**所有四种技术都可以在本地完成，无需导入任何额外的库。

# 使用 Delete 关键字

如果你想变得简单，清除数组中的值的最简单的方法是使用`delete`关键字。只需使用变量名和您希望清除的索引。

```
let numbers = [1,2,3,4];delete numbers[1];
console.log(numbers); // [1, undefined, 3, 4]
```

不过，要小心。虽然这种方法看起来很简单，但它经常会产生令人头痛的问题，因为现在您的数组留下了一个漏洞。

从`delete`技术中得到的重要收获是，当从数组中删除一个值时，第二个问题——我需要重新索引我的数组吗？*——*必须回答。

# 使用。pop()方法

需要从数组中移除最大的索引值吗？想要重新索引数组，以便末尾没有未定义的索引吗？那么`.pop()`法正是医生所要求的。

```
let numbers = [1,2,3,4];numbers.pop();
console.log(numbers); // [1,2,3]
```

# 使用。splice()方法

也许你想从数组中移除某个值，而不是特定的索引。如果是这种情况，并且您可以重新索引数组，那么`.splice()`方法是一个不错的选择。

拼接一个数组将创建一个新的数组，该数组从一个特定的索引开始，删除一些值，并将所有值默认为向右。

```
let numbers = [1,2,3,4];numbers.splice(2);
console.log(numbers); // [1,2]
```

如果您只想删除这一个值，请使用`splice()`方法中的第二个(可选)参数。

```
let numbers = [1,2,3,4];numbers.splice(2,1);
console.log(numbers); // [1,2,4]
```

请注意，我们没有立即打印拼接的结果。这是因为移除的值将由方法返回。

```
let numbers = [1,2,3,4];console.log(numbers.splice(2,1)); // [3]
```

这很好，但是在现实世界的例子中，您可能无法硬编码一个索引值。因此，让我们使用`.indexOf()`方法来根据条件查找值的索引。

```
// delete the value 3 from the array
let numbers = [1,2,3,4];
i = numbers.indexOf(3);if(i >= 0) {
   numbers.splice(i,1);
}console.log(numbers); // [1,2,4]
```

需要代码示例中的`if`条件来验证我们在数组中找到了值。当没有找到值时，`indexOf()`方法将返回`-1`。因此，如果`i`大于或等于`0`，那么它在数组中被发现。

# 使用。filter()方法

虽然`.splice()`对于查找和删除单个值很方便，但有时您希望从数组中删除一个值(或条件)的所有出现。在这些场景中，`.filter()`方法是你的朋友。

该方法将一个函数作为参数，并在过滤函数中保留评估为`true`的值。为了过滤掉特定的值，我们必须检查否定的情况。

```
let numbers = [1,2,3,4];numbers = numbers.filter((n) => {return n != 3});
console.log(numbers); // [1,2,4]
```

请注意，我们必须将数字重新分配给它们自己。这是因为与`splice()`方法不同，`filter()`将返回新数组，而不是对原始数据执行操作。

# 结论

这并不是从数组中删除一个值的仅有的四种方法，但是这为您选择自己喜欢的方法或创建自己的技术提供了很大的空间。