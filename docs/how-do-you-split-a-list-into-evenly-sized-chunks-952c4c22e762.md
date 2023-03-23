# 如何将一个列表分割成大小相等的块？

> 原文：<https://betterprogramming.pub/how-do-you-split-a-list-into-evenly-sized-chunks-952c4c22e762>

## 给定一个未知长度的列表，我们如何将列表分成大小相等的块呢？

![](img/297ba00d5d7be2be7f11cc63bfdaa1a7.png)

照片由 [Mae Mu](https://unsplash.com/@picoftasty?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/chunks?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 挑战

给定一个未知长度的列表，我们如何将列表分成大小相等的块呢？假设列表没有按照指定的块大小均匀划分，余数将放在最后一个块中。例如，一个包含 15 个条目的列表被分成 4 个块，分别是 4、4、4 和 3。

# 解决方案

定义一个函数，接受一个列表`l`和最大块大小`n`，返回一个列表，其中每个嵌套列表的长度为`n`项。我们使用`yield`关键字来产生一个生成器，它对于长列表来说更有性能；但是，结果是该函数必须嵌套在要打印的列表中。

```
def even_chunks(l, n):
   for i in range(0, len(l), n):
      yield l[i:i + n]data = [3,4,5,2,3,4,3,5,6,7,2,3,4,5,4,7]
print(list(even_chunks(data,3)))
# [[3, 4, 5], [2, 3, 4], [3, 5, 6], [7, 2, 3], [4, 5, 4], [7]]
```

# 荣誉奖

列表理解:如果你不想弄乱生成器和`yield`命令，简单的列表理解也可以解决这个问题。确保相应地匹配变量名和块大小。

```
data = [3,4,5,2,3,4,3,5,6,7,2,3,4,5,4,7]
chunked_data = [
   data[i:i + 3]
   for i in range(0, len(data), 3)
]print(chunked_data)
# [[3, 4, 5], [2, 3, 4], [3, 5, 6], [7, 2, 3], [4, 5, 4], [7]]
```