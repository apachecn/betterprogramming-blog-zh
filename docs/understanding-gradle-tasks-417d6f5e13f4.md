# 了解梯度任务

> 原文：<https://betterprogramming.pub/understanding-gradle-tasks-417d6f5e13f4>

## 学习如何创建、使用和查看任务的结果，等等

![](img/5e892197899f201270c5ac83d1c43d55.png)

作者图片

你可能很少处理 Gradle 任务，大多数时候，新的`Task`要么作为插件的一部分添加，要么作为从库的“如何开始”指南中复制粘贴的一段代码添加。不了解任务是如何工作的，它们的结构，以及它们的生命周期，使得开发人员更有可能远离对那些任务添加任何改变，即使有改进的空间。

在本文中，让我们试着了解一下什么是梯度任务，它们是如何创建和使用的，以及它们可以采取什么形式。

关于`Task`你应该知道的基本事情:

*   `Task`是一段包含一系列动作的可执行代码。
*   动作通过`doFirst{}`和`doLast{}`闭包添加到`Task`中。
*   通过执行`./gradlew tasks`可以访问可用任务列表。

为了更好地理解 Gradle 项目实体之间的结构、语法和关系，请看我之前的文章。

在本文中，假设您正在测试 Android 项目或任何其他包含 Gradle wrapper 的项目中的代码，执行任务 X 意味着在 Mac 上运行`./gradlew X`或在 Windows 上运行`gradlew.bat X`。

# 任务是什么样子的？

您可能已经知道，在 Gradle 中，事情有许多不同的形式，任务也不例外。任务定义可以通过许多不同的方式来完成，最常见的方式如下:

代码片段#1

从上面运行 `taskName1`时，输出并不明显:

```
> Configure project :app
Why is this printed first?
```

```
> Task :app:taskName
First?
Last?
```

但是等等，事情变得更混乱了。让我们为同一任务添加一个更明确的替代形式，只是名称不同:

代码片段#2

上面的代码更具描述性，不言自明。正如你所看到的，我们正在项目的[任务容器](https://docs.gradle.org/current/javadoc/org/gradle/api/tasks/TaskContainer.html)对象`tasks`上调用`create()`，之后我们配置了新创建的`Task`的组属性并将动作添加到列表中。

让我们再次执行我们的`taskName1`,看看输出:

```
> Configure project :app
Why is this printed first?
Why is this printed first?
```

```
> Task :app:taskName
First?
Last?
```

如你所见，“为什么先打印这个？”被打印两次。那么它为什么会发生，又是从哪里来的呢？

## 构建生命周期和阶段

与上面声明的任务不同，大多数任务相互依赖。要执行一项任务，Gradle 必须了解项目中可用的任务以及该任务有哪些依赖关系。为此，Gradle 创建了一个有向无环依赖图，并根据它执行任务。

不要对有向无环依赖图这个术语感到震惊。它的意思是:

*   任务和它们的依赖关系组成一个图结构，其中节点代表任务，顶点/线代表依赖关系。
*   顶点的方向表示一个任务如何依赖于其他任务。
*   非循环意味着不存在这样的任务 A 和 B，其中两者直接或过渡地相互依赖。

## 有三个构建阶段:

*   初始化——从根据`settings.gradle`文件创建一个`Settings`对象开始，构建 Gradle 项目中包含的子项目(在 Android Studio 中称为模块)的层次结构。
*   配置——配置在初始化阶段发现的每个项目，然后转到各自的`build.gradle`文件并配置`Project`实例，构建目标任务直接或间接依赖的任务图。
*   执行—执行一个任务，并且所有任务都依赖于所执行的任务，这在配置阶段是已知的。

了解了构建阶段，我们现在可以理解，尽管我们不直接执行`taskName2`，但是`configure{}`闭包内的代码仍然在配置阶段执行，这导致了`Why is this printed first?`出现两次。

## 这能避免吗？

是的，为此，Gradle 有[配置避免 API](https://docs.gradle.org/current/userguide/task_configuration_avoidance.html) 。这是创建任务的推荐方法，通过避免直接使用`Task`实例，而是使用`TaskProvider`并创建对`Task`的引用来帮助减少配置时间。

代码被剪切#3

使用`TaskContainer.register()`将阻止任务被包括在配置阶段，除非注册的任务被直接执行或者被包括在正在执行的任务的依赖图中。

尝试再次运行`taskName1`，并查看输出是否与添加`taskName3`之前相同。同时，运行`taskName3`向日志的配置部分增加了一行，因为现在它与`taskName2`和`taskName1`一起包含在配置阶段

```
> Configure project :app
Why is this printed first?
Why is this printed first?
Why is this printed first?
```

```
> Task :app:taskName
First?
Last?
```

## 为什么我们有`doFirst`和 doLast？

为什么仅仅把动作按正确的顺序执行是不够的，为什么我们需要`doFirst{}`和`doLast{}`？

暂且把这些闭包看作是在 X 前后执行的东西，那么 X 是什么呢？

为了回答这个问题，让我们定义一个简单的任务类并运行新创建的任务类的任务，演示定义任务的另一种方法。

代码片段#4

将日志中与配置相关的部分放在一边，新定义类型的两个任务的输出将是:

```
> Task :app:taskName5
First?
Before and after actions annotated with @TaskAction
Last?
```

因此，提供给`doFirst{}`和`doLast{}`的动作在用`@TaskAction`标注的动作之前和之后执行。

正如您在代码片段#4 中看到的， `CustomTaskType`扩展了`DefaultTask`类，这是一个基本类，您可以扩展它来实现定制的任务类。Gradle 有几个有用的、现成的任务类型，你可以[在 Gradle 的 Github](https://github.com/gradle/gradle/tree/master/subprojects/core/src/main/java/org/gradle/api/tasks) 中找到。

## 关于 Gradle 任务还有什么要知道的？

`Task`结果表明在构建过程中任务发生了什么。凭直觉，您可能会猜测一个任务可能有三种结果——不执行、使用缓存的结果执行和只是执行。

在 Gradle 中，有五种任务结果:

1.  `NO-SOURCE` —任务未执行，因为未找到执行任务所需的输入数据。输入的一个例子可以是注释有`@InputFiles` `@SkipWhenEmpty`和`@Incremental`的文件，其未能由先前的任务产生。
2.  `SKIPPED` —由于某种原因被跳过。这种原因可以是——在任务体中标记为`enabled = false`的任务，或者通过命令行参数`-x`和一些其他参数从执行过程中排除的任务。
3.  `UP-TO-DATE` —自上次构建以来，任务结果未发生变化，可以重复使用。这是[增量构建](https://docs.gradle.org/current/userguide/more_about_tasks.html#sec:up_to_date_checks)特性的一部分。
4.  `FROM-CACHE` —任务可以从以前的构建中获取。它使用了一个叫做[任务输出缓存](https://docs.gradle.org/current/userguide/build_cache.html#sec:task_output_caching)的特性。这是在`UP-TO-DATE`中使用的增量构建的一个进步，因为它可以通过从 CI 获取远程缓存来重用它们。除非您在`gradle.properties`中有`org.gradle.caching=true`或者您在执行任务时使用`--build-cache`标志，否则这不会应用到您的构建中。对于一个可捕捉的任务，它应该被注释为`@CacheableTask`。
5.  `EXECUTED` —任务执行成功。该标签不会显示在日志中。

为了让这些结果可见，使用一个标志`--console=plain`，例如，在一个 Android 项目中，你可以在`assembleDebug`上使用它:

```
./gradlew assembleDebug --console=plain
```

理想情况下，一个构建应该有尽可能多的`UP-TO-DATE`和`FROM-CACHE`以获得更快的执行时间。

## 任务排序和相关性

已经提到过一个`Task`可以依赖于其他任务，但是在代码中是什么样子的呢？以下这些指标表示任务相互依赖:

明确定义两个任务之间的关系:

*   `dependsOn` — `task X { dependsOn Y }`，任务 X 的执行需要任务 Y，如果 Y 执行失败，X 的执行将不会发生。
*   `finalizedBy` — `task X { finalizedBy Y }`任务 Y 将在任务 X 之后执行，即使任务 X 未能执行或被跳过。

输入和输出注释:

*   `@OutputFile`和`@InputFile` —是通过注释任务的输入和输出来创建依赖关系的隐式方法。这种方法需要配置具有匹配输入和输出的任务。

# 结论

任务可以用多种方式定义，但并不是所有的都一样好。对于配置时间，使用配置避免 API 并使用`TaskContainer.register()`注册任务。

理解`Task`结果以识别构建的弱点，并通过适当地构建任务之间的依赖关系并使增量构建和任务输出缓存机制发挥作用，尽可能地缓存任务执行结果，这是很好的做法。

```
**Want to Connect?**

Connect with me on [Twitter](https://twitter.com/leonov_dmitrii) and [LinkedIn](https://www.linkedin.com/in/dmitrii-leonov/).
```