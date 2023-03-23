# 如何在 Python 中更好地使用 For 循环

> 原文：<https://betterprogramming.pub/how-to-use-for-loops-better-in-python-1dfbc3d9e91f>

## 几个可以改善循环逻辑的函数

![](img/bd7dafd0a217859fa92e4465f831a1cd.png)

奥利弗·黑尔在 [Unsplash](https://unsplash.com/s/photos/loop?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

当我们在 Python 中对一个序列执行一些动作时，我们大多数时候使用`for`循环。序列是一个通用术语，可以指一个列表、一个元组、一个字典、一个集合或者一个字符串，所有这些都可以在 Python 中的一个`for`循环中使用。这里有一个我们通常如何使用`for`循环的例子。

```
>>> students = ["John", "Mike", "Sandra", "Jennifer"]
>>> for student in students:
...     print(student)
... 
John
Mike
Sandra
Jennifer
```

除了这个简单的用例之外，还有几个内置函数，您可以使用`for`循环将它们与序列的迭代一起使用。如果使用得当，这些函数可以让您的代码更加整洁。

# 1.项目()和值()

假设我们有一本像下面这样的字典。

```
>>> scores = {"John": 94, "Mike": 95, "Sandra": 98, "Jennifer": 95}
```

当您迭代这个字典时，常规的`for`循环将只检索字典的键。在这种情况下，它将只打印出每个项目的名称。它的功能与迭代列表的`keys`相同。

```
>>> for score in scores:
...     print(score)
... 
Mike
John
Jennifer
Sandra
```

相反，我们可以使用 items()函数，这将使我们能够访问每个项目的键和值。

```
>>> for score in scores.items():
...     print(score)
... 
('Mike', 95)
('John', 94)
('Jennifer', 95)
('Sandra', 98)
```

您可以更进一步，命名元组的项，以便可以直接访问这两个项。

```
>>> for name, score in scores.items():
...     print("Student Name: " + name + ", Score: " + str(score))
... 
Student Name: Mike, Score: 95
Student Name: John, Score: 94
Student Name: Jennifer, Score: 95
Student Name: Sandra, Score: 98
```

有时，如果您只对字典的值感兴趣，您可以简单地使用`values()`函数。

```
>>> for score in scores.values():
...     print(score)
... 
95
94
95
98
```

# 2.枚举()

假设我们有一个如下的列表。

```
>>> grades = ["Freshman", "Sophomore", "Junior", "Senior"]
```

有时，我们希望访问索引和项目本身。在这种情况下，您可以使用`enumerate()`函数，将列表作为参数传递。

```
>>> for grade in enumerate(grades):
...     print(grade)
... 
(0, 'Freshman')
(1, 'Sophomore')
(2, 'Junior')
(3, 'Senior')
```

类似地，如果引用元组的项，可以分别访问索引和项。此外，您可以指定 enumerate()函数的起始索引，这在我们的例子中非常方便。

```
>>> for year, name in enumerate(grades, start=1):
...     print("Year " + str(year) + ": " + name)
... 
Year 1: Freshman
Year 2: Sophomore
Year 3: Junior
Year 4: Senior
```

# 3.反转()

假设我们有一个如下的列表。这个列表显示了按时间顺序到达教室的学生。

```
>>> arrived_students = ["John", "Mike", "Sandra", "Jennifer"]
```

然后老师想检查他们的作业，从最晚到达的学生开始。所以可以这样做。我们可以简单地用列表调用`reversed()`函数。

```
>>> for student in reversed(arrived_students):
...     print(student)
... 
Jennifer
Sandra
Mike
John
```

需要注意的一点是，您希望区分`reversed()`函数和`reverse()`函数，后者反转给定列表的顺序，而不返回列表本身。换句话说，你可以调用`arrived_students.reverse()`，但是不能通过调用`for student in arrived_students.reverse()`直接迭代。

# 4.已排序()

假设我们有一个如下的列表。

```
>>> students = ["John", "Mike", "Sandra", "Jennifer"]
```

教师希望根据学生的姓名对他们进行分类，并执行一些操作(例如，问一个问题)。这可以通过使用`sorted()`函数来完成，方法是将学生列表作为参数传入，这将生成一个按升序排列学生姓名的列表。您可以选择为`reverse`参数设置一个布尔值来请求升序或降序，就像这样:`sorted(students, reverse=True)`。

```
>>> for student in sorted(students):
...     print(student)
... 
Jennifer
John
Mike
Sandra
```

如果列表中的项目是字典，如下所示，会怎么样？

```
>>> students = [{"name": "John", "id": 1}, {"name": "Mike", "id": 4}, {"name": "Sandra", "id": 2}, {"name": "Jennifer", "id": 3}]
```

本质上，我们正在对字典列表进行排序。这可以通过使用`sorted()`功能中的`lambda`设置`key`参数来完成。

```
>>> for student in sorted(students, key = lambda i: i["id"], reverse=True):
...     print(student)
... 
{'name': 'Mike', 'id': 4}
{'name': 'Jennifer', 'id': 3}
{'name': 'Sandra', 'id': 2}
{'name': 'John', 'id': 1}
```

在上面的例子中，我们请求使用字典中的`id`键按照`reverse`顺序对列表进行排序，这在打印输出中可以清楚地反映出来。

如果您想使用多个键进行排序，您只需将 key 参数更改为类似于`key = lambda i: (i["id"], i["name"])`的值。

# 5.过滤器()

假设我们仍然使用下面的学生列表。

```
>>> students = [{"name": "John", "id": 1}, {"name": "Mike", "id": 4}, {"name": "Sandra", "id": 2}, {"name": "Jennifer", "id": 3}]
```

我们只想检索 id 号为偶数的学生的信息。这是我们可以方便地通过使用 lambda 使用`filter()`函数来完成的。

```
>>> for student in filter(lambda i: i["id"] % 2 == 0, students):
...     print(student)
... 
{'name': 'Mike', 'id': 4}
{'name': 'Sandra', 'id': 2}
```

# 6.zip()

假设我们有下面两个列表。

```
names = ["John", "Mike", "Sandra", "Jennifer"]
ids = [1, 3, 2, 4]
```

使用`zip()`功能，我们可以方便地使用两个列表中的信息。同样，和以前一样，如果你给元组分配了变量名，你就可以直接访问它们，就像`for name, id in zip(names, ids)`。

```
>>> for student in zip(names, ids):
...     print(student)
... 
('John', 1)
('Mike', 3)
('Sandra', 2)
('Jennifer', 4)
```

# 结论

在我们的代码中,`for`循环使用得非常频繁。通过在 for 循环中使用上述函数，您可以潜在地减少代码长度，使其更容易阅读，也更有趣！

PS:有一篇很有启发性的[文章](https://medium.com/better-programming/how-to-replace-your-python-for-loops-with-map-filter-and-reduce-c1b5fa96f43a)是由 [Jonathan Hsu](https://medium.com/@jhsu98) 在 Medium 上写的，建议用`map()`、`filter()`和`reduce()`函数替换`for`循环。我建议你也去看看！您可以成为`for`循环的主人:)