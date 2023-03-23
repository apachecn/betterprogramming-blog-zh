# 17 Python 面试问答

> 原文：<https://betterprogramming.pub/17-python-interview-questions-and-answers-ab438c92866c>

## 熟悉一些常见的 Python 面试问题，这样它们就不会在你下次面试时让你措手不及

![](img/501f0569e722c8f695ec4e8a1956a4b7.png)

由 [KOBU 机构](https://unsplash.com/@kobuagency?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

科技行业正以前所未有的速度增长。我们不时会看到新的软件产品在市场上发布。因此，无论您是新手还是有经验的 Python 开发人员，总会有机会等着您。

唯一的要求是你必须用你的技能说服你的潜在雇主。这可以通过出现在 Python 编程访谈中来实现。

但是你必须做好准备。否则，别人可能会得到这份工作。你可以尝试 [Python 编程挑战](https://livecodestream.dev/challenge/)或者简单概述一下常见的 Python 面试问题和答案。

今天，我将与您分享我在 Python 采访中的亲身经历。我会列出他们问我的问题，包括他们可能的解决方案。所以这将是你成为 Python 程序员的终极指南。

# 1.采用 Iris 数据集，只检索那些“Sepal”。“长度”大于 6 且“萼片。“宽度”大于 3.3

## **虹膜数据集详情**

*   [官网](https://archive.ics.uci.edu/ml/datasets/Iris)
*   [以 CSV 格式下载](https://tableconvert.com/?output=csv)

## **代码**

```
import pandas as pdiris = pd.read_csv('iris.csv')df = pd.DataFrame(iris) for i, j in df.iterrows():
    if (j['sepal_length'] > 6) & (j['sepal_width'] > 3.3):
        print(j)
        print()
```

## **输出**

```
sepal_length          7.2
sepal_width           3.6
petal_length          6.1
petal_width           2.5
species         virginica
Name: 109, dtype: objectsepal_length          7.7
sepal_width           3.8
petal_length          6.7
petal_width           2.2
species         virginica
Name: 117, dtype: objectsepal_length          7.9
sepal_width           3.8
petal_length          6.4
petal_width             2
species         virginica
Name: 131, dtype: objectsepal_length          6.3
sepal_width           3.4
petal_length          5.6
petal_width           2.4
species         virginica
Name: 136, dtype: objectsepal_length          6.2
sepal_width           3.4
petal_length          5.4
petal_width           2.3
species         virginica
Name: 148, dtype: object
```

# 2.我们如何将相应的项目添加到这两个数组中？

## **阵列**

```
a = [23,67,1]
b = [98,543,7]
```

## **代码**

```
import numpy as npa = np.array([23,67,1])
b = np.array([98,543,7])c = np.sum((a,b), axis=0)j = 0for i in c:
    print("Index_" + str(j) + ":", i)
    j += 1
```

## **输出**

```
Index_0: 121
Index_1: 610
Index_2: 8
```

# 3.什么是`'*args’`和`'**kwargs’`？分别举例说明。

这两者都用于在函数中传递可变数量的参数。我们将`*args`用于非关键字参数，而`**kwargs`用于基于关键字的参数(例如，键值对)。

## `**'*args'**` **例题**

```
def myFun1(*argv): 
    for a in argv: 
        print(a)

myFun1('Welcome', 'to', 'Live Code Stream')
```

## **输出**

```
Welcome
to
Live Code Stream
```

## `'**kwargs’` **举例**

```
def myFun2(**kwargs): 
    for k, v in kwargs.items():
        print ("%s = %s" %(k, v))myFun2(username = 'John Doe', email = 'example@domain.com', password = 'Abc123')
```

## **输出**

```
username = John Doe
email = example@domain.com
password = Abc123
```

# 4.如何检查模块中所有可用的函数和属性？

我们可以在`dir()`函数中传递模块名来检索它的函数和属性名。

## **举例**

假设我们有一个名为`m.py`的模块，它有一个变量和两个用户定义的函数。

```
name = "Juan"def myFun():
    returndef myFun2():
    return
```

现在，我们可以在另一个文件中显示它的属性和函数名，使用:

```
import m
print(dir(m))
```

## **输出**

```
['__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', 'myFun', 'myFun2', 'name']
```

在这里，您可以看到`dir()`函数也获得了所有的内置属性和方法。

# 5.Python 中的文字是什么？

在 Python 中，*文字*是赋给变量或常量的数据/值。例如，Python 有四种不同类型的文字。

*   数字文字
*   字符串文字
*   布尔文字
*   特殊文字

# 6.如何连接两个元组？

元组的*连接*指的是我们可以连接两个或更多元组的过程。例如，假设我们有两个元组:

```
tuple_1 = (True,"Juan",561)
tuple_2 = (47,100,False)
```

现在，我们可以使用加号`+`将它们连接在一起。基本上，这个语句会在`tuple_1`的末尾加上`tuple_2`的元素。

```
tuple_1 + tuple_2
```

像这样:

```
(True, 'Juan', 561, 47, 100, False)
```

# 7.Python 中的 Lambda 是什么？

λ是 Python 中的一个小函数，它只能处理一个表达式。但是我们可以根据需要添加任意多的参数。

通常，在另一个函数中使用 lambda 函数更合适。

让我们使用 lambda 函数将 14 乘以一个通过参数传递的数字。

```
x = lambda a: a * 14
print(x(3))
```

## **输出**

```
42
```

# 8.切片是什么？

*切片*是检索字符串、数组、列表或元组的一部分的过程。基本上，我们传递一个开始和结束索引来指定我们感兴趣的数据的位置。值得注意的是，开始索引处的值包含在结果中，而结束索引处的值被排除在外。

我们甚至可以通过一个*步长值*来跳过一些数据。例如，这可以从数组中检索每隔一项。

在下面的代码片段中，切片是使用方括号`[]`执行的。我们传递了三个参数，并用冒号`:`分隔它们。第一个参数指定切片的开始位置，第二个参数用于标记结束位置，最后一个参数用于定义步骤。

```
countries = ("Germany", "Pakistan", "China", "Turkey", "Australia", "France")print(countries[0:5:2])
```

## **输出**

```
('Germany', 'China', 'Australia')
```

切片的三个参数都是可选的。如果我们不指定开始，那么 Python 将假设一个 0 索引作为开始位置。同样，当我们跳过第二个参数时，那么将使用数组/字符串/元组/列表的长度。然而，默认情况下，Python 认为 1 是一个步骤。

# 9.什么是 Python Decorators？

Python 装饰器是用来增强现有函数或类的功能的特性。当开发人员想要动态更新一个函数的工作而不实际修改它时，这是首选。

假设我们有一个打印网站开发者名字的函数。但是现在的要求是向用户显示欢迎消息，然后显示开发人员姓名。

我们可以在装饰函数的帮助下添加这个功能。

```
def welcome_user(func):
    def inner(a, b):
        print("Welcome to Live Code Stream!")
        return func(a, b)
    return inner@welcome_user
def dev_name(first_name, last_name):
    print("Developed By:", first_name, last_name)dev_name("Juan", "Cruz Martinez")
```

在这里，你可以看到`welcome_user()`是一个装饰器，而`dev_name()`是我们动态更新的主函数。

## **输出**

```
Welcome to Live Code Stream!
Developed By: Juan Cruz Martinez
```

# 10.`the 'sort()’`和`'sorted()'`函数使用哪种算法？

`sort()`和`sorted()`函数实现了 Timsort 算法。这种排序算法非常稳定和高效。大 O 在其最坏情况下的值是 *O(N log N)。*

# 11.如何调试 Python 程序？

默认情况下，Python 附带了一个内置的调试器，称为 [pdb](https://docs.python.org/3.6/library/pdb.html) 。

我们可以通过执行如下命令开始调试任何 Python 文件:

```
python -m pdb your-python-file.py
```

# 12.什么是酸洗和拆洗？

在 Python 中，有一个非常流行的库叫做 [pickle](https://docs.python.org/3/library/pickle.html) 。它用于对象序列化——这意味着它将 Python 对象作为输入，并将其转换为字节流。转换 Python 对象的整个过程被称为*酸洗。*

另一方面，*拆线*是它的对立面。这里，一个字节流作为输入被接受，并被转换成一个对象层次结构。

# 13.什么是列表理解？举个例子。

*列表理解*是创建 Python 列表的快捷方式。我们可以通过迭代数据来填充列表，而不是手动为每个索引输入一个值。

假设我想创建一个列表，其中每个索引都包含一个来自我名字的字母，按顺序排列。

```
name_letters = [ letter for letter in 'Juan Cruz Martinez' ]
print( name_letters )
```

## **输出**

```
['J', 'u', 'a', 'n', ' ', 'C', 'r', 'u', 'z', ' ', 'M', 'a', 'r', 't', 'i', 'n', 'e', 'z']
```

# 14.`(i for i in (54, 6, 71)):`是元组理解吗？

不。在 Python 中，没有元组理解这样的概念。

# 15.Python 中的猴子补丁是什么？

在运行时动态改变一个类或模块的过程被称为*猴子补丁。*

## **website_name.py**

```
class Website_name: 
     def func(self): 
          print("Live Code Stream")
```

## **main.py**

```
import website_name 
def welcome(self): 
     print("Welcome to Live Code Stream")

# replacing address of "func" with "welcome" 
website_name.Website_name.func = welcome 
obj = website_name.Website_name() 

# calling function "func()" whose address got replaced with function "welcome()"
obj.func()
```

## **输出**

```
Welcome to Live Code Stream
```

你注意到了吗，我实际上调用了`func()`方法，但是我收到的输出来自`welcome()`？

# 16.预测下面代码的输出？解释一下你的答案。

## **代码**

```
class Parent(object):
    x = 53class Child_1(Parent):
    passclass Child_2(Parent):
    passprint(Parent.x, Child_1.x, Child_2.x)
Child_1.x = 12
print(Parent.x, Child_1.x, Child_2.x)
Parent.x = 89
print(Parent.x, Child_1.x, Child_2.x)
```

## **输出**

```
53 53 53
53 12 53
89 12 89
```

## **解释**

这段代码中最容易混淆的地方出现在最后一个`print()`语句中。

在打印之前，我们只是更新了`Parent`类中 *x* 的值。它会自动更新`Child_2.x`的值，但不会更新`Child_1.x`的值。这是因为我们已经设置了`Child_1.x`的值。

换句话说，Python 试图首先使用子类的属性/方法。如果在子类中没有找到属性/方法，它只搜索父类。

# 17.如何在二叉树中显示给定节点的祖先？

假设我们有一棵二叉树。现在，检索`65`的祖先并使用 Python 代码显示它们。

```
 58
            /    \
          42       3
        /    \
      0       65
    /
  17
```

## **代码**

```
class Node:  
    # Create a new node 
    def __init__(self, data): 
        self.data = data  
        self.left = None
        self.right = Nonedef displayAncestors(root, target):
    # Base case 
    if root == None: 
        return False 

    if root.data == target: 
        return True 

    # Print this node if target is present in either left or right subtree
    if (displayAncestors(root.left, target) or 
        displayAncestors(root.right, target)):
        print(root.data)
        return True # Else
    return False# Test above function
root = Node(58)
root.left = Node(42)
root.right = Node(3)
root.left.left = Node(0)
root.left.right = Node(65)
root.left.left.left = Node(17)displayAncestors(root, 65)
```

## **输出**

```
42
58
```

# 结论

为面试而练习对获得你梦想中的工作非常重要。在今天的文章中，我们讨论了一些流行的面试问题，但是还有更多你应该知道的。有很多网站可以让你为下一次面试做好准备——这是一个很大的话题，所以继续学习吧。

感谢阅读！