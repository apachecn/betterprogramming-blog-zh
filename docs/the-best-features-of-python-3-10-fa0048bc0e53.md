# Python 3.10 的新特性

> 原文：<https://betterprogramming.pub/the-best-features-of-python-3-10-fa0048bc0e53>

## 更新到最新 Python 版本的 4 个理由

![](img/100e9a871c77b507973e612065ca7d55.png)

由 [Ardi Evans](https://unsplash.com/@ardievans?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

现在已经发布了 Python 3.10。这个新版本没有增加新的模块，但是引入了很多新的有用的特性。新版本的安装可以从[官方 python 站点](https://www.python.org/downloads/release/python-3100a7/)完成。

# 1.有用的语法错误

添加了一些新功能，在调试代码时可能会有所帮助，除了提供“语法错误”之外，还提供了更多指出错误语法的信息。

对于刚开始使用 Python 的人来说，Python 3.10 在解决错误时会派上用场，而不是使用 StackOverflow。

```
if rocket.position > event_horizonFile "<stdin>", line 1if rocket.position > event_horizon^SyntaxError: expected ':
```

另一个很有意义的例子是:

```
{x,y for x,y in zip(‘abcd’, ‘1234’)}File “<stdin>”, line 1{x,y for x,y in zip(‘abcd’, ‘1234’)}^^^SyntaxError: did you forget parentheses around the comprehension target?
```

# **2。结构模式匹配**

顾名思义，结构化模式匹配以 match 语句和 case 语句的形式使用。模式可以是序列、映射、原始数据类型和类实例。最后，Python 有一个比 switch 语句更强大的 switch 语句。

一个简单的例子:

```
numbers = [1,2,3,4]
for n in numbers:
    match n:
        case 1:
            print("Number is 1")
        case 2:
            print("Number is 2")
        case 3:
            print("Number is 3")
        case _:
            print("Number is not 1,2 or 3")
```

关键字`match`后面是变量名。如果有匹配，将执行 case 块中的语句。
如果不匹配，则执行`case _`中的语句。

结果看起来像这样:

```
Number is 1
Number is 2
Number is 3
Number is not 1,2 or 3
```

新的结构模式匹配不仅有基本的语法，还可以处理复杂的模式。

```
def human_age(person):  # person = (name, age, gender)
    match person:
        case (name, _, "male"):
            print(f"{name} is man.")
        case (name, _, "female"):
            print(f"{name} is woman.")
        case (name, age, gender):
            print(f"{name} is {age} old.")
```

结果看起来像这样:

```
human_age(("Carol", 25, "female"))
Carol is woman.
```

python 的官方[文档](https://www.python.org/dev/peps/pep-0636/#matching-specific-values)中也有关于结构模式匹配的教程。

# **3。新型联合运算符**

一种新的类型 union 操作符以`X|Y`的形式引入，而不是使用类型化。工会。这为表达 X 型或 y 型提供了一种清晰的形式。

```
def square(number: int|float):
    return number ** 2
```

结果:

```
square(2.5)
6.25
```

新运算符也可以作为第二个参数应用于`isinstance()`和`issubclass()`。

```
isinstance("3",int|str)
Trueisinstance("GoodBye",int|str)
True
```

# **4。改进模块**

## **1。pprint()**

`pprint()`增加了新的关键字参数:`underscore_numbers`。`_`是为长数字添加的。大整数用`_`字符分隔，以使`pprint.pformat()`可读性更好。

```
>>> pprint.pformat(int(1e9),underscore_numbers=True)
'1_000_000_000'
```

## **2。人口统计**

我们可以使用人口计数或`int.bit_count()`来计算整数的二进制表示中的位数。

显示执行人口统计的示例:

```
value = 50print(bin(value))
0b101010print(value.bit_count())
3
```

## **3。统计数据**

`statistics`模块增加了新的功能。

像`statistics.covariance()`返回两个输入的联合可变性:

```
>>> import statistics
>>> x = [1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> y = [1, 2, 3, 1, 2, 3, 1, 2, 3]
>>> statistics.covariance(x,y)
0.75
```

如果两个输入的长度不同，则`StatisticsError`升高:

```
>>> x = [1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> y = [1, 2, 3, 1, 2, 3, 1, 2]
>>> statistics.covariance(x,y)
statistics.StatisticsError: covariance requires that both inputs have same number of data points
```

`statistics.correlation()`是该模块的另一个新功能。

皮尔逊相关系数 r 衡量线性关系的强度和方向。它取-1 到+1 之间的值:

```
>>>x = [1, 2, 3, 4, 5, 6, 7, 8, 9]
>>>y = [9, 8, 7, 6, 5, 4, 3, 2, 1]
>>>correlation(x, x)
1.0
```

输入必须具有相同的长度，并且不能是常数。

`statistics.linear_regression()`:线性回归描述了自变量 x 和因变量 y 之间的关系，用线性函数表示:-

```
y = slope * x + intercept + noise
```

其中`slope`和`intercept`是估计的回归参数，`noise`代表数据的可变性。

```
>>> years = [2001,2005,2010]
>>> houses_built = [5,8,14]
>>> slope, intercept = statistics.linear_regression(years, houses_built)>>> round(slope * 2017 + intercept)
21
```

如果自变量`x`为常数，将导致`StatisticsError`。

# 结论

我发现的最强大的特性是新版本 Python 中的结构模式匹配。结构模式匹配可用于匹配模式或过滤数据。

在早期使用的模块中有很多改进。

# 资源

1.  [https://docs.python.org/3.10/whatsnew/3.10.html](https://docs.python.org/3.10/whatsnew/3.10.html)
2.  [https://towards data science . com/whats-new-in-python-3-10-a 757 c6c 69342](https://towardsdatascience.com/whats-new-in-python-3-10-a757c6c69342)