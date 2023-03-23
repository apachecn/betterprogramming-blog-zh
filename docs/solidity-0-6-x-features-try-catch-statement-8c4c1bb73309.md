# Solidity 0.6.x 特性:Try/Catch 语句

> 原文：<https://betterprogramming.pub/solidity-0-6-x-features-try-catch-statement-8c4c1bb73309>

## 最初发表于 [Solidity 以太坊基金会博客](https://solidity.ethereum.org/2020/01/29/solidity-0.6-try-catch/)

![](img/28d1dcb3760316cabad7e1844907a2ce.png)

吉姆·威尔逊在 [Unsplash](https://unsplash.com/s/photos/require?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

0.6.0 中引入的 [try/catch 语法可以说是自 v0.4.22 中发布`revert`和`require`的原因字符串以来，错误处理能力在可靠性方面的最大飞跃。从 v0.5.9](https://solidity.readthedocs.io/en/latest/control-structures.html#try-catch) 开始，`try`和`catch`都是保留关键字[，现在我们可以使用它们来处理`external`函数调用中的失败，而无需回滚整个事务(被调用函数中的状态更改仍会回滚，但调用函数中的不会)。](https://solidity.readthedocs.io/en/v0.5.9/miscellaneous.html#reserved-keywords)

我们正在远离事务生命周期中最纯粹的要么全有要么全无的方法，这不符合我们经常想要的实际行为。

# 处理外部呼叫失败

try/catch 语句允许您对失败的*外部*调用和*契约创建*调用做出反应，因此您不能将其用于*内部*函数调用。注意，要用 try/catch 在同一个契约中包装一个公共函数调用，可以通过用`this`调用函数在外部进行。

下面的示例演示了在协定创建可能失败的工厂模式中如何使用 try/catch。下面的`CharitySplitter`契约在其构造函数中需要一个强制的地址属性`_owner`。

```
pragma solidity ^0.6.1;contract CharitySplitter {
    address public owner;
    constructor (address _owner) public {
        require(_owner != address(0), "no-owner-provided");
        owner = _owner;
    }
}
```

有一个工厂契约——`CharitySplitterFactory` ——用于创建和管理`CharitySplitter`的实例。在工厂中，我们可以将`new CharitySplitter(charityOwner)`包装在一个 try/catch 中，作为当构造函数可能因为传递一个空的`charityOwner`而失败时的安全措施。

```
pragma solidity ^0.6.1;
import "./CharitySplitter.sol";
contract CharitySplitterFactory {
    mapping (address => CharitySplitter) public charitySplitters;
    uint public errorCount;
    event ErrorHandled(string reason);
    event ErrorNotHandled(bytes reason);
    function createCharitySplitter(address charityOwner) public {
        try new CharitySplitter(charityOwner)
            returns (CharitySplitter newCharitySplitter) 
        {
            charitySplitters[msg.sender] = newCharitySplitter;
        } catch {
            errorCount++;
        }
    }
}
```

请注意，使用 try/catch，只捕获外部调用本身内部发生的异常。表达式内部的错误不会被捕获——例如，如果`new CharitySplitter`的输入参数本身是内部调用的一部分，它引发的任何错误都不会被捕获。

证明这种行为的一个例子是修改过的`createCharitySplitter`函数。这里，`CharitySplitter`构造函数的输入参数是从另一个函数`getCharityOwner`中动态获取的。如果这个函数恢复，在这个例子中是用`"revert-required-for-testing"`，那么 try/catch 语句不会捕捉到它。

```
function createCharitySplitter(address _charityOwner) public {
    try new CharitySplitter(getCharityOwner(_charityOwner, false))
        returns (CharitySplitter newCharitySplitter)
    {
        charitySplitters[msg.sender] = newCharitySplitter;
    } catch (bytes memory reason) {
        ...
    }
}
function getCharityOwner(address _charityOwner, bool _toPass)
        internal returns (address) {
    require(_toPass, "revert-required-for-testing");
    return _charityOwner;
}
```

# 正在检索错误消息

我们可以进一步扩展`createCharitySplitter`函数中的 try/catch 逻辑，以便在失败的`revert`或`require`发出错误消息时检索错误消息，并在事件中发出错误消息。有两种方法可以实现这一点:

## 1.使用'`catch Error(string memory reason)'`

```
function createCharitySplitter(address _charityOwner) public {
    try new CharitySplitter(_charityOwner) returns (CharitySplitter newCharitySplitter) 
    { 
        charitySplitters[msg.sender] = newCharitySplitter;
    }
    catch Error(string memory reason) 
    {
        errorCount++;
        CharitySplitter newCharitySplitter = new
            CharitySplitter(msg.sender);
        charitySplitters[msg.sender] = newCharitySplitter;
        // Emitting the error in event
        emit ErrorHandled(reason);
    }
    catch 
    {
        errorCount++;
    }
}
```

这将在失败的构造函数要求错误上发出以下事件:

```
CharitySplitterFactory.ErrorHandled(
    reason: 'no-owner-provided' (type: string)
)
```

## 2.使用'`catch (bytes memory reason)'`

```
function createCharitySplitter(address charityOwner) public {
    try new CharitySplitter(charityOwner)
        returns (CharitySplitter newCharitySplitter) 
    {
        charitySplitters[msg.sender] = newCharitySplitter;
    } 
    catch (bytes memory reason) {
        errorCount++;
        emit ErrorNotHandled(reason);
    }
}
```

这将在失败的构造函数要求错误上发出以下事件:

```
CharitySplitterFactory.ErrorNotHandled(
  reason: hex'08c379a0000000000000000000000000000000000000000000000000000000000000002000000000000000000000000000000000000000000000000000000000000000116e6f2d6f776e65722d70726f7669646564000000000000000000000000000000' (type: bytes)
```

上述两种检索错误字符串的方法产生了类似的结果。不同之处在于，第二种方法不对错误字符串进行 ABI 解码。第二种方法的优点是，如果 ABI 解码错误字符串失败或者没有提供原因，也会执行第二种方法。

# 未来计划

我们计划发布对错误类型的支持，这意味着我们将能够以类似于事件的方式声明错误，从而允许我们捕捉不同类型的错误，例如:

```
catch CustomErrorA(uint data1) { … }
catch CustomErrorB(uint[] memory data2) { … }
catch {}
```