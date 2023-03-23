# 使用 Heroku 部署 Kotlin 应用程序

> 原文：<https://betterprogramming.pub/deploying-a-kotlin-app-with-heroku-a9b2880529e0>

## 用 PostgreSQL 数据库构建一个简单的 API

![](img/5a0e54aaa83a4f5d4c597948672f71ae.png)

照片由[你好我是尼克](https://unsplash.com/@helloimnik?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

自从最早发布以来，Java 一直标榜自己是一种“[编写一次，到处运行](https://en.wikipedia.org/wiki/Write_once,_run_anywhere)”的编程语言。这个想法是，程序员可以用 Java 开发一个应用程序，将其编译成字节码，并成为一个可在任何平台上运行的可执行文件，而不管是什么操作系统或平台。它之所以能够做到这一点，部分是因为运行时被称为 Java 虚拟机，或 JVM。

值得称赞的是，JVM 过去是(现在仍然是！)一个令人难以置信的微调运行时，它抽象出了计算机的底层硬件。虽然 Java 作为一种编程语言生存到了今天，但它经常被认为是笨重、乏味的，并且是实现解决方案的过时方法的代表。

在过去的十年中，越来越多运行在 JVM 上的语言已经开发出来，但是看起来和感觉上一点也不像 Java。一种这样的语言是科特林语。由于 JVM 的原因，它与普通 Java 相比并没有真正的性能优势。尽管如此，它的优势在于它以一种 Java 没有的方式优先考虑易读性。例如，考虑用 Java 打印一个子字符串:

```
// Java
String input = "What is the answer to the Ultimate Question of Life, the Universe, and Everything? 42";
String answer = input.substring(input.indexOf("?") + 1);
System.out.println(answer);
```

你必须首先得到你想要在子串中的字符的索引，加一(因为字符串是零索引的)，然后调用`System.out.println`写到 stdout。

在科特林，这要短得多:

```
// Kotlin
val input = "What is the answer to the Ultimate Question of Life, the Universe, and Everything? 42"
val answer = input.substringAfter("?")
println(answer)
```

Kotlin 吸引了如此多的兴趣，以至于谷歌甚至推荐它而不是 Java 来开发 Android 应用程序。

在这篇文章中，我们将快速了解如何用 Kotlin 开发一个应用程序。我们将使用 PostgreSQL 数据库构建一个简单的 API，并将其部署到 Heroku 进行现场演示。

# 先决条件

在我们开始之前，您需要确保您的计算机上安装了以下软件:

1.  关于 Heroku 的报道。这是完全免费的，不需要任何支付信息。
2.  Heroku CLI 。一旦你的应用程序在 Heroku 上，这将使管理它更容易。
3.  你需要安装[kot Lin](https://kotlinlang.org/docs/command-line.html)(>= 1.4)。
4.  你还需要安装[Gradle](https://gradle.org/install/)(>= 7.0)。

你还需要稍微熟悉一下 Git，并在你的机器上安装它。

我们将为这个 Kotlin 应用程序使用 IntelliJ IDE。他们的文档提供了一些关于如何创建新项目的指导。确保选择以下选项:

*   我们想要创建一个使用 Gradle 作为构建系统的 Kotlin 应用程序
*   将项目名称设置为`kotlin-api`
*   将 JDK 版本设置为 16。如果您没有安装此版本，可以从下拉列表中选择下载 JDK…,然后选择 Oracle Open JDK 版本 16

IDE 完成所有设置后，您应该有一个大致如下所示的目录结构:

```
kotlin-api
├── build.gradle.kts
└── src
    ├── main
    │   ├── kotlin
```

我们的 Kotlin 文件将保存在`src/main/kotlin`中，我们的构建逻辑将保存在`build.gradle.kts`中。

# 入门指南

Gradle 是多种语言的构建工具。它还充当依赖管理工具，类似于 Maven。在您的`build.gradle.kts`文件中已经有了一些行，IDE 自动添加了这些行以提供帮助。您可以删除所有这些内容，并粘贴以下代码行:

```
plugins {
    id("java")
    id("org.jetbrains.kotlin.jvm") version "1.5.10"
    id("org.springframework.boot") version "2.4.3"
    id("io.spring.dependency-management") version "1.0.11.RELEASE"
}
group "com.example"
version "0.0.1"
repositories {
    mavenCentral()
}
dependencies {
    implementation("org.jetbrains.kotlin:kotlin-stdlib") 
    implementation("org.springframework.boot:spring-boot-starter-web")    
    implementation("org.springframework.boot:spring-boot-starter")
    developmentOnly("org.springframework.boot:spring-boot-devtools")
}
```

这些行指定了我们项目的依赖项以及在哪里可以找到它们。例如，我们想在版本 2.4.3 中使用`[org.springframework.boot](https://plugins.gradle.org/plugin/org.springframework.boot)`，这就是为什么它被定义在`plugins`块中。我们指出在哪里可以找到包——在`mavenCentral()`——以及我们想要使用哪些公开的类(`implementation( "org.springframework.boot:spring-boot-starter-web")`)。

让我们创建两个小文件来测试我们的设置。在`src/main/kotlin`文件夹中创建一个名为`Application.kt`的文件，并粘贴如下内容:

```
package com.exampleimport org.springframework.boot.SpringApplication
import org.springframework.boot.autoconfigure.SpringBootApplication@SpringBootApplication
open class Applicationfun main(args: Array<String>) {
    SpringApplication.run(Application::class.java, *args)
}
```

这将启动一个使用 Spring 框架的默认应用程序。真正神奇的事情发生在下一个文件`Controller.kt`中，您应该在 *src/main/kotlin* 中与`Application.kt`一起创建这个文件:

在这里，我们定义了一条路由(`@GetMapping("/{name}")`，其中`{name}`是一个动态值。通过将这个装饰器放在一个 Kotlin 方法上(`fun get`，或者“一个名为 get 的函数”)，我们能够将路由匹配到我们想要的任何行为——在本例中，为一个`GET`请求返回一个带有路径名的问候。

为了让 IDE 知道如何启动我们的应用程序，我们需要创建一个运行配置。在 IDE 菜单的顶部，单击“添加配置”按钮。选择添加新的运行配置，然后从列表中选择 Gradle。

对于 Gradle 项目名称，输入`kotlin-api`。在“任务”字段中，键入`bootRun`。`[bootRun](https://docs.spring.io/spring-boot/docs/2.5.0/gradle-plugin/reference/htmlsingle/#running-your-application)`是 Spring 框架提供的一个 Gradle 任务，它将编译我们的代码并启动服务器。单击“Ok ”,现在 IDE 菜单栏中应该有一个绿色的 Play 按钮。当您点击它时，IDE 将执行`gradle bootRun`来构建这个 Kotlin 应用程序并启动服务器。完成后，导航至`http:*//localhost:8080/world*`。你应该看到一个很好的问候。

# 与数据库交互

现在，让我们进入(有点)严肃的话题。假设我们想在这个项目中附加一个数据库。在 Maven/Java 世界中，我们需要更新 XML 文件并添加对 JAR 文件的引用。在 Gradle 中，我们只需在我们的`build.gradle.kts`文件中添加几行就可以了:

```
dependencies {
    # ... implementation("com.zaxxer:HikariCP:4.0.3")
    runtimeOnly("org.postgresql:postgresql") # ...
}
```

这里，我们在项目中包含了 [HikariCP](https://github.com/brettwooldridge/HikariCP) ，它是一个流行的数据库连接驱动程序。我们还指出我们希望在运行时“加载”库`org.postgresql`。仅用这两行代码，我们就让我们的配置知道我们想要与 PostgreSQL 数据库进行交互。如果您已经有一个 PostgreSQL 数据库在本地运行，那就太好了。您将能够在本地继续本指南的剩余部分，并在浏览 localhost 时看到结果。如果你没有 PostgreSQL，不要担心——我们将向你展示在 Heroku 上部署这个应用程序是多么容易，它将为你管理基础设施。

回到`Controller.kt`，用下面的内容替换它。这需要一些我们以前有的东西，但是增加了它。我们将很快检查这些变化。

这里发生了很多事情！让我们从底层开始。我们定义了一个名为`dataSource`的函数，它提供了到我们数据库的连接。因为我们正在构建[一个 12 因素的应用](https://12factor.net/config)，我们的数据库凭证存储在一个名为`DATABASE_URL`的环境变量中。我们获取那个 URL，如果存在的话，从中提取用户名和密码。如果没有，我们检查另外两个环境变量来获取信息— `DATABASE_USERNAME`和`DATABASE_PASSWORD`。

然后，我们将所有信息放在一起，形成数据库连接器需要的格式。`initDb`函数创建一个名为`names`的表，其中有一个名为`name`的文本列。和之前一样，`/everyone`端点有一个`@GetMapping`装饰器。这定义了一个`GET /everyone`路由，它从数据库中获取所有的名字。

最后，我们添加了一些相当新的东西:一个`@PostMapping`装饰器。这里，我们需要定义这个`POST`路由可以接受什么类型的内容。在这种情况下，它是`consumes`一个`TEXT_PLAIN_VALUE`媒体类型(换句话说，`"Content-Type: text/plain"`)。我们放在请求体中的任何信息字符串都将被添加到数据库中。只用几行代码，我们就构建了一个可以添加和查询的小 API。

如果您现在启动此服务器，并且您在本地运行 PostgreSQL，您应该能够与它进行交互。尝试提出以下请求:

```
$ curl -H "Content-Type: text/plain" -X POST http://localhost:8080/add-name -d 'Frank'
```

如果你导航到`http://localhost:8080/everyone`，你会看到`Frank`被包括在内。

# 部署到 Heroku

是时候看看让 Kotlin 在 Heroku 上运行有多容易了。首先，我们需要创建一个特定于 Heroku 的文件:[proc file](https://devcenter.heroku.com/articles/procfile)。这个文本文件定义了我们的应用程序应该如何引导和运行。

在根目录下创建一个名为`Procfile`的文件，就在你的`build.gradle.kts`文件旁边。将以下几行复制并粘贴到其中:

```
web: java -jar build/libs/kotlin-api.jar --server.port=$PORT
```

在这里，我们说，我们希望 Heroku 运行`java -jar build/libs/kotlin-api.jar`。这个 JAR 是在部署过程中打包和构建的；Heroku 会自动为我们创建它，因为它知道如何执行 Gradle 任务来这样做。我们还绑定了`$PORT`环境变量，以便 Heroku 知道服务器正在监听哪个端口。

接下来，运行以下 Git 命令:

```
$ git init
$ git add .
$ git commit -m "Preparing my first Kotlin app"
```

既然我们已经安装了 Heroku CLI，我们可以[在命令行上调用 heroku create 来生成一个 app](https://devcenter.heroku.com/articles/creating-apps) :

```
$ heroku create
Creating app... done, ⬢ desolate-plains-67007
Created http://desolate-plains-67007.herokuapp.com/ | git@heroku.com:desolate-plains-67007.git
```

您的应用程序将被分配一个随机名称——在本例中，它是`desolate-plains-67007`——以及一个公共访问的 URL。

为了提供数据库，我们使用了`[heroku addons](https://devcenter.heroku.com/articles/heroku-cli-commands#heroku-addons-all-app-app)`命令。无参数调用它会让我们知道是否存在:

```
$ heroku addons
No add-ons for app desolate-plains-67007.
```

我们的应用程序不存在任何附加组件，这很有意义——我们只是创建了它！要添加 PostgreSQL 数据库，我们可以像这样使用`addons:create`命令:

```
$ heroku addons:create heroku-postgresql:hobby-dev
```

Heroku 提供了几层 PostgreSQL 数据库。`hobby-dev`是免费层，所以我们可以不花一分钱就玩这个。

您的代码已准备好；您的 Heroku 应用程序已配置好，您可以开始部署了。这是容易的部分！只需输入以下命令:

```
$ git push heroku master
```

你的代码会被推送到 Heroku。从那时起，赫罗库将接管一切。你会看到你的构建日志在你的终端上滚动。这将向您显示 Heroku 正在为您安装什么，以及您在构建过程中的位置。完成后，您可以在浏览器中访问您的特殊 URL(在本例中为`https://desolate-plains-67007.herokuapp.com`)并与互联网上的 API 进行交互！

# 了解更多信息

Kotlin 的[高性能设计和清晰的语法](https://blog.heroku.com/rise-of-kotlin)——结合 Gradle 的易用性——使其成为需要依赖久经考验的 Java 包的企业的理想选择。由于与 Java 的互操作性，Kotlin 是理想的过渡语言；大量的 Java 代码可以被转换成 Kotlin，同时仍然保持应用程序的功能。部署到 Heroku 很顺利，我甚至没有利用不同的方法[来微调 Java 和基于 JVM 的应用程序进行部署](https://devcenter.heroku.com/categories/java-support)。