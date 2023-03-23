# Solidity 中的数组快速指南

> 原文：<https://betterprogramming.pub/mastering-arrays-in-solidity-c6e96479c64a>

## 添加、删除、修改元素

![](img/8134654702767f8add30a07b3785e516.png)

照片由 [JJ 英](https://unsplash.com/@jjying)在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上拍摄。

Solidity 编程语言中的数组可以保存原始数据类型，如整数、布尔和字符串。数组可以用固定大小或动态大小初始化。如果我们将它们与其他更高级的编程语言相比较，可以用来处理数组的函数非常有限。尽管如此，我们可以添加、删除、获取数组的大小等等。

# 初始化数组

我们可以创建一个坚固的数组，它可以包含动态的或者固定大小的元素。

使用固定大小的阵列可以降低气体成本，但我们并不总是能够预测元素的大小。

# 动态大小

在 Solidity 中创建动态数组时，我们需要提供它可以容纳的数据类型。不幸的是，数组不能在一个数组中有多种数据类型。

```
uint256[] array;
```

# 固定大小

另一种选择是提供数组可以容纳的元素的大小。这可以节省汽油成本，尤其是在以太坊区块链创建智能合约时。

```
uint256[10] array;
```

在幕后，Solidity 用默认值填充数组，对于`uint256`是 0(零)。

我们还可以在初始化数组时提供值。

```
uint256[] array = [1,2,3];
```

# 添加元素

我们可以为动态类型的数组添加一个新元素。为此，我们需要使用`push`方法。它将一个元素添加到数组的末尾。

下面是一个向保存`uint256`值的数组添加 100 的例子。

```
array.push(100);
```

# 更改元素

当改变一个元素时，我们需要提供索引和新值。

假设我们想改变数组中第一个元素的值。

```
array[0] = 101;
```

# 获取数组的大小

为了得到数组的大小，我们应该使用`length`函数。

```
uint256 size = array.length;
```

# 去除

我们可以使用`delete`函数从数组中移除一个元素。这里的问题是它不会改变数组的大小。它重置为默认值，例如在`uint256`的情况下为 0(零)。

本示例将数组中的第一个元素重置为 0(零)。

```
delete array[0];
```

如果我们想要删除一个元素并改变数组的长度，我们可以从想要删除的元素向上移动所有的元素。然后弹出最后一个元素，因为不再需要它了。

```
for (uint256 i = _index; i < _array.length - 1; i++) {
    _array[i] = _array[i + 1];
}
_array.pop();
```

# TL；速度三角形定位法(dead reckoning)

Solidity 编程语言中的数组可以保存原始数据类型，如`uint`、`boolean`、`address`或`string`。我们可以创建一个固定或动态大小的数组。固定大小的阵列对天然气成本的影响较小。我们可以改变元素，删除，得到数组的大小。

# 链接

*   [样本代码](https://gist.github.com/fassko/de8a9ca0657d25d656b7ad19b1705ce2)
*   [官方文件](https://docs.soliditylang.org/en/v0.8.11/types.html)
*   [示例的可靠性—数组](https://solidity-by-example.org/array/)