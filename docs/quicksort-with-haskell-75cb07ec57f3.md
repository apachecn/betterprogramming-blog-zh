# 算法:用 Haskell 快速排序

> 原文：<https://betterprogramming.pub/quicksort-with-haskell-75cb07ec57f3>

## 以及 ST monad 如何启用可变数组

![](img/1030649c2d4a6e1c1924a0c43f6a80cc.png)

[在这篇文章](https://www.mmhaskell.com/blog/2019/5/6/making-arrays-mutable)中，我们引用了`ST`单子，并研究了它如何启用可变数组。`ST` mondad 是`IO` monad 的替代物，它给我们可变的数据而没有副作用。现在，我们暂时停止向我们的[迷宫游戏](https://www.github.com/jhb563/MazeGame)添加功能，而是看一个具体的例子，其中可变数据可以允许不同的算法。

考虑一下[快速排序算法](https://www.geeksforgeeks.org/quick-sort/)。我们可以“就地”做这件事，改变一个输入数组，但是 Haskell 中不可变的数据使这种方法很难实现。让我们研究另一种使用普通的不可变列表的方法，然后看看我们如何使用一个更常见的使用`ST`的快速排序算法。最终，按照我们希望的方式进行这项工作仍然有困难，但这仍然是一个有用的尝试。

对 Haskell 中的 monads 还陌生吗？你会发现这个关于[单子和功能结构](https://www.mmhaskell.com/monads)的系列很有用。它将帮助你从头开始学习单子，从像函子这样简单的概念开始。

# 圣莫纳德

在我们深入使用数组之前，让我们花点时间来理解一下`ST`单子的用途。我第一次尝试在迷宫游戏中使用可变数组时使用了一个`IOArray`。这是可行的，但是它导致了`generateRandomMaze`使用`IO`单子。您应该警惕任何将您的代码从纯代码更改为使用`IO`的行为。这个函数的新版本可能会有许多旧版本中没有的奇怪的错误和副作用，比如文件系统访问。此外，这使得使用和测试这些代码变得更加困难。

在我的具体案例中，有一个更紧迫的问题。从`eventHandler`内部运行随机生成变得不可能。这意味着我不能以我想要的方式重启游戏。处理程序是一个纯函数，不能使用`IO`。

单子正是我们所需要的。它允许我们运行可以在适当的位置**改变值的代码，而不像`IO`那样允许任意的副作用**。我们可以使用通用的`runST`函数将`ST`单子中的计算转换成它的纯结果。这类似于我们如何使用`runState`来运行一个纯粹的`State`计算。

```
runST :: (forall. s ST a) -> a
```

`s`参数有点神奇。我们通常不必指定它是什么，但是该参数可以防止外界对数据产生额外的副作用。不要太担心。

另一个函数`runSTArray,` 做同样的事情——除了它处理可变数组:

```
runSTArray :: (forall. s ST s (STArray s i e)) -> Array i e
```

这允许我们使用`STArray`而不是`IOArray`作为可变数据类型。在本文的后面，我们将使用这种类型来实现我们的“就地”快速排序算法。首先，让我们看看这个算法的一个更简单的版本。

# 慢速快速排序

[学习 Haskell 以获得更好的效果](http://learnyouahaskell.com/recursion)简要介绍了快速排序算法。它展示了我们表达递归解的优雅。

```
quicksort1 :: (Ord a) => [a] -> [a]
quicksort1 [] = []
quicksort1 (x:xs) =
  let smallerSorted = quicksort1 [a | a <- xs, a <= x]
      biggerSorted = quicksort1 [a | a <- xs, a > x]
  in  smallerSorted ++ [x] ++ biggerSorted
```

这个看起来不错！它抓住了快速排序的一般思想。我们将第一个元素作为枢纽，然后将剩余的列表分成大于枢纽和小于枢纽的元素。然后我们递归地排序这些子列表中的每一个，并把它们和中间的枢纽结合起来。

然而，我们制作的每个新列表都需要额外的内存。我们在每一个递归步骤中都复制了列表的一部分。因此，对于这个算法，我们将至少使用`O(n)`内存。

我们还可以注意到这个算法选择其枢纽的方式:它总是选择第一个元素。这对于某些输入(排序或反向排序的数组)来说效率很低。为了让性能满足我们的期望，我们希望随机选择枢纽指数。然而，我们需要一个额外的类型为`StdGen`的参数，所以在本文中我们将忽略它。

当然，可以“就地”进行快速排序，而不用复制数组的任何部分，但是这需要可变内存。为了了解这个算法，我们首先用 Java 实现它。可变数据在 Java 中更自然，所以代码更容易理解。

# 就地快速排序(Java)

快速排序算法是递归的，但是我们将在一个助手中处理递归。助手将接受两个额外的参数:quicksort 部分的“start”和“end”的 int 值。`quicksortHelper`的目标是确保我们只对这部分进行了排序**。出于风格上的原因，我使用“end”来表示我们排序的点之后的一个索引。所以我们的主`quicksort`函数会用`0`和`arr.length`调用助手。**

```
public static void quicksort(int[] arr) {
  quicksortHelper(arr, 0, arr.length);
}public static void quicksortHelper(int[] arr, int start, int end) {
  ...
}
```

在我们看这个函数的其余部分之前，让我们设计两个更小的助手。第一个很简单，它将交换数组中的两个元素:

```
public static void swap(int[] arr, int i, int j) {
  int temp = arr[i];
  arr[i] = arr[j];
  arr[j] = temp;
}
```

下一个助手将包含算法的核心——这是我们的`partition`函数。它负责选择一个支点(同样，为了简单起见，我们将使用第一个元素)，然后它划分数组，使得比支点小的所有元素都在数组的第一部分。接下来，我们插入枢轴，并获得更大的元素。它返回分区的索引:

```
public static int partition(int[] arr, int start, int end) {
  int pivotElement = arr[start];
  int pivotIndex = start + 1;
  for (int i = start + 1; i < end; ++i) {
    if (arr[i] <= pivotElement) {
      swap(arr, i, pivotIndex);
      ++pivotIndex;
    }
  }
  swap(arr, start, pivotIndex - 1);
  return pivotIndex - 1;
}
```

现在我们的快速排序助手很简单！它将对数组进行分区，然后对子部分进行递归调用。还要注意基本情况:

```
public static void quicksortHelper(int[] arr, int start, int end) {
  if (start + 1 >= end) {
    return;
  }
  int pivotIndex = partition(arr, start, end);
  quicksortHelper(arr, start, pivotIndex);
  quicksortHelper(arr, pivotIndex + 1, end);
}
```

因为我们已经做好了一切准备，所以我们没有分配任何新的阵列！所以我们的函数定义只为临时值增加了额外的内存。因为堆栈深度平均为`O(log n)`，所以这是该算法的渐进内存使用量。

# 就地快速排序(Haskell)

现在我们已经熟悉了就地算法，让我们看看它在 Haskell 中是什么样子的。我们想用`STArray`做到这一点，但我们还是会写一个纯输入输出的函数。不幸的是，这意味着我们无论如何都要使用`O(n)`内存。`thaw`函数必须复制数组来创建它的可变版本。然而，我们的其余操作将在可变数组上就地工作。我们将遵循与 Java 代码相同的模式。让我们从简单开始，编写我们的`swap`函数:

```
swap :: ST s Int a -> Int -> Int -> ST s ()
swap arr i j = do
  elem1 <- readArray arr i
  elem2 <- readArray arr j
  writeArray arr i elem2
  writeArray arr j elem1
```

现在让我们写出我们的`partition`函数。我们将尽可能使它看起来像我们的 Java 版本，但是这有点棘手，因为我们没有 for 循环。我们将正面处理这个问题，设计一个函数来处理这个循环。

该循环为最终的枢纽索引生成我们的值，但是我们必须跟踪它的当前值。这听起来像是单子上的工作。我们的状态函数将把`pivotElement`和数组本身作为参数，然后它将把最后一个参数作为我们在 Java 版本的分区循环中的`i`值。

```
partitionLoop :: (Ord a)
  => STArray s Int a
  -> a
  -> Int
  -> StateT Int (ST s) ()
partitionLoop arr pivotElement i = do
  ...
```

我们用与 Java 版本相当的代码填充它。我们读取当前的 pivot 和当前`i`索引的元素，然后，如果它更小，我们在数组中交换它们并增加 pivot:

```
partitionLoop :: (Ord a)
  => STArray s Int a
  -> a
  -> Int
  -> StateT Int (ST s) ()
partitionLoop arr pivotElement i = do
  pivotIndex <- get
  thisElement <- lift $ readArray arr i
  when (thisElement <= pivotElement) $ do
    lift $ swap arr i pivotIndex
    put (pivotIndex + 1)
```

接下来，在得到 pivot 元素后，我们将这个循环合并到我们的主要`partition`函数中。我们将使用`mapM`对状态动作进行排序，并将其传递给`execStateT`。然后我们将返回最后一个支点(减 1)。不要忘记把轴心换到数组的中间！

```
partition :: (Ord a)
 => STArray s Int a
 -> Int
 -> Int
 -> ST s Int
partition arr start end = do
  pivotElement <- readArray arr start
  let pivotIndex_0 = start + 1
  finalPivotIndex <- execStateT
    (mapM (partitionLoop arr pivotElement) [(start+1)..(end-1)])
    pivotIndex_0
  swap arr start (finalPivotIndex - 1)
  return $ finalPivotIndex - 1
```

现在很容易将这些合并到我们的最终函数中:

```
quicksort2 :: (Ord a) => Array Int a -> Array Int a
quicksort2 inputArr = runSTArray $ do
  stArr <- thaw inputArr
  let (minIndex, maxIndex) = bounds inputArr
  quicksort2Helper minIndex (maxIndex + 1) stArr
  return stArrquicksort2Helper :: (Ord a)
  => Int 
  -> Int
  -> STArray s Int a
  -> ST s ()
quicksort2Helper start end stArr = when (start + 1 < end) $ do
  pivotIndex <- partition stArr start end
  quicksort2Helper start pivotIndex stArr
  quicksort2Helper (pivotIndex + 1) end stArr
```

这就完成了我们的算法！请再次注意，我们使用了`thaw`和`freeze`。这意味着我们的主`quicksort2`函数可以有纯粹的输入和输出，但这是以额外的内存为代价的。不过，我们可以在纯函数中使用可变数据，这很酷！

# 结论

由于我们必须复制列表，这个例子并没有带来太多的改进。事实上，当我们对这些函数进行基准测试时，我们发现第一个函数的执行速度要快得多。然而，理解我们如何在 Haskell 中“就地”操作数据仍然是有用的。单子允许我们以一种“纯”的方式来做这件事。如果我们愿意接受不纯的代码，那么`IO`单子也是可能的。