# 如何在 Python 中设置数据类

> 原文：<https://betterprogramming.pub/how-to-setup-data-classes-in-python-ffd85549523c>

## 使用冻结和属性的 Python 数据类的高效数据管理

![](img/ab613dc5277407ab9fdc08a8cde4e434.png)

照片由 [Aaron Burden](https://unsplash.com/@aaronburden?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在之前的帖子中，我介绍了 Python [decorators](https://medium.com/@hrmnmichaels/python-decorators-816236066ed8) 。今天我们将仔细看看一些内置的装饰器——特别是那些可以用于“管理数据”这个广泛主题的装饰器。这里，我指的是设计用来包含和存储数据的类(`@dataclass`)，以及通过在 Python 中应用面向对象编程(OOP)风格来简化属性访问的类(`@property`)。

# 数据类

Python 中的`dataclass`装饰器为一个类配备了存储数据的助手功能——比如自动添加构造函数、重载`__eq__`操作符和`repr`函数。

让我们从一个例子开始:我们将设计一个简单的存储公司雇员的类。他们有一个名字，一份薪水，还有一个描述他们在公司工作年限的属性。

在传统 Python 中，您可能会按照以下代码行来实现这一点:

```
class Employee:
    def __init__(self, name: str, salary: int, years_with_company: int = 0):
        self.name = name
        self.salary = salary
        self.years_with_company = years_with_company

    def __eq__(self, other: "Employee"):
        return (self.name == other.name and self.salary == other.salary and self.years_with_company == other.years_with_company)

first_employee = Employee("First", 80000)
second_employee = Employee("First", 80000)

print(first_employee)
print(first_employee == second_employee)
```

上面的代码实现了一个具有前面描述的属性的类，并重载了 equals ( `__eq__`)操作符:如果没有这个操作符，检查`first_employee == second_employee`将会失败，因为 Python 不知道如何有意义地比较这个特定的类，并退回到检查两个实例是否是相同的对象。

我们进一步注意到，字符串表示(`print(first_employee)`)并没有给出太多信息，相反，它只是打印出类似于`<__main__.Employee object at 0x7f402e682670>`的内容。

现在，让 Python 来完成我们的工作，用`dataclass`装饰器来装饰我们的类！

```
from dataclasses import dataclass

@dataclass
class Employee:
    name: str
    salary: int
    years_with_company: int = 0

first_employee = Employee("First", 80000)
second_employee = Employee("First", 80000)

print(first_employee)
print(first_employee == second_employee)
```

使用这个装饰器使得 Python 自动为我们实现了一个构造函数，它的工作方式与我们之前实现的一样:它根据传递的参数分配实例属性，并且还可以处理可选参数。

接下来，在一个`dataclass`中，equals 操作符被覆盖以检查所有类属性的相等性，这与我们在上面手工实现的相同——在这个相等检查中正确地返回`true`。

此外，类实例的字符串表示现在看起来更好了(例如，`__repr__`被有意义地覆盖了)，产生了:`Employee(name=’First’, salary=80000, years_with_company=0)`。

`dataclass`装饰者做的[甚至更多](https://docs.python.org/3/library/dataclasses.html)——但是我们现在将关注这三个，可能是最常用的效果。

# 冻结数据类

由于 decorators 实际上是函数，所以它们也可以带参数:`frozen`是这里可能的参数之一。表示`dataclass`建造后不能修改。特别是，以下内容将会失败:

```
from dataclasses import dataclass

@dataclass(frozen=True)
class Employee:
    name: str
    salary: int
    years_with_company: int = 0

employee = Employee("First", 80000)

employee.name = "Test"
```

这在很多情况下是有意义的。例如，`dataclasses`用来纯粹表示不可变的数据集。

# 性能

让我们恢复上面的`frozen`更改，改为进行更细粒度的访问控制——全面实现访问实例属性的常用方式。

这个帖子不会是对 OOP 的介绍，简单来说:我们从来不想直接公开实例属性。相反，我们将有意义地把函数/变量分成`public`、`private`或`protected`(我可能会偏离 C++，但仅此而已)，并通过 getter 和 setter 方法公开公共变量——简而言之:属性。

getter 方法负责返回变量值，setter 用于设置变量值。这有助于更好地控制变量(例如，我们不希望将除数设置为 0)。

这次我将跳过实现 getter/setter 的繁琐示例，而是直接使用 Python 的`property`装饰器展示解决方案:

```
from dataclasses import dataclass

@dataclass()
class Employee:
    _name: str
    _salary: int
    _years_with_company: int = 0

    @property
    def name(self):
        return self._name

    @property
    def salary(self):
        return self._salary

    @salary.setter
    def salary(self, new_salary: int):
        if new_salary > self.salary:
            self._salary = new_salary
        else:
            print("New salary must be greater than previous!")

employee = Employee("First", 80000)

employee.salary = 75000
employee.salary = 85000
print(employee)
employee.name = "this will fail"
```

请注意变量的重命名。我们现在使用前导下划线(`_`):按照 Python 惯例。这个变量被理解为`protected`——这意味着您不应该直接访问它。

另外，请注意`@property`和`@salary.setter`以及修饰函数是如何用于定义 getter 和 setter 的，并且`employee.salary`可以用作任何其他变量(在本例中，我们假设一个慷慨的 HR 部门，它只允许加薪)。我们还跳过了为`name`实现一个 setter，使这个变量成为只读的(同样，这不是一个非常现实的假设，尽管名称在将来可能会改变)。

注意我没有为`years_with_company`实现类似的东西，但是它看起来是一样的。

至此，我们完成了“数据管理”这一主题。感谢收听，希望不久能看到你的另一篇文章！