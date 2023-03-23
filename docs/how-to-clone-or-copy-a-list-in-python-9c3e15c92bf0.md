# 如何在 Python 中克隆或复制列表

> 原文：<https://betterprogramming.pub/how-to-clone-or-copy-a-list-in-python-9c3e15c92bf0>

## Python 中有多种复制/克隆列表的选项

![](img/d4c1837ff3c852d12b4beb1698ee4099.png)

凯莉·西克玛在 [Unsplash](https://unsplash.com/s/photos/list?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

使用列表与原始数据类型有点不同，因为将新变量设置为与现有变量相等将创建一个引用，而不是一个独立的副本。

下面将说明这一点:

```
a = 4
b = a
a = 5
print(a,b) # 5,4a = [1,2]
b = a
a.append(3)
print(a,b) # [1,2,3] [1,2,3]
```

注意，当追加到列表 *a* 时，我们的新列表 *b* 也受到了影响。所以回到我们最初的问题:我们如何克隆或复制一个列表？

# 定义新列表

一个简单的方法是使用`list()`函数来定义一个新的列表。

```
a = [1,2]
b = list(a)
a.append(3)
print(a,b) # [1,2,3] [1,2]
```

# 切片符号

Python 的切片符号是一种返回列表中指定部分的机制。通过省略 start(冒号前)和 end(冒号后)索引，将返回完整的列表，并随后将其赋给新变量。

```
a = [1,2]
b = a[:]
a.append(3)
print(a,b) # [1,2,3] [1,2]
```

# 。copy()方法

使用`.copy()`方法导入`copy`库将执行浅层复制，行为类似于前面提到的方法。

该方法也内置于列表数据类型中。

```
import copya = [1,2]
b = copy.copy(a)
a.append(3)
print(a,b) # [1,2,3] [1,2]
```

# 。deepcopy()方法

同样是从`copy`库中，`deepcopy()`方法可预测地执行深度复制，为每个列表项创建新元素。

这与浅层拷贝不同，因为每个元素都是重新创建的，而不是共享的。

为了说明这一点，让我们创建一个包含在列表中的对象，并对其进行更改。

```
import copyclass Test():
   def __init__(self,n):
      self.n = n
   def __repr__(self):
      return str(self.n)obj = Test(5)a = [1,2,obj]
b = copy.copy(a)
c = copy.deepcopy(a)obj.n = 3print(a,b,c)
# [1,2,3] [1,2,3] [1,2,5]
```