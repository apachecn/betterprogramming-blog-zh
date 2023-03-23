# 如何让 JSON 和 Python 互相对话

> 原文：<https://betterprogramming.pub/how-to-make-json-and-python-talk-to-each-other-41531d58e59d>

## 用 Python 处理和创建 JSON 数据

![](img/385ca3b55d8239472d552f2a5608f26e.png)

由[维普·贾](https://unsplash.com/@lordarcadius?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript Object Notation (JSON)是一种流行的数据格式，常用于不同系统之间的数据交换。例如，许多 API 以 JSON 数据的格式返回结果。鉴于 JSON 出色的可读性和类似对象的结构，了解 Python 如何处理 JSON 数据是很有用的。在本文中，我们将了解 JSON 是什么，以及如何用 Python 中内置的`json`模块来处理它。

# JSON 的数据结构

JSON 数据的结构是 JSON 对象，它以键值对的形式保存数据，就像 Python 字典一样。下面的代码片段向您展示了典型的 JSON 对象的样子。

```
{ "firstName": "John", "lastName": "Smith", "age": **35**, "city": "San Francisco"}
```

本质上，JSON 对象由一对花括号限定范围，其中存储了键值对。JSON 对象要求它们的键只能是字符串，这个要求允许不同系统之间的标准通信。显示的值包括字符串和整数，但是 JSON 支持其他数据类型，包括布尔值、数组和对象。

*   字符串:用双引号括起来的字符串
*   数字:数字文字，包括整数和小数
*   Boolean:布尔值，真或假
*   数组:支持的数据类型列表
*   对象:用花括号括起来的键值对
*   Null:任何有效数据类型的空值(null)

在这些类型中，需要特别注意的是，与可以使用单引号或双引号的 Python 字符串不同，JSON 字符串只能用双引号括起来。不恰当地使用单引号会使 JSON 数据无效，普通的 JSON 解析器无法处理这些数据。

除了这些受支持的数据类型，知道 JSON 支持嵌套数据结构也很重要。例如，您可以将一个 JSON 对象嵌入到另一个对象中。对于另一个实例，数组可以由任何支持的数据类型组成，包括对象。下面是一些例子:

```
an object resides in another object:
{
  "one": 1, 
  "two": {"one": 1}
}an array consists of multiple objects:
[
  {"one": 1},
  {"two": 2},
  {"three": 3}
]
```

混合不同数据类型的灵活性允许我们用清晰的结构信息构建非常复杂的数据，因为所有数据都以键值对的形式保存。

# JSON 和 Python 之间的数据类型匹配

作为一种常见的数据交换格式，JSON 数据类型有相应的原生 Python 数据结构。请注意，这是双向通信——JSON 数据如何转换为 Python 数据，当您将 Python 数据转换为 JSON 数据时，同样的转换规则(除了少数例外)也适用。

```
+-----------+----------------+
|   JSON    |     Python     |
+-----------+----------------+
| String    | str            |
| Number    | int or float   |
| Boolean   | bool           |
| Array     | list           |
| Object    | dict           |
| Null      | NoneType       |
+-----------+----------------+
```

这些转换应该非常简单，只是 Python 没有与 JSON 对象中的数字相匹配的原生数据类型。相反，当 JSON 数是整数或实数时，我们必须使用 int 和 float 来表示它们。您可能还注意到 Python 数据列的表缺少 tuple 和 set。值得注意的是，元组被转换为数组，而集合本身不能转换为数组。

# 读取 JSON 字符串

当我们读取 JSON 数据并将其解码为其他编程语言(如 Python)的数据结构以进行进一步处理时，我们说我们**反序列化了 JSON 数据**。换句话说，读取和解码过程被称为**去串行化**。在 Python 的标准库中，我们有专门用于反序列化 JSON 数据的`json`模块。

我们知道 web 服务使用 JSON 对象作为 API 响应是很常见的。假设您收到以下响应。为了便于讨论，我们将其表示为 Python 字符串对象。

```
employee_json_data = """{
  "employee0": {
    "firstName": "John",
    "lastName": "Smith",
    "age": 35,
    "city": "San Francisco"
  },
  "employee1": {
    "firstName": "Zoe",
    "lastName": "Thompson",
    "age": 32,
    "city": "Los Angeles"
  }
}"""
```

要读取这个 JSON 字符串，我们只需使用`loads`方法。如下所示，在读取包含上述 JSON 对象的字符串后，我们能够获得一个`dict`对象。

`loads`方法灵活。当您有一个表示 JSON 对象列表的字符串时，这个方法足够智能，知道如何相应地解析数据。考虑下面的例子。

```
employee_json_array = '[{"employee2": "data"}, {"employee3": "data"}]'employee_list = json.loads(employee_json_array)
print(employee_list)***# [{'employee2': 'data'}, {'employee3': 'data'}]***
```

除了这些结构化的 JSON 对象，`loads`方法还可以解析除对象以外的任何 JSON 数据类型。下面是一些例子。

```
>>> json.loads("2.2")
**2.2**>>> json.loads('"A string"')
'A string'>>> json.loads('false')
False>>> json.loads('null') is None
True
```

# 读取 JSON 文件

上一节讨论了 JSON 字符串反序列化的各个方面。但是，您并不总是直接处理字符串。有时，您会有机会使用 JSON 文件。假设您运行下面的代码来创建一个保存 JSON 字符串的文件。

```
# the JSON data to save
json_to_write='{"name": "John", "age": 35}'# write the JSON data to a file
with open("json_test.txt", "w") as file:
    file.write(json_to_write)
```

当然，您可以直接读取文件来创建字符串，该字符串可以发送给`loads`方法。

```
with open(“json_test.txt”) as file:
    json_string = file.read()
    parsed_json0 = json.loads(json_string)
    print(parsed_json0)# output: {'name': 'John', 'age': **35**}
```

值得注意的是，`json`模块提供了`load`方法，允许我们直接使用文件来解析 JSON 数据:

```
with open(“json_test.txt”) as file:
    parsed_json1 = json.load(file)
    print(parsed_json1)# output: {‘name’: ‘John’, ‘age’: 35}
```

通过省去创建中间字符串对象的需要，它肯定比前面的实现更清晰。

在这里，我们了解了`load`和`loads`方法的最基本场景。应该注意的是，解析 JSON 数据是通过`JSONDecoder`类进行的。虽然这个基类足够强大，可以处理大多数情况，但是可以通过创建`JSONDecoder`类的子类来定义更多定制的行为。但是，如果您不想创建子类，`load`和`loads`方法提供了其他参数，通过这些参数您可以定义定制的解析行为。好奇的读者可以参考[官方文档](https://docs.python.org/3/library/json.html)获得进一步说明。

# 将 Python 数据写入 JSON 格式

与读取 JSON 数据一样，将 Python 数据写入 JSON 格式涉及两个对应的方法，即`dump`和`dumps`。与反序列化 JSON 数据相反，创建 JSON 数据被称为序列化。因此，当我们将 Python 数据转换为 JSON 数据时，我们说我们将 Python 对象序列化为 JSON 数据。

就像`load`和`loads`方法一样，`dump`和`dumps`方法具有几乎相同的调用签名。最重要的区别是`dump`方法将数据写入 JSON 文件，而`dumps`方法写入 JSON 格式的字符串。为简单起见，我们将只关注`dumps`方法。考虑下面的例子。

```
import jsondifferent_data = ['text', False, {"0": None, **1**: [**1.0**, **2.0**]}]json.dumps(different_data)
# output: '["text", false, {"0": null, "1": [1.0, 2.0]}]'
```

在这个例子中，我们注意到`dumps`方法创建了一个保存不同种类 JSON 数据的 JSON 数组。最有意义的观察是，尽管原始的`list`对象使用原生 Python 数据结构，但是生成的 JSON 字符串具有转换后的 JSON 数据结构。与之前显示的转换表一致，请注意以下转换。

*   用单引号括起来的字符串“text”现在使用了双引号“text”。
*   Python bool 对象`False`变为 false。
*   对象`None`变为空。
*   因为只有字符串可以是 JSON 键，所以数字 1 被自动转换成它的对应字符串“1”。

除了这些自动转换，还有两个我们经常使用的显著特性。第一个是通过使用适当的缩进，以更易读的格式创建 JSON 对象。为此，我们需要在`dumps`方法中设置缩进参数。

如上所示，每一级都很好地缩进，以表示 JSON 对象及其键值对的相对结构。

另一个有用的特性是`sort_keys`参数的规范。通过将其设置为`True`，创建的 JSON 字符串的键按字母顺序排序，这使得我们更容易查找信息，尤其是当有多个条目时。观察下面的功能。

我们已经了解到`load`和`loads`方法用于反序列化，而`dump`和`dumps`方法用于序列化。这些方法名称对一些人来说可能听起来很混乱。这里有一些提示可以帮助你区分它们。

*   JSON 数据是 Python 外部的，当你需要访问它们的数据时，我们需要“加载”到 Python 中。所以加载是指读取 JSON 数据。
*   相比之下，为了将 Python 数据导出到 JSON 数据，我们“转储”数据。所以转储指的是写 JSON 数据。
*   如果输入或输出 JSON 数据是字符串，那么可以把“s”看作字符串，这样我们就可以把字母“s”附加到 load 方法上。类似地，如果我们想要 JSON 字符串，我们将字母“s”附加到 dump 方法。

# 将定制实例写入 JSON 数据

我们一直关注内置 Python 数据结构，在许多应用程序中，当需要将这些自定义实例对象序列化为 JSON 数据时，您将定义自己的自定义类。让我们考虑下面的类，我们从它创建一个实例:

```
**class** **Employee**:
    **def** __init__(self, name, employee_id):
        self.name = name
        self.employee_id = employee_idemployee = Employee("John Smith", **40**)
```

如果我们尝试在`employee`上调用`dumps`，您预计会发生什么？能成功吗？让我们看看:

```
json.dumps(employee)
# TypeError: Object of type Employee is not JSON serializable
```

不，它不起作用。失败的原因是`dumps`方法试图创建一个有效的 JSON 字符串。然而，对于自定义类的实例，它不知道应该对什么数据进行编码。尽管您可以创建自己的 JSONEncoder 类，但一个快速的解决方案是通过设置`default`参数为`dumps`方法提供编码指令。

```
>>> json.dumps(employee, default=lambda x: x.__dict__)
'{"name": "John Smith", "employee_id": 40}'
```

这里，我们指定了一个 lambda 函数，它通过访问`__dict__`特殊属性来检索实例的 dict 表示。我们知道内置的`dict`对象是 JSON 可序列化的，因此`dumps`知道要“转储】对象。

# 结论

在本文中，我们回顾了用 Python 处理 JSON 数据的关键技术。以下是关键要点:

1.  JSON 数据是一种标准的交换数据格式。当您创建供他人使用的 API 时，可以考虑将 JSON 作为您的响应数据的一种可能格式。
2.  Python 分离了处理 JSON 字符串和文件的方法。这些方法有相似的调用签名。
3.  使用适当的缩进来提高 JSON 数据的可读性。如果您正在创建一个 JSON 字符串，这一点尤其重要。在序列化 Python 对象时，只需指定 indent 参数。
4.  当 JSON 对象有多个键值对时，对键进行排序通常是个好主意，这样可以更容易地查找信息。
5.  记住 JSON 键必须是字符串，并且需要双引号。
6.  要序列化自定义实例，您需要提供有关序列化的特定说明。