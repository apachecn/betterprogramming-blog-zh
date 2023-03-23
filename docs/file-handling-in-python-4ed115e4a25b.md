# Python 中的文件处理

> 原文：<https://betterprogramming.pub/file-handling-in-python-4ed115e4a25b>

## 使用 Python 对文件执行基本操作的指南

![](img/f366a70d31c97d19b1ae659e72cbeb0b.png)

由 [Kelly Sikkema](https://unsplash.com/@kellysikkema?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/folder-sharing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

即使你是初级、中级或高级开发人员，实际上也没关系，因为有一天你将不得不处理文件处理。

文件处理只不过是对文件执行的各种操作的组合，例如打开文件、读取文件和写入文件等。

通常有两种类型的文件—文本文件和二进制文件。

文本文件存储文本信息，例如，任何说“Hello World”并另存为的消息。txt 扩展名，而图像以二进制数的形式存储，如 0 或 1，并保存为。bin 扩展。

因此，在本指南结束时，您将了解最常用的文件操作，如下所示:

*   打开和读取文件。
*   创建和写入新文件。
*   在文件中追加新信息。
*   从目录中删除文件。

[Python](https://www.python.org/) 内置的`open()`函数以两个参数作为自变量，以下列方式打开文件:

*   `'r’`，以只读方式打开文件。
*   `'w’`，以只写模式打开文件。
*   `'a’`，以追加模式打开文件。
*   `'r+’`，该模式以读写模式打开文件。

# 打开和读取文件

你应该记住，如果你没有在`open()`函数中提供第二个定义文件打开模式的参数，那么编译器会以只读模式打开它。

在我工作的同一个目录中，有一个名为`“demofile.txt”`的文件，其中包含一些文本信息，我试图打开并阅读它，如下所示:

```
file = open('demofile','r')
for text in file:
    print(text)
```

上面给出的第一行代码告诉编译器`open()`函数正试图以只读模式打开`“demofile.txt”`文件，而 *for 循环则从文件中打印文本。*

如果您不提供`‘r’`模式，那么您仍然能够读取文件的内容，因为默认情况下`open()`功能以读取模式打开文件。

输出:

```
Hello world, in this guide we’ll learn how we can perform basic file operations.
```

如果您只想读取文件的一部分，如几行或几个字符，也可以:

```
file = open('demofile')
print(file.read(11))
```

输出:

```
Hello world
```

# 创建新文件

下面的代码以写模式创建一个文件名为`“medium.txt”`的新文件并将文本信息写入其中，最后调用`close()`函数来释放资源。

```
file = open('medium.txt','w')
file.write("Medium is a platform for readers and writers")
file.write("It gives writers full freedom to speak out loud with their thoughts and opinions.")
file.close()
```

输出:

```
Look into the directory you’re working on, there will be file created recently, open up this file and you’ll find the text information.
```

# 在追加模式下打开文件

当你在一个文本文件中已经有了一些文本信息，而你想在其中添加额外的信息，那么你可以使用`append()`功能。

```
file = open('medium.txt','a')
file.write("this is additional information which wasn't added at the time of file creation.")
file.close()
```

输出:

```
Now open the file and you will see this information has been added into the same file.
```

# 删除文件

要在 Python 中删除文件，您必须导入`os`模块，因为 file 对象没有删除功能。

```
import os
os.remove('medium.txt')
```

`remove()`功能从目录中删除了`“medium.txt”`文件，您也可以使用`os.rmdir()`删除文件夹。

# 结论

Python 中的文件处理非常简单，因为大多数基本操作只需要一行代码就可以完成，正如我们在本文中看到的。

我希望你喜欢读这篇文章，你也可以访问我的 [**网站**](http://thehimanshuverma.com/) ，在那里我会定期发布文章。

[**订阅**](https://mailchi.mp/b08da935e5d9/himanshuverma) 我的邮件列表，直接在您的收件箱中提前获得我的文章，或者关注我自己在 Medium 上发表的文章[**The Code Monster**](https://medium.com/the-code-monster)以完善您的技术知识。

# 了解你的作者

希曼舒·维尔马毕业于印度勒克瑙的 APJ 阿卜杜勒·卡拉姆大学博士。他是 Android & IOS 开发人员、机器学习和数据科学学习者、金融顾问和博客作者。