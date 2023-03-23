# 如何计算 Python 列表中的出现次数

> 原文：<https://betterprogramming.pub/how-to-count-occurrences-in-a-python-list-f799072538b3>

## 哪一个最快:filter()、list comprehension 或。count()？

![](img/55250e1a349d25bf12bedd13e420237c.png)

照片由 [Djim Loic](https://unsplash.com/@loic?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/count?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

计算一个值在一个列表中出现的次数是一项常见的任务，无论你是计算失败的清单项目的数量，通过测试的学生的数量，甚至是一颗红色糖果的数量。

与编程中的大多数事情一样，有许多方法可以解决同一个问题。我们将看看三种不同的选择:列表理解、`filter()`和`count()`方法。

这里有两句话概述每一个以及为什么包括它们。

*   **列表理解**:数据理解是 Python 的一个标志性特征，它支持使用函数、`if`逻辑，甚至嵌套迭代来定义列表。当你试图思考 Pythonic 时，你的大脑倾向于问这样一个问题，“我能用理解来做这个吗？”
*   **`**filter()**`**函数**:`filter()`函数——[许多其他流行语言](https://medium.com/better-programming/how-to-start-using-map-filter-and-reduce-e01edba0d81?)中的一个方法——是一个通用的实现，它用一个表达式查询每一项，并且只保留那些评估为`true`的项。如果您来自另一种语言，您可能对这种方法很熟悉，因此，它作为一种选择出现。**
*   ****`**.count()**`**方法**:由于 filter 是其他语言的内置方法，Python 列表内置了`.count()`方法。很多时候，解决问题是我们的天性；然而，看看有什么现成的是值得的。****

# ****如何计算 Python 列表中的出现次数****

## ****使用列表理解****

****列表理解是一种创建新列表的速记技术。在 Python 中，我们能够在方括号中嵌入一个`for`循环和一个条件`if`表达式来形成我们的新数组。****

****我们要做的是，在遍历原始列表时，将表达式设置为只包含我们要查找的项。一旦我们有了过滤后的列表，我们就使用`len()`函数来获得我们的计数。****

```
**numbers = [1,1,2,4,5,3,2,1,6,3,1,6]sixes = [
   number
   for number in numbers
   if number == 6
]count_sixes = len(sixes)**
```

****注意，为了清楚起见，我将列表理解分成了多行。第一行是我们的临时变量名。因为我们没有修改这个值，所以这里没有什么特别的事情发生。第二行是`for`循环，它将把`numbers`中的每一项分配给临时变量`number`。第三行是我们的条件，它将决定列表中包含哪些项目。****

## ****使用 filter()函数****

****`filter()`函数将接受两个参数:一个函数和一个序列。序列将是我们的列表，函数将是一个 [lambda 表达式](https://medium.com/better-programming/how-to-use-lambda-expressions-in-python-a96330b513d4?)，这是一个单行匿名表达式——类似于匿名函数。****

```
**numbers = [1,1,2,4,5,3,2,1,6,3,1,6]sixes = list(filter(lambda number: number == 6, numbers))count_sixes = len(sixes)**
```

****lambda 表达式与 list comprehension 示例有许多相似之处。为了明确语法，临时变量被定义在关键字`lambda`之后，冒号之前。表达式如下，只有评估为`True`的值将被包含在我们的过滤器中。****

****认识到`filter()`函数返回一个过滤器对象也很重要。因此，我们需要将其返回强制到一个列表中，然后确定长度。****

## ****使用。count()列表方法****

****`.count()`方法内置于 list 类中，与 list 表达式和`filter()`不同，我们在这里不会使用表达式。要使用`.count()`，您需要做的就是传递圆括号内匹配的值。****

```
**numbers = [1,1,2,4,5,3,2,1,6,3,1,6]count_sixes = numbers.count(6)**
```

****超级简单。正是这些类型的本地方法，让许多在阅读手册之前就开始钻研的人错过了。****

# ****确定最快的技术****

## ****测试场景****

****为了测试哪种技术最快，我编写了一个简单的测试脚本，创建了 1，000，000 个从 1 到 100，000 的随机整数的列表，以及 1 到 100，000 之间的搜索值。****

****每个方法的时间将被记录下来，这个场景将运行 10 次。最后，每项技术的平均时间将用于比较。****

```
**from random import randint
import timecount_time = []
list_time = []
filter_time = []for _ in range(0,10):
    numbers = [randint(1,100000) for _ in range(0,1000000)]
    search = randint(1,1000000) count_start = time.time()
    count_matches = numbers.count(search)
    count_time.append(time.time() - count_start) list_start = time.time()
    list_matches = len([x for x in numbers if x == search])
    list_time.append(time.time() - list_start) filter_start = time.time()
    filter_matches = len(list(
        filter(lambda x: x == search, numbers)
    ))
    filter_time.append(time.time() - filter_start)print(sum(count_time)/len(count_time))
print(sum(list_time)/len(list_time))
print(sum(filter_time)/len(filter_time))**
```

## ****结果****

****速度测试的结果如下:****

*   ******计数方法**:0。46860 . 68686868661****
*   ******过滤功能**:0 . 46860 . 46866868661****
*   ****列表理解:0 . 46860 . 36868686861****

****这三种技术都不是特别慢，但是`.count()`方法无疑是最好的。此外，排在第二位的`filter()`函数更接近于计数的速度，而不是列表理解的速度。****

****鉴于`.count()`不仅速度最快，而且最容易阅读和编写，我相信它在客观上优于其他两个类似用例的选项。****