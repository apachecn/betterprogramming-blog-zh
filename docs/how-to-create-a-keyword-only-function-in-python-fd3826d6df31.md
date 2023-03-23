# 如何在 Python 中创建仅包含关键字的函数

> 原文：<https://betterprogramming.pub/how-to-create-a-keyword-only-function-in-python-fd3826d6df31>

## 要求函数调用中的每个参数都有变量名

![](img/d413cd057ee29c2515847f470b56b640.png)

米克·豪普特在 Unsplash[拍摄的照片](https://unsplash.com/s/photos/scrabble?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

从 Python 3.8 开始，可以将函数参数显式定义为仅关键字。

什么是关键字参数？传统上，当您定义和调用函数时，参数是按顺序列出的，并且必须以相同的方式调用。这意味着第一个传递的值被赋给第一个定义的参数，依此类推。

以前在 Python 中，参数中有默认值的函数也可以通过关键字调用。

```
def areaRectangle(length=1, width=1):
   print("Length:", length)
   print("Width:", width)
   print("Area:", length * width)areaRectangle(3,5)
"""
Length: 3
Width: 5
Area: 15
"""areaRectangle(width=3, length=5)
"""
Length: 5
Width: 3
Area: 15
"""
```

虽然灵活，但还是希望函数的定义明确。

# 定义仅关键字函数

如果您想要定义一个函数，其中所有参数都需要通过其变量名来引用，请以星号作为第一个参数来开始您的参数列表。

```
def areaRectangularPrism(*,length, width, height):
   return length * width * heightareaRectangularPrism(3,4,5)
# TypeError: areaRectangularPrism() takes 0 positional arguments but 3 were givenareaRectangularPrism(width=3, height=4, length=5)
# 60
```

注意，传递位置参数会导致一个`TypeError`。另外，确保用逗号分隔星号和第一个参数。将它们视为两个独立的参数。

那么什么时候你想定义一个只有关键字的函数呢？

当参数列表很长并且所有参数都有默认值时，我发现使用只有关键字的函数是成功的。在这些情况下，函数可以空着传递，也可以带有任何排列的参数。在我看来，像这样的时候，要求使用关键词会让电话更加清晰易读。