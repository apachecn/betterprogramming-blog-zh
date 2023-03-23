# 不要使用内置的 Python 方法来查询数据，而是使用 JMESPath

> 原文：<https://betterprogramming.pub/dont-use-built-in-python-methods-to-query-data-use-jmespath-instead-5c76c1e57ba6>

## 在 JSON 和字典中搜索有一个更好的选择

![](img/dc3e0409c0b58f03d49847686de2d064.png)

照片由[江户努格罗霍](https://unsplash.com/@edonugroho?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

还记得我第一篇关于媒介的文章吗？你可能不知道，所以让我提醒你一下:

[](/how-to-work-with-json-files-in-python-bedb5b37cbc9) [## 如何在 Python 中使用 JSON 文件

### 使用 Python 读写 JSON 文件

better 编程. pub](/how-to-work-with-json-files-in-python-bedb5b37cbc9) 

自 2020 年 3 月以来发生了很多变化，我也改变了用 Python 阅读 JSON 的方法——或者更确切地说是查询它。

我们都知道内置的`json`模块以及它是如何工作的(如果你不知道，请阅读上面链接的文章)。它完美而高效地工作。

那么这有什么问题呢？没什么。有没有更好的查询 JSON 数据和字典的方法？是的，有 JMESPath。

这不是替代。我们仍然需要`json`模块(例如用于将 JSON 文件加载到字典中)。另外，`json.loads()`不是一个用于查询的 API，而是一种将 JSON 作为字典加载的方式。但是我们不使用通常的`.get()`或`[]`符号，而是使用一种更好、更有效的符号(不是指速度，因为 Python 中的内置方法总是更快)。

为什么？让我用几个代码示例来解释一下。

*注意:记住你需要使用* `*pip install jmespath*` *来安装这个模块。*

# 简单查询

让我们从几件简单的事情开始，向我们展示 JMESPath 是如何工作的。首先，让我们看看它如何在给定一个键的情况下获取一个值。

我们需要定义我们的数据:

```
data = {"name": "Tommaso", "surname": "De Ponti", "writesOnMedium": 1}
```

为了让我的名字出现在 JMESPath 中，我要做以下事情:

```
print(jmespath.search('name', data))
```

目前没有什么特别的，但是到本文结束时，您也将开始使用 JMESPath。

# 通过多个对象

现在，让我们使用 JMESPath 查询多个对象，如下图所示:

```
data = {"person": {"tommaso": {"skills": {"python": ['web dev', 'security', 'automation', 'more']}}}}
```

现在，如果我想得到`web dev`，我可以使用下面的:

```
print(jmespath.search('person.tommaso.skills.python[0]', data))
```

开始明白我的意思了吗？那我们来说说切片吧。

# 限幅

让我们重复使用上面的数据:

```
data = {"person": {"tommaso": {"skills": {"python": ['web dev', 'security', 'automation', 'more']}}}}
```

如果我只想得到`security`和`automation`怎么办？我可以使用切片:

```
print(jmespath.search('person.tommaso.skills.python[1:3]', json_data))**Output : ['security', 'automation']**
```

此外，您可以应用`::`符号。同样，使用上面的字典，我们可能想要颠倒列表的顺序:

```
print(jmespath.search('person.tommaso.skills.python[::-1]', json_data))**Output : ['more', 'automation', 'security', 'web dev']**
```

# 通配符投影

这就是事情开始变得有趣的地方。假设您看到以下 JSON:

你想知道所有这些作品的浏览次数。你是如何进行的？在你开始考虑之前，这里有一个很好的解决方案:

```
print(jmespath.search('pieces[*].viewsCount', json_data))**Output : [20000, 10000, 15000]**
```

使用通配符(`*`)可以轻松完成这项任务。

此外，您不仅可以使用通配符进行投影，还可以使用切片对定义的对象进行投影:

```
print(jmespath.search('pieces[1:3].viewsCount', json_data))**Output : [10000, 15000]**
```

# 过滤

过滤是另一个非常有用的技巧。使用相同的 JSON 数据:

比方说，我只想获得浏览量超过 12000 的文章。我可以简单地在我的 JMESPath 查询中添加一个条件:`?viewsCount >= `12000``。下面一行完成了这项工作:

```
print(jmespath.search("pieces[?viewsCount >= `12000`].article", json_data))
```

*重要提示:请记住，数字必须在反斜线内(例如* `*`12000`*` *)。*

现在输出的是`[‘Work with JSON’, ‘Dictionaries instead of if-elifs’]`。

# 结论

正如您所看到的，这个库对于查询字典和 JSON 数据变得非常有用和高效，允许我们简单地执行复杂的指令，比如条件。

本文探讨了 JMESPath 的能力，如果您想更深入地了解，我建议您看一下它的[规范页面](https://jmespath.org/specification.html#spec)。

以下是您可能会喜欢的更多 Python 相关内容:

[](/how-to-work-with-json-files-in-python-bedb5b37cbc9) [## 如何在 Python 中使用 JSON 文件

### 使用 Python 读写 JSON 文件

better 编程. pub](/how-to-work-with-json-files-in-python-bedb5b37cbc9) [](/too-many-if-elif-conditions-in-python-use-dictionaries-instead-5486299af27e) [## Python 中 If-Elif 条件太多？请改用字典

### Python 字典清理代码的一种非常规方式

better 编程. pub](/too-many-if-elif-conditions-in-python-use-dictionaries-instead-5486299af27e)