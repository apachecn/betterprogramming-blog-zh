# 坚实度映射背后的故事

> 原文：<https://betterprogramming.pub/the-story-behind-mapping-in-solidity-e1c428aa8cbe>

## 看看 Solidity 的散列表方法

![](img/5c377a56872d75d16a40959f908465a1.png)

由[沃尔坎·奥尔梅斯](https://unsplash.com/@volkanolmez)在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上拍摄的照片。

映射在 Solidity 编程语言中起着重要的作用。这些散列表可以有一个键和值。当我们使用它们时，有一些警告，特别是来自其他编程语言的警告。让我们在本文中探索它们。

# 映射键和值

映射本质上是哈希表，所以这意味着键被保存为哈希表。为了访问该值，key 被散列并在映射表中找到。

# 限制

映射可以是状态变量、函数中的存储引用类型或库函数的参数。映射不能返回，也不能在包含数组的结构或数组中使用。

密钥类型可以是任何内置类型、字节、字符串或枚举值。不允许使用我们定义的类型。这是因为 Solidity 知道如何散列值。

值类型可以是任何类型——内置类型或我们定义的类型。它甚至可以是创建双哈希表的映射。

# 从映射读取和写入

让我们回顾一下如何在映射中读写数据。假设我们想要建立一个幸运数字注册中心。每个有钱包地址的人都可以设置和读取他们的幸运数字。

首先，我们需要建立幸运数字的映射。

```
mapping(address => uint256) private luckyNumbers;
```

如果我们将访问级别设置为 public，默认情况下 getter 函数是编译的。

# 获取映射中的值

要从映射中获取值，我们需要知道键。后台的 Solidity 会对其进行哈希处理，从哈希表中找到。

```
function getMyLuckyNumber() external view returns(uint256) {
  require(luckyNumbers[msg.sender] != 0, "0 can't be lucky"); return luckyNumbers[msg.sender];
}
```

如果值不存在，我们得到默认值，在`uint256`的情况下是零。

# 设置映射中的值

我们需要一个定义类型的键和值，映射可以在设置值时保存它。请注意，如果已经设置了值，这也会更新该值。

```
function setMyLuckyNumber(uint256 number) external {
  require(number > 0, "Can't be set to zero"); luckyNumbers[msg.sender] = number;
}
```

# TL；速度三角形定位法(dead reckoning)

映射类型实际上是 Solidity 编程语言中带有键和值的散列表。有一些限制，特别是我们可以定义为关键的东西。我们需要密钥来从映射中获取数据。记住我们在值中使用的类型的默认值，以防值没有被设置。当我们设置值时，我们也在覆盖旧的值。

# 链接

*   [样本代码](https://gist.github.com/fassko/488573feba1cf976b5854faeb1fd2ba8)
*   [官方文件](https://docs.soliditylang.org/en/v0.8.13/types.html)
*   [实例的可靠性——映射](https://solidity-by-example.org/mapping/)