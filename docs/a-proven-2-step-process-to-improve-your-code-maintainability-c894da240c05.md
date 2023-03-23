# 提高代码可维护性的成熟的两步过程

> 原文：<https://betterprogramming.pub/a-proven-2-step-process-to-improve-your-code-maintainability-c894da240c05>

## 优化内聚性和耦合性，使您的代码更易于维护

![](img/ba9d530bc2b3b19fe95cdacc4c5ea4b4.png)

照片由[克比岛黑](https://unsplash.com/@cobyshimabukuro?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

> 在软件体系结构层次上，通过将系统分成子系统来降低问题的复杂性。人类理解几条简单的信息比理解一条复杂的信息更容易。”
> 
> —史蒂夫·麦康奈尔

软件架构经常被视为艺术作品，坦率地说，就像该领域的其他学科一样，它包含了创造力、科学和风格。

它类似于建筑吗？让我们看看。

想象设计一座摩天大楼；我想你会想要一个设计，包括一个别致的入口，一个友好的大厅，安全和可居住的空间。此外，建筑必须支撑其重量，抵御风和地震。

同样，在设计软件时，你会想要直观的界面、干净和健壮的代码组件。设计还应该是可行的、模块化的、安全的，没有崩溃或泄漏的机会，并且最终在编辑它的一个组件时抵抗退化。

我想说有一些相似之处，尽管把你的代码库想象成一座摩天大楼有点吓人，但别担心！设计好的软件背后是有规则的，甚至是科学的。

在今天的文章中，我们将介绍获得更好的可维护性的两步过程。

为了确保将来重构一个模块不会影响其余的模块，您需要预测潜在的代码更改，这可不是一件容易的事情。

**内聚**和**耦合**如何来拯救？请继续阅读，寻找答案。

# **我们来介绍一下术语**

在进入流程之前，理解提到的术语**内聚**和**耦合**非常重要。所以我们花几秒钟简单介绍一下。

假设您有一个模块化设计，将功能划分为离散的可扩展和可重用的模块。

**内聚性**是衡量一切与模块目的相关程度的指标。

**耦合**是模块对其他模块或外部资源的依赖程度。

让我们开始吧。

# 第一步:**最大化凝聚力**

内聚描述了模块内的关系，确保给定模块内的每个元素都与其目的相关是至关重要的。

我们来看一个高内聚的类的例子。

一个“House”类应该包含一扇门、一组窗户和一组房间。

```
House
-----
Door _door;
Array<Window> _windows;
Array<Room> _rooms;
```

让我们给我们的“House”类添加一个地址。

```
House
-----
Door _door;
Array<Window> _windows;
Array<Room> _rooms;
String _address;
```

现在，一个问题将会出现。`_address`有哪些要素？以及客户如何检查它的正确性。

假设`_address`由街道名称、城市和邮政编码组成。

第一种选择:

```
House
-----
Door _door;
Array<Window> _windows;
Array<Room> _rooms;
String _address;
-----
boolean ValidateAdress();
```

在这个选项中，`House` 应该实现函数`ValidateAdress()`，这与`House`作为一个类的目的并不真正相关。有更好的选择吗？

为了达到最大的内聚性，您应该为`Address`添加一个新的类。

第二种选择:

```
Address
-------
String _street;
String _city;
Long _postalCode;
-------
boolean ValidateAdress();
```

结果将是:

```
House
-----
Door _door;
Array<Window> _windows;
Array<Room> _rooms;
Address _address;
```

在该选项中，`House` 将不包含任何与其用途无关的逻辑。仅此而已。已经达到了最大的凝聚力。

# 步骤 2:最小化耦合

内聚显示模块内部的关系，**耦合显示模块之间的关系。如果你尽可能地减少模块之间的依赖，那将是最好的。**

现在让我们看看如何最小化耦合。

让我们给我们设计的`House` 加上一个`Garden` 。

```
Garden
------
Array<Tree> _trees;
Grass _grass;
```

现在让我们看看如何在不引起类之间耦合的情况下支持这些功能。

```
void WaterTheGarden();
void CutATree();
void CleanTheHouse();
void PrintTheAddress();
```

一种选择是在一个`House`中包含一个`Garden`和一个`Address`(**组合**)。因此，调用任何功能都必须通过`House`。

第一种选择:

```
House
-----
Door _door;
Array<Window> _windows;
Array<Room> _rooms;
Address _address;
Garden _garden;
-----
void WaterTheGarden();
void CutATree();
void CleanTheHouse();
void PrintTheAddress();
```

如果在`House`内部实现`CutATree()`，从`Garden`内部的`Tree` 数组中移除一个`tree` ，则`House`和`Garden`之间会发生**内容耦合**。

更好的方法是创建一个名为`Property`的新类，它将包含一个`House`、一个`Garden`和一个`Address`。

第二种选择:

```
Property
-----
House _house;
Address _address;
Garden _garden;
-----
void WaterTheGarden();
void CutATree();
void CleanTheHouse();
void PrintTheAddress();
```

使用最后一种方法。另外，在相应的类中实现上述所有功能将把**与**的耦合降到最低。

总之，看看既有高内聚又有松耦合的最终设计，很明显，编辑或重构其中一个类`House`、`Address`或`Garden`不会影响其他两个类。

**正交性**是一个更高级的数学术语，描述了软件设计中的这种方法**。**

如果你不是一个数学迷，我想你可能想跳过下一部分，直接跳到外卖。

接下来是正交性发挥作用的数学证明。

# 什么是正交性？

正交性是垂直度的概括，垂直度是一个数学术语，表示两个向量的点积是否等于 0。

为了增加一些清晰度，在几何学中，如果两个欧几里得向量是垂直的，即它们形成直角(90 度)，则它们是正交的。

什么是点积？

矢量`a`的大小由`‖a‖`表示

```
‖a‖ = ∑ an² = a1² + a2² + ,..., an²
```

两个矢量`a`和`b`的点积定义为

```
a·b = ‖a‖ * ‖b‖ * cos(*θ)*
```

`*θ*` 是`a`和`b`之间的角度。

如果两个向量的点积等于 0，则这两个向量是正交的。

```
a·b = ‖a‖ * ‖b‖ * cos(*θ)* = 0
```

因为

```
cos(90*)* **= 0**
```

如果两个向量成直角(90 度)，它们的点积等于 0。无论我们赋予`a`和`b`什么值，或者它们的大小是多少。

通过尝试这个很酷的点积可视化工具，您可以更好地理解点积:

[](https://www.geogebra.org/m/Yu6869By) [## 点积可视化

### 点产品:交互式调查

www.geogebra.org](https://www.geogebra.org/m/Yu6869By) 

现在正交性终于可以用软件术语来解释了。想象一下。

假设我们把两个向量`a`和`b`想成`module-a`和`module-b`。并且点积作为由于编辑`module-b`而在`module-a`中需要的工作量。

然后假设`θ`是`module-a`对`module-b`的依赖程度，其中`θ=0`是这种情况，他们非常依赖(低内聚高耦合)，而`θ=90`是他们完全不依赖(高内聚低耦合)。

我们可以得知，如果两个模块是正交的 **(** 不依赖 **)** ，那么`module-a`中由于编辑`module-b`而需要的工作量(点积)为 0。

那有多厉害？！

# 外卖

代码是一个不断进化的生命体；并不断进行修改和修订。

除了文档化、简单性和一致性等常见的东西，区分“不可维护”和“可维护”代码的一个东西是缺乏正交性，即低内聚和高耦合。

在处理下一个项目时尝试此过程；正交性将有助于它长寿和繁荣。

> “……通过适当的设计，这些功能价格低廉。这种方法很艰难，但不断取得成功。”
> 
> —丹尼斯·里奇

资源:

[](https://www.geogebra.org/m/Yu6869By) [## 点积可视化

### 点产品:交互式调查

www.geogebra.org](https://www.geogebra.org/m/Yu6869By)