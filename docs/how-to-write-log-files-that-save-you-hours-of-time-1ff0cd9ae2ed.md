# 如何写日志文件来节省你的时间

> 原文：<https://betterprogramming.pub/how-to-write-log-files-that-save-you-hours-of-time-1ff0cd9ae2ed>

## 使用更周到和描述性的错误进行更简单的调试

![](img/c65c5fba72e686e1cab48891933b73f6.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Pietro Jeng](https://unsplash.com/@pietrozj?utm_source=medium&utm_medium=referral) 拍摄的照片。

我需要在这里大声疾呼:我在工作中维护的程序有许多可爱的怪癖。*谓发生了错误*不在其列。这不仅仅是错误信息开始的方式。这也是它的结局。

或者“关于错误的信息存储在服务器日志中”，但是没有指示哪个服务器实际抛出了该异常？

这篇文章是关于如何写不糟糕的日志和错误信息。

# 你真的需要这个吗？

这篇文章的大部分是关于给*更多的*信息。但是就一会儿，让我们来看一些可以安全剪切的日志消息。为什么？好吧，当你不得不通读一英里长的日志文件时，你会感谢没有消耗你的理智和注意力的一切。

*   “一切都好”记录循环中每一次迭代的条目。只要我知道程序开始得很好，现在可能要进入那个循环，我真正关心的是错误。
*   当我不进行性能测试时，随机记录性能日志。
*   记录函数/计算的每一步。当您已经在记录“a+b=c”时，您实际上不需要记录“a”和“b”

正如我所说的，在大多数情况下，拥有比你需要的更多的信息要比太少的好，但仍然值得记住，以避免从另一端掉下来。

# 请告诉我错误是什么

我们在日常生活中处理的大多数异常都没有被显式处理，这意味着我们经常会得到一个堆栈回溯，它告诉我们到底发生了什么，在哪里，什么时候。那很好。这不是特别可读，但至少你可以追踪到源头。

但是，如果您要捕获一个异常，然后使用 catch 块将“Something not work yo”打印到控制台，甚至不将其打印到日志文件中，请不要这样做。如果你写了一个定制的异常处理程序，那么就花额外的时间来解释错误是什么。是不是一个错误的数据库条目，比如一个格式不正确的名字？很好，现在我不必克隆回购和调试，只需立即修复数据库中的条目即可。

# 请告诉我错误源自哪里

这是我在修复 bug 时最大的痛点:我们每天都在监控错误消息，但是即使写得再好的错误消息也不会让我走得太远，因为我甚至不知道它来自哪里。只要写下这样的话:

```
fatal error: invalid cast at serverIWouldLikeToSlaughter
```

我也开心多了。

# 给我一些额外的参数

坚持前面的例子，当你写错误消息的时候，你可能手头上有所有的信息，那么你为什么不把它给我呢？只需打印出客户 ID、账单号、电子邮件地址，这些都是不能反序列化的。

```
Fatal error: Invalid cast type Boolean to Integer numberOfSwearwords at serverIWouldLikeToSlaughter 
```

# 仅重载特定的异常

如果您想为错误的数据类型或类似的东西抛出异常，您应该挂钩到`invalidCastException`或类似的东西——而不是“异常”这样，最常见的错误将会有一个很棒的错误消息，带有一个有声的名称和描述。但是当任何不寻常的事情发生时，您仍然记录完整的堆栈跟踪，它不会丢失。

```
Try{thisOrthat();}
Catch castEx as InvalidCastException{
  log.error("detailed description of erro");
}
Catch ex as Exception{
  log.error(ex.stacktrace);
}
```

# 让我们看一些日志文件的例子，好吗？

以下是我认为有用的日志条目的一些随机选择的示例——可以更容易地理解错误源自哪里以及如何修复错误:

```
*-booting up server1 application4 
-Connecting to otherServer at Port 1337…ok
-Checking database Connection to databaseServer5 Error 400:Of course it doesn’t work, your credentials are outdated but not even the database admins have a clue what the new one are.
-program shut down at 00:34 pm, 1234 files processed successfully, 23 unsuccessful, 45 skipped
-Can't access file asdfadsfa.txt because the file is open in another process
-error updating database table tb_xyz, maximum column length of 50 exceeded at column "name"
-processing HTML text box failed, this is often caused by formatting issues on the database side. Check with database admins, the following value was retrieved from DB: <a\">helloworld\"</a>*
```

与根本不记录或者以无效的方式记录相比，这些错误消息中的每一条都可以节省我半个小时查找特定 bug 的时间。

# 外卖食品

你的一分钟可以为你节省几个小时。

我希望你觉得这篇文章很有趣。这是一个看起来很小很迂腐的问题，直到你已经修复了几年的 bug，并且看到了日志记录可能出错的所有方式。