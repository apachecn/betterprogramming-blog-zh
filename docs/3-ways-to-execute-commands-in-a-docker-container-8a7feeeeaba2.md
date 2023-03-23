# 在 Docker 容器中执行命令的 3 种方式

> 原文：<https://betterprogramming.pub/3-ways-to-execute-commands-in-a-docker-container-8a7feeeeaba2>

## 提高你的码头游戏

![](img/921b5cd8ea69721505a0ff6243d6374d.png)

图片由[壁纸耀斑](https://www.wallpaperflare.com/man-shooting-on-basketball-hoop-outdoor-ballin-swish-outdoor-court-wallpaper-zueky)

Docker 容器是一个轻量级的可执行程序，它包含了从运行时到系统工具和库的所有东西来运行应用程序。当容器化一个应用程序时，我们可能经常需要在容器内执行不同的命令——例如，为了调试目的或者为了手动运行应用程序。

在 Docker 容器中有三种执行不同命令的方法，我将在本文中讨论每种方法。

# 在 DockerFile 文件中使用“CMD”

在为应用程序构建映像时，我们需要指定一个 docker 文件，其中包含如何创建该映像以及它将执行什么应用程序的指令。

下面的代码是一个 DockerFile 代码，用于封装我创建的一个简单的 Java 应用程序。出于演示目的，该应用程序向标准输出输出一行代码。

```
**FROM** frolvlad**/**alpine-oraclejre8
**ARG *JAR_FILE_NAME* ADD** target**/**${***JAR_FILE_NAME***} app.jar

**CMD** java **-**jar **/**app.jar
```

最后一条指令是我们感兴趣的。它表示当运行该图像中的容器时，将执行`java -jar /app.jar`命令。这个命令将运行 Java 应用程序，打印在`Main`类中指定的输出，然后结束。

下面的代码片段展示了构建映像、从该映像运行容器以及从 Java 应用程序获得输出的过程。

```
docker image build -t docker-demo . --build-arg JAR_FILE_NAME=demo.jardocker container run docker-demo:latest**Output:***Java app is running inside a container*
```

# 启动容器时运行命令

执行 Java `run`命令的另一种方式是在启动容器时指定该命令。我们需要首先从 docker 文件中删除`CMD`行，然后重新构建图像，这样这个方法才能工作。

为了在容器启动时启动 Java 应用程序，我们将在`docker container run`命令后指定 Java `run`命令，如下所示。注意，下面的容器不是在分离模式下运行的，所以 Java 应用程序输出将在标准输出中打印。然后应用程序将退出。

```
docker container run docker-demo:latest **java -jar /app.jar**
```

这样，我们也可以运行其他命令，而不仅仅是应用程序启动命令。例如，我们可以使用`ls`命令列出容器中的所有文件。

```
docker container run docker-demo:latest **ls -l**
```

# 直接从容器外壳运行命令

我们可以启动一个容器，直接访问它的交互式外壳来执行命令，就像在 Linux 终端中一样。我们可以使用以下命令在交互模式下启动容器:

```
docker container run **-it** docker-demo:latest **/bin/bash**
```

参数`-it`指定交互模式和伪`tty`的分配，而`/bin/bash`指定允许我们在容器上执行命令的外壳。

执行该命令后，我们可以访问容器的外壳，并从那里启动 Java 应用程序:

```
**bash-4.4#** java -jar /app.jar
Java app is running inside a container
```

值得注意的是，您创建的映像可能没有安装 shell——就像我的例子一样——当使用上面的命令运行容器时，您会得到下面的错误消息。

```
starting container process caused **"exec: \"/bin/bash\"**: stat **/bin/bash**: no such file or directory
```

在这种情况下，如果我们想从容器中运行命令，我们必须在构建映像时指定 shell 安装。应该将以下几行添加到 docker 文件中，以使 shell 可用。

```
**RUN** apk update
**RUN** apk upgrade
**RUN** apk add bash
```

感谢您阅读本文，希望您觉得有用。

保重！