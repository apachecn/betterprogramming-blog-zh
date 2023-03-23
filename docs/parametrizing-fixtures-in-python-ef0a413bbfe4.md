# Python 中的参数化夹具

> 原文：<https://betterprogramming.pub/parametrizing-fixtures-in-python-ef0a413bbfe4>

## 在 Pytest 中实现带参数的 fixtures 的两种方法

![](img/10a95df4b742caf99d6f5b07e80e654f.png)

照片由[克里斯里德](https://unsplash.com/@cdr6934?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/@cdr6934?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

用参数定制夹具是一个常见的用例。然而，我发现很少有人记载和交流这是可能的。

我将介绍 Pytest 中参数化夹具的两种方法(夹具工厂和隐式参数化)，并以讨论何时使用哪种方法结束。

# 夹具概述

Fixtures 是减少重复代码块的辅助函数。主要的例子是一组单元测试，我们在每个测试中做一些或多或少复杂的初始化——比如创建一个类:

对于这篇文章，推荐理解一般的单元测试，特别是`pytest`:一个可能的阅读是我以前的一篇文章，也是[介绍夹具和参数化](https://medium.com/@hrmnmichaels/unit-testing-with-pytest-5c59cdf89529)——我们在这里构建的基础。

```
class MySummationClass:
    def sum(self, x, y):
        return x + y

def test_sum():
    my_summation_class = MySummationClass()
    assert my_summation_class.sum(2, 3) == 5

def test_sum_neg():
    my_summation_class = MySummationClass()
    assert my_summation_class.sum(-2, -3) == -5
```

我们可以将这些共享的代码封装在一个夹具中，并将其合并到每个测试中，而不是放在每个单元测试中:

```
@pytest.fixture
def my_summation_class():
    return MySummationClass()

def test_sum(my_summation_class):
    my_summation_class = MySummationClass()
    assert my_summation_class.sum(2, 3) == 5

def test_sum_neg(my_summation_class):
    my_summation_class = MySummationClass()
    assert my_summation_class.sum(-2, -3) == -5
```

# 参数化夹具

让我们看看下面这个激励人心的例子:

```
import math
from dataclasses import dataclass
from typing import Callable
import pytest

@dataclass
class Circle:
    radius: float
    def get_area(self) -> float:
        return 2 * math.pi * self.radius

@pytest.fixture
def new_circle() -> Circle:
    return Circle(5)

def test_get_area_v1(new_circle: Circle) -> None:
    assert new_circle.get_area() == pytest.approx(31.4159265359)
```

我们定义了一个定义圆的[数据类](https://medium.com/better-programming/how-to-setup-data-classes-in-python-ffd85549523c):它通过半径来描述，并提供了一个计算圆的面积的函数。

然后我们定义一个 fixture，创建并返回一个新的`Circle`实例——最后测试函数`get_area`,使用这个 fixture 提供一个样本对象。

现在，如果我们想要参数化这个夹具——也就是说，有某种方法来定义一个以半径为参数并返回相应圆的夹具，该怎么办？这迟早会派上用场，而且也很容易做到——尽管在我看来交流很少。因此，我们在这里对此进行详细描述。

# 方法一:夹具工厂

每当我们想在函数内部的任何地方创建多个对象时，都可以使用 fixture factory。

我们使用一个普通的 Pytest fixture，但是我们没有返回容易构造的对象，而是返回一个 function 对象，将`radius`作为一个参数并创建一个适当的圆。另外，注意正确的 [mypy 注释](https://mypy.readthedocs.io/en/stable/):

```
@pytest.fixture
def new_circle_factory() -> Callable[[float], Circle]:
    def _create(radius: float) -> Circle:
        return Circle(radius)
    return _create

def test_get_area_v2(new_circle_factory: Callable[[float], Circle]) -> None:
    assert new_circle_factory(5).get_area() == pytest.approx(31.4159265359)
    assert new_circle_factory(15).get_area() == pytest.approx(94.2477796077)
```

# 方法 2:隐式参数化

另一种方法源于夹具已经隐式支持参数化这一事实。当我们想将参数化夹具与其他类型的参数化结合起来时，可以使用这种方法。

为此，我们向 fixture 函数添加一些参数——然后在我们的“普通”参数化修饰器中定义这些参数——但是不要在测试函数的签名中包含这些参数。这样，`pytest`将隐式地把这个参数传递给 fixture:

```
@pytest.fixture
def new_circle_parametrized(radius: float) -> Circle:
    return Circle(radius)

@pytest.mark.parametrize(
    "radius, expected_area", [(5, 31.4159265359), (15, 94.2477796077)]
)
def test_get_area_v3(new_circle_parametrized: Circle, expected_area: float) -> None:
    assert new_circle_parametrized.get_area() == pytest.approx(expected_area)
```

# 比较

我们以关于何时使用这两种方法的简短讨论来结束本文:没有哪一种方法更好或更差，两者都有其合理的用例及应用场景。相反，正如在他们各自的介绍中已经提到的，这主要取决于我们想在哪里以及如何使用固定物。以下是一些使用它们的方法:

1.  当我们想要灵活地使用 fixture 来定义对象时，可能是按需的，并且在一个或多个测试函数中的许多不同的地方——我们需要方法 1。
2.  如果我们需要“在正常程序范围之外”的 fixture，例如，在另一个参数化的装饰器内部——我们必须求助于方法 2。

这就结束了参数化夹具的使用。希望这对你有用。感谢阅读！