# 如何在 Python 中检查文件是否存在无异常

> 原文：<https://betterprogramming.pub/how-to-check-if-a-file-exists-without-exceptions-in-python-f6730886dbd0>

## 使用`os`和`isFile`功能进行探索

![](img/f666b6beba45c9f824b2d7afb10ff2f0.png)

照片由[布雷特·乔丹](https://unsplash.com/@brett_jordan?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/collections/8567307/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

当执行取决于成功执行的操作时，try/except 语句的变体用于捕获异常并相应地做出响应。这里的基本前提是在看之前跳跃，知道你已经为错误的行动准备好了计划。但是如果你想三思而后行呢？特别是在处理文件的时候？

我们所有的解决方案都将使用`os`库和`isfile()`方法，返回真或假。其余部分由要检查的文件相对于执行文件的位置决定。

# 勘探指南

问自己以下问题将确保彻底搜索。

## 文件在同一个目录吗？

要检查与执行文件在同一目录中的文件，只需将文件名作为字符串传递给方法。

```
os.path.isfile("file_in_same_dir.py")
```

## 文件在子目录中吗？

如果要检查的文件位于执行文件的子目录中，则将相对路径传递给方法。

```
os.path.isfile("images/logo.png")
```

## 该文件是否在不同的目录中？

如果文件位于完全不同的目录中，或者您不想被限制为相对路径，请将绝对路径传递给该方法。

```
os.path.isfile("/var/www/html/blog/images/logo.png")
```