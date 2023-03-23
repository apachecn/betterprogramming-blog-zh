# 如何用 Express 和 Node.js 写出更智能的路由

> 原文：<https://betterprogramming.pub/how-to-write-smarter-routing-with-express-and-node-js-4cc53bbc55e5>

## 让节点文件系统来完成繁重的工作

![](img/24fb206dff7c76173c1fc8d68aa9545b.png)

[优 X 创投](https://unsplash.com/@youxventures?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/@youxventures?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

[Express](https://expressjs.com/) 是[节点](https://nodejs.org/en/)的 web 应用框架。它提供了各种特性，使 web 应用程序的开发变得快速而简单，如果只使用 Node，这项任务将花费更多的时间。

使用 Express 有许多方法可以创建和管理路由，最简单直接的方法是使用`delete`、`get`、`post`和`put`方法。它们中的每一个都被映射到对应的请求 [HTTP 动词](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)。

在上面的例子中，我们有三个路由(`/`、`/home`、`/about`)，每一个都有两个不同的 HTTP 动词(`get`、`post`)和它自己的逻辑，向客户端发送一个唯一的文本

从这个小例子中可以看出，当路由数量增加时，这种方法会变得非常混乱。

通过使用`[route](https://expressjs.com/en/4x/api.html#app.route)`方法和链接到相等路径的方法，有一种方法可以使代码更好:

这样我们可以使代码更短一点，但是仍然很混乱，因为我们在同一个文件中有多个`route`路径。

我们的下一步是为每个路径创建一个单独的文件，并使用 Express `[Router](https://expressjs.com/en/4x/api.html#router)` [](https://expressjs.com/en/4x/api.html#router)对象。`Router`对象是中间件和路由的独立实例。您可以将它视为一个迷你应用程序，只能够执行中间件和路由功能。

这样，我们在主文件`index.js`中有了更干净的代码，每条路由都在自己的文件中定义。除了`Route`对象之外，我们还利用了快速方法`[use](https://expressjs.com/en/4x/api.html#app.use)`将路径映射到它的配置文件。

这比我们在前面的例子中看到的要好得多，但我仍然可以看到一些问题，比如每个路由文件中的代码重复，主要是，每次我们向应用程序添加新的路由文件时，我们都需要更改主文件来映射文件的路径。当我们有大量的路由时，我们有同样的问题:主文件代码变得更大更乱。

为了解决第二个问题，我们可以创建一个单独的文件来将每个路径映射到它的 route 文件，并在主文件中执行一个简单的`require`。

routes 文件夹索引文件从主文件接收应用程序实例，并进行路径映射。主文件利用[立即调用的函数表达式](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)来请求并执行通过所创建的应用程序的路线索引文件。现在我们有了一个更干净的主文件，但是我们仍然有一个问题，那就是需要手动将每个路径映射到它的文件。

如果我们能让 Node 循环遍历`routes`文件夹的文件并进行映射，它可以帮助我们做得更好。为了实现这一点，我们将利用节点的`[fs.readdirSync](https://nodejs.org/api/fs.html#fs_fs_readdirsync_path_options)`。此方法用于同步读取给定目录的内容。方法返回一个包含目录中所有文件名或对象的数组。

而你可能会问，*路径呢？*如果我们没有一种自动的方法来发现每个路由文件的路径，我们仍然必须为每个添加的路由编辑列表。我认为这个问题有两种解决方案:使用约定优于配置，或者在路由文件中添加一个带有路径的导出。

为了使用约定胜于配置，我们将使用文件名作为路由路径。为了更加安全并删除潜在的有问题的字符，我们将使用 [Lodash](https://lodash.com/docs/4.17.15#snakeCase) 将文件名转换为 [snakeCase](https://en.wikipedia.org/wiki/Snake_case) ，其中字符串由下划线分隔。

我们要做的另一个改变是只在 routes 配置文件中使用`Router`对象，这使得 route 代码更简单并避免代码重复。

除此之外，我们将使用`[path.join](https://nodejs.org/api/path.html#path_path_join_paths)`节点方法使我们的代码跨平台，因为基于 Windows 和 Linux 的系统使用不同的路径名:

如前所述，另一个选项是在路由文件中导出所需的路径名，并在路由配置文件中使用该信息。

为了实现这一点，我们必须在 route 文件中导出一个带有 path 和 config 键的对象。在这种配置中，我强烈建议您使用 filename path 方法作为后备，以避免在忘记添加路径导出时出现错误。这样，我们有一个额外的好处:一个在两种情况下都能工作的解决方案。

在我们的最后一个例子中，我们将把 home route 路径改为`my_home`,并保持 about route 文件不变。

仅此而已。这是我们的智能快速路由器配置。通过使用这种配置——以及像 [PM2](https://pm2.keymetrics.io/) 或 N [odemon](https://nodemon.io/) 这样的解决方案来监视你的应用程序文件夹并在文件改变时自动重启——你所要做的就是在`routes`文件夹中添加一个文件来添加一个新的路由。

当然，还有很多改进的空间，比如错误检查，但是这种特性我将留给您，这样您可以习惯所提供的代码并练习您的编码技能，同时尝试实现您认为在我的示例中没有实现的任何特性。为了让您的旅程更加轻松，我在 [CodeSandbox](https://codesandbox.io/s/articlemediumexpressrouting-q2fb8) 中提供了最终代码。请随意叉和工作。

我希望你从这篇文章中学到了一些有用的东西。下次见！