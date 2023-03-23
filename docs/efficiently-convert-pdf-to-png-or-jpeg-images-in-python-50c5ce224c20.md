# 在 Python 中高效地将 PDF 转换为 PNG 或 JPEG 图像

> 原文：<https://betterprogramming.pub/efficiently-convert-pdf-to-png-or-jpeg-images-in-python-50c5ce224c20>

## 仅使用内存/RAM，您可以转换 pdf。你不需要存储任何文件！

![](img/c97b1ba830de480d2a799d21d41c3f43.png)

照片由[替代代码](https://unsplash.com/@altumcode?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 摘要

这篇文章将教你一种将 PDF 文件转换成 PNG 图像文件的技术。本文旨在解决的问题是，在转换过程中，一个人不想在他的系统上存储任何数据。

进行转换的系统不存储任何东西。这对于开发者来说非常重要，因为例如在驻留在服务器中的 API 中，需要有效地使用服务器硬件/存储资源。它不应该用于转储无用的数据。由于代码不在本机系统上存储任何东西，因此在转换完成时不使用任何存储。

在花了很多时间在互联网上，然后测试我的代码，我想出了一个解决方案。

# 代码及其解释。

1.  将文件存储在 python 中的一个变量中。我的文件来自网络，因此我使用请求来访问上传的文件。

```
file = request.files["file"]
```

2.从文件中提取文件名。warkzeug 文档明确告诉调用 `file.filename`上的安全函数来获得文件名的安全版本。

```
file.filename = secure_filename(file.filename)
```

3.导入`pdf2image`包，通过调用`pdf2image`包提供的函数将 pdf 字节转换为 RGB 值字节。这是一个字节到字节的转换。到目前为止，我们没有得到实际的。png 格式图像。

```
from pdf2image import convert_from_bytespages = convert_from_bytes(file.read())
```

4.创建一个内存缓冲区对象，并将文件另存为。内存缓冲区中的 png。

我们需要定义一个可以存储字节的内存缓冲区。这就像一个存储数据的 python 变量，但这里的数据类型是“字节”。你不能仅仅通过定义一个变量来存储它。

将字节存储在内存中的另一种方法是将字节写入一个文件，但是该文件将保存在您的计算机上。想象一下，如果这个 API 被调用 100 次，并且每次给出 5 MB 的文件，那么您最终会浪费 500Mb 的磁盘存储空间。

```
for page in pages: in_mem_file = io.BytesIO() page.save(in_mem_file , format = "png") in_mem_file.seek(0)break
```

`page.save()`将页面中的字节转换成 png 格式的字节，并保存在 in-mem 变量中。`.seek(0)`重新定位字节指针，再次指向数组的开头。

当调用`page.save`时，填充`in_mem_file`的指针已经到达其长度的末端，因此我们将读取它；我们需要重新定位开始。如果我们不重新定位，将不会读取任何字节。由于单个指针既做写操作又做读操作，我们需要手动管理它。

现在，您可以在网络上将 png 图像字节再次发送到其他地方，或者使用多几行代码将其存储在您的系统中。

使用 for 循环是因为 pages 对象是可迭代的。这意味着如果您的 pdf 有多个页面，它们都存储在 pages 中。Pages 就像一个列表，其中每个索引包含一个页面。

当一页完成时，我使用 break 来停止转换。您可以删除 break 语句，但是要记住处理`in_mem_file`并防止 for 循环覆盖它。

希望这篇教程对你有用。

```
from pdf2image import convert_from_bytesfile = request.files["file"]
file.filename = secure_filename(file.filename)
pages = convert_from_bytes(file.read())for page in pages: in_mem_file = io.BytesIO() page.save(in_mem_file , format = "png") in_mem_file.seek(0) break
```