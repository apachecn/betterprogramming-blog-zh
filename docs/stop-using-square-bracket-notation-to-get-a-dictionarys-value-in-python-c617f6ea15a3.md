# 在 Python 中，停止使用方括号符号来获取字典的值

> 原文：<https://betterprogramming.pub/stop-using-square-bracket-notation-to-get-a-dictionarys-value-in-python-c617f6ea15a3>

## 超越传统方法，保护您的代码

![](img/b8ec65c120637900ec54a08a373b9b92.png)

照片由 [Pixabay](https://www.pexels.com/photo/book-book-pages-bookcase-browse-415071/) 在[像素](https://www.pexels.com/)上拍摄

字典是一组无序的术语和定义。这意味着:

*   每个数据点都有一个标识符(术语)和一个值(定义)。
*   这些术语必须是该词典独有的，不能重复。
*   与列表不同，术语没有明确的顺序。

要定义字典，请使用花括号并用逗号分隔每个术语/定义对。

```
author = {
   "first_name": "Jonathan",
   "last_name": "Hsu",
   "username": "jhsu98"
}
```

# 访问字典值的传统(不好的)方法

访问字典中的值的传统方法是使用方括号符号。该语法将术语的名称嵌套在方括号中，如下所示。

```
author = {
   "first_name": "Jonathan",
   "last_name": "Hsu",
   "username": "jhsu98"
}print(author['username']) # jhsu98
print(author['middle_initial']) # KeyError: 'middle_initial'
```

注意尝试引用一个不存在的术语是如何导致`KeyError`的。这可能会造成很大的麻烦，尤其是在处理不可预测的业务数据时。

虽然我们可以用一个`try/except`或`if`语句来包装我们的语句，但是对字典术语的这种关注会很快堆积起来。

```
author = {}try:
   print(author['username'])
except KeyError as e:
   print(e) # 'username'if 'username' in author:
   print(author['username'])
```

如果您来自 JavaScript 背景，您可能会尝试引用带有点符号的字典值。这在 Python 中不起作用。

```
author = {
   "first_name": "Jonathan",
   "last_name": "Hsu",
   "username": "jhsu98"
}print(author.username)
# AttributeError: 'dict' object has no attribute 'username'
```

# 使用。get()方法

当您想要访问一个字典的值时，最安全的方法是使用`.get()`方法。该方法有两个参数:

*   First(必选):要检索的术语的名称。这可以是一个字符串或变量，允许动态检索。
*   Second(可选):术语不存在时用作默认值的值。

```
author = {
   "first_name": "Jonathan",
   "last_name": "Hsu",
   "username": "jhsu98"
}print(author.get('username')) # jhsu98
print(author.get('middle_initial', None)) # None
```

当这个术语先前被声明时，`.get()`与传统的方括号引用没有什么不同。如果没有定义这个术语，就会返回一个默认值，这样您就不必处理异常了。

这个默认值可以是你想要的任何值，但是记住它是可选的。当不包含默认值时，使用`None`—Python 中 null 的等价形式。

# 使用。setdefault()方法

有时，您不仅希望避免字典中未定义的术语，还希望代码能够自我纠正其数据结构。`.setdefault()`的结构与`.get()`相同。然而，当术语未定义时，除了返回默认值之外，字典的术语也将被设置为该值。

```
author = {
   "first_name": "Jonathan",
   "last_name": "Hsu",
   "username": "jhsu98"
}print(author.setdefault('username')) # jhsu98
print(author.setdefault('middle_initial', None)) # None
```

在上面的例子中，我们看到当术语存在时，`.setdefault()`与方括号符号或`.get()`完全相同。此外，当术语不存在时，它的行为与`.get()`相同，返回传递的默认值。

与`.get()`的不同之处在于，术语和定义现在是字典的一部分，如下所示。

```
author = {
   "first_name": "Jonathan",
   "last_name": "Hsu",
   "username": "jhsu98"
}print(author.setdefault('middle_initial',None)) # None
print(author)
"""
{
  'first_name': 'Jonathan',
  'last_name': 'Hsu',
  'username': 'jhsu98',
  'middle_initial': None
}
"""
```

在引用字典值时，`.get()`和`.setdefault()`都是很好的技巧……只是打破旧习惯并采用这种做法可能需要一些时间。

当你不想改变原始数据时，`.get()`就是你的赢家。

当您想要更改原始数据时，使用`.setdefault()`并收工。