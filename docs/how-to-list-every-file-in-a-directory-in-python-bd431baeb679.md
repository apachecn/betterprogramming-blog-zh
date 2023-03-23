# 如何用 Python 列出目录中的每个文件

> 原文：<https://betterprogramming.pub/how-to-list-every-file-in-a-directory-in-python-bd431baeb679>

## 使用“os”库和 filter()函数

![](img/e2cbfdd3795cb05563dcc6e3b3dcbccb.png)

照片由 [Goran Ivos](https://unsplash.com/@goran_ivos) 在 [Unsplash](https://unsplash.com/) 上拍摄

遍历本地文件存储是一项方便的技能。在本地系统中工作，`os`库将是你的朋友。在`os`中有多种策略可以用来检索目录中的所有文件。

在本教程中，我们将使用`listdir()`函数并操纵结果只返回文件。

# 导入起始库

首先，我们需要来自`os`库中的三个方法。

*   **listdir** :检索目录的内容——包括目录和文件。
*   **连接**:将两个组件组合成一条路径。
*   **isfile** :如果给定的路径成分是文件，则返回 true。

```
from os import listdir
from os.path import join, isfile
```

# 检索目录的内容

导入必要的方法后，就该设置目录的路径并检索其内容了。

```
from os import listdir
from os.path import join, isfiledirectory_path = "/some/path"
contents = listdir(directory_path)
```

# 过滤掉目录

现在我们已经拥有了目录的所有内容，是时候过滤掉目录了——只留下文件。

```
from os import listdir
from os.path import join, isfiledirectory_path = "/some/path"
contents = listdir(directory_path)files = filter(lambda f: isfile(join(directory_path,f)),contents)
```

`filter()`函数有两个参数:一个确定项目是否包含的函数，以及要过滤的序列。我们使用一个λ表达式来确定连接的路径和目录内容是否是一个文件。重要的是要记住`filter()`将返回一个过滤器对象，因此您应该将`files`转换成一个列表以便打印内容。

```
from os import listdir
from os.path import join, isfiledirectory_path = "/Users/jhsu/Desktop"contents = listdir(directory_path)files = filter(lambda f: isfile(join(directory_path,f)),contents)print(files) # <filter object at 0x10a5203a0>
print(list(files)) # [list of files]
```