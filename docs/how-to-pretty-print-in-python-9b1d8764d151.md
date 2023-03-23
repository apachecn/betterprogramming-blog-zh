# 如何用 Python 漂亮地打印

> 原文：<https://betterprogramming.pub/how-to-pretty-print-in-python-9b1d8764d151>

## 开始使用 pprint 库

![](img/c88cba37912289a904f942190fd5e064.png)

伊利亚·巴甫洛夫在 [Unsplash](https://unsplash.com/s/photos/computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

每个人都需要看到变量的输出、计算和循环过程——你将学会使用的第一个函数是`print()`。

很容易就此打住。这个函数完成了工作——在某种程度上。但是，如果您曾经打印过字典、列表或任何其他复杂的数据类型，那么您就已经体验过伴随着`print()`而来的一些棘手问题。

格式化打印输出是对生活质量的巨大改善。幸运的是，Python 中有一个简单的解决方案，即`pprint`库。

# 什么是 pprint？

pprint 是 Pretty Printer 的缩写，是一个本地 Python 库，允许您自定义输出的格式。参考[官方文档](https://docs.python.org/3/library/pprint.html)获取完整的库和所有可用属性列表。在本教程中，我们将回顾一下`pprint()`方法和`PrettyPrinter()`类的基本用法。

# pprint 入门

要使用 pprint，首先要在 Python 文件的顶部导入库。

```
import pprint
```

从这里开始，您可以使用`.pprint()`方法或者用`PrettyPrinter()`实例化您自己的 pprint 对象。

```
import pprintpprint.pprint("Hello pretty printer")my_printer = pprint.PrettyPrinter()
my_printer.pprint("Hello pretty printer")
```

# pprint()和 PrettyPrinter()有什么区别？

两者的区别在于`pprint()`方法将使用库的默认设置。我们将在以后讨论可以配置什么，但是现在，这意味着每个方法调用都是相互独立的。

当您创建自己的`PrettyPrinter()`对象时，您可以在创建时覆盖那些默认配置。现在，当从您创建的对象使用`pprint()`方法时，您设置的配置将被使用。

**设置不与**T10 一起存储

```
import pprint
coordinates = [
   {
       "name": "Location 1",
       "gps": (29.008966, 111.573724)
   },
   {
       "name": "Location 2",
       "gps": (40.1632626, 44.2935926)
   },
   {
       "name": "Location 3",
       "gps": (29.476705, 121.869339)
   }
]pprint.pprint(coordinates, depth=1)
# [{...}, {...}, {...}]pprint.pprint(coordinates)
"""
[{'gps': (29.008966, 111.573724), 'name': 'Location 1'},
 {'gps': (40.1632626, 44.2935926), 'name': 'Location 2'},
 {'gps': (29.476705, 121.869339), 'name': 'Location 3'}]
"""
```

**设置*与***T11 一起存储

```
import pprintmy_printer = pprint.PrettyPrinter(depth=1)coordinates = [
   {
       "name": "Location 1",
       "gps": (29.008966, 111.573724)
   },
   {
       "name": "Location 2",
       "gps": (40.1632626, 44.2935926)
   },
   {
       "name": "Location 3",
       "gps": (29.476705, 121.869339)
   }
]my_printer.pprint(coordinates)
# [{...}, {...}, {...}]
```

因此，如果您想要一次性的，那么使用`.pprint()`方法是阻力最小的途径。事实上，如果你打算这样做，直接导入方法。

```
from pprint import pprintpprint([(3,5),(7,5),(4,3)])
```

# 可以配置什么？

使用`.pprint()`可以配置六个参数。以下是每个参数、括号中的默认值以及简短描述:

*   **stream** `**(None)**`:当你想漂亮的打印到一个文件时，使用 stream 属性。它的默认行为——值为`None`—是使用`sys.stdout`
*   **缩进** `**(1)**`:每行缩进的空格数，这个值在需要特定格式时会有帮助。
*   **宽度** `**(80)**`:每行的最大字符数。如果一行超出了该值，则剩余的文本将在下面的行换行。
*   **深度** `**(None)**`:要显示的深度级别(嵌套数据类型)的数量。默认显示所有数据。指定时，超出深度限制的数据将显示为嵌套在适当数据类型语法中的`...`。
*   **compact** `**(False)**`:启用后，compact 属性将合并行内的复杂数据结构，只要它们不超过宽度限制。
*   **sort_dicts** 设置为`False`时，字典键将按插入顺序显示。