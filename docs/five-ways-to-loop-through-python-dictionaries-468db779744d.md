# 遍历 Python 字典的五种方法

> 原文：<https://betterprogramming.pub/five-ways-to-loop-through-python-dictionaries-468db779744d>

## 学习如何迭代数据是任何编程语言中的一项关键技能

![](img/7cadbd7519beea63892013aecd1521cc.png)

[皮斯特亨](https://unsplash.com/@pisitheng?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/dictionary?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

如果您过去使用过其他语言，那么您肯定熟悉类似于下面这样的控制结构:

```
while(i < 10) {
   // do stuff
   i = i + 1;
}for(var i=0; i<10; i++) { // do stuff
}
```

Python 有自己的循环风格，但是简单地采用 Python 中相同的旧结构实际上是低估了这种语言。

这里有五种不同的方法来用 Python 编写一个`for`循环，从传统方法的转换开始。

# 1.传统 For 循环

传统的`for`回路有三个组成部分:

*   变量初始化。
*   比较表达式。
*   结束表达式。

一般来说，这意味着将一个索引设置为零，在表达式中进行比较，然后递增 1。为了在 Python 中重现这一点，实际上可以使用一个`while`循环，但是我们可以模仿用于引用索引的增量变量的想法。

这个方法不适用于字典，因为数据需要一个带有位置值的 iterable，但是我想把它包含进来作为参考。

```
mock_data = [90, 45, 32, 44]for i in range(len(data)):
   print(data[i]) # 90, 45, 32, 44
```

# 2.没办法

好吧，去查字典。让我们从编写 Python `for`循环的最简单方法开始。

```
mock_data = {
  "id": 1,
  "first_name": "Cory",
  "last_name": "Schimmang",
  "email": "cschimmang0@skype.com",
  "gender": "Female",
  "ip_address": "186.239.104.33"
}

for x in mock_data:
    print(x) # id, first_name, last_name, email, gender, ip_address
    """
    id
    first_name
    last_name
    email
    gender
    ip_address
    """
```

变量`x`将在`mock_data`内分配给每个变量。这种方法类似于 PHP 等语言中的`foreach`循环。

每个键都存储在 x 中，在我们的例子中，可以通过打印`mock_data[x]`来引用这个值。

# 3.使用。按键()

另一种迭代字典中的键的方法是使用`.keys()`方法，该方法返回一个`dict_keys`对象，当我们将每个值存储在我们的`x`变量中时，该对象将被迭代。

```
mock_data = {
  "id": 1,
  "first_name": "Cory",
  "last_name": "Schimmang",
  "email": "cschimmang0@skype.com",
  "gender": "Female",
  "ip_address": "186.239.104.33"
}

for x in mock_data.keys():
    print(x)
    """
    id
    first_name
    last_name
    email
    gender
    ip_address
    """
```

# 4.使用。值()

如果我们想得到字典中的每个值，那么使用`.values()`方法将把术语的值存储在`x`中，而不是存储在键中。

```
mock_data = {
  "id": 1,
  "first_name": "Cory",
  "last_name": "Schimmang",
  "email": "cschimmang0@skype.com",
  "gender": "Female",
  "ip_address": "186.239.104.33"
}

for x in mock_data.values():
    print(x)
    """
    1
    Cory
    Schimmang
    cschimmang0@skype.com
    Female
    186.239.104.33
    """
```

# 5.使用。项目()

通常情况下，您会希望同时访问键和值。在这个场景中，使用`.items()`方法，它将每个键值对作为一个两值元组返回。

要预分割元组，请在您的`for`循环中指定两个变量，以便第一个元组值(键)和第二个元组值(值)分别存储在第一个和第二个变量中。

```
mock_data = {
  "id": 1,
  "first_name": "Cory",
  "last_name": "Schimmang",
  "email": "cschimmang0@skype.com",
  "gender": "Female",
  "ip_address": "186.239.104.33"
}

for x in mock_data.items():
    print(x)
    """
    ('id', 1)
    ('first_name', 'Cory')
    ('last_name', 'Schimmang')
    ('email', 'cschimmang0@skype.com')
    ('gender', 'Female')
    ('ip_address', '186.239.104.33')
    """

for k,v in mock_data.items():
    print(k,v)
    """
    id 1
    first_name Cory
    last_name Schimmang
    email cschimmang0@skype.com
    gender Female
    ip_address 186.239.104.33
    """
```

# 结论

我希望你已经学会了利用 Python 中的`for`循环的新技术。

有许多可用的方法，每种方法都有各自的优缺点。一旦你进入更大的执行，你可能会发现性能差异，但是，特别是在初学者的水平，尝试有意识地迭代什么是必要的。