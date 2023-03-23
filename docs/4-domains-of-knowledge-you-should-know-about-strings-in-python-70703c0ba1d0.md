# 关于 Python 中的字符串，你应该知道的 4 个知识领域

> 原文：<https://betterprogramming.pub/4-domains-of-knowledge-you-should-know-about-strings-in-python-70703c0ba1d0>

## 对您的 Python 字符串知识的高层次回顾

![](img/18ec5c8d876ff6e94e3ca3df0aefd341.png)

布雷特·乔丹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

文本是最基本的信息交流形式。无论您正在构建什么样的应用程序，您都不可避免地要以某种方式处理文本信息。因此，很好地理解使用和处理 Python 中的字符串的基本技术是至关重要的。在这篇文章中，我想提供一个关于字符串的四个知识领域的高级回顾。

# 1.使用和搜索子字符串

## 使用下标访问一个或多个字符

字符串是 Python 中的一种序列数据类型，因此可以使用下标访问单个或多个字符。下面是一些例子:

```
greeting = "Hello, World!"**assert** greeting[0] == "H"
**assert** greeting[1:3] == "el"
```

当你只使用一个索引时，你得到的是一个字符。当你使用切片(`start_index:end_index`)时，你会得到多个字符。请注意，当您使用切片时，不包括结束索引。您还可以向切片添加一个步骤，以便可以检索不连续的字符:

```
**assert** greeting[1:10:2] == 'el,Wr'
```

就像 Python 中的其他序列数据一样，如果想要检索字符串末尾的字符，也可以使用负索引。对于负索引，-1 表示最后一个，-2 表示倒数第二个，依此类推。

```
**assert** greeting[-1] == '!'
**assert** greeting[-3:-1] == "ld"
```

## 检查子字符串是否存在

因为字符串是序列，这意味着它支持使用`in` : `substr in text`来检查字符串中是否存在子串。

```
**assert** "Wor" in greeting
```

如果要检查一个字符串的开头是否包含子串，最好使用`startswith`方法。你应该知道还有另一个相关的方法`endswith`，它检查结束。

```
**assert** greeting.startswith("Hello")
**assert** greeting.endswith("ld!")
```

## 定位子字符串

有时候，仅仅知道一个子字符串是否存在于另一个字符串中是不够的。相反，我们想知道确切的位置。这种情况下，我们可以用`index`或者`find`。

```
**assert** greeting.index("ello") == 1
**assert** greeting.find("ello") == 1
```

似乎两者都在做同样的事情。但是，我建议您使用`find`，因为在一个不存在的子串上调用`index`会引发一个异常，这可能会使您的程序崩溃。

```
>>> greeting.find("no_substr")
-1
>>> greeting.index("no_substr")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: substring not found
```

上面注意，当字符串中不存在子串时，`find`返回-1，而`index`引发`ValueError`。

# 2.f 弦

说到字符串格式化和插值，你应该很了解 f 字符串。当我们说插值时，我们的意思是将非字符串变量转换成它们的字符串表示。

string(或 string)是使用 F 或 F 作为前缀的字符串文字，F 表示格式化。下面展示了如何创建一个简单的 f 字符串。

```
>>> message = f"First Message: {greeting}"
>>> print(message)
First Message: Hello, World!
```

## 字符串连接/插值

当您想要连接多个字符串/变量时，最好使用 f 字符串，因为它们可以自动处理插值。当您使用`+`操作将字符串与非字符串变量连接起来时，每个参与者都必须是字符串。

```
price = 1.23
volume = 12
product = "water"description0 = "Product Name: " + product + "; Price: $" + str(price) + "; Volume: " + str(volume) + " oz"print(description0)
# output: Product Name: water; Price: $1.23; Volume: 12 oz
```

正如你在上面看到的，从多个变量构造一个字符串是非常复杂的，特别是包括一些非字符串变量。相比之下，f 字符串使这种连接操作简单得多:

```
description1 = f"Product Name: {product}; Price: ${price:}; Volume: {volume} oz"**assert** description0 == description1
```

我们使用 f 字符串来创建描述 1，它与描述 0 相匹配。正如你所看到的，f 弦消除了不同部分之间的物理空间，使得连续阅读更加容易。

## 格式规范

对于 f 字符串，您还应该熟悉格式说明符，它允许您在变量后面使用`:`来应用额外的格式要求。下面是一些例子:

```
# Big numbers separator
big_number = 98765432123456789
**assert** f"{big_number:_d}" == '98_765_432_123_456_789'# Floating numbers formatting
more_digits = 2.345678
**assert** f"{more_digits:.2f}" == '2.35'
**assert** f"{more_digits:.4f}" == '2.3457'# Scientific notation
sci_number = 0.0000043203
**assert** f"{sci_number:e}" == '4.320300e-06'
```

## 联盟

您还可以根据字符串的对齐方式提供格式要求，例如左对齐或居中对齐。我们也可以应用填充来填补空白。下面是一些例子:

```
s0, s1 = 'a', 'bb'# Left-aligned with padding *
print(f'{s0:*<7}\n{s1:*<7}')
# output the following:
a******
bb*****# Right-aligned with padding %
print(f'{s0:%>8}\n{s1:%>8}')
# output the following:
%%%%%%%a
%%%%%%bb# Center-aligned
print(f'{s0:@^9}\n{s1:@^9}')
# output the following:
@@@@a@@@@
@@@bb@@@@
```

如上所示，我们使用< to indicate that we want left alignment, >进行右对齐，使用^进行中心对齐。

# 3.自定义类的字符串表示形式

当您定义定制类时，您想要实现两个特殊的方法:`__str__`和`__repr__`。

## 覆盖 __repr__

在您的自定义类中，您可以覆盖`__repr__`，它应该返回一个字符串。更具体地说，该字符串可用于重建具有相同属性的另一个实例对象。考虑下面的例子。

```
class Student:
    def __init__(self, name: str, grade: int) -> None:
        self.name = name
        self.grade = grade def __repr__(self) -> str:
        print("__repr__ is invoked")
        return f"Student({self.name!r}, {self.grade})"
```

使用这个类，我们可以创建一个实例，并检查它。

```
>>> student = Student("John Robinson", 6)
>>> student
__repr__ is invoked
Student('John Robinson', 6)
```

正如您所看到的，当我们在交互式 Python 控制台中输入实例变量时，会调用`__repr__`方法并显示自定义实例的字符串表示。

你可能已经注意到，在 f 弦中，当我们插值`self.name`时，我们包含了`!r`。这就是所谓的格式转换——我们指定这个变量应该通过调用它的`__repr__`方法进行插值。这里，对于一个字符串，`!r`将把它用引号括起来，允许用户通过调用`Student(‘John Robinson’, 6)`来重建一个`Student`实例。

相比之下，如果不使用`!r`，表示就变成了`Student(John Robinson, 6)`，如果调用它，这是无效的语法。请注意`!r`只需要用于字符串，不需要用于整数，如我们的例子所示。

## 覆盖 __str__

在您的自定义类中，您还应该覆盖`__str__`，它也返回一个字符串。与 __repr__ 返回的字符串相比， `__str__`返回的字符串应该更具信息性，如下所示:

```
class Student:
    def __init__(self, name: str, grade: int) -> None:
        self.name = name
        self.grade = grade def __repr__(self) -> str:
        print("__repr__ is invoked")
        return f"Student({self.name!r}, {self.grade})" def __str__(self) -> str:
        print("__str__ is called")
        return f"Student Name: {self.name}; Grade: {self.grade}"
```

有了更新后的类，我们可以运行下面的代码来看看如何调用`__str__`。

```
>>> student = Student("John Robinson", 6)
>>> print(student)
__str__ is called
Student Name: John Robinson; Grade: 6
```

我们现在用实例调用`print`函数，而不是直接检查实例。如您所见，我们获得了由`__str__`创建的字符串。因此，作为一般规则，当您在实例对象上调用 print 时，它是由`__str__`返回的字符串。

# 4.正则表达式

Python 字符串有许多适合基本文本数据处理的方法。但是，当您有更复杂的字符串处理作业时，您可能必须使用正则表达式。为了简单起见，我将正则表达式称为 regex。

在文本处理中，Regex 有时被认为是一种独立的语言，尽管许多编程语言都集成了它并创建了各自的“方言”值得注意的是，大多数用法或多或少是相似的，如果你很了解 Python，你可以很容易地学习另一种语言的 regex 相关特性。

## 构建模式

在 Python 的标准库中，re 模块提供了 regex 所需的功能。使用正则表达式的第一步是构建正确的模式。用字符串表示的模式决定了字符串应该是什么样子。

对于常规字符串，它们只是表示它们看起来是什么样子。例如，`“abc”`表示字符串应该有`“abc”`。然而，正如您可能意识到的，这些字符串的功能不足以检测多种匹配更一般模式的字符串。为此，有几类模式构建元素。

**边界锚**
第一类属于模式的前缀和后缀。下面列出了一些常见的粗体锚点。

```
**^hello**         starts with hello**world$**         ends with world**^hello world$**  starts and ends with “hello world”, and thus exact matching
```

**数量词**
你也可以指定某个字符串应该有多少次重复。它们被称为量词。一些常见的如下所示:

```
**he?**       h followed by zero or one e**he* **      h followed by zero or more e**he+ **      h followed by one or more e**he{3}**     h followed by eee**he{1,3}**   h followed by e, ee, or eee**he{2,} **   h followed by 2 or more e
```

**字符类**
你可以只用几个字符来表示一大组字符，这可以大大简化模式。

```
**\d**       any decimal digit**\D **      any character that is not a decimal digit**\s**       any whitespace, including space, \t, \n, \r, \f, \v**\S **      any character that isn't a whitespace**\w**       any word character, means alphanumeric plus underscores**\W**       any character that is not a word character**. **       any character except a newline**[abc]**    a set of defined characters, in this case, a, b, or c
```

**逻辑运算符**
Regex 和其他语言一样，有自己的逻辑运算符。下面是一些例子:

```
**a|b**       a or b**(abc)**     abc as a group**[^a]**      any character other than a
```

## 使用该模式

在您构建了一个模式之后，是时候测试它是否按预期工作了。请注意，这是非常常见的，你可能要调整你的模式多次，才最终工作。所以，如果你正在努力让你的模式正确，不要担心。要使用一个模式，有两种方法。

如果您需要多次使用该模式，最好编译该模式，这样当您的程序再次使用它时，可以节省时间。您可以在下面找到这种用法:

```
import repattern = re.compile("^hi")pattern.search("hi, Python")
# output: <re.Match object; span=(0, 2), match='hi'>pattern.search("hi, JavaScript")
# output: <re.Match object; span=(0, 2), match='hi'>pattern.search("hello, C#")
# output: None
```

当您只使用该模式一次时，我们可以简单地使用`re`模块中的各种函数，如下所示。

```
re.search(r"^hi", "hi Python")
# output: <re.Match object; span=(0, 2), match='hi'>
```

## 匹配对象

当使用字符串模式时，最重要的数据是`Match`对象，如上例所示。对于`Match`对象，它包括匹配的跨度，以及匹配了什么。可以找到它常用的方法和各自的效果。

```
match = re.search(r"(\w\d)+", "xyzdda2b1c3ee")print(match)
# output: <re.Match object; span=(5, 11), match='a2b1c3'>print("matched:", match.group())
# output: matched: a2b1c3print("span:", match.span())
# output: span: (5, 11)print(f"start: {match.start()} & end: {match.end()}")
# output: start: 5 & end: 11
```

值得注意的是，`Match`对象被评估为真，因此，您可以使用:

```
match = re.search("the_pattern", "the_string")
if match:
    # when a match is found, do the operation
else:
    # when a match isn't found, do the other operation
```

## 解决现实生活中的问题

让我们把事情放在一起，解决一个现实生活中的问题。假设我们有以下数据:

```
students_data = """101, John Robinson; good at maths
some random nonsense
102, Ashley Young; good at sports
54, random; record
103, Zoe Apple; All As
1234, random; record
Another random record"""
```

如你所见，这是关于有学生信息的数据。每行代表一个学生的信息。但是，文本数据包含其他不正确的数据，我们希望提取正确的记录。

通过观察所有这些行，我们可以看到学生证号是 3 位数，后面是姓名和描述。因此，我们可能会提出下面的模式，并给出详细的解释。

```
r"(\d{3}), (.+); (.+)"**(\d{3})**:   a group of 3 digits **-> for the ID number****,** :        string literals, a comma and a space**(.+)**:     a group of one or more word characters **-> for the name****;** :        string literals, a semicolon and a space**(.+)**:      a group of one or more characters -> **for the description**
```

应用这种模式，我们可以提取所需的记录:

```
regex = re.compile(r"(\d{3}), (.+); (.+)")
desired_records = []for line in students_data.split("\n"):
    match = regex.match(line)
    if match:
        print(f"{'Matched:':<12}{match.group()}")
        desired_records.append(line)
    else:
        print(f"{'No Match:':<12}{line}")print(desired_records)# output the following lines:Matched:    101, John Robinson; good at maths
No Match:   some random nonsense
Matched:    102, Ashley Young; good at sports
No Match:   54, random; record
Matched:    103, Zoe Apple; All As
No Match:   1234, random; record
No Match:   Another random record

['101, John Robinson; good at maths', '102, Ashley Young; good at sports', '103, Zoe Apple; All As']
```

正如您所看到的，我们正确地提取了所需的记录，这突出了 regex 的灵活性——我们构建了一个通用模式，它可以匹配多个记录。

# 结论

在本文中，我回顾了关于在 Python 中使用字符串的四个关键知识领域。其中，前三个域应该是简单明了的。对于正则表达式，在你熟悉它们之前，确实需要大量的练习。