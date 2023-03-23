# 掌握 Python 函数需要了解的 24 个关键概念

> 原文：<https://betterprogramming.pub/24-key-concepts-to-know-for-mastering-python-functions-9cba097b29b3>

## 检查您的 Python 知识的广度

![](img/7a8222dfffb7683b21d39cb725cf8a0d.png)

帕特里克·托马索在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

任何应用程序的核心都是数据，如果没有函数的处理，数据是没有意义的。因此，你可以说函数是所有应用程序的引擎。使用和编写函数是任何程序员的基本要求。在本文中，我想回顾一下与在 Python 中定义和调用函数相关的 24 个概念。

由于考虑到文章的长度，我将保持讨论的简洁，并在适当的地方提供对我的早期文章的必要的交叉引用。所以，你可以跳过你已经熟悉的概念，专注于对你来说新的东西。

在适用的地方，我在相应的标题上提供了我以前的一些文章的链接。有兴趣的读者可以参考这些文章了解更多。

# 1.[一等公民](https://medium.com/swlh/everything-is-an-object-in-python-learn-to-use-functions-as-objects-ace7f30e283e)

从本质上讲，Python 是一种面向对象的编程语言。因此，它的整个实现涉及所有类型的对象、包、模块、类、实例，当然还有函数。是的，你没听错。Python 函数也是一等公民，就像其他对象一样。你对一个普通对象所做的事情(如果我们狭隘地认为只有类实例才是对象)也适用于函数。例如，Python 函数有自己的属性，如下所示:

```
>>> def doubler(a):
...     return a*2
...
>>> doubler.__name__
'doubler'
```

# 2.[高阶函数](https://medium.com/swlh/higher-order-functions-in-python-map-filter-and-reduce-34299fee1b21)

因为函数是一等公民，所以它们被用作常规对象。一个含义是，你可以将一个函数作为参数发送给另一个函数。将函数作为参数或返回函数作为返回值的函数通常被称为高阶函数，因为它们处理函数。在 Python 中，有一些值得注意的内置高阶函数，比如`map`和`filter`。下面的代码向您展示了`map`函数的用法，它接受一个函数和一个 iterable，并将该函数应用于 iterable 的每一项。

```
>>> numbers = [1, 2, 3]
>>> mapped = map(doubler, numbers)
```

map 函数的返回对象是一个 map 对象，它是一个迭代器。如果您想查看其项目，可以使用 map 对象创建一个列表对象，如下所示:

```
>>> list(mapped)
[2, 4, 6]
```

# 3.[可调用:函数与类](/the-practical-implications-of-python-objects-callability-50ec2f54e24a)

函数的一个基本用法就是我们所说的函数。在 Python 中，调用是通过调用操作符——一对括号来实现的。相反，如果一个对象可以被调用，我们称之为可调用对象。有了对可调用概念的理解，我们需要为内置的“函数”了解函数和类之间的细微差别。我在函数上加了引号，因为许多内置函数不是真正的函数——它们是类，调用它们实质上是调用类实例构造函数。

例如，`list`和`dict`是内置函数的一部分。当你使用它们通过运行`list()`和`dict()`来创建一个`list`或`dict`对象时。看起来你在调用一个函数，但实际上，你在调用一个构造函数。您可以对类使用 call 操作符的原因很简单，因为这些类也是可调用的。

# 4.隐式和显式返回值

当我们定义一个函数时，我们可以有一个返回值，这样调用者就可以使用它进行后续的数据处理，就像我们之前定义的`doubler`函数一样。然而，我们并不总是需要函数返回值。例如，我们使用内置的`print`函数将一些消息打印到控制台。如你所知，我们不期望或从调用`print`函数中获得任何返回值。但是，请注意以下几点:

```
>>> from_print = print("message")
message
>>> print(from_print)
None
```

`print`函数实际上确实返回了一些东西——值`None`。这个例子并不特殊——事实上，所有 Python 函数都显式或隐式返回值(当没有 return 语句时，编译后的函数将为我们返回`None`)。

# 5.[关键字参数](/8-things-to-know-to-master-python-function-arguments-75ed7597463e)

当我们定义一个函数时，我们可以包含参数，这样用户就可以用不同的参数集来调用这个函数，这提高了函数的灵活性。这些参数可以定义为关键字参数，这意味着每个参数都与一个标识符相关联。当您调用函数时，您为标识符设置一个值。

```
print(**objects*, *sep=' '*, *end='\n'*, *file=sys.stdout*, *flush=False*)
```

例如，在上面所示的`print`函数中，参数如 sep 和 end 都是关键字参数。当您调用 print 时，您可以使用相应的标识符来设置它们。

```
print("a message", end=";", sep="|")
```

我故意做的一件事是交换 sep 和 end 的顺序。您只需要知道关键字参数的顺序并不重要，因为 Python 理解哪些参数被设置为哪些参数。

# 6.位置参数

与关键字参数密切相关的是位置参数，这些参数是根据它们的位置进行解析的。与顺序无关紧要的关键字参数不同，位置参数必须遵循函数定义指定的顺序。我相信这是显而易见的——只需考虑下面的例子。你不能调换顺序。

```
def create_fraction(a, b):
    return a / b
```

重要的是，位置参数和关键字参数通常在调用函数的上下文中讨论(比如 Python 如何解析/分析调用)。例如，`create_fraction`函数似乎包括两个位置参数。但是，我们可以使用关键字参数来调用它。也请注意，我交换了这两个参数的顺序。

```
>>> create_fraction(b=2, a=3)
1.5
```

# 7.定义仅关键字参数

正如您可能知道的，关键字参数比位置参数更清楚，因为标识符指示参数代表什么数据/信息。然而，仍然与参数的解析相关，关键字参数也可以在位置上设置。

```
>>> def multiplier(a, by=1, offset=0):
...     return a * by - offset
... 
>>> multiplier(5, 2, 1)
9
```

在上面的代码片段中，我们似乎使用一个位置参数和两个带有默认值的关键字参数来定义函数。然而，当我们调用它时，我们只是简单地设置了三个位置参数。

为了提高代码的可读性，我们可以实施一种模式，在这种模式下，必须使用标识符来设置参数。这个特性被称为定义仅关键字参数。这种技术使用星号来表示位置参数的结束，在此之后，所有参数都必须通过各自的标识符来设置。下面是应用了仅关键字参数技术后的`multiplier`的修改版本。作为对比，我还包含了一个错误的调用。

```
>>> def multiplier_keyword(a, *, by=1, offset=0):
...     return a * by - offset
... 
>>> multiplier_keyword(5, 2, 1)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: multiplier_keyword() takes 1 positional argument but 3 were given
>>> multiplier_keyword(5, by=2, offset=1)
9
```

# 8.定义仅位置参数

我们已经看到了只有关键字的参数在提高可读性方面的作用。您可能知道，我们还可以定义只接受位置参数的函数。然而，我发现这个特性没有只有关键字的参数特性有用。不过，让我们在这里快速回顾一下这个特性。这种技术使用正斜杠(/)表示在符号之前，每个参数都需要按位置设置。下面我们来看一个例子。

```
>>> def custom_sum_pos(iterable, /, start=0):
...     result = sum(iterable) + start
...     return result
... 
>>> custom_sum_pos([1, 2, 3], start=10)
16
>>> custom_sum_pos(iterable=[1, 2, 3], start=10)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: custom_sum_pos() got some positional-only arguments passed as keyword arguments: 'iterable'
```

如上所示，如果我们试图使用标识符设置第一个参数，我们得到了`TypeError`。原因是我们在`iterable`参数后面用了正斜杠，表示必须按位置设置。

# 9.可变数量的位置参数(*args)

让我们再来看看`print`函数的签名:

```
print(**objects*, *sep=' '*, *end='\n'*, *file=sys.stdout*, *flush=False*)
```

你注意到`objects`变量前的星号了吗？这个特性被称为可变数量的位置参数，这意味着您可以传递多个位置参数。见下文:

```
>>> print("Hello", "World", "Python")
Hello World Python
```

当您使用可变数量的位置参数定义函数时，您需要记住的一件事是，位置参数被打包到一个元组中，您可以从该元组中访问数据。你可以在下面看到一个例子。

```
>>> def greet(*persons, word="Hi"):
...     print("Persons:", persons)
...     print("Type:", type(persons))
...     message = word + ", " + ", ".join(persons)
...     print(message)
... 
>>> greet("Aaron", "John", "Jennifer")
Persons: ('Aaron', 'John', 'Jennifer')
Type: <class 'tuple'>
Hi, Aaron, John, Jennifer
```

# 10.可变数量的关键字参数(**kwargs)

以类似的方式，我们也可以在一个函数中定义可变数量的关键字参数。让我们看看下面的例子，我们将参照例子解释这个特性。

```
>>> def mean_score(**scores):
...     print("Scores:", scores)
...     print("Type:", type(scores))
...     average_score = sum(scores.values()) / len(scores)
...     print("Mean Score:", average_score)
... 
>>> mean_score(John=99, Jennifer=97, Aaron=95)
Scores: {'John': 99, 'Jennifer': 97, 'Aaron': 95}
Type: <class 'dict'>
Mean Score: 97.0
```

*   变量`**scores`表示函数声明中关键字参数的可变数量。
*   当我们调用这个函数时，我们使用学生的名字作为标识符，传递学生的列表和他们的分数。
*   如你所见，这些带有分数的名字被打包到一个`dict`对象中(即`scores`参数)。

当您不确定在调用期间设置了什么标识符时，可变数量的关键字参数非常有用。

# 11.[发电机功能](/python-7-advanced-features-that-you-may-not-know-about-generators-574a65fd6e45)

生成器是一种迭代器，它在被请求时一个一个地呈现它们的元素。与其他迭代器相比，生成器的内存效率更高，因为它们动态地呈现项目，而不是将项目存储在内存中。我们可以用一个函数来创建一个生成器，我们称这样的生成器函数。这种技术的典型特征是使用`yield`关键字，它呈现下一个适用的项目。您可以参考下面的代码片段作为示例。

```
>>> def simple_generator():
...     x = 1
...     while x < 4:
...         yield x
...         x += 1
... 
>>> num_gen = simple_generator()
>>> numbers = list(num_gen)
>>> print(numbers)
[1, 2, 3]
```

如您所见，`num_gen`呈现了三个项目:1、2 和 3。这个生成器用于创建`list`对象。

# 12.[关闭](/5-essential-aspects-of-python-closures-494a04e7b65e)

在 Python 中，闭包是由外部函数创建并返回的内部函数，它使用外部函数的变量。这个定义不是最容易理解的。让我们看一个例子来理解它。

Python 闭包

*   `incrementer`是外部函数`make_incrementer`中的内部函数。
*   外部函数`make_incrementer`返回内部函数`incrementer`作为其返回值。
*   内部函数使用外部函数的变量:`counter`和`step`。

您可能想知道了解闭包的重要性。嗯，这是一个编程特性，是其他技术的基础，其中最著名的是 decorators，这将在下面讨论。

# 13.[装修工](/python-decorators-5-advanced-features-to-know-17dd9be7517b)

装饰器是高阶函数，它改变其他函数的外观/行为，而不影响这些函数的原始操作。这个定义可能会让你们中的一些人感到困惑，但是我敢打赌，许多人可能在别人的代码中看到过装饰符的用法。为了说明什么是装饰者，考虑下面的例子。

装饰示例

*   `logging_time`是一个装饰函数。这个函数的作用是记录修饰函数的时间。
*   在`logging_time`函数中，我们定义了一个内部函数`logger`。在`logger`函数内，我们调用原函数，简称为`func`。
*   `logging_time`函数返回 logger 函数作为它的返回值。

根据我们在前一节中描述的内容，`logger`实际上是一个闭包(您可以交叉引用前一节来查看是否满足所有三个标准)。为了使用 decorator，我们在 decorator 函数前使用@符号作为前缀，并将其放在被修饰函数的顶部。当您调用修饰函数时，您会看到我们现在可以看到函数操作所用的时间，如下所示:

```
>>> calculate_sum(10000)
Calling calculate_sum time: 0.00026
50005000
```

# 14. [LEGB 规则](/6-things-to-know-to-demystify-namespaces-and-scopes-in-python-f5353c7ff56f)

我没有讨论的一件事是我们在闭包部分用来创建闭包的关键字`nonlocal`。它属于属性查找顺序的主题。虽然我们说属性，但它也适用于函数，因为你可以把一切都看作属性，因为它们属于一些对象，按照 Python 的 OOP 设计模式。这条规则被称为**L**ocal-**E**nclosing-**G**lobal-**B**内置(LEGB)规则。

*   **局部**。当你创建一个函数时，它定义了一个局部作用域。在闭包例子中，每个函数(内部或外部)都有自己的局部作用域——函数体。
*   **括住**。当你有一个内部函数时，它有一个封闭作用域，本质上是外部函数的局部作用域。要修改封闭函数中的变量，必须使用`nonlocal`关键字。否则，Python 会认为你试图在定义局部变量之前访问它。
*   **全局**。这个范围本质上是您的 Python 文件或模块级别。虽然这里没有涉及，但是有些人可能已经看到了`global`这个关键词。拥有全局变量的基本原理类似于`nonlocal`关键字，前者与改变定义在全局范围内的变量有关。
*   **内置**。启动 Python 控制台时您可以访问的任何内容都被视为内置范围，例如所有内置函数和模块。

每当你使用一个变量或者一个函数时，LEGB 规则被应用，试图通过搜索范围来弄清楚你的代码意味着什么，如上所述。

# 15.[类型标注](/advanced-python-consider-these-10-elements-when-you-define-python-functions-61c0be8a10ed)

Python 是一种动态编程语言，当我们需要将特定类型的现有变量赋值为不同类型时，我们可以灵活地使用它。这种操作在其他一些语言中可能是非法的，在这些语言中，变量是在声明之后键入的。

```
number = 1
number = "one"
```

因此，指定任何 Python 变量的类型是一个长期被忽视的话题。从 Python 3.5 开始，提供类型提示已经成为可能。让我们将它应用于一个函数。

```
def single_dict(key: int, value: str) -> dict:
    return {key: value}
```

要给变量一个类型提示，只需使用语法:`var_name: type`。在括号中，您可以对每个参数应用此语法。要指示返回值的类型，可以在冒号前的括号后面使用`-> type`。

# 16.[函数文件字符串](/advanced-python-consider-these-10-elements-when-you-define-python-functions-61c0be8a10ed)

当您定义一个函数时，为了描述该函数做什么，为该函数编写文档字符串是很有用的。文档串可以被认为是函数的正式文档。它通常有四个基本组成部分。

*   总体描述:用一句话来描述该功能的操作、预期目的等。
*   参数列表:描述每个参数
*   返回值:函数返回的内容
*   异常(可选):描述该函数可以引发哪些异常。

下面的代码向您展示了一个如何为函数编写 docstring 的示例。

# 17.函数与方法

假设我们从一个`list`对象开始:

```
numbers = [1, 2, 3, 4, 5]
```

我们想删除列表中的最后一项对象编号。为此，我们可以使用`pop`方法，如下所示。

```
>>> numbers.pop()
5
>>> numbers
[1, 2, 3, 4]
```

你注意到我用 method 来引用`pop`函数了吗？总的来说，你听说过有些函数是方法吗？所有的方法都是函数。方法的特别之处在于它们“属于”一个对象。当一个函数被定义在一个全局层次上时，我们通常简称它为函数。然而，如果它是一个类中定义的函数，它就是一个方法。

值得注意的是，当一个函数在一个类中定义时，有不同种类的方法，如下所述。

# 18.[实例、类和静态方法](https://medium.com/swlh/declare-your-first-python-class-understand-3-basic-components-15768c8d35b0)

当您创建自定义类时，为了增加代码的灵活性，我们通常在类中定义多个函数。根据函数的用途，这些方法可以分为三类。

*   实例方法。这些方法旨在由类的实例对象调用。因此，它们应该是与实例对象相关的操作。
*   静态方法。这些方法与任何实例或类都没有密切关系。相反，它们通常提供一些实用函数，即使在类之外，这些函数对对象也很有用。他们的定义包括使用`staticmethod`装饰器。
*   类方法。这些方法旨在由类调用，并且应该与类的操作相关。他们的定义包括使用`classmethod`装饰器。

下面的代码向您展示了这些类型的方法。`study`函数是一个实例方法，因为每个学生都需要学习。`is_adult`函数是一个静态方法，因为我们可以使用这个函数来测试一个学生是否是成年人，或者测试任何人(不同班级的)是否是成年人。`students_from_list`函数是一个类方法，因为我们需要使用类构造函数来创建多个实例。

顺便说一下，实例方法中的`self`指的是调用该方法的实例对象，类方法中的`cls`指的是类(也就是我们例子中的`Student`)。

类方法

# 19.[物业装修](/how-to-use-property-decorators-to-prevent-api-break-changes-ed0e8c8b4949)

在一个类中，与实例方法一起使用的一个常见装饰器是`property`装饰器。通过使用属性装饰器，您可以使用点符号来访问函数，而无需显式调用它。换句话说，该方法似乎变成了一个常规属性。考虑下面的例子:

物业装饰

property decorator 还支持 setter 和 deleter，它们处理这个属性的设置和删除。您可以参考我以前的文章来了解关于这个特性的更多信息，以及您希望使用属性的原因。

# 20.[公共与非公共方法](/advanced-python-9-best-practices-to-apply-when-you-define-classes-871a27af658b)

如果你曾经使用过其他的 OOP 语言，你不会对封装感到陌生，它涉及到公共和非公共方法的区别。在 Python 中，严格来说，没有什么是公共的。但是，有一个使用下划线作为前缀来表示非公共方法的约定。这些非公共方法是供内部使用的，而公共方法是供类的用户使用的。考虑下面的代码片段作为示例。

```
class Product:
    def _adjust_price(self):
        print("This is a protected method.") def __order_more(self):
        print("This is a private method")
```

*   我们使用一个下划线作为前缀来表示一个受保护的方法，它是在内部访问的或者是对一个子类的访问。
*   我们使用双下划线作为前缀来表示私有方法，它只能在类内部访问。这意味着这个方法不能在类之外的任何地方使用。

尽管这不是期望的行为，但是您实际上可以访问一个包含 mangling 技术的私有方法，它将私有方法转换为`_Class__private`，如下所示。

```
>>> product = Product()
>>> product._Product__order_more()
This is a private method
```

# 21.[特殊方法](/5-pairs-of-magic-methods-in-python-you-should-know-f98f0e5356d6)

与在定义函数时使用下划线相关，有一种函数使用两对双下划线，一对在函数名之前，另一对在函数名之后。这些函数被称为特殊方法，或魔术方法，或邓德方法。例如，要得到一个物体的大小，你可以使用`__sizeof__`的特殊方法:

```
>>> numbers = [1, 2, 3, 4, 5]
>>> numbers.__sizeof__()
104
```

在类中，可以重写特殊方法来实现自定义行为。最值得注意的是初始化方法:`__init__`。另一个值得注意的是`__repr__`，这是一个实例的字符串表示方法，使用它可以重新创建实例对象。下面显示了一个示例:

字符串表示

# 22.[匿名函数(Lambdas)](/the-top-4-misuses-of-lambdas-in-python-e419f426b74f)

匿名函数是未命名的函数，其声明使用 lambda 关键字，因此它们也被称为 lambda 函数或简称为 lambda。它使用以下语法:`lambdas params: expression`。lambda 函数最常见的用途是创建一个执行简单工作的小函数。例如，我们可以使用 lambdas 来设置 sorted 函数中的 key 参数，以定义自定义的排序行为。下面显示了一个示例:

```
>>> grades = [("John", [95, 94, 89]), ("Jen", [83, 100, 98]), ("Zoe", [95, 88, 93])]
>>> sorted(grades, key=lambda x: sum(x[1]))
[('Zoe', [95, 88, 93]), ('John', [95, 94, 89]), ('Jen', [83, 100, 98])]
```

尽管 lambda 函数似乎是定义函数的一种简单方法，但它们永远不应该取代常规定义的函数。

# 23.[部分功能](/5-advanced-python-function-concepts-explained-with-examples-dcf10389ac9a)

部分函数是通过向现有函数传递参数而创建的函数。部分函数的一个主要优点是，它可以通过在创建部分函数时设置默认参数来简化函数调用，从而可以省略这些参数。以下代码片段向您展示了如何创建部分函数:

部分功能

如上所示，部分函数`incrementer_by_one`现在只需要一个参数，而不是两个。

# 24.[异步功能](/getting-started-with-asynchronous-programming-in-python-2797129dd0b3)

异步是一种通用的编程技术，旨在通过将一些计算量大或时间长的进程委托给不同的进程或线程来提高项目的性能。例如，当您发出 web API 请求时，您可以实现异步编码以在不同的线程中触发请求，并且您仍然可以在当前线程上工作，当 API 响应时，您可以在响应可用时使用它，而不需要等待可能延长的时间。

以上是对异步编程非常笼统的讨论。在 Python 中，我们可以利用`asyncio`模块实现异步编程。具体来说，我们可以创建异步函数。作为对比，让我们考虑一个不使用任何异步函数的 web 应用程序场景。

无异步的函数

如你所见，完成应用程序页面的加载需要 5 秒钟。当然，出于模拟的目的，我有意延长了每个步骤，以向您展示可能的差异。通过应用异步技术，我们将获得以下实现:

异步函数

在这个版本中，现在只需要 3 秒钟就可以为用户完成应用程序的加载，显著改善了用户体验。如您所见，异步函数的定义需要使用`async`关键字。在函数中，你可以看到一些新的技术，比如关键字`await`，以及`asyncio`的`gather`函数。想了解更多，可以参考[我之前的文章](/getting-started-with-asynchronous-programming-in-python-2797129dd0b3)。

## 结论

在本文中，我们几乎回顾了 Python 函数的所有重要内容。这些要点应该足以让您为在 Python 中定义和使用函数打下坚实的基础。

感谢阅读这篇文章。这是一篇很长的文章，但是我希望你喜欢它，要么学习新的东西，要么更新你所熟知的东西。