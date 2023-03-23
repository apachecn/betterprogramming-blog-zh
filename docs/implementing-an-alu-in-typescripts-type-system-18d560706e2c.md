# 在 TypeScript 的类型系统中实现 ALU

> 原文：<https://betterprogramming.pub/implementing-an-alu-in-typescripts-type-system-18d560706e2c>

## 使用您自己的类型计算斐波纳契数

![](img/5821d668f5621a7a667d1db48147cb92.png)

稳定扩散生成

在本文中，我们给出了一个 [ALU](https://en.wikipedia.org/wiki/Arithmetic_logic_unit) 的示例实现。我们将实现的一些操作是`bitwise`操作，如`shifts` 和`arithmetic`操作，如`addition`和`subtraction`。

最后，我们将看到如何使用这些操作来实现一个可以计算`fibonacci`数的类型。

我们使用的所有构造都是 ts 类型，所有计算都是在构建时完成的。没有 JS 运行我们的类型。我们依靠打字系统来做计算。

记住这一点，让我们开始吧。

# 基本类型

我们需要一种方法来表示一点。为此，我们从表示一个位可能具有的两种状态(0 或 1)开始。

下一步是将一个位定义为两个状态的联合类型。

位的类型声明

有了这个，我们可以继续定义一个字节。为此，我们将使用[元组](https://www.typescriptlang.org/docs/handbook/2/objects.html#tuple-types)。

```
type Byte = [Bit, Bit, Bit, Bit, Bit, Bit, Bit, Bit];
```

这是 TS 类型系统中一个字节的一个非常简单的表示，但是它足以开始我们的 ALU 操作。

我们现在可以定义一些可以使用的助手类型。

```
type ZERO_BYTE = [Zero, Zero, Zero, Zero, Zero, Zero, Zero, Zero]
type ONE_BYTE  = [Zero, Zero, Zero, Zero, Zero, Zero, Zero, One]
```

# 位运算

有了 bit 的类型，让我们看看如何实现 bit 的`not`、`and`、`or`和`xor`。

为此将大量使用[约束泛型](https://www.typescriptlang.org/docs/handbook/2/generics.html#generic-constraints)和[条件类型](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html)。

位运算

让我们看一下实现细节:

比特否定是最简单的一种。我们约束`T`为`Bit`，然后检查它是否为`Zero`。如果是这种情况，我们将类型定义为`One`。否则，我们将类型定义为`Zero`。

这意味着我们现在可以像这样使用`BitNot`。

```
type OneNegated = BitNot<One>
```

而那个类型实际上会是`Zero`。

我们做一些类似于`BitAnd`的事情，但是这一次我们需要定义它有两个通用类型:`LHS`和`RHS`。我们使用条件类型来实现类型系统内部的分支。定义`AND`最简单的方法是检查它们是否都扩展了`One`并在那种情况下返回`One`。

这与下面的代码非常相似:

```
if (LSH === One) {
 if (RHS === One) {
   return One 
 } else {
   return Zero
 }
} else {
 return Zero
}
```

由于条件类型的性质，所有分支都必须解析为一种类型。这就是我们有`Zero`出现两次的原因。这使得这个实现更加冗长。

我们为`BitOr`做类似的事情。

`BitXor`有点冗长，因为没有办法简化评估，所以我们必须考虑所有四种可能性。

# 字节操作

我们现在已经拥有了实现字节操作所需的一切。

从现在开始，我们将严重依赖[在条件类型](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html#inferring-within-conditional-types)内进行推断。我们需要一种方法来遍历元组并将操作应用到每个组件。实现这一点的一种方法是对条件类型使用泛型，并只推断元组的一部分。我们可以做类似的事情:

```
type Walkable<T extends Byte> = T extends [infer U extends Bit, ...infer R extends Bit[]] ? U : []
```

这是很难一次完成的，所以让我们看一下。

我们正在指定`T extends Byte`。这意味着它是由 8 个`Bit`组成的元组。

下一步是使用 conditional with `extends`来拆分元组，并能够引用元组的头。在本文中，`the head of the tuple`指的是`Byte`中的第一个`Bit`。

我们可以在条件真分支上引用类型`U`。这同样适用于`R`，它是一个元组类型，包含所有剩余的`Bits`。

我们现在可以实现的最简单的操作是`ShiftLeft`和`ShiftRight`。

左移和右移

让我们仔细看看`ShiftLeft`是怎么回事。我们正在为最左边的`Bit`推断类型，然后我们创建一个新的类型，忽略它，解包`R`并添加`Zero`。这实质上是将最低最高 7 位向左移动。

现在我们可以这样使用它:

```
ShiftLeft<[Zero, Zero, Zero, One, Zero, Zero, Zero, One]>
```

或者使用我们之前定义的助手:

```
ShiftLeft<ONE_BYTE>
```

那将会是:

```
[Zero, Zero, One, Zero, Zero, Zero, One, Zero]
```

类似的事情也发生在`ShiftRight`身上。

# 字节非和异或

为字节实现`not`和`xor`使用了类似的技术，但是有点复杂。

之所以更多地涉及到`not`和`xor`，与一个字节的`head`和`tail`拆分后，我们需要对`tail`再次应用同样的规则有关。问题是`tail`不再是`Byte`，因为它是一个只有`7`元素的`tuple`。解决这个问题的方法是使用另一个允许任何`Bit[]`的`type`定义。

下面是`Not`的一个小实现:

ByteNot 实现

这里发生的是，我们使用`BitNot`来翻转磁头。然后，我们再次应用该类型，但这次是针对字节中的剩余位。这实际上是尾递归的一种形式，我们将在未来的操作中更多地使用它。

我们可以这样使用它:

```
type Flipped = ByteNote<[Zero, One, One, Zero, One, One, One, Zero]>
```

而`Flipped`实际上将是:

```
[One, Zero, Zero, One, Zero, Zero, Zero, One]
```

XOR 有点复杂，因为我们需要 2 个`Byte`来创建运算。但是我们可以使用和以前一样的概念:

*   受约束的泛型
*   条件类型
*   使用推断和尾部递归遍历字节

因为我们有两个操作符，并且我们需要条件类型来推断`Byte`的`head`和`tail`，所以代码更难阅读。但是使用的技术是一样的。

异或运算

关于实现需要注意的一点是，我们通过返回一个空数组`[]`来停止递归。这样做的原因是，如果在`extends`子句中`TS`不能再进行推理，它将选择另一条路线。在这种情况下，使用`empty tuple`作为类型。

# 算术运算

我们现在已经具备了开始实现算术运算的所有条件。我们的目标是能够得到`add`和`subtract`字节。

## 增量

我们首先可以建立的是`increment`的能力。这意味着我们想要将`1`添加到`Byte`中。

在实现这些操作时，您会经常看到这种情况:

```
T extends [...infer U extends Bit[], infer R extends Bit]
```

这一次，我们需要从尾部开始处理`Byte`，所以我们将使用相同的`infer`技术，但是我们将捕获最后一个`Bit`，然后对剩余的位使用递归。

现在我们需要检查`R`是否有值`One`。

为什么这很重要？

如果`R`是`Zero`，那么我们把它改成`One`，打开`U`，我们就完成了。另一方面，如果`R`是`One`，那么我们需要将其改为`Zero`，然后`continue`将`One`添加到下一个`Bit`。这是递归部分。

```
T extends [...infer U extends Bit[], infer R extends Bit] ? R extends One ? [...PartialAddOne<U>, Zero] : [...U, One] : []
```

完整的实现如下:

增量

我们现在可以这样使用它:

```
type ONE = AddOne<[Zero, Zero, Zero, Zero, Zero, Zero, Zero, Zero]>
```

结果将是:

```
[Zero, Zero, Zero, Zero, Zero, Zero, Zero, One]
```

请注意，我们最后得到的数字是它的`Byte`表示，而不是我们习惯意义上的数字。

我们现在可以生成一个正数(实际上，我们可以生成任何`Byte`表示，因为它是否为正数是一个语义问题)。

所有这些现在都是有效的操作:

```
type ZERO = [Zero, Zero, Zero, Zero, Zero, Zero, Zero, Zero]type ONE = AddOne<ZERO>
type TWO = AddOne<ONE>
type THREE = AddOne<AddOne
type FOUR = AddOne<THREE>
```

## 用进位相加

在我们开始实现 2 个`Bytes`之间的完整`add`之前，我们需要能够保持`bits`之间的`carry`加法。

alu 通常必须携带可用于此目的的标志(`CF`)。但是从这个角度来看，`TS`并没有全局状态。所以我们不能把它存放在某个地方，以后再用。我们需要总是能够传递它。

为此，我们创建了一些类型来帮助我们:

```
type CarryFlag = Bit;
type BitWithCarry = {
    cf: CarryFlag
    value: Bit
}
```

现在我们可以在需要的时候参考`BitWithCarry`旗。

就像我们实现`Bit`逻辑操作一样，这样我们可以稍后通过使用`tail recursion`在`Byte`上使用它们，我们现在将做同样的事情。

我们需要实现将 2 位相加的能力，存储结果`Bit`和`carry value`的值。

我们可以查看该操作的真值表来指导我们的实现:

```
+------+------+------+------+------+
| Bit1 | Bit2 |  CF  | BitR |  CR  |
+------+------+------+------+------+
| 1    | 1    | 1    | 1    | 1    |
+------+------+------+------+------+
| 1    | 1    | 0    | 0    | 1    |
+------+------+------+------+------+
| 1    | 0    | 1    | 0    | 1    |
+------+------+------+------+------+
| 1    | 0    | 0    | 1    | 0    |
+------+------+------+------+------+
| 0    | 1    | 1    | 0    | 1    |
+------+------+------+------+------+
| 0    | 1    | 0    | 1    | 0    |
+------+------+------+------+------+
| 0    | 0    | 1    | 1    | 0    |
+------+------+------+------+------+
| 0    | 0    | 0    | 0    | 0    |
+------+------+------+------+------+
```

我们现在需要在`TS`中使用条件类型来实现这一点。这是一项艰巨的任务，但我们最终得到了类似的结果:

有了这些，我们可以开始为`bytes`实现`add`操作。

我们将使用与之前相同的技术:

*   推断最后一位
*   在最后一位使用`AddBitsWithCarry`
*   对头位使用辅助类型
*   将`cf`的值传递给辅助类型，因为其余的位需要它

我们最终会得到类似的结果:

ByteAdd 实现

需要注意的几件事:

*   我们在这里使用了通用参数的默认值`CH extends CarryFlag = Zero`。在我们的例子中，这是强制性的，但是它确实使`PartialAdd`更加灵活。
*   当递归引用类型时，我们调用`AddBitsWithCarry`两次。首先，获取`value`，然后获取`cf`，即使我们传递的是相同的参数。这也是因为在`TS`类型系统中没有全局状态。

## 减法和乘法

现在我们已经完成了基础工作，实现其他操作就容易多了。

对于减法，我们将使用 [2 的补码](https://en.wikipedia.org/wiki/Two%27s_complement)。简而言之，我们需要否定`Byte`然后再否定`add`一个。对于我们拥有的所有类型，这非常容易做到:

```
type Byte2Complement<T extends Byte> = AddOne<ByteNot<T>>
```

现在减法变得非常容易。我们需要计算`RHS`的二进制补码，然后将其加到`LHS`中:

```
type Subtract<LHS extends Byte, RHS extends Byte> = Add<LHS, Byte2Complement<RHS>>
```

作为一个方便的操作，我们可以实现`decrement`:

```
type SubtractOne<LHS extends Byte> = Subtract<LHS, ONE_BYTE>
```

我们现在可以着手实现`multiply`了。我们需要把乘法看作是重复的`addition`。我们将再次执行`recursion`，并递减`LHS`，直到它达到`One`。

到达`One`是这个递归的停止条件。

我们还需要迎合`LHS`实际上是`Zero`的情况，因此我们有一个特殊的条件。最后，我们以这个结束:

```
type Mul<
  LHS extends Byte,
  RHS extends Byte,
  Result extends Byte = RHS
> = ONE_BYTE extends LHS
  ? Result
  : ZERO_BYTE extends LHS
  ? ZERO_BYTE
  : Mul<SubtractOne<LHS>, RHS, Add<Result, RHS>>;
```

注意`Result`如何默认为`RHS`。这就是为什么我们涵盖了由于递归而停止和`LHS`实际上是`One`的两种情况。

现在我们可以这样使用它:

```
Mul<ONE_BYTE, ZERO_BYTE>
Mul<[Zero, Zero, Zero, Zero, Zero, Zero, One, Zero], [Zero, Zero, Zero, Zero, Zero, Zero, One, Zero]>
```

在第二种情况下，结果会是:

```
[Zero, Zero, Zero, Zero, Zero, One, Zero, Zero]
```

# 比较实现

如果没有一些比较实现，我们的 ALU 将是不完整的(实际上，我们也可以使用一些除法，但我们将在稍后进行)。

注意:所有的比较操作都是以无符号的方式完成的。

## 情商

我们可以实现的第一个比较，也是最简单的，是`EQ`。我们想看看 2 `Bytes`是否相等。我们可以通过从另一个中减去一个`Byte`并检查它是否为零来轻松做到这一点。在我们的语言中，我们可以这样写:

```
type EQ<LHS extends Byte, RHS extends Byte> = ZERO_BYTE extends ByteXOR<LHS, RHS> ? true : false
```

## LT(小于)

为了实现`LT`，我们首先需要一些帮助。实现这一点的主要思想是从左到右(从最高有效位到最低有效位)遍历字节，并决定不同的第一位。

当位相同时，当前操作为`undecided`。这意味着我们需要移动到下一对。

为了帮助完成这一步，将创建一个助手类型，它将返回`true`、`false`或`undecided`。这将对以后的打字有很大帮助。现在，让我们来看看实际情况:

```
type BitLTB<LHS extends Bit, RHS extends Bit> = Zero extends LHS
  ? One extends RHS
    ? true
    : "undecided"
  : One extends RHS
  ? "undecided"
  : false;
```

现在我们可以开始完整的实现了。想法和以前一样:

*   从两个操作数中推断头部和尾部
*   使用`BitLTB`来看看我们是否可以调用，然后我们是否可以返回那个值
*   如果值为`undecided`，则转到下一位

和以前一样，实现有点难以理解:

LT 实施

同样，因为我们没有全局状态，所以我们需要多次调用`BitLTB`。如果我们检查一下`boolean extends BitLTB`，我们可能会让这个更短一点。

现在我们可以这样使用它:

```
LT<ZERO_BYTE, ONE_BYTE> // will result in true
LT<ONE_BYTE, ZERO_BYTE> // will result in false
```

有了`LT`的实现，剩下的比较操作就很容易实现了:

## 长期演进

不外乎`LTE`或者`EQ`

```
type LTE<LHS extends Byte, RHS extends Byte> = true extends LT<LHS, RHS> ? true : EQ<LHS, RHS>
```

## 大型旅行车

仅仅是`LT`，但是交换了操作数。

```
type GT<LHS extends Byte, RHS extends Byte> = LT<RHS, LHS>
```

## GTE

同样的，但是有了`LTE`的帮助。

```
type GTE<LHS extends Byte, RHS extends Byte> = LTE<RHS, LHS>
```

# 分开

我们要实现的最后一个操作是带余数的除法。

我们将使用`LT`和`Subtraction`来完成这个任务。我们将返回一个有两个数量的类型:

*   商
*   剩余物

我们确实需要考虑`division by zero`。因此，我们将对后续的`subtraction`使用内部类型，而对`zero`检查使用主类型。我们最终会得到这样的结果:

```
type DivInternal<LHS extends Byte, RHS extends Byte, Q extends Byte = ZERO_BYTE> = true extends LT<LHS, RHS> ? {
    q: Q,
    r: LHS
} : DivInternal<Subtract<LHS, RHS>, RHS, AddOne<Q>>type Div<LHS extends Byte, RHS extends Byte> = ZERO_BYTE extends RHS ? "Division by 0" : DivInternal<LHS, RHS>
```

主`Div`类型检查`RHS`是否为`Zero`并返回一个`"Division by 0"`字符串。

在`RHS`不是`0`的情况下，我们遵从`DivInternal`。它从`LHS`中减去`RHS`，直到`LHS`小于`RHS`。我们知道这是可以实现的，因为`RHS`不为零。我们现在可以这样使用它:

```
Div<TWO, ONE> // we get {q: TWO, r: ZERO_BYTE}
Div<ONE, ZERO_BYTE> // we get "Division by 0"
Div<[Zero, Zero, Zero, Zero, One, Zero, One, Zero], TWO> // we get {q:[Zero, Zero, Zero, Zero, Zero, One, Zero, One], r: ZERO_BYTE}
```

# 斐波那契实现

所有这些把我们带到了可以实现`fibonacci`数字的地方。

这种实现的思想建立在与以前相同的原则之上:

*   泛型约束类型
*   条件类型

我们定义了一个有三个泛型参数的`FIBONACCI`类型

*   `C`当前指数在计算中
*   `A`序列中的当前编号
*   `B`序列中的前一个数字

然后我们只需`decrement C`计算一个新的`A`作为`A`和`B`的和，将新的`B`改为旧的`A`。我们还需要有一个停止条件。这在`C`为 1 或 2 时表示。让我们来看看实际情况:

斐波纳契

有了这些，我们可以通过传递数字的二进制表示来计算`fibonacci`序列号。

```
FIBONACCI<[Zero, Zero, Zero, Zero, Zero, One, One, One]>
```

它将计算斐波纳契数列中的第 7 个值，即:

```
[Zero, Zero, Zero, Zero, One, One, Zero, One] // 13
```

# 结束语

我们展示了一种使用 TypeScript 的类型系统实现简单 ALU 的方法。

我们使用的主要技术有:

*   泛型约束类型
*   实现分支的条件泛型
*   引用自身来实现循环的条件泛型(递归)

在这个 [GitHub repo](https://github.com/hades2510/TS-TypingSystem-ALU) 中可以找到它的完整实现。回购协议还包括一系列用于验证操作的测试。

我希望你喜欢读这篇文章。下一篇文章再见。