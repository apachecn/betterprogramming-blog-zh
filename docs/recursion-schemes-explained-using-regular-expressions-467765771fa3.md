# 使用正则表达式解释递归方案

> 原文：<https://betterprogramming.pub/recursion-schemes-explained-using-regular-expressions-467765771fa3>

## 变形和同态的教程

![](img/f2656051970000eaf2d7d49ad1179feb.png)

由史蒂芬·米勒拍摄的照片

递归方案是一种抽象递归的方式。有些人认为没有递归方案的函数式编程等同于没有循环的命令式编程，但是有语句。

> 正如使用`while`和`for`循环而不是`goto`给命令式控制流带来了结构和协调，使用递归方案而不是手写递归给递归计算带来了相似的结构。这一观点非常重要，我将重复一遍:递归模式对于惯用的函数式编程来说就像`for`和`while`对于惯用的命令式编程一样重要。— [帕特里克·汤普森](https://t.co/3akhET7qPk)

我没有这么强烈的意见。我认为递归方案非常有趣，而且我也对 Patrick Bahr 与树自动机的联系有偏见，因为我已经用树自动机做了相当多的工作，看到它被应用到实践中对我来说比对你来说更令人兴奋。

![](img/c95bd33bc1566edea99ac9c20bddc9d4.png)

米先生试图递归地召唤米先生来完成不可能完成的任务——从杰瑞的高尔夫比赛中拿掉两杆——正如在[里克和莫蒂](https://en.wikipedia.org/wiki/Rick_and_Morty)中看到的

与我们习惯的递归不同，这些递归方案限制了我们对将在函数中应用的递归类型的明确性。递归模式有助于可读性(最终),并允许递归求值函数的更简单实现，但在定义数据类型时确实需要一些额外的样板文件，并需要一些实践来学习如何使用它们。

递归有几种类型，所以我们有几个方案来涵盖它们。在本文中，我将尝试解释两种最常见的递归方案:

*   变形:一个简单的自底向上递归的别称。
*   同态性:另一个稍微聪明一点的需要一些堆栈信息的自底向上递归的别称。还是挺简单的。

***注意*** *:本文要求对 Haskell 有所了解，至少达到实现一个函子。*

# 正则表达式的导数算法

解释递归方案需要一个例子。为此，我选择了正则表达式的导数算法，我在之前的文章中已经介绍过了。

在这篇文章中，我将只包括使用普通递归的实现代码，这样我们就可以看到添加递归方案会有什么不同。这里是完整的正则表达式匹配算法，使用我们习惯的递归。

给定一个字符作为输入，derivative 或`deriv`函数返回要匹配的正则表达式。我们使用`foldl`对输入字符串进行循环，以获得在我们用完整个字符串后剩下的匹配正则表达式。然后我们使用`nullable`函数检查得到的正则表达式是否与空字符串匹配，以了解使用了整个字符串的正则表达式是否与该字符串匹配。

从技术上来说，`foldl`已经抽象了一些递归，但是我们将通过递归方案把这种抽象提升到另一个层次。

# 一种味道

在我们深入之前，让我们先了解一下我们要去哪里。`nullable`函数是一个完美的变形，因为它只做自底向上的递归。自底向上递归是指函数的结果仅依赖于应用于其子函数的递归函数的结果，而不依赖于递归函数中传递的任何中间结果。我们将用下面的等价函数替换上面原始实现中的`nullable`函数:

注意`nullableAlg`函数不需要对自己做任何递归调用。在本文中，我们将解释这是如何实现的，以及:

*   什么是`FAlgebra`
*   为什么会有一种新的类型叫做`RegexF`
*   `cata`函数如何执行自底向上的递归

# 什么函子

我们的递归方案需要一个地方来存储递归过程中的中间结果。我们需要一个容器来存放这些结果。我们知道仿函数是一个很好的容器，所以我们将通过参数化把我们的`Regex`数据类型变成仿函数。这个参数可以在我们的递归算法中存储各种中间结果。

我们首先将`Regex`重命名为`RegexF`，其中`F`代表函子。我们对所有的递归`Regex`字段进行参数化。代码如下:

如果你回头看一下`Regex`的原始定义，你会发现我们已经用`r`替换了所有递归出现的`Regex`。我们将参数`r`命名为递归或结果。这个`r`将用于存储布尔结果，因为我们使用 catamorphism 对可空函数进行自底向上的递归，但很快会有更多内容。

首先，我们有一个问题。我们必须选择将`r`设置为什么参数，以使用`RegexF`的定义恢复我们原来的`Regex`。

*   如果我们把`r`做成`Bool`，那就只能造出像`EmptyString`这样非常小的表情，其他的就更没意义了:`Concat True False`。
*   如果我们选择`r`为`Regex`，我们得到`RegexF Regex`，这将工作，但是我们失去了递归仿函数的属性。
*   我们可以尝试选择`r`为`RegexF`，然后我们得到`RegexF RegexF`，这是接近的，但是现在我们要求另一个`RegexF`为第二个`RegexF`的参数，所以我们得到`RegexF (RegexF RegexF)`，但是这是一个永无止境的问题。在某个时刻，我们希望这个递归结束。
*   我们可以尝试两个层次的`RegexF`，然后我们得到`RegexF (RegexF (RegexF ()))`，但是这样我们只能表示两个层次深的正则表达式。

我们想要一个函子，但是我们也不想被最大递归深度所限制。

# 修好它，菲利克斯

![](img/43f7414eff5f3d639b945122de249ef4.png)

在监狱里，如果你想逃跑，修理东西是没什么用的——就像在《破坏王拉尔夫》里看到的那样

不动点是函数收敛的地方，或者是函数的输入等于输出的地方。比如:f(x) = x 因为 1 = 1 所以有一个不动点 1。

我们可以使用一个固定点重新创建我们的等价数据类型:

```
type Regex = Fix RegexF
```

这个`Fix RegexF`怎么等同于我们原来的`Regex`？为了理解这一点，我们需要仔细看看`Fix`。`Fix`是定点数据类型:

```
newtype Fix f = Fix (f (Fix f))
```

我花了很长时间来理解这一点，但最终，我的思想达到了一个固定的点。

但是如果你冷静地看每一件作品，它就有意义了:

```
newtype Fix f = Fix (f (Fix f))
```

1.  第一个`Fix`是类型名。
2.  第二个`Fix`是类型构造函数名。
3.  第三个`Fix`是正在使用的类型。

`Fix`类型接受一个名为`f`的类型参数。`f`是一个函子，这意味着它也需要一个类型参数。在这种情况下，type 参数将是最后一个`Fix f`。

如果我们选择`RegexF`作为我们的`f`，我们的类型参数`r`就会是`Fix RegexF`，但是我们知道`Fix RegexF`真的是`Fix (RegexF (Fix RegexF))`，我们知道真的是`Fix (RegexF (Fix (RegexF (Fix RegexF))))`等等。这正是我们想要的。我们现在可以表示任何深度的正则表达式。费利克斯修好了！

*screen it Ralph*也可以取消固定我们的`Fix`数据类型，这将有助于实现 catamorphism 函数。

`wreckit`模式与`Fix`匹配并返回内部值:

```
wreckit :: Fix f -> f (Fix f)
wreckit (Fix x) = x
```

# 什么是 F 代数

现在我们已经创建了函子表达式，让我们来介绍一些关于如何抽象递归的理论。

代数由以下内容组成:

1.  形成表情的能力。例如，`Regex`构造函数和
2.  评估这些表达式的能力，例如`nullable :: Regex -> Bool`

尽管代数由两部分组成，但评估函数通常被称为代数:

```
type Algebra e r = e -> r
```

这意味着`nullable`功能实际上是`NullableAlgebra`

```
type NullableAlgebra = Algebra Regex Bool
nullable :: NullableAlgebra
```

范畴理论中的 F-代数由下列组成:

1.  形成函子表达式的能力，例如`RegexF r`和
2.  评价这些表情的能力，比如:`nullable :: RegexF Bool -> Bool`。

这意味着 F-代数的类型是:

```
type FAlgebra f r = f r -> r
```

`nullableAlg`功能就是`NullableAlgebra`:

```
type NullableFAlgebra = FAlgebra RegexF Bool
nullableAlg :: NullableFAlgebra
```

# 变形:自底向上递归

`cata`函数如何抽象出自底向上的递归？下面是整个函数:

```
cata :: Functor f => FAlgebra f r -> Fix f -> r
cata alg = alg . fmap (cata alg) . wreckit
```

这个很抽象。让我们说得更具体些。为了有助于可读性，让我们首先[将 Elm 的管道操作符添加到 Haskell](https://github.com/tfausak/flow/blob/main/source/library/Flow.hs) 中，它相当于 Haskell 中的`&`操作符，但它看起来更像一个 Unix 管道，并指示方向。抱歉，我觉得这个更好读。

```
infixl 0 |>
(|>) :: a -> (a -> b) -> b
x |> f = f x
```

现在，让我们使`cata`函数的实现更具体到`nullableAlg`函数的应用:

```
cata :: NullableFAlgebra -> Regex -> Bool
cata nullableAlg regex = 
  wreckit regex
  |> fmap (cata nullableAlg)
  |> nullableAlg
```

作为第一步，`wreckit`将取出我们的`Regex`并移除外部的`Fix`:

```
wreckit :: Regex -> RegexF Regex
```

现在我们有了一个函子，这意味着我们可以`fmap`超过`RegexF`。`fmap`向下递归一级。我们想要递归的函数是与`cata nullableAlg`等价的`nullable`函数。

```
nullable :: Regex -> Bool
nullable = cata nullableAlg
```

在递归的每一层，我们都想调用我们的`nullable`函数。因此，每当我们`fmap`向下一级，我们就向下传递`cata nullableAlg`以在下一级被调用。

```
fmap (cata nullableAlg) :: RegexF Regex -> RegexF Bool
```

当我们递归返回时，我们返回一个`RegexF Bool`，它存储了较低级别的`nullable`计算的中间结果。然后我们必须让它通过最终的`nullableAlg`来得到最终的`Bool`结果。

```
nullableAlg :: RegexF Bool -> Bool
```

这意味着我们现在可以定义没有任何递归的`nullableAlg`函数，因为`cata`函数将为我们的`nullable`函数做所有自底向上的递归:

这只是使用同态的一个例子，同态仅限于简单的自底向上递归，所以让我们学习另一个递归方案。

# 智能构造函数

在我们检查下一个递归方案之前，您可能会担心 API 会是什么样子。你将如何向用户解释定点对你的图书馆用户来说是什么？我们的想法是将这些实现细节限制在库的内部。我们的 API 不会暴露任何固定点。它不会向这个库的用户公开`nullableAlg`函数，只公开`nullable`函数。数据类型构造函数也是如此。我们不想将`Fix`暴露在我们的库之外，所以我们创建了更智能的构造器，为用户构造我们可以暴露的固定点:

这也是为了我们在下一节使用同态实现`deriv`函数时的内部便利。

# 同质异晶

> 如果你不理解循环，它们只是一个自然数上的同态——约瑟夫·斯文宁松

让我们再来看看最初的`deriv`功能:

`deriv`函数不是简单的自底向上递归，因为`Concat`步骤需要检查第一个表达式`r`是否可为空。这是仅在堆栈上可用的信息。一个同态不仅在函子参数中保留一个间歇的结果，而且保留一个原始表达式的副本，这样我们就可以检查它是否可为空。这需要一个叫做`RAlgebra`的新代数:

```
type RAlgebra f r = f (Fix f, r) -> r
```

函子现在包含一个元组，其中第一个参数是原始表达式的副本，第二个是中间结果。我们的`DeriveRAlgebra`，看起来会有点混乱，因为我们的中间结果与原始结果的副本是同一类型的:

```
type DeriveRAlgebra = RAlgebra RegexF Regex :: RegexF (Regex, Regex) -> Regex
```

这个`RAlgebra`将由`para`函数进行评估:

```
para :: (Functor f) => RAlgebra f r -> Fix f -> r
para alg f =
  wreckit f
  |> fmap (\x -> (x, para alg x))
  |> alg
```

我们可以针对`derivAlg`功能做更具体的说明:

```
para :: DeriveRAlgebra -> Regex -> Regex
para derivAlg regex = 
  wreckit regex
  |> fmap (\x -> (x, para derivAlg x))
  |> derivAlg
```

与`cata`函数的唯一不同之处是在元组中存储原始`Regex`、`x`的副本，而不仅仅是中间结果`para derivAlg x`。

1.  我们使用`wreckit :: Regex -> RegexF Regex`移除`Fix`的外层。
2.  我们使用`fmap`向下递归一级仿函数。
3.  我们在较低层次应用的函数取一个`Regex`并返回原始的`Regex`以及派生的`Regex` : `Regex -> (Regex, Regex)`
4.  结果在一个函子`RegexF (Regex, Regex)`中，我们可以使用`derivAlg c :: RegexF (Regex, Regex) -> Regex`对其求值，得到最终的派生结果`Regex`。

这意味着我们现在可以定义没有任何递归的`derivAlg`函数，因为`para`函数将为我们的`deriv`函数做所有的递归工作:

您可能会注意到，我们需要交换`derivAlg`函数的输入参数才能让它工作。

# 完整的算法

![](img/321c7a9d8f5983792989492bf569575c.png)

这只是使用递归方案的两个例子，但它足以完成我们的算法:

你可以在 GitHub 上找到完整的[演示。](https://github.com/awalterschulze/recursion-schemes-demo)

# 结论

我们只讨论了两种递归方案，但是还有很多其他的方案:

*   除了`para`和`cata`之外，还有一个文件夹叫做`histo`。历史形态保存了所有递归计算的历史，这对于需要记忆以提高效率的算法很有用，比如 Fibonacci。
*   展开，包括`ana`、`apo`和`futu`。变形是变形主义的范畴理论对偶。在希腊语中，`cata`是毁灭的意思，而`ana`是建设的意思。变形可以递归地构建一个表达式，例如构建一个给定长度的零列表。
*   然后还有重折:`hylo`，是一个`ana`后的一个`cata`和`chrono`，是一个`histo`和一个`futu`。

如果您有兴趣了解更多关于递归方案的知识，我在参考资料部分链接了一些资源，这样您就可以用 morph 继续您自己的史诗般的冒险:

![](img/197473c0e67625fa0efea29d99025346.png)

我小时候在电视上看的:变形

# 谢谢你

*   Andor Pénzes 校对并解释所有其他递归方案，尤其是因为我仍然只理解其中的一些。
*   Max Heiber 发现了一个错误，并对自底向上递归提出了更好的解释。

# 参考

*   [递归方案介绍——帕特里克·汤姆森](https://blog.sumtypeofway.com/posts/introduction-to-recursion-schemes.html)
*   [递归方案，第三部分:上下文中的折叠——帕特里克·汤姆森](https://blog.sumtypeofway.com/posts/recursion-schemes-part-3.html)
*   [理解 F-代数—巴托什·米莱斯基](https://bartoszmilewski.com/2013/06/10/understanding-f-algebras/)
*   [从树自动机中导出模块递归方案——帕特里克·巴尔](https://bahr.io/pubs/talks/bahr12uucs.html)
*   [使用递归方案的正则表达式导数的 Haskell 实现— awalterschulze](https://github.com/awalterschulze/recursion-schemes-demo)