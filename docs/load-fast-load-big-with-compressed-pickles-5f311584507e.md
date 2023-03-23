# 使用压缩 Pickles 在 Python 中更快地加载数据

> 原文：<https://betterprogramming.pub/load-fast-load-big-with-compressed-pickles-5f311584507e>

## 以更小的文件大小更快地存储任何 Python 对象

![](img/4a7b8f17fd930fdf4b286db241e2b2a8.png)

迈克尔·贾斯蒙德在 [Unsplash](https://unsplash.com/s/photos/file?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

你讨厌加载数据需要多长时间吗？您的硬盘可用空间是否不足？这里有四个易于实现的函数，可以帮助任何 Python 程序员(从初学者到高级程序员)管理他们的项目。

# 压缩泡菜

如果你已经在 Python 中工作了一段时间，你可能对`[_pickle](https://docs.python.org/3/library/pickle.html)`库很熟悉。

它将几乎所有 Python 对象(包括海量数据集)保存为字节。它把装载时间缩短了一点点。根据对象的不同，这也可以为您节省一些空间。然而，这往往是不够的。

进入 python 的`[bz2](https://docs.python.org/3/library/bz2.html)`库，它为任何文件启用 bz2 压缩。通过牺牲一些通过酸洗数据获得的速度，您可以将其压缩到原始大小的四分之一。

# 四大功能

下面是四个 Python 方法，它们可以快速处理数据，我在我参与的任何项目的`utils.py`文件中都包含了这些函数。

## *进口*

```
import bz2
import pickle
import _pickle as cPickle
```

## 1.全泡菜

*`full_pickle`*方法几乎可以获取任何对象(`list`、`dictionary`、`pandas.DataFrame`等等)，并将其保存为`.pickle`文件。**

```
**# Saves the "data" with the "title" and adds the .pickle
def **full_pickle**(title, data):
 pikd = open(title + ‘.pickle’, ‘wb’)
 pickle.dump(data, pikd)
 pikd.close()**
```

**示例用法:**

```
**full_pickle('filename', data)** 
```

*   **`filename`是不带扩展名的文件*的名称。***
*   **`data`是任何物体。**

## **2.放松**

**使用`loosen` 方法加载您或其他人保存的 pickle 文件。在`file`参数中包含`.pickle`扩展。**

```
**# loads and returns a pickled objects
def **loosen**(file):
 pikd = open(file, ‘rb’)
 data = pickle.load(pikd)
 pikd.close()
 return data**
```

**示例用法:**

```
**data = loosen('example_pickle.pickle')** 
```

*   **`file`是以为扩展名`.pickle`的文件名*。***

## **3.压缩泡菜**

**`compressed_pickle`的工作原理和`full_pickle`一样。它甚至采用了相同的论点。它创建一个`pickle`对象，然后使用`bz2`库压缩它，自动将`.pbz2`扩展名添加到保存的文件中。**

```
**# Pickle a file and then compress it into a file with extension 
def **compressed_pickle**(title, data):
 with bz2.BZ2File(title + ‘.pbz2’, ‘w’) as f: 
 cPickle.dump(data, f)**
```

**示例用法:**

```
**compressed_pickle('filename', data)** 
```

*   **`filename`是没有扩展名的文件*的名称。***
*   **`data`是任何物体。**

**请注意，这是压缩一个 pickle 文件，反过来效果就不好了。**

## **4.减压泡菜**

**`decompress_pickle`方法就像`loosen`函数一样工作。在`file`参数中包含`.pbz2`扩展。**

```
**# Load any compressed pickle file
def **decompress_pickle**(file):
 data = bz2.BZ2File(file, ‘rb’)
 data = cPickle.load(data)
 return data**
```

**示例用法:**

```
**data = decompress_pickle('example_cp.pbz2')** 
```

*   **`file`是扩展名为`.pbz2`的文件名。**

# **基准**

**那么，酸洗速度提高了多少，我们节省了多少空间？**

**下面是我在 AWS 虚拟机上进行的基准测试，价格不到 1 美分(0.01 美元)[，使用的是我为云计算创建的模块](https://medium.com/swlh/mastering-the-aws-spot-instance-2ecf59e242fa)。**

```
**Save CSV File: 3.384 seconds
Load CSV File: 1.977 seconds
CSV File Size: 39,575,154 bytesSave Pickle File: 3.422 seconds
Load Pickle File: 0.156 seconds
Pickle File Size: 40,759,166 bytesSave Compressed Pickle: 4.837
Load Compressed Pickle: 1.139
Compressed Pickle File Size: 1,467,842**
```

**将 39 MB 的`pandas.DataFrame()`对象保存为`.csv`文件花费了 3.4 秒。几乎与保存`.pickle`文件的时间一样长，比压缩文件的时间快一秒多。**

**`.pickle`文件和`.csv`文件占用的空间差不多，大约 40 MB，但是压缩的 pickle 文件只占用 1.5 MB。这节省了很多空间。**

**另一个很大的区别是加载时间。如果你在寻找更快的加载速度，这两个功能都可以，只是取决于你的空间需求。**

**加载`.csv`文件需要 2 秒，加载压缩的 pickle `.pbz2`文件只需要 1.2 秒，而加载 pickle 文件只需要 0.15 秒。**

# **要尝试或注意的事情**

*   **酸洗然后压缩的顺序经过测试，可以正常工作，不会降低数据质量。改变顺序会导致性能下降。**
*   **您可能想尝试比 bz2 压缩更适合您需求的其他压缩方法。**
*   **酸洗或压缩某些类对象可能不起作用，在这种情况下，尝试保存类属性(通常可以作为字典访问)，然后加载另一个类实例并为其分配属性。**

**就是这样。将来，我会写更多的文章，介绍我在项目中经常使用的简单却非常有用的函数和类。有些是基于我们在这里看到的，有些不是。**

**感谢阅读。**