# 如何在 Python 3 中创建新文件

> 原文：<https://betterprogramming.pub/how-to-create-new-files-in-python-3-a905ff7a2808>

## 二进制文件、读写文件等等

![](img/a8463a0ecff4d97cbc7660c24c4fabf3.png)

照片由[马腾·范·登·霍维尔](https://unsplash.com/@mvdheuvel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

当你想在 Python 中创建新文件时，有一点令人困惑，那就是如何独占地创建一个新文件。通常，您会看到一些代码示例，向您展示如何在写入文件的过程中创建新文件。这是可行的，但是您需要检查以确保您没有覆盖现有的文件。幸运的是，有一种方法可以在 Python 中创建一个空文件，自动检查该文件是否存在。

# 以独占方式创建新文件

当您使用读或写创建一个新文件时，Python 不会检查该文件是否存在。相反，如果存在以您指定的名称命名的文件，Python 会打开该文件并覆盖它。如果文件不存在，Python 会为您创建一个文件并写入其中。通过这样做，您可以省去检查文件的麻烦:

```
newfile = open(“unique_new_file.txt”, “x”)
```

`x`表示仅当文件不存在时才打开新文件。如果该文件不存在，将创建一个新的空文本文件。如果文件确实存在，将返回一个错误:

```
newfile = open(“unique_new_file.txt”, “x”)
FileExistsError: [Errno 17] File exists: ‘unique_new_file.txt’
```

如果有多个进程试图创建一个具有相同标题的文件，或者如果您不想意外覆盖您自己的工作，那么以这种方式创建新文件是非常有用的。

# 如果我想创建一个新的二进制文件呢？

就像 Python 中的其他文件命令一样，您可以用`x`来组合东西。(不能和`r`或者`w`组合。)可以用`b`。如果您想要一个新的空二进制文件，其命令如下所示:

```
newfile = open(“unique_binary_file.fileending”, “xb”) 
newfile.close()
```

使用你想要的文件结尾，而不是`fileending`，因为这不是最好的文件类型。；)

**注意:**请记住，如果您以这种方式打开文件，它将一直保持打开状态，直到您将其关闭。完成后一定要添加一个`close`语句。让一堆文件处于打开状态最终会影响性能，尽管如果您只是在尝试一些小例子，您可能不会注意到这一点。如果你不想记住关闭你的文件，你可以使用一个`with`语句来代替。一个`with`语句会记得为你关闭文件。因为我们没有对例子中的文件做任何事情，所以我没有那样写例子。我们将在下一节看到一个使用`with`的例子。

# 如果我想读写我的新文件怎么办？

您可以读写使用`x`创建的文件，但是不能通过指定`r`或`w`来实现。相反，你可以使用一个`+`。添加这个将允许您在新创建的文件中读写。该命令如下所示:

```
newfile = open(“new_text_file.txt”, “x+”) 
```

这里有几件事你需要注意。您还不能读取该文件，因为它是空的。如果你试着把它输出到控制台，你不会得到一个错误，只有一个空格。您可以写入文件，这样做:

```
with open(“text_file.txt”, “x+”) as myfile:
    myfile.write("Hello World!")
```

我更喜欢这种方法，因为在你完成了`with`语句后，文件会自动关闭。

现在你已经在你的文件里放了一些东西，你可能会忍不住说，“嘿，给我读一遍。”如果你试图这样做，你会得到一个空白。这是因为每个文件都有一个读/写位置。在你写下“你好，世界！”您将文件中的位置向前移动到紧接其后的位置。你的文件是新的，所以没什么可看的。

您可以关闭该文件，然后再次打开并阅读它。或者你可以这样做。我们将在前面的例子中添加一些内容(假设您还没有尝试创建该文件):

```
with open(“text_file.txt”, “x+”) as myfile:
    myfile.write("Hello World!")
    myfile.seek(0) 
    print(myfile.read()) 
```

如果你运行这个，你将得到你的文件的内容。使用`seek`，您将文件中的位置设置回起始位置，因此现在您可以阅读它了。

希望对你将来的文件创建有所帮助！感谢阅读。