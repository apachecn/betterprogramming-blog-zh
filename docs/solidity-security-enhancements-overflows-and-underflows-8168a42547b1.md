# 可靠性安全增强:溢出和下溢

> 原文：<https://betterprogramming.pub/solidity-security-enhancements-overflows-and-underflows-8168a42547b1>

## 我们将着眼于一个你在编写智能合约时应该注意的主要安全特性:防止溢出和下溢。

![](img/0b884d6022b0692edd269709f59e51f8.png)

什么是**溢出**？

假设我们有一个`uint8`，它只能有 8 位。这意味着我们可以存储的最大数是二进制数`11111111`(或者十进制数，2^8 - 1 = 255)。

看一下下面的代码。`number`最后等于什么？

```
uint8 number = 255;
number++;
```

在这种情况下，我们已经导致它溢出——因此`number`现在违反直觉地等于`0`,即使我们增加了它。(如果你给二进制数`11111111`加 1，它会重置回`00000000`，就像时钟从`23:59`到`00:00`。

下溢是类似的，如果你从等于`0`的`uint8`中减去`1`，它现在将等于`255`(因为`uint`是无符号的，不能为负)。

虽然我们在这里没有使用`uint8`，并且每次递增`1`时`uint256`似乎不太可能溢出(2^256 是一个非常大的数字)，但在我们的合同中加入保护措施仍然是很好的，这样我们的 DApp 在未来就不会有意外的行为。

# 使用 SafeMath

为了防止这种情况，OpenZeppelin 创建了一个名为 [SafeMath](https://docs.openzeppelin.com/contracts/2.x/api/math) 的**库**，默认情况下可以防止这些问题。

但是在我们进入那个之前…什么是图书馆？

图书馆是一种特殊的不动产合同。它的用处之一是将函数附加到本地数据类型上。

例如，对于 SafeMath 库，我们将使用语法`using SafeMath for uint256`。SafeMath 库有 4 个功能— `add`、`sub`、`mul`和`div`。现在我们可以从`uint256`访问这些功能，如下所示:

```
using SafeMath for uint256;

uint256 a = 5;
uint256 b = a.add(3); *// 5 + 3 = 8*
uint256 c = a.mul(2); *// 5 * 2 = 10*
```

我们很快就会看到这些函数的作用。

# 让我们来看看 SafeMath 背后的代码:

```
library SafeMath {

  function mul(uint256 a, uint256 b) internal pure returns (uint256) {
    if (a == 0) {
      return 0;
    }
    uint256 c = a * b;
    assert(c / a == b);
    return c;
  }

  function div(uint256 a, uint256 b) internal pure returns (uint256) {
    *// assert(b > 0); // Solidity automatically throws when dividing by 0*
    uint256 c = a / b;
    *// assert(a == b * c + a % b); // There is no case in which this doesn't hold*
    return c;
  }

  function sub(uint256 a, uint256 b) internal pure returns (uint256) {
    assert(b <= a);
    return a - b;
  }

  function add(uint256 a, uint256 b) internal pure returns (uint256) {
    uint256 c = a + b;
    assert(c >= a);
    return c;
  }
}
```

首先我们有关键字`library`——库与`contract`相似，但是有一些不同。出于我们的目的，库允许我们使用`using`关键字，它会自动将库的所有方法附加到另一种数据类型上:

```
using SafeMath for uint;
// now we can use these methods on any uint
uint test = 2;
test = test.mul(3); // test now equals 6
test = test.add(5); // test now equals 11
```

注意，`mul`和`add`函数每个都需要 2 个参数，但是当我们声明`using SafeMath for uint`时，我们调用 on ( `test`)函数的`uint`会自动作为第一个参数传入。

让我们看看`add`背后的代码，看看 SafeMath 做了什么:

```
function add(uint256 a, uint256 b) internal pure returns (uint256) {
  uint256 c = a + b;
  assert(c >= a);
  return c;
}
```

基本上`add`只是像`+`一样加了 2 个`uint`，但是它也包含了一个`assert`语句来确保总和大于`a`。这保护我们免受溢出。

`assert`类似于`require`，如果为假，将抛出错误。`assert`和`require`的区别在于`require`会在功能失效时退还用户剩余的汽油，而`assert`不会。所以大多数时候你想在代码中使用`require`；`assert`通常在代码出现严重错误时使用(比如`uint`溢出)。

因此，简单地说，SafeMath 的`add`、`sub`、`mul`和`div`是进行基本的 4 种数学运算的函数，但是如果发生上溢或下溢，就会抛出错误。

# 在代码中使用 SafeMath。

为了防止溢出和下溢，我们可以在代码中寻找使用`+`、`-`、`*`或`/`的地方，并用`add`、`sub`、`mul`、`div`替换它们。