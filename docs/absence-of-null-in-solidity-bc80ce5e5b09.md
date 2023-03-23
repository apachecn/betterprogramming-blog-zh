# 实性中零的缺失

> 原文：<https://betterprogramming.pub/absence-of-null-in-solidity-bc80ce5e5b09>

## 可靠性编程语言不具有可空性特征

![](img/18b0acef849d8237107c83e117bf0c05.png)

尼克·史密斯在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上拍摄的照片。

Solidity 编程语言最奇怪的地方之一是没有 null。对于以可空性为核心构建块之一的 Swift 来说，这感觉很陌生。一开始我不明白没有这么有用的功能怎么编码。这篇文章详细阐述了在构建智能合同时，如何在 Solidity 中找到一种解决方法。

# 未定义的概念

`undefined`、`null`、`nil`、`None`等概念。存在于 JavaScript、Java、Python、Swift 等语言中，但不存在于 Solidity 中。

在坚固性上，我们可以称之为零值或缺省值概念。这是因为每个值一旦被创建，就会在内存中获得一个位置，并且应该包含一些内容。

# 默认值

要讨论默认值，我们应该将实度类型分为两类:

*   动态调整大小的类型，如`string`、`bytes`和数组；
*   非动态大小的类型，如`int`、`bool`和`address`。

# 非动态调整大小的类型

对于非动态调整大小的类型，这个游戏非常简单。以下是这些的默认值:

*   `int`或`uint256`默认值为零`0`；
*   对于`bool`是`false`；
*   `address`类型默认值为零地址`0x0000000000000000000000000000000000000000`。

如果我们想创建一个`struct`，缺省值是它所有成员的缺省值的元组。

对于`enum`默认值是第一种情况。这可能是一个非常陌生的方法，但那是因为所有幕后的`enum`案例都是一个`uint8`整数数组。

# 动态大小的类型

对于动态调整大小的类型，情况就不同了:

*   `string`的默认值为空`string`；
*   数组的默认值是一个空数组；
*   `bytes`默认值为空或无字节。

# 跟着代码走

为了说明代码中的默认值，让我们创建一个带有`enum` `EmployeeType`的雇员`struct`。

```
enum EmployeeType {
  Employee,
  Contractor,
  PartTime
}struct Person {
  EmployeeType employeeType;
  bool deleted;
  string name;
  uint256 yearOfBirth;
  address walletAddress;
  uint256[] doorAccess;
}
```

现在我们可以初始化一个`Employee`的新实例`struct`。记住，我们不需要提供任何成员值，而是在内存中分配它们。

```
Person person;
```

如果我们打印出`person`的值，我们将得到每个`Person` `struct`成员的一组默认值。

```
tuple(uint8,bool,string,uint256,address,uint256[]): 0,false,,0,0x0000000000000000000000000000000000000000,
```

# 检查可空性

现在我们知道了默认值，我们可以检查某个东西是否为“null ”,或者准确地说，它是否有默认值。

```
function check() external view {
  console.log(person.deleted == false);
  console.log(bytes(person.name).length == 0);
  console.log(person.yearOfBirth == 0);
  console.log(person.walletAddress == address(0));
  console.log(person.employeeType == EmployeeType.Employee);
  console.log(person.doorAccess.length == 0);
}
```

一旦我们执行了这个函数，所有的检查都会得到`true`。这就是我们如何理解没有定义的价值。

```
Deleted true
 Name true
 Year of birth true
 Wallet address true
 Employee type true
 Door access true
```

值得一提的是，我们将其转换为`bytes`的‘字符串’类型，并检查其长度。还有另一种方法可以实现这一点，但我们将在以后的帖子中讨论。

# TL；速度三角形定位法(dead reckoning)

Solidity 编程语言没有可空性特性，这在 Swift 和 JavaScript 等许多语言中都很常见。相反，类型有默认值，比如零`0`代表`uint`。知道缺省值是至关重要的，因为一旦我们想要检查某个东西是否被定义了，它就会派上用场。

# 链接

*   [样本代码](https://gist.github.com/fassko/86af7e7598ae950ad5ed2fdba7b66309)
*   [正式文件—类型](https://docs.soliditylang.org/en/v0.8.16/types.html)
*   [范围界定和声明](https://docs.soliditylang.org/en/v0.8.16/control-structures.html#default-value)
*   [StackOverflow 讨论](https://ethereum.stackexchange.com/questions/93109/how-to-set-a-require-for-a-string-to-not-be-null)