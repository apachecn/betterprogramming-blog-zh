# 使用 Python 中的专用字典实现提高效率

> 原文：<https://betterprogramming.pub/boost-your-efficiency-with-specialized-dictionary-implementations-7799ec97d14f>

## 创建包含有序和只读条目的字典，为不存在的键返回默认值，等等

![](img/eaf332a80999349e049a6511bde07d1a.png)

照片由[奥克萨纳 v](https://unsplash.com/@arttravelling?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 动机

您可能熟悉 Python 中的一个字典，它允许高效地查找、插入和删除与给定键关联的任何值。简单来说，想象一下去一家杂货店，如果你想找到一件物品的价格，你只需要输入物品的名称，与该物品相关的价格就会立即返回。

```
food = {
    'fish': 10,
    'broccoli': 3,
    'tofu': 5,
    'mushroom': 3
}
food['fish']
```

结果:`10`

但是如果你想让你的字典以一种特殊的方式运行，而这种方式需要一些思考才能实现呢？让我们来看四种不同的场景，在这些场景中，您希望您的字典有一个专门的实现:

*   创建有序字典。
*   当没有请求的键时返回默认值。
*   将多个字典组合成一个映射。
*   创建只读字典。

# 创建有序字典

## 方案

你想用字典来组织你想在周末完成的任务。字典关键字是任务，值是完成任务所需的时间。因为您希望按照输入的*顺序完成任务，而*不希望字典混淆任务的顺序，所以您决定使用`OrderedDict`。

## 订单

如果按键顺序对你的目标很重要，那么`collections.OrderedDict`是理想的选择。

```
import collectionstasks = collections.OrderedDict(laundry=0.5, shopping=2, clean=2)tasks['movie'] = 2taskstasks.keys
```

结果:

```
OrderedDict([('laundry', 0.5), ('shopping', 2), ('clean', 2), ('movie', 2)])odict_keys(['laundry', 'shopping', 'clean', 'movie'])
```

我们可以看到，列表中的项目是按照输入的顺序排列的。现在你只需要按照你的字典，以正确的顺序完成你的周末任务。

# 当没有请求的键时返回默认值

## 方案

您希望创建一个字典，将类别映射到房间号。由于许多课程是在室外教授的(因为天气很好)，您不想花时间将这些课程映射到`Outside`值。你决定使用`Defaultdict`。

## 默认字典

`collections.Defaultdict`可以用不带参数的函数初始化，如果找不到请求的键，则提供默认值*。*

```
*from collections import defaultdictclasses = defaultdict(lambda: 'Outside')classes['Math'] = 'B23'
classes['Physics'] = 'D24'classes['Math']*
```

*胜负:`B23`。`Defaultfict`行为就像一个标准字典，除了它允许返回一个不存在的键值。*

```
*classes['English']*
```

*胜负:`Outside`。*

*当提供了不存在键时，将返回我们指定的默认值。现在你不需要花时间把外面教的 20 门课映射到`Outside`值。*

# *将多个字典分组到一个映射中*

## *方案*

*你想记下你的朋友列表和他们的信息。每个朋友的信息都会表现为一个字典，上面有姓名和年龄。一旦你有了所有朋友的信息，你就要考虑如何把所有的信息放在一个地方。你发现`ChainMap`。*

## *链式地图*

*`collections.ChainMap`顾名思义。数据结构允许你像一条链一样将多个字典组合在一起。*

```
*from collections import ChainMap#Create multiple dictionaries of friends and their informationfriend1 = {'name':'Ben','age':23}
friend2 = {'name':'Thinh', 'age': 25}#Group these dictionaries togetherfriends = ChainMap(friend1, friend2)
friends*
```

*结果:*

```
*ChainMap({'name': 'Ben', 'age': 23}, {'name': 'Thinh', 'age': 25})*
```

*那么字典如何搜索提供的键呢？它将从左到右搜索键，并在找到键后返回值。*

```
*friends['name']*
```

*结果:`Ben`*

# *创建只读字典*

## *方案*

*如您所知，字典允许用户访问和更改字典中的条目。如果您只想使用字典向用户提供关于键*的信息，而不改变* *it* 该怎么办？例如，创建学生不能更改的班级地图。那么你应该考虑使用`MappingProxyType`。*

## *MappingProxyType*

*`MappingProxyType`提供一个*只读视图*到包装字典的数据中。这阻碍了用户编辑词典。*

```
*from types import MappingProxyType#Can read and edit
classes = {'Math': 'B23','Physics':'D24'}
classes['Math']
classes["Math"] = 'B21'#Can read
classes['Math']
#But can no longer edit
classes["Math"] = "D15"*
```

*结果:*

```
*TypeError: 'mappingproxy' object does not support item assignment*
```

*如你所见，现在学生们再也不能换房间来迷惑他们的同学了。*

# *结论*

*恭喜你！如果您已经阅读了本文的末尾，您就会知道还有哪些其他的字典实现可用，以及何时使用它们。当您希望词典执行特定的功能时，了解这些知识将非常有用。*

*那么，应该用这些特殊的字典实现来切换标准字典吗？我建议你使用标准字典，如果你需要一个特定的功能，就使用这些工具。通常，你会发现用标准词典就可以了。保存这篇文章，然后回到这里，因为您需要以不同的方式实现字典。在[这个 Github repo](https://github.com/khuyentran1401/Data-science/blob/master/python/dictionary.ipynb) 中，您可以随意派生和使用本文的代码。*

*我喜欢写一些基本的数据科学概念，并尝试不同的算法和数据科学工具。你可以在 LinkedIn 和 Twitter 上与我联系。*

*如果你想查看我写的所有文章的代码，请点击这里。在 Medium 上关注我，了解我的最新数据科学文章，例如:*

*[](https://towardsdatascience.com/python-tricks-for-keeping-track-of-your-data-aef3dc817a4e) [## 跟踪数据的 Python 技巧

### 如何用列表、字典计数器和命名元组来跟踪信息

towardsdatascience.com](https://towardsdatascience.com/python-tricks-for-keeping-track-of-your-data-aef3dc817a4e) [](https://towardsdatascience.com/timing-the-performance-to-choose-the-right-python-object-for-your-data-science-project-670db6f11b8e) [## 高效 Python 代码的计时

### 如何比较列表、集合和其他方法的性能

towardsdatascience.com](https://towardsdatascience.com/timing-the-performance-to-choose-the-right-python-object-for-your-data-science-project-670db6f11b8e) [](https://towardsdatascience.com/how-to-use-lambda-for-efficient-python-code-ff950dc8d259) [## 如何使用 Lambda 获得高效的 Python 代码

### lambda 对元组进行排序并为您创造性的数据科学想法创建复杂函数的技巧

towardsdatascience.com](https://towardsdatascience.com/how-to-use-lambda-for-efficient-python-code-ff950dc8d259) [](https://towardsdatascience.com/maximize-your-productivity-with-python-6110004b45f7) [## 使用 Python 最大化您的生产力

### 你创建了一个待办事项清单来提高效率，但最终却把时间浪费在了不重要的任务上。如果你能创造…

towardsdatascience.com](https://towardsdatascience.com/maximize-your-productivity-with-python-6110004b45f7) [](https://towardsdatascience.com/cython-a-speed-up-tool-for-your-python-function-9bab64364bfd) [## cy thon——Python 函数的加速工具

### 当调整你的算法得到小的改进时，你可能想用 Cython 获得额外的速度，一个…

towardsdatascience.com](https://towardsdatascience.com/cython-a-speed-up-tool-for-your-python-function-9bab64364bfd)*