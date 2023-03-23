# 使用鲜为人知的 Classmethod Decorator 向类中添加构造函数

> 原文：<https://betterprogramming.pub/use-the-little-known-classmethod-decorator-to-add-constructors-to-your-classes-464e96d8fe86>

## 改进您的 Python

## 学习一些关于类方法装饰器、类和继承的有用知识

![](img/478e8757787329b8468b2687b535613f.png)

照片由 [Denys Nevozhai](https://unsplash.com/@dnevozhai?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/giving-down?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

装饰器、类和继承是相当高级的概念。大多数时候，有另一种方法来解决一个给定的问题，并且很可能你还没有给 decorators 或类太多的考虑，更不用说继承了。理应如此。你没必要这么做。

但是，当你在成为更好的程序员的旅程中前进时，你开始在这里和那里遇到类。

随着您越来越关注代码的可读性和可维护性，而不是“我只是想让它工作”这一部分，类就成了您的武器库中不可或缺的工具。

在这个故事中，我想展示一个典型的类用例，尤其是`@classmethod`装饰器。

# Python 中的类简介

## 类别定义

虽然 Python 当然可以，而且经常被用作脚本语言，但它是作为面向对象的编程语言而设计的。

面向对象编程(OOP)意味着程序具有围绕数据或对象而不是功能和逻辑来组织的强烈倾向。

这通常意味着一个类代表了一些更有形的东西背后的思想或概念，比如一个合理地特定于您的业务的实际对象。

例如，当使用谷歌广告时，一个课程可以是一个活动。当使用市场时，一个类可以是一个参与者。使用教育应用程序时，班级可以是学生、教师或教室。

## 对象实例化

类通常定义一个`__init__`函数，当调用它时，实例化一个对象。

```
**IN:**
class Car():
    def __init__(self,make,year):
        self.make = make
        self.year = yearmy_car = Car('Honda',1984)print(my_car.make)**OUT:** 
'Honda'
```

一个对象(`my_car`)是一个实例化的类(`Car`)，实际上对于一个给定类可以实例化多少个对象没有限制。例如，你可以实例化更多的汽车，比如`your_car = Car('BMW', 2017)`。一个类就像一个蓝图或者一个模式，一个对象就是一个真实的东西。

## 实例方法

现在您有了一个对象，在这一点上它只不过是一个数据容器。现在，您可以在类上定义每个实例化的对象都可以访问的函数(方法)，如下所示:

```
**IN:**
class Car():
    def __init__(self,make,year):
        self.make = make
        self.year = year
        self.status = 'dirty'def clean_car(self):
        print('cleaning the car!')
        self.status = 'clean'my_car = Car('Honda',1984)print(f'The car is : {my_car.status}')
my_car.clean_car()
print(f'The car is : {my_car.status}')**OUT:** 
The car is : dirty
cleaning the car!
The car is : clean
```

正如`__init__`中设定的那样，每辆车一开始都很脏。但是这不是问题，因为我们已经定义了一个`clean_car()`方法，它清洗汽车。

## 遗产

在下面的例子中，我们将利用一个叫做继承的概念。像几乎所有面向对象语言一样，Python 中的类可以被子类化。

能够子类化意味着你可以将原始类的通用逻辑和一些特定的逻辑添加到子类中。

```
**IN:**
class Person(object):
    def __init__(self,first_name,last_name):
        self.first_name = first_name
        self.last_name = last_nameclass Teacher(Person):
    def __init__(self,first_name,last_name,grade):
        self.grade = grade
        super(Teacher, self).__init__(first_name,last_name)mr_smith = Teacher('Joe','Smith',4)
print(mr_smith.first_name)
print(mr_smith.grade)**OUT:** Joe
4
```

在上面的逻辑中，`Teacher`类用老师教的年级扩展了`Person`类。

# 更高级的例子

## 设置

在下面的代码片段中，我们设置了一个伪数据库和一个读写方法。

实际上，您可能在自己选择的数据库之上有一些抽象层。但是逻辑，即通过 ID 的存储、读取和写入逻辑，可能是相似的。

## 示例:基类

首先，我们创建一个基类。当看到`__init__`方法时，我们的基类不应该被实例化的事实变得很明显。

这个类的实例化只会产生一个带有 `_id`的对象，其他什么都没有。虽然你可以做，但这没有用。

让我们一步一步地了解它的功能:

*   `__init__`为`self._id`分配一个传递的`_id`值或随机生成的值。
*   `@classmethod` `_instanciate_from_db`需要两个自变量，1。`cls`(类的简称)和 2。安`_id`。然后，它根据给定的 ID 从`cls._table`获取一条记录，解包并将其传递回类。在基类上定义`_table`是没有意义的，因为这将告诉我们在哪个表中寻找`_id`。因为对应于我们的派生类的实例的数据库对象将驻留在不同的表中，所以有必要在派生类而不是基类上实现`_table`。
*   `to_dict`以字典形式返回不以`_`开头的类的属性。
*   `__repr__`调用`to_dict`并通过`json.dumps`将其序列化。

## 示例:子类

我们有两个，来自`Base`，派生类，`Student`和`ClassRoom`。

两个派生类都定义了`_table`和一个`__init__`方法。两者都调用 super(即，在它们的 init 方法中基类上的`__init__`方法)。

## 示例:将所有内容放在一起

```
**IN:**
print(Student(name='Charly',age=16))
print(Student._instanciate_from_db(_id='1234'))print(ClassRoom(floor=13,seats=30))
print(ClassRoom._instanciate_from_db(_id='abcd'))**OUT:** {"age": 16, "name": "Charly"}
{"age": 15, "name": "John Doe"}
{"floor": 13, "seats": 30}
{"floor": 10, "seats": 15}
```

我们现在可以在`Student`和`ClassRoom`对象上使用`_instanciate_from_db`方法。两者都从基类继承方法。

由于我们使用了`@classmethod` decorator，该方法可以访问类对象，因此可以使用我们在派生类中定义的`_table`来指定我们想要在哪个表中查找 ID。

# 值得注意的观察

*   虽然方法`_instanciate_from_db`是在基类中定义的，并且引用了一个`cls`，但是当在派生类中调用时，它引用的是被调用方法的派生类，而不是基类。
*   `@classmethod`装饰器导致类(相对于实际的对象)被传递给函数。
*   `@classmethod`装饰器是实现额外构造函数的完美方式。