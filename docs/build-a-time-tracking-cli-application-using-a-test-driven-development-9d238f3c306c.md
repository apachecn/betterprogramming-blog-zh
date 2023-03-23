# 使用测试驱动开发构建时间跟踪 CLI 应用程序

> 原文：<https://betterprogramming.pub/build-a-time-tracking-cli-application-using-a-test-driven-development-9d238f3c306c>

## 使用 oclif 测试库的 TDD

![](img/5251c1b9ed98d3c62aff8c103b057284.png)

凯文·Ku 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

虽然编写 CLI 工具很有趣，但初始设置和样板文件——解析参数和标志、验证、子命令——对于每个 CLI 来说通常都是一样的，而且很麻烦。这就是 oclif 框架拯救世界的地方。编写单命令或多命令 CLI 的样板文件消失了，您可以快速进入您真正想要编写的代码。

但是等等——还有更多！`oclif`还有一个测试框架，让您可以像用户一样执行 CLI，捕捉标准输出和错误，以便您可以测试预期。在本文中，我将向您展示如何轻松编写和测试一个`oclif` CLI 应用程序。

# 我们要建造什么？

我们都厌倦了在典型的 TODO 应用程序上工作。相反，让我们构建一些不同但简单的东西。我们将使用测试驱动开发(TDD)方法来构建一个时间跟踪应用程序。我们的 CLI 将允许我们执行以下操作:

*   添加项目
*   这些项目的开始和结束计时器
*   查看项目的总支出
*   查看给定项目在每个条目上花费的时间

下面是一个与`time-tracker` CLI 交互的示例:

```
~ time-tracker add-project project-one
Created new project "project-one"~ time-tracker start-timer project-one
Started a new time entry on "project-one"~ time-tracker start-timer project-two
 >   Error: Project "project-two" does not exist~ time-tracker add-project project-two
Created new project "project-two"~ time-tracker start-timer project-two
Started a new time entry on "project-two"~ time-tracker end-timer project-two
Ended time entry for "project-two"~ time-tracker list-projects
project-one (0h 0m 13.20s)
- 2021-09-20T13:13:09.192Z - 2021-09-20T13:13:22.394Z (0h 0m 13.20s)
project-two (0h 0m 7.79s)
- 2021-09-20T13:13:22.394Z - 2021-09-20T13:13:30.189Z (0h 0m 7.79s)
```

我们将在一个“数据库”(一个简单的 JSON 数据文件)中管理所有关于添加项目和活动计时器的数据。

我们的时间跟踪应用程序项目的源代码可以在[这里](https://github.com/bloveless/oclif-time-tracker)找到。

因为我们是用 TDD 的方式来做的，所以让我们开始吧…先测试一下！

# 我们的时间跟踪器:特征和测试

当我们描述我们的应用程序的特性时，我们应该考虑我们可以编写的测试来断言我们对这些特性的期望。以下是我们应用程序的功能列表:

## 创建新项目

*   **快乐路径**:新项目被创建，其记录被存储在底层数据库中。用户收到一条确认消息。
*   **Sad path** :如果项目已经存在，那么用户会看到一条错误消息。底层数据库将保持不变。

## 启动项目计时器

*   **快乐路径**:请求的项目已经存在，所以我们可以开始一个新的时间条目，将 startTime 设置为当前日期/时间。当计时器开始计时时，用户将收到通知。
*   **快乐之路**:如果定时器已经在另一个项目上运行，那么该定时器将停止，新的定时器将在所请求的项目上开始。当计时器开始计时时，用户将收到通知。
*   **Sad path** :如果项目不存在，那么用户会看到一条错误信息。底层数据库将保持不变。

## 结束项目的计时器

*   **快乐之路**:请求的项目上有一个计时器处于活动状态，因此我们可以结束该计时器并通知用户。
*   **Sad path** :如果项目不存在，那么会给用户一个错误信息。底层数据库将保持不变。
*   **Sad 路径**:如果项目存在，但没有激活的定时器，那么用户将被通知。底层数据库将保持不变。

## 列出项目

*   **快乐路径**:向用户显示所有项目、总次数、条目、条目次数。

## 数据库存在(对于所有命令)

*   **Sad path** :如果当前目录下没有`time.json`文件，那么会给用户一个错误信息。

对于数据存储——我们的“数据库”——我们将时间条目作为 JSON 存储在磁盘上一个名为`time.json`的文件中。以下是该文件的外观示例:

# 设计决策

最后，让我们讨论一下整个应用程序的一些设计决策。

首先，我们将在 JSON 数据的顶层存储一个`activeProject`。我们可以用它来快速检查哪个项目是活动的。其次，我们将在每个项目的*中存储一个`activeEntry`字段，它存储当前正在处理的条目的索引。*

有了这两条信息，我们可以直接导航到活动项目及其活动条目，以便结束计时器。我们还可以立即确定该项目是否有任何活动条目，或者是否有任何活动项目。

# 项目设置

现在我们已经打下了所有的基础，让我们创建一个新的项目，并开始深入研究。下面是第一条命令:

```
npx oclif multi time-tracker
```

该命令创建一个新的[多命令 oclif 应用程序](https://oclif.io/docs/multi)。有了多命令 CLI，我们可以运行像`time-tracker add-project project-one`和`time-tracker start-timer project-one`这样的命令。在这些例子中，`add-project`和`start-timer`都是独立的命令，每个命令都存储在项目中自己的源文件中，但是它们都在`time-tracker` CLI 下。

# 关于存根的一句话

我们想利用`@oclif/test`提供的测试助手。为了测试我们的特定应用程序，我们需要编写一个简单的存根。原因如下:

我们的应用程序写入文件系统上的一个`timer.json`文件。想象一下，如果我们并行运行我们的测试，并且有十个测试同时写入同一个文件。那会变得混乱，并产生不可预测的结果。

一个更好的方法是让每个测试写入它自己的文件，对照那些文件进行测试，然后自己清理。更好的是，每个测试可以写入内存中的一个对象，而不是一个文件，我们可以断言我们对该对象的期望。

编写单元测试的最佳实践是用别的东西替换驱动程序。在我们的例子中，我们将使用一个`MemoryStorage`驱动程序来清除默认的`FilesystemStorage`驱动程序。

@oclif/test 是围绕 [@oclif/fancy-test](https://github.com/oclif/fancy-test) 的一个简单包装器，它增加了一些测试 CLI 命令的功能。我们将使用`@oclif/fancy-test`中的[存根功能](https://github.com/oclif/fancy-test#stub)来替换测试命令中的存储驱动程序。

# 我们的第一个命令:添加项目

现在，让我们谈谈“添加项目”命令和与模拟文件系统相关的重要部分。每个新的 oclif 项目都以`src/commands`中的`hello.js`文件开始。我们将它重命名为`add-project.js`文件，并用最少的文件填充它。

# 用于测试的可交换存储

注意我是如何静态地将一个`FilesystemStorage`实例分配给`AddProjectCommand.storage`的。这允许我在测试中用内存存储实现替换文件系统存储。下面我们来看看`FilesystemStorage`和`MemoryStorage`类。

`FilesystemStorage`和`MemoryStorage`有相同的接口，所以我们可以在测试中用一个替换另一个。

# 添加项目命令的第一个测试

在`test/commands`中，我们将`hello.test.js`重命名为`add-project.test.js`，并且我们已经编写了我们的第一个测试:

神奇的事情发生在`stub`呼叫中。我们用`MemoryStorage`替换掉`FilesystemStorage`(用一个空对象作为初始数据)。然后，我们断言对存储内容的期望。

# 从 [@oclif/Test](http://twitter.com/oclif/Test) 解包测试命令

在实现我们的命令之前，让我们确保理解了我们的测试文件。我们的`describe`块调用`test`，它是`@oclif/fancy-test`(从`@oclif/test`再导出)的入口点。

接下来，`.stdout()`方法捕获命令的输出，让您通过使用`ctx.stdout`断言对它的期望。还有一个`.stderr()`方法，但是我们稍后会看到在`@oclif/fancy-test.`中有另一个更好的处理错误的方法

对于大多数应用程序，您通常不会断言要写入标准输出的内容。然而，在 CLI 的情况下，这是您与用户的主要接口之一，因此根据标准输出进行测试是有意义的。

请记住，这里有一个主要问题！如果您在开发过程中使用`console.log`进行调试，那么`.stdout()` **也将捕获该输出。**除非你断言反对`ctx.stdout`，否则你可能永远看不到那个输出。

```
.stub(AddProjectCommand, 'storage', new MemoryStorage({}))
```

我们已经讨论了一点`.stub`方法，但是我们在这里做的是用`MemoryStorage`代替默认的`FilesystemStorage`来替换我们命令上的静态属性。

```
.command(['add-project', 'project-one'])
```

方法`.command`是事情变得真正酷的地方。该行调用您的 CLI，就像您从命令行调用一样。你可以传入标志和它们的值或者一个参数列表，就像我在这里做的一样。`@oclif/test`将调用您的命令，就像最终用户在命令行调用它一样。

```
.it('test description', () => [...])
```

你可能对`it`块很熟悉。这是您通常要做的所有工作，来设置您的测试并针对结果运行断言。这里的情况非常相似，但是您可能已经完成了与来自`@oclif/test`和`@oclif/fancy-test`的其他助手一起设置测试的艰苦工作，并且`it`块只需要断言命令的输出。

最后，现在我们对测试做了更多的了解，我们可以用`npm test`来运行我们的测试。因为我们还没有编写任何实现代码，所以我们预计我们的测试会失败。

```
1) add project
       should add a new project:
     Error: Unexpected argument: project-one
See more help with --help
      at validateArgs (node_modules/@oclif/parser/lib/validate.js:10:19)
      at Object.validate (node_modules/@oclif/parser/lib/validate.js:55:5)
      at Object.parse (node_modules/@oclif/parser/lib/index.js:28:7)
      at AddProjectCommand.parse (node_modules/@oclif/command/lib/command.js:86:41)
      at AddProjectCommand.run (src/commands/add-project.js:1:1576)
      at AddProjectCommand._run (node_modules/@oclif/command/lib/command.js:43:31)
```

完美！正如我们所料，测试失败了。让我们写代码来达到绿色。

# 走向绿色:执行我们的命令

现在，我们只需要根据错误来编写命令。首先，我们需要更新`AddProjectCommand`类来知道我们想要传入的参数。在这种情况下，我们只传入一个项目名称。让我们用下面的代码进行更改:

```
class AddProjectCommand extends Command {
  ...
}AddProjectCommand.storage = new FilesystemStorage()AddProjectCommand.description = 'Add a new project to the time tracking database'// This is the update
AddProjectCommand.args = [
  {name: 'projectName', required: true},
]
```

我们需要告诉 oclif 我们命令的预期参数及其属性。在我们的例子中，只有一个参数`projectName`，并且是必需的。你可以在这里了解更多 oclif 参数[，在这里](https://oclif.io/docs/args)了解 oclif 标志[。](https://oclif.io/docs/flags)

现在，我们再次运行测试，如下所示:

```
1) add project
       should add a new project:AssertionError: expected {} to deeply equal { Object (activeProject, projects) }
      + expected - actual-{}
      +{
      +  "activeProject": [null]
      +  "projects": {
      +    "project-one": {
      +      "activeEntry": [null]
      +      "entries": []
      +    }
      +  }
      +}at Context.<anonymous> (test/commands/add-project.test.js:11:55)
      at async Object.run (node_modules/fancy-test/lib/base.js:44:29)
      at async Context.run (node_modules/fancy-test/lib/base.js:68:25)
```

精彩！我们现在看到，虽然我们期望创建“project-one ”,但是底层数据结构没有发生变化。

让我们用通过测试所需的最少代码来更新这个命令。为了简洁起见，我们将只在`src/commands/add-project.js`中显示`run()`方法。

默认情况下，如果没有文件存在，那么当从存储器加载时，我们将收到一个空对象。这段代码创建任何默认属性及其值(如果它们不存在的话)(例如，`activeProject`和`projects`)，然后创建一个具有默认结构的新项目——一个空的`entries`数组和设置为`null`的`activeEntry`。

再次运行测试，我们看到下一个错误:

```
1) add project
       should add a new project:
     AssertionError: expected '' to include 'Created new project "project-one"'
      at Context.<anonymous> (test/commands/add-project.test.js:20:27)
      at async Object.run (node_modules/fancy-test/lib/base.js:44:29)
      at async Context.run (node_modules/fancy-test/lib/base.js:68:25)
```

这就是`.stdout()`功能发挥作用的地方。我们期望我们的 CLI 告诉用户我们创建了他们的新项目，但是它什么也没说。这个很容易修理。我们可以在调用`storage.save()`之前添加下面一行。

```
this.log(`Created new project "${args.projectName}"`)
```

瞧啊。我们的第一个快乐路径测试通过了。现在我们在巡航！

```
add project
    ✓ should add a new project (43ms)1 passing (44ms)
```

# 还有一个测试

我们还有一个关于`AddProjectCommand`的测试。我们需要确保用户不能添加与当前项目同名的另一个项目。对于这些测试，我们将反复需要为单个项目生成一个数据库。让我们为此创建一个助手。

在`test/test-helpers.js`中添加以下内容:

现在，我们可以在`add-project.test.js`中添加下一个测试:

这个测试中有一个新方法:

```
.catch('Project "project-one" already exists')
```

我之前提到过，我们不需要嘲弄`stderr`来断言反对它。这是因为我们可以使用这个`catch`方法来断言运行期间发生的任何错误。在这种情况下，我们预计会发生错误，并且底层存储不会发生变化。

再次运行我们的测试后，我们看到以下内容:

```
1) add project
       should return an error if the project already exists:
     Error: expected error to be thrown
      at Object.run (node_modules/fancy-test/lib/catch.js:8:19)
      at Context.run (node_modules/fancy-test/lib/base.js:68:36)
```

在我们从存储器中加载`db`之后，我们需要检查并查看项目是否已经存在，如果存在就抛出一个错误。

```
const db = await AddProjectCommand.storage.load()// New code
if (db.projects?.[args.projectName]) {
    this.error(`Project "${args.projectName}" already exists`)
}
```

现在，当我们运行我们的测试，他们都通过了！我们做到了！我们现在可以添加尽可能多的项目来跟踪我们的时间。

```
add project
    ✓ should add a new project (46ms)
    ✓ should return an error if the project already exists (76ms)
```

# 结论(目前)

在本文中——我们关于`oclif`测试库的两部分系列的第一部分——我们讨论了`oclif`,它的测试框架，为什么存根有用，以及如何使用它们。然后，我们开始为我们的`time-tracker` CLI 编写测试和实现。

这是一个很好的开始。在本系列的下一部分中，我们将继续用更多的命令构建我们的 CLI，同时涵盖重要的测试概念，如数据存储测试和初始化。