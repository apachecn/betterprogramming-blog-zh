# Solidity 0.6.x 特性:继承

> 原文：<https://betterprogramming.pub/solidity-0-6-x-features-inheritance-5d760dc1ad22>

## 了解 Solidity 中的继承，以及如何使用它来构建智能契约

![](img/374fec04e09318d1818f0fd9f03809b2.png)

照片由[克雷格·赛伯特](https://unsplash.com/@contrastband?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](/s/photos/twins?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

类似于 Solidity 中的面向对象编程——一种面向契约的语言——继承和多态特性被广泛采用，并对语言的发展至关重要。几乎没有任何 Solidity 开发人员没有在他们的合同中使用这些语言特性来分离逻辑和增加代码重用。在语言的 0.6 版本中，除了引入接口继承和不允许危险的状态变量隐藏之外，引入的主要改进是使现有规则显式化。编译器继续使用 C3 线性化——参见[坚实度文档](https://solidity.readthedocs.io/en/latest/contracts.html#inheritance)了解继承。

# 显式虚拟和 O `verride`

默认情况下，函数不再是虚拟的。这意味着对非虚函数的调用将总是执行该函数，而不管继承层次结构中的任何其他契约。这减少了 0.5 版本中存在的不确定性，在 0.5 版本中，所有函数都是隐式虚拟的，允许它们在继承结构中被覆盖。这在大型继承图中尤其危险，因为这种模糊性会导致意想不到的行为和错误。

例如，在下面的契约`C`中，调用`setValue`将调用来自契约`B`的最派生的实现。然而，从实现来看，这并不明显。

```
**pragma** solidity **^**0.5.17;
**contract** A {
    **uint** **public** x;
    **function** setValue(**uint** _x) **public** {
        x **=** _x;
    }
}

**contract** B {
    **uint** **public** y;
    **function** setValue(**uint** _y) **public** {
        y **=** _y;
    }
}

**contract** C **is** A, B {
}
```

对于以上示例的 0.6 版本，编译器会引发一个类型错误:`Derived contract must override function "setValue". Two or more base classes define functions with the same name and parameter types`。上面是一个多重继承的例子，同一个函数从多个基类继承而来:`A`和`B`。在这种情况下，它必须被重写，并且基必须在重写说明符中列出。需要注意的是`override(A,B)`中的顺序并不重要——具体来说，它不会改变`super`的行为。这仍然由继承图的 C3 线性化决定，该线性化由`contract C is A, B { ... }`声明中的顺序决定。

```
**pragma** solidity **^**0.6.10;
**contract** A {
    **uint** **public** x;
    **function** setValue(**uint** _x) **public** **virtual** {
        x **=** _x;
    }
}

**contract** B {
    **uint** **public** y;
    **function** setValue(**uint** _y) **public** **virtual** {
        y **=** _y;
    }
}

**contract** C **is** A, B {
    **function** setValue(**uint** _x) **public** **override**(A,B) {
        A.setValue(_x);
    }
}
```

请注意，您只能覆盖标记为`virtual`的函数。此外，任何覆盖函数必须标记为`override`。如果它再次被覆盖，它也必须被标记为`virtual`。

函数是隐式的，所以当实现一个接口时，你必须在实现中显式地覆盖它的函数。这个设计[正在这里](https://github.com/ethereum/solidity/issues/8281)进行讨论。

值得注意的是，关键字`super`的作用和以前一样。它调用扁平继承层次结构中更高一级的函数。同样没有变化:`super`仍然不允许在`external`功能上使用。

# 接口可以继承

此功能是 0.6 版的新增功能，允许接口继承。产生的接口是契约必须实现的所有继承接口功能的组合。

```
**pragma** solidity **^**0.6.10;
**interface** X {
    **function** setValue(**uint** _x) **external**;
}

**interface** Y **is** X {
    **function** getValue() **external** **returns** (**uint**);
}

**contract** Z **is** Y {
    **uint** x;
    **function** setValue(**uint** _x) **external** **override** { x **=** _x; }
    **function** getValue() **external** **override** **returns** (**uint**) { **return** x; }
}
```

注意，如果合同没有实现所有功能，则必须标记为`abstract`。

```
**pragma** solidity **^**0.6.10;
**abstract** **contract** Z **is** Y {
    **uint** x;
    **function** setValue(**uint** _x) **external** **override** { x **=** _x; }
}
```

# 抽象合同

在 0.5 版中，没有实现其所有功能的契约被编译器隐式地抽象为:

```
**pragma** solidity **^**0.5.17;
**contract** X {
    **function** setValue(**uint** _x) **public** **virtual**;
}
```

对于 0.6，这种区别必须是明确的，编译器会产生错误`contract X should be made abstract`。否则:

```
**pragma** solidity **^**0.6.10;
**abstract** **contract** X {
    **function** setValue(**uint** _x) **public** **virtual**;
}
```

# 公共变量更安全地覆盖外部函数

虽然这个特性在 0.6 之前就存在了，但是现在增加了检查变量的 getter 函数(由编译器生成)是否与被覆盖的外部函数的参数和返回类型相匹配的功能，这样就更安全了。在 0.5 版中，可能允许有所不同，如下例所示:

```
**pragma** solidity **^**0.5.17;
**interface** A
{
    **function** f() **external** **pure** **returns**(**uint8**);
}

**contract** B **is** A
{
    **uint256** **public** f **=** 257;
}
```

契约`A`对基础契约`B`的调用将返回`1`，因为`257`的值在转换为`uint8`时溢出。

有了 0.6，现在上面生成了`TypeError: Overriding public state variable return types differ`，迫使我们解决类型冲突，避免溢出:

```
**pragma** solidity **^**0.6.10;
**interface** A
{
    **function** f() **external** **pure** **returns**(**uint256**);
}

**contract** B **is** A
{
    **uint256** **public** **override** f **=** 257;
}
```

注意`public`状态变量只能覆盖`external`函数，并且不允许变量覆盖`internal`或`public`函数。

# 没有状态变量阴影

在 0.5 版中，编译器允许继承同名的可见状态变量，只有一些静态分析工具才会提出这个问题。下面的例子说明了这种设计的问题:

```
**pragma** solidity **^**0.5.17;
**contract** A {
    **uint** **public** x;

    **function** setValue1(**uint** _x) **public** { x **=** _x; }
}

**contract** B **is** A {
    **uint** **public** x;

    **function** setValue2(**uint** _x) **public** { x **=** _x; }
}
```

在这个上下文中，引用`x`的契约`A`中的函数正在使用它自己的状态变量实例- `A.x`。对于`B`，是`B.x`。因此调用`B.setValue2(100)`的结果是`B.x`将被设置为`100`，而调用`B.setValue1(200)`将`A.x`设置为`200`。

在 0.6 版本中，这是被禁止的，并且会引发一个编译器`DeclarationError: Identifier already declared`错误。