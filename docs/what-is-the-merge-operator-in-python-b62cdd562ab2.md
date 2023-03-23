# Python 中的归并运算符是什么？

> 原文：<https://betterprogramming.pub/what-is-the-merge-operator-in-python-b62cdd562ab2>

## 合并字典的更好方法

![](img/9db2e83186f84d553d3d70dd449c56c0.png)

照片由[赞·李](https://unsplash.com/@zane4004?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/patterns?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

字典是一种类似于 JSON 对象的数据类型。通常被称为“字典”，它们是一组术语(唯一标识符)，每个术语都有一个定义(值)。

```
myDict = {
  "first_name": "Jonathan",
  "last_name": "Hsu"
}
```

由于字典术语必须是唯一的，如果多个字典在合并时有相同的术语，就会产生冲突。

在这些情况下，规则通常是——最后一个获胜。换句话说，最后一个要合并的词典具有优先权，如果一个术语的值之前已经被定义过，那么它将会被覆盖。

从 Python 版开始，merge 操作符和它的伙伴——增强赋值操作符，将成为组合字典的实际方法。

# 合并字典的老方法

合并字典的老方法是通过`.update()`方法，或者最近使用的解包“splat”操作符，这两种方法都是本机可用的。

从主字典调用`.update()`方法，并接受另一个字典作为参数。

```
myDict = { "first_name": "Jonathan", "last_name": "Hsu" }
myDict.update({"first_name": "Jet", "age": 15})print(myDict)
# {'first_name': 'Jet', 'last_name': 'Hsu', 'age': 15}
```

原来的字典被修改了…如果你想保持原来的形式，这可能不是你想要的结果。要使用 update 保存原始文件，您必须复制原始文件，然后使用`.update()`方法继续。我喜欢的步骤太多了。

另一种方法是使用双星号运算符，它用于解包字典:

```
d1 = { "first_name": "Jonathan", "last_name": "Hsu" }
d2 = { "first_name": "Jet", "age": 15 }
d3 = { **d1, **d2 }print(d3)
# {'first_name': 'Jet', 'last_name': 'Hsu', 'age': 15}
```

虽然有效地表达了所有字典都是平等的，并且最后一个应该获胜，但是代码不容易阅读。事实上，根据 [PEP 584](https://www.python.org/dev/peps/pep-0584/) ，“如果你问一个典型的 Python 用户如何将两个字典合并成一个新的，我怀疑很多人会想到`{**d1, **d2}`”

# 使用合并运算符

为了解决这两个问题，引入了合并操作符。使用单个竖线，merge 操作符可以被认为是字典的加法操作符。

```
d1 = { "first_name": "Jonathan", "last_name": "Hsu" }
d2 = { "first_name": "Jet", "age": 15 }
d3 = d1 | d2print(d3)
# {'first_name': 'Jet', 'last_name': 'Hsu', 'age': 15}
```

如您所见，merge 操作符的功能与 splat 技术相同，并且不修改原始字典。

但是如果我们要修改原来的字典呢？在这种情况下，您可以使用增强赋值操作符，类似于任何其他复合赋值操作符。

```
myDict = { "first_name": "Jonathan", "last_name": "Hsu" }
myDict |= {"first_name": "Jet", "age": 15}print(myDict)
# {'first_name': 'Jet', 'last_name': 'Hsu', 'age': 15}
```

在这种情况下，扩充赋值操作符在功能上等同于`.update()`方法，但在语法上类似于合并操作符。

请记住，这些操作符仅在 Python 3.9 及更高版本中可用，因此请确保您在代码中添加了适当的版本验证。

*感谢您的阅读，请在下面留下您的评论和想法！*