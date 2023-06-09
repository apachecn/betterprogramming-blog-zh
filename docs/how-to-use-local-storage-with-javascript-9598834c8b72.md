# 如何在 JavaScript 中使用本地存储

> 原文：<https://betterprogramming.pub/how-to-use-local-storage-with-javascript-9598834c8b72>

![](img/5e1d771ff6228967ef5d1f71628c600b.png)

本地存储是本地存储在客户端浏览器中的数据。也是纯 JavaScript。

有两种类型的存储可供您使用:

*   **本地存储**:即使刷新页面或关闭浏览器，数据仍然存在。
*   **会话存储**:浏览器关闭时将被清除的数据。

# 为什么本地存储和会话存储有用？

您不必使用 cookies 来存储临时数据，临时数据需要由 web 服务器创建，并且不会随每个 HTTP 请求一起发送。

因此，如果你正在构建一个静态网站，你不需要任何后端语言或逻辑来存储数据。此外，您的本地存储提供 5MB 的数据存储，而您在 cookie 中最多只能存储 4KB。

一些技术性的东西:

*   您可以设置键:值对。
*   您只能存储字符串。
*   你可以存储数组和对象，但是你必须先把它们转换成字符串，使用一个叫做`JSON.stringify()`和`JSON.parse()`的方法。稍后将详细介绍。

以下是本地存储的方法:

![](img/cdd1ea97daf9b162a94494ecac09481b.png)

将数据设置到本地存储:

![](img/4398787fcdc2cadf6048db7abda4e556.png)

它们被保存为键:值对。

将数据设置到会话存储中:

![](img/b05de4c273b81ebc99fa68192c23e381.png)

这些也被保存为键:值对。

如果你在你的浏览器(`CMD+Option+J`)中进入你的控制台，进入应用程序选项卡，你可以看到你的数据。

![](img/882e4f3be8564b5ff41875bfac47635f.png)

我们可以`get`从存储:

![](img/aa27cf6c3a21402d17fa3c0cc1a142e0.png)

使用 console.log()作为显示数据的示例。

我们可以从存储中`remove`:

![](img/a561f83f87decbc56108bf2e95c3083c.png)

我们可以`clear`全部从存储:

![](img/81e43816392c875c3afd2cacb23e8291.png)

数据将变为空。

# 示例:保存任务

快速举例。如果你有一个表单，比如说，一个任务列表，你想保存用户提交的任务，你可以这样做:

查找您的表单输入:

![](img/3662092b18514355214fb5f7b653eecd.png)

将事件`‘submit’`添加到表单输入:

![](img/2d875a803bf6b2a80dc87f548011dfdd.png)

抓取表单的输入`value`:

![](img/e8c6c17681f78b98c4453b4660f37dbb.png)

我有一个 ID 为“task”的输入标签，我使用 value 方法获取用户的输入，并将其存储在一个变量中。

这是有点棘手的部分。如果我们想要存储来自用户的多个任务，我们需要创建一个任务数组，并将其存储为一个字符串。

1.  首先，我们需要检查本地存储中是否有内容，如果有，就将其取出并添加到。如果没有，设置一个空数组。

![](img/e7cc5b0d24c94127d3aaae035175aabc.png)

2.现在我们想用一个叫做`JSON.parse()`的方法来包装我们的`localStorage.getItem(‘tasks’);`。然后，我们想把我们的`task`变量传递给我们的`tasks`变量。

![](img/13dfc1e27562474d23cbea9747d8e6ac.png)

3.我们将任务转换回字符串，因为我们只能在本地和会话存储中存储字符串。

![](img/725b48bfd477544a688caa4f4308cac5.png)![](img/79329b8308d827070b3ff889c501d9c8.png)

4.现在，如果我们想遍历它并使用一个`forEach()`，我们必须解析它，否则我们会得到`TypeError` : `forEach`不是一个函数错误，因为 tasks 是一个数组——我们从只存储字符串的本地存储中取出它。

![](img/88546d11551c76c9e57efe68a1f603fc.png)

感谢阅读！