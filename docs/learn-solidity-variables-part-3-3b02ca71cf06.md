# 学习可靠性:变量(第三部分)

> 原文：<https://betterprogramming.pub/learn-solidity-variables-part-3-3b02ca71cf06>

## 引用类型，应该显式指定数据位置

![](img/11a8df45afa00c73e733f96f3b02dcc9.png)

照片由[缺口缺口](https://unsplash.com/@jannerboy62?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[缺口](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上拍摄

欢迎阅读学习可靠性系列的另一篇文章。在[上一篇文章](https://medium.com/better-programming/learn-solidity-variables-part-2-f3b842f5bfb8)中，我们已经看到了数据位置是如何工作的，以及何时可以使用三个位置:`memory`、`storage`和`calldata`。

在这篇文章中，我们将继续我们学习固体变量的旅程。这一次我们将关注引用类型，正如我们在以前的文章中提到的，引用类型应该显式地指定数据位置。我们还将看到如何定义映射、枚举和常量。

# 数组

在 [Solidity](https://docs.soliditylang.org/en/v0.7.4/) 中，我们有两种类型的数组:存储数组和内存数组。

## 存储阵列

这些数组被声明为状态变量，可以有固定或动态长度。

具有动态长度的存储数组可以调整大小，这意味着它们可以访问`push()`和`pop()`方法。

## 存储器阵列

这些数组用`memory`作为它们的数据位置来声明。它们也可以有固定或动态的长度，但是动态大小的内存数组不能调整大小(即不能调用`push()`和`pop()`方法)；数组的大小必须事先计算好。

使用关键字`new`声明动态大小的内存数组，如下所示:

```
Type[] memory a = new Type[](size)
```

这里要提到的另一点是，当你使用内存数组时，你要写这样的东西:

```
uint256[] memory array;
array[0] = 1;
```

你不会得到任何警告，但是你会得到一个无效的操作码，因为根据内存中[布局的描述`array`将指向零槽，这是不应该被写入的。请记住，在使用数组之前，一定要对其进行初始化，这样才能获得有效的地址。](https://solidity.readthedocs.io/en/v0.7.4/internals/layout_in_memory.html)

## 数组切片

数组切片只能与`calldata`数组一起使用，并被写入`x[start:end]`。切片的第一个元素是`x[start]`，最后一个元素是`x[end - 1]`。

`start`和`end`都是可选的:`start`默认为`0`，`end`默认为数组的长度。

# 特殊的动态大小数组

1.  `byte[]`或`bytes`

这些数组可以保存任意长度的原始字节数据。两者的区别在于`byte[]`遵循数组类型的规则，而在[这部分文档中提到过，](https://solidity.readthedocs.io/en/v0.7.4/internals/layout_in_memory.html#layout-in-memory)Solidity 中内存数组的元素总是占据 32 字节的倍数。这意味着如果一个元素小于 32 字节的倍数，它将被填充到合适的大小。

在`byte`数组的情况下，这将为每个元素浪费 31 个字节，而对于`bytes`或`string`则不是这样。我要提醒你的是，从内存中读取或写入一个字(32 字节)要花费 3 gas，这就是为什么推荐使用`bytes`而不是`byte[]`的原因。

2.`string`

`string`是 UTF-8 数据的动态数组。与其他语言相反，`string` in Solidity 不提供获取字符串长度或执行两个字符串的连接或比较的函数(需要使用库)。
使用`bytes(<string>)`可以将一个字符串转换成一个字节数组。这将返回字符串的 UTF-8 表示的低级字节。

**注**:一个字符可以编码成多个字节，这意味着字节数组的长度不一定是字符串的长度。

## 字符串文字

参见[本部分文档](https://solidity.readthedocs.io/en/v0.7.4/types.html#string-literals-and-types)。

## 字符串对`bytes`

文档中的大多数例子都使用了`bytes32`而不是`string`，而且他们也明确表示，如果字符串的字节数有限，就使用值类型`bytes1`到`bytes32`，因为这样更便宜。

# 结构

与 C 和 C++一样，结构允许您定义自己的类型，如下所示:

```
struct Donation {
      uint256 value;
      uint256 date;
}
```

一旦你定义了你的结构，你就可以开始把它作为一个状态变量或者在你的函数中使用。

为了初始化一个结构，我们有两种方法:

*   使用位置参数:

```
Donation donation = Donation(
msg.value,
block.timestamp
);
```

*   使用关键字:

```
Donation donation = Donation({
value : msg.value,
date: block.timestamp
});
```

第二种方法将避免我们必须记住结构成员的顺序，因此它可能比第一种方法更有用。

使用点号访问结构的成员:

```
uint256 donationDate = myDonation.date;
```

> 尽管结构本身可以是映射成员的值类型，也可以包含其类型的动态大小数组，但结构不可能包含其自身类型的成员。这种限制是必要的，因为结构的大小必须是有限的。— [坚固性文件](https://solidity.readthedocs.io/en/v0.7.4/types.html#structs)

# 映射

您可以将映射想象成一个巨大的键/值存储库，其中存在所有可能的键，并且任何值都可以用键一次性设置或检索。

映射声明如下:

```
mapping( KeyType => ValueType) VariableName
```

`KeyType`可以是任何内置值类型(我们在[第 1 部分](https://medium.com/better-programming/learn-solidity-variables-part-1-657fc27c2cc1)中看到的类型)、字节或字符串，或者任何契约或枚举类型。`ValueType`可以是任何类型，包括映射、数组和结构。

这里要提到的一个重要的事情是，唯一允许映射变量的数据位置是`storage`，这意味着您只能将它们声明为状态变量、存储指针或库函数的参数。

# 枚举

枚举将允许您在自定义类型下对相关值进行分组，如下例所示:

```
enum Color { green , blue, red }
```

使用以下语法访问`enum`值:

```
Color defaultColor = Color.green;
```

**注意**:枚举也可以在文件级声明，在契约或库定义之外。

# 常量和不可变状态变量

状态变量可以声明为`constant`或`immutable`。在这两种情况下，都不能在构造了协定之后修改变量。对于`constant`变量，该值必须在编译时固定，而对于`immutable`，它仍然可以在构造时赋值。

编译器不会为这些变量保留存储槽，每次出现都会被相应的值替换。

常量使用关键字`constant`声明:

```
uint256 constant maxParticipants = 10;
```

对于不可变的状态变量，使用关键字`immutable`声明它们:

```
contract C {
      address immutable owner = msg.sender;
      uint256 immutable maxBalance;

      constructor(uint256 _maxBalance){
           maxBalance = _maxbalance;
      }
}
```

你可以在文档的[部分找到更多关于常量和不可变状态变量的细节。](https://solidity.readthedocs.io/en/v0.7.4/contracts.html#constant)

**注意**:也可以在文件级定义`constant`变量。

# delete 关键字

最后我想补充的是`delete`在坚固性上的使用。
用于将变量设置为初始值，这意味着该语句`delete a`将表现如下:

*   对于整数，相当于`a = 0`。
*   对于数组，它分配一个长度为零的动态数组或一个相同长度的静态数组，所有元素都设置为初始值。
*   `delete a[x]`删除数组索引`x`处的项目，保留所有其他元素和数组长度不变。这尤其意味着它会在数组中留下一个间隙。
*   对于结构，它分配一个所有成员都重置的结构。
*   `delete`对映射没有影响(因为映射的关键字可能是任意的，通常是未知的)。

# 练习时间:简单的 Crud

在本练习中，我们将创建一个用于管理用户的合同。

以下是说明:

*   创建一个新文件并添加一个名为 Crud 的契约。
*   创建一个名为 User 的结构，其中包含用户的 id 和名称。
*   添加两个状态变量并将其公开:1)用户的动态数组，以及 2)每次我们创建新用户时都会递增的 id。

下一步是创建 crud 函数，但是因为我没有向您介绍 Solidity 函数，所以我将向您提供声明函数的语法。在下一篇文章中，我们将对它们进行详细的讨论:

```
function function_name(<param_type> <param_name>) <visibility> <state mutability> [returns(<return_type>)]{ ... }
```

可见性可以是公共的、私有的、内部的、外部的。
状态可变性可以是:查看、纯、应付。

这是您将创建的函数的描述。

## 1.增加

可见性:公共
状态可变性:空

该函数将用户名作为参数，创建一个具有新 id 的 user 实例(每次添加新用户时，id 都会自动增加)，并将新创建的用户添加到数组中。

## 2.阅读

可见性:公共
状态可变性:视图

该函数获取要查找的用户 id，如果找到则返回用户名，如果没有找到则返回事务(稍后将详细介绍异常处理)。

## 3.更新

可见性:公共
状态可变性:空

该函数将获取用户的 id 和一个新名称，如果找到相应的用户，则更新该用户，如果该用户不存在，则恢复交易。

## 4.破坏

可见性:公共
状态可变性:空

这个函数获取要删除的用户的 id，如果找到了就从数组中删除，如果用户不存在就恢复事务。

提示:因为最后三个函数都需要查找用户，所以您需要创建一个私有函数，该函数将获取用户的 id，如果找到了，就返回它们在数组中的索引，以避免重复相同的代码。

当你完成后，你可以在 GitHub 上找到解决方案[。](https://gist.github.com/wissalHaji/94c8a5f356cb6f8da02702cc58584bd9)

我们对变量的讨论到此结束。下一次我们将会看到函数以及如何在 Solidity 中使用它们，所以如果你想了解更多，请继续关注即将到来的文章。