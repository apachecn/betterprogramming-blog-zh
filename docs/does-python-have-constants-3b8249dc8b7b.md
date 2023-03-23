# Python 有常量吗？

> 原文：<https://betterprogramming.pub/does-python-have-constants-3b8249dc8b7b>

## 你能定义不变的值吗？

![](img/b4b67d8ad54067580cb337d7b3717d41.png)

Gabriel Crismariu 在 [Unsplash](https://unsplash.com/s/photos/timeless?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

从其他语言(包括 PHP 和 JavaScript)过渡而来的常量已经在我的实践中根深蒂固。

当我采用 Python 时，我很快发现自己在问这个问题，Python 有常量吗？

答案是有几分，但也不尽然。我们来深入挖掘一下！

# 什么是常数？

在我们继续之前，让我们定义一下什么是常数，以防你不熟悉。

常量值类似于变量，只是一旦设置就不能更改。常量有多种用途，从设置静态值到编写更多语义代码。

# 如何在 Python 中实现常数

我之前说过 Python“有点，但不是真的”有常量。那是什么意思？这意味着您可以遵循一些标准约定来模拟常量的语义感觉，但是 Python 本身并不像其他实现常量的语言那样支持不变的值赋值。

如果你和我一样，经常使用常量来编写更清晰的代码，那么请遵循以下指导方针，在 Python 代码中实现常量:

*   **在名字中全部使用大写字母:**首先，也是最重要的，你希望你的常量从你的变量中脱颖而出。这在 Python 中甚至更为关键，因为从技术上讲，您可以覆盖您设置为常量的值。
*   **不要过度缩写名字:**常量——实际上是任何变量——的目的是在以后被引用。这需要明确。避免使用单字母或通用名称，如`N`或`NUM`。
*   **创建一个单独的 constants.py 文件:**要在结构和命名中添加有意组织的元素，通常的做法是为常量创建一个单独的文件，然后导入该文件。

这一切在实践中看起来像什么？

我们将创建两个文件，`constants.py`和`app.py`来演示。

第一，`constants.py` **:**

```
# constants.py
RATIO_FEET_TO_METERS = 3.281
RATIO_LB_TO_KG = 2.205
```

接下来，`main.py`:

请注意，常量值在代码中以及在文件本身之外更加明显。这两种品质都有助于传达常量和变量之间的区别。

如果您不想连续键入`constant.`，您可以单独导入值:

```
from constants import RATIO_LB_TO_KG
from constants import RATIO_FEET_TO_METERSprint(RATIO_LB_TO_KG) # 3.281
print(RATIO_FEET_TO_METERS) # 2.205
```

不幸的是，在采取了所有这些步骤之后，仍然有可能覆盖这些值:

```
from constants import RATIO_LB_TO_KGprint(RATIO_LB_TO_KG) # 3.281
RATIO_LB_TO_KG = 1
print(RATIO_LB_TO_KG) # 1
```

你怀念在 Python 中使用真常量吗？你同意 Python 把他们排除在外的决定吗？在下面留下你的想法和经历吧！