# 整个事情:编写文件

> 原文：<https://betterprogramming.pub/the-whole-shebang-compose-files-5b6f50dd196c>

## 通过理解和实践 Docker Compose 的每个可用选项，掌握多容器应用程序

![](img/459ccdf8161241e45dba2e4a7941e469.png)

照片由[卡梅隆通风](https://unsplash.com/@ventiviews?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我最近一直在写关于 Docker 和容器的文章。我非常感谢那些使用我的内容并提供反馈的人。我明白你的意思，很自然，下一个要解决的话题是 Docker Compose——因为现在你已经知道如何构建自己的容器映像并运行它们，下一个逻辑里程碑是创建复杂的多容器应用程序，并将所有这些作为单个软件进行管理。

# Docker Compose:它是什么，为什么我需要它？

> **Compose** 是一个定义和运行多容器 **Docker** 应用的工具。

您可以从[官方文档](https://docs.docker.com/compose/)中了解更多信息，但是为了简化，请考虑以下内容:您使用 **Docker** 来管理容器、图像、卷等。当你做`docker run`时，你基本上是在执行一个**单个**容器。如果您想启动第二个容器，以某种方式与第一个容器进行交互，您需要再次运行`docker run`,并分别处理它们的卷和网络，这样它们就可以相互查看和通信。

Docker Compose 通过在 Docker 之上提供一个**包装器**抽象了所有这些(是的！Docker Compose 只是 Docker 的一个高级 CLI)。事实上，您会看到大多数选项的命名方式与`docker run`子命令中的选项非常相似。

使用 Docker Compose，您可以通过名为 **Compose Files** 的 YAML 文件，以声明方式**定义您的所有容器(在 Docker Compose 世界中称为`services`…,因为您也可以在 Swarm 模式下使用 Compose 文件……但在另一篇文章中会有更多相关内容)、卷和网络及其配置。这样，每当你需要管理你的多容器应用程序时，你只需要接触这个 compose 文件，然后让 Docker Compose 处理其他的事情。**

# 撰写文件

我们将把 Docker Compose CLI 留给另一篇文章，因为首先，您需要学习如何编写 Compose 文件，以便能够正确地将多容器应用程序投入使用。

compose files 上的[Docker docs](https://docs.docker.com/compose/compose-file/)非常完整，但是我坚信实用主义和从经验中学习，所以我将整理所有的 Compose File 选项，并提供简单的解释和真实的例子——开始吧！

**注 1:** 我将只讨论 compose files 规范 v3 的选项和模式(这是撰写本文时的最新版本，所以应该没问题)

**注 2:** 对于每个选项，我将只解释和举例说明它们的常见用法(即我将使用我多年的 Docker 经验作为参考，因此如果有某个选项参数我从未使用过，我会将其视为“不常用”)。关于什么是可能的以及如何使用某个选项的不常用参数的更多细节，请查看[正式文档](https://docs.docker.com/compose/compose-file/compose-file-v3/)

在深入示例之前，请注意所有的合成文件都以关键字`version`开头，表示我们正在编写的合成文件的模式。如上所述，我们的将是最新的(`version: “3”`)。

## 建设

基本上，当您开始使用 Docker Compose 时，您可以提供 Docker 图像或 Docker 文件。如果您希望 Docker 在启动相应的容器之前构建您的容器映像，而不必手动构建映像，那么您可以使用这个选项。

例如:

```
**$** cat >Dockerfile <<EOF
# This is the Dockerfile
FROM ubuntuCMD ["echo", "Hello Medium readers"]# End of the Dockerfile
EOF
**$
$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  my-test-container:
    build: .
EOF
**$
$** docker-compose up 
Creating network "mediumcom_default" with the default driver
Building my-test-container
Sending build context to Docker daemon  1.337MB
Step 1/2 : FROM ubuntu
 ---> ba6acccedd29
Step 2/2 : CMD ["echo", "Hello Medium readers"]
 ---> Using cache
 ---> 6c8b22fa650c
Successfully built 6c8b22fa650c
Successfully tagged mediumcom_my-test-container:latest
Creating mediumcom_my-test-container_1 ... done
Attaching to mediumcom_my-test-container_1
my-test-container_1  | Hello Medium readers
mediumcom_my-test-container_1 exited with code 0
**$**
```

如您所见，Docker Compose 指示 Docker 从我们的 Dockerfile(位于与 Compose 文件相同的路径中)构建一个图像，然后自动启动我们的容器。

注意:正如您所料，您通常在`docker build`中使用的一些选项也可以在这里使用，比如传递构建参数、指定 docker 文件的路径和名称等。查看[文档](https://docs.docker.com/compose/compose-file/compose-file-v3/#build)了解更多详情。

## 图像

很明显，你不需要在每次启动应用程序时都构建 Docker 映像…那会很麻烦。所以你要么选择`build`，要么简单地指定容器`image`。

例如，假设前一个示例中的 docker 文件，并采用以下合成文件:

```
**$** # just to clean the previous example
**$** # from here on, make sure this command is executed on every **$** #example, just for the sake of simplicity
**$** docker-compose down -v --remove-orphans   
**$
$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    build: . second:
    image: hello-world
EOF**$** # Dockerfile hasn't changed, so the 1st image won't be rebuilt **$ docker-compose up**   
Creating network "mediumcom_default" with the default driver
Creating **mediumcom_first_1**  ... done
Creating **mediumcom_second_1** ... done
Attaching to mediumcom_second_1, mediumcom_first_1
**first_1   | Hello Medium readers**
second_1  | 
**second_1  | Hello from Docker!**
second_1  | This message shows that your installation appears to be working correctly.
second_1  | 
second_1  | To generate this message, Docker took the following steps:
second_1  |  1\. The Docker client contacted the Docker daemon.
second_1  |  2\. The Docker daemon pulled the "hello-world" image from the Docker Hub.
second_1  |     (amd64)
second_1  |  3\. The Docker daemon created a new container from that image which runs the
second_1  |     executable that produces the output you are currently reading.
second_1  |  4\. The Docker daemon streamed that output to the Docker client, which sent it
second_1  |     to your terminal.
second_1  | 
second_1  | To try something more ambitious, you can run an Ubuntu container with:
second_1  |  $ docker run -it ubuntu bash
second_1  | 
second_1  | Share images, automate workflows, and more with a free Docker ID:
second_1  |  [https://hub.docker.com/](https://hub.docker.com/)
second_1  | 
second_1  | For more examples and ideas, visit:
second_1  |  [https://docs.docker.com/get-started/](https://docs.docker.com/get-started/)
second_1  | 
mediumcom_second_1 exited with code 0
mediumcom_first_1 exited with code 0
```

如你所见，现在我们已经在同一个多容器应用程序中，从单个合成文件和单个`docker-compose`命令并行启动了 2 个容器。其中一个容器使用我们定制的 Docker 映像来表示“Hello Medium readers”，而另一个容器从 Docker Hub 中提取 Docker 映像“hello-world ”,并打印出自己的 Hello World 消息。

## 命令

这允许您定义容器的命令。因此，如果您的 Docker 映像定义了一个入口点，这个命令将是它的参数。示例:

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: ubuntu
    command: echo "I am at your command!"EOF
**$** docker-compose up
Creating network "mediumcom_default" with the default driver
Creating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
**first_1  | I am at your command!** mediumcom_first_1 exited with code 0
```

超级直白吧？

## 入口点

顾名思义，它允许您(重新)定义容器的入口点。假设您希望您的容器作为一个`echo`可执行文件运行:

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: ubuntu
    entrypoint: ["echo"]
    command: "I am just an arg"EOF
**$** docker-compose up
Creating network "mediumcom_default" with the default driver
Creating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
**first_1  | I am just an arg**
mediumcom_first_1 exited with code 0
```

简单吧？如果你想知道`command`对`entrypoint`等等。，你不是唯一一个。这是一个非常典型的困惑点。我已经在我的另一篇文章[“The Whole she bang:docker files”](/the-whole-shebang-dockerfiles-5d59ace94d28)中谈过了。简而言之，这是我在需要协调这两个选项时的备忘单:[https://docs . docker . com/engine/reference/builder/# understand-how-cmd-and-entry point-interact](https://docs.docker.com/engine/reference/builder/#understand-how-cmd-and-entrypoint-interact)

## cap_add

如果您的容器需要额外的功能，您可以这样添加它们:

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: ubuntu:14.04
    command: ip link add dummy0 type dummy
    cap_add:
     - NET_ADMINEOF
**$** docker-compose up
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
mediumcom_first_1 **exited with code 0**
```

现在，如果您要运行与上面相同的容器，但是没有该功能，您会得到一个错误:

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: ubuntu:14.04
    command: ip link add dummy0 type dummyEOF
**$** docker-compose up
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
**first_1  | RTNETLINK answers: Operation not permitted**
mediumcom_first_1 **exited with code 2**
```

## cap_drop

与`cap_add`完全相反。使用它从容器中删除功能。假设我们有一个超级特权容器:

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: ubuntu:14.04
    command: ip link add dummy0 type dummy
    cap_add:
     - **ALL**EOF
**$** docker-compose up
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
mediumcom_first_1 exited with code **0**
```

但是我们不希望它管理主机的网络堆栈:

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: ubuntu:14.04
    command: ip link add dummy0 type dummy
    cap_add:
     - ALL
    cap_drop:
     - NET_ADMINEOF
**$** docker-compose up
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | **RTNETLINK answers: Operation not permitted**
mediumcom_first_1 exited with code 2
```

## cgroup_parent

如果你是第一次接触`cgroups`，这里有一篇关于它们的很好的短文。简而言之，Linux 内核为用户提供了通过控制组(又名`cgroups`)按进程严格控制系统资源使用的能力。在 Docker 中，这意味着您可以控制容器可以使用多少特定资源。默认情况下，容器会尽可能多地获取资源:

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: ubuntu
    command: sleep 30EOF
**$** docker-compose up -d 
Creating network "root_default" with the default driver
Creating **root_first_1** ... done
**$** docker inspect root_first_1 --format '{{.HostConfig.CgroupParent}}'**$**
```

如您所见，默认情况下，没有为容器分配控制组。但是我们可以指定一个。我们甚至可以分配我们自己的:

```
**$** cgcreate -g cpu:test-cgroup-medium
**$** ls /sys/fs/cgroup/cpu
cgroup-limit           cgroup.sane_behavior  cpu.shares    cpuacct.usage         cpuacct.usage_percpu_sys   cpuacct.usage_user  release_agent  **test-cgroup-medium**
cgroup.clone_children  cpu.cfs_period_us     cpu.stat      cpuacct.usage_all     cpuacct.usage_percpu_user  docker              system.slice   user.slice
cgroup.procs           cpu.cfs_quota_us      cpuacct.stat  cpuacct.usage_percpu  cpuacct.usage_sys          notify_on_release   tasks
```

通过使用这个选项，我们将能够通过`cgroup` "cpu:test-cgroup-medium ":

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: ubuntu
    command: sleep 30
    cgroup_parent: "cpu:test-cgroup-medium"EOF
**$** docker-compose up -d 
**Recreating root_first_1** ... done
**$** docker inspect root_first_1 --format '{{.HostConfig.CgroupParent}}'
**cpu:test-cgroup-medium
$**
```

## 容器名称

这个选项没有火箭科学可言。它基本上允许您修改 Docker 合成部署产生的容器的名称。

您可能已经注意到，默认情况下，Docker Compose 会为您的容器添加一个后缀，与 Docker Compose 项目的名称相匹配(如果您没有明确指定这个项目的名称，Docker Compose 会将您的当前路径作为默认路径)。

因此，如果您想覆盖这个 Docker 组合行为，并为生成的容器修改您自己的自定义名称，您可以使用 container_name。示例:

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: ubuntu
    container_name: my-own-container-nameEOF
**$** docker-compose up
Creating network "mediumcom_default" with the default driver
Creating **my-own-container-name** ... done
Attaching to **my-own-container-name**
**my-own-container-name** exited with code 0
```

**P.S.:** 是的，正如您可能想知道的那样，这个 Docker Compose 部署中的其他容器可以通过容器名相互联系。示例:

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: sleep 5
    container_name: my-own-container-name second:
    image: alpine
    command: ping -c 1 my-own-container-nameEOF
**$** docker-compose up
Recreating my-own-container-name ... done
Creating mediumcom_second_1      ... done
Attaching to mediumcom_second_1, my-own-container-name
second_1  | PING my-own-container-name (172.18.0.3): 56 data bytes
second_1  | 64 bytes from 172.18.0.3: seq=0 ttl=64 time=0.743 ms
second_1  | 
second_1  | --- my-own-container-name ping statistics ---
second_1  | 1 packets transmitted, 1 packets received, 0% packet loss
second_1  | round-trip min/avg/max = 0.743/0.743/0.743 ms
mediumcom_second_1 exited with code 0
my-own-container-name exited with code 0
```

## 依赖于

让我先说**不**，这个选项**不能让你控制你的容器**应用**的启动顺序！这是一个常见的错误。但是请想一想…您的容器内部正在运行一个或多个进程，因此假设 Docker(管理所述容器的人)完全了解在容器内部运行的任何应用程序/进程的商业智能是幼稚的。**

这是一个细微的差别，但是这个选项的作用是指示 Docker Compose 以什么顺序开始/停止容器。

示例:

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    depends_on:
     - second second:
    image: alpine
    depends_on:
     - firstEOF
**$** docker-compose up
Creating network "mediumcom_default" with the default driver
Creating **mediumcom_second_1** ... done
Creating **mediumcom_first_1**  ... done
Attaching to mediumcom_second_1, mediumcom_first_1
mediumcom_second_1 exited with code 0
mediumcom_first_1 exited with code 0
```

看到了吗？因为`first`依赖于`second`，所以`second`容器会先启动，然后`first`才会启动。同样的，当你阻止他们的时候——`second`会最后倒下，因为`first`就靠它了！

```
**$** docker-compose down
Removing **mediumcom_first_1**  ... done
Removing **mediumcom_second_1** ... done
Removing network mediumcom_default
```

**附:**如果你陷入混乱，而你使`second`也依赖`first`怎么办？😝别担心，Docker 很聪明，会告诉你这样的话:

> 错误:第一个和第二个之间存在循环依赖

## 卷

我不会深入解释 Docker 卷。这是[相当大的话题](https://docs.docker.com/storage/volumes/)。假设您有 4 种方法来管理容器应用程序中的数据:

1.  将所有数据保留在容器的可写层中(无卷)
2.  在主机和容器之间创建一个装载点(绑定装载)
3.  创建专用的 Docker 资源来托管和管理数据，独立于容器的生命周期(卷)
4.  通过不保存任何数据，而是将数据保存在内存中(tmpfs)来优化容器的性能。

现在，尽管如此，您显然可以从合成文件中管理所有这些类型的卷。到目前为止，上面所有的例子都利用了容器的可写层。但是我们可以使用剩余的 3 个数据管理选项，如下所示:

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: ls /
    volumes:
     - /:/my-hostfs
     - my-volume:/my-volume
     - type: tmpfs
       target: /my-tmpfsvolumes:
  my-volume:EOF
**$** docker-compose up
Creating network "test_default" with the default driver
Creating volume "test_my-volume" with default driver
Creating test_first_1 ... done
Attaching to test_first_1
first_1  | bin
first_1  | dev
first_1  | etc
first_1  | home
first_1  | lib
first_1  | media
first_1  | mnt
first_1  | **my-hostfs**
first_1  | **my-tmpfs**
first_1  | **my-volume**
first_1  | opt
first_1  | proc
first_1  | root
first_1  | run
first_1  | sbin
first_1  | srv
first_1  | sys
first_1  | tmp
first_1  | usr
first_1  | var
test_first_1 exited with code 0
```

这就对了，三种不同类型的坐骑一气呵成。顺便说一下，你可能已经注意到我使用了 3 种不同的语法…你可以选择你喜欢的，或者最适合你的坐骑的(在这里阅读更多关于它们的信息)。

此外，`tmpfs`也可以通过专用选项设置…阅读下一节的示例。

## tmpfs

这在上一节中已经描述过了，但是正如您所知道的，这只是为您的容器设置非持久挂载点的另一种更简单的方法:

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: ls /
    tmpfs: /my-tmpfsEOF
**$** docker-compose up
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | bin
first_1  | dev
first_1  | etc
first_1  | home
first_1  | lib
first_1  | media
first_1  | mnt
**first_1  | my-tmpfs** first_1  | opt
first_1  | proc
first_1  | root
first_1  | run
first_1  | sbin
first_1  | srv
first_1  | sys
first_1  | tmp
first_1  | usr
first_1  | var
mediumcom_first_1 exited with code 0
```

## 设备

顾名思义，它允许您访问用于管理系统设备的容器。你有理由怀疑这与直装订有什么不同。你可以简单地把一个设备绑定到你的容器上，对吗？嗯，是也不是…这取决于你的容器是如何处理那个设备的。设备文件与常规文件和目录略有不同(即使它们看起来像您的`/dev`文件夹下的普通文件)。设备文件提供了到设备驱动程序和物理设备的接口，因此具有更细粒度的访问控制，这通过`cgroups`来管理(你可以在这里阅读更多关于它的[)。](https://www.kernel.org/doc/Documentation/cgroup-v1/devices.txt)

因此，简而言之，如果您的容器没有运行在特权模式下，您仍然可以通过这个选项授予您的容器对特定设备文件的访问权限。

假设我们的电脑上安装了一个网络摄像头，

```
**$** lsusb -s 001:003
Bus 001 Device 003: ID 046d:0826 **Logitech, Inc. HD Webcam C525**
```

我们想在集装箱里用它来拍照。然后考虑以下场景:

*   首先，让我们尝试从容器内部访问网络摄像头，不应用任何选项

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: ccordeiro/fswebcam:armv7l
    command: fswebcam -r 640x480 --jpeg 85 -D 1 picture.jpgEOF
**$** docker-compose up
Creating network "tmp_default" with the default driver
Creating tmp_first_1 ... done
Attaching to tmp_first_1
first_1  | --- Opening /dev/video0...
**first_1  | stat: No such file or directory
first_1  | tmp_first_1 exited with code 0**
```

…但显然，容器对任何设备都不可见…

*   其次，让我们尝试直接绑定挂载设备文件

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: ccordeiro/fswebcam:armv7l
    command: fswebcam -r 640x480 --jpeg 85 -D 1 picture.jpg
    volumes:
     - /dev/video0:/dev/video0EOF
**$** docker-compose up
Recreating tmp_first_1 ... done
Attaching to tmp_first_1
first_1  | --- Opening /dev/video0...
first_1  | Trying source module v4l2...
**first_1  | Error opening device: /dev/video0
first_1  | open: Operation not permitted** first_1  | Trying source module v4l1...
**first_1  | Error opening device: /dev/video0
first_1  | open: Operation not permitted** first_1  | Unable to find a source module that can read /dev/video0.
first_1  | tmp_first_1 exited with code 0
```

…嗯，容器可以看到它，但是它有访问它的权利。

*   最后，让我们使用`device`

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: ccordeiro/fswebcam:armv7l
    command: fswebcam -r 640x480 --jpeg 85 -D 1 picture.jpg
    devices:
     - /dev/video0:/dev/video0EOF
**$** docker-compose up
Recreating tmp_first_1 ... done
Attaching to tmp_first_1
first_1  | --- Opening /dev/video0...
first_1  | Trying source module v4l2...
first_1  | /dev/video0 opened.
first_1  | No input was specified, using the first.
first_1  | Delaying 1 seconds.
first_1  | --- Capturing frame...
first_1  | Captured frame in 0.00 seconds.
**first_1  | --- Processing captured image...
first_1  | Setting output format to JPEG, quality 85
first_1  | Writing JPEG image to 'picture.jpg'.** first_1  | tmp_first_1 exited with code 0
```

我们成功了！

## 网络模式

你可能知道，Docker 的联网系统是基于[联网驱动](https://docs.docker.com/network/)的。使用此选项，您可以指定容器应该以哪种网络模式运行。

假设我们要部署 4 个容器:1 个在默认桥接网络中，1 个在主机网络中，1 个在与第一个相同的网络中，1 个没有任何网络。你应该这样做:

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: sh -c 'ifconfig eth0 && sleep 5'
    network_mode: bridge second:
    image: alpine
    command: ifconfig eth0
    network_mode: host third:
    image: alpine
    command: ifconfig eth0
    depends_on:
     - first
    network_mode: service:first fourth:
    image: alpine
    command: ifconfig eth0
    network_mode: noneEOF
**$** docker-compose up
Creating mediumcom_first_1  ... done
Creating mediumcom_fourth_1 ... done
Creating mediumcom_second_1 ... done
Creating mediumcom_third_1  ... done
Attaching to mediumcom_second_1, mediumcom_fourth_1, mediumcom_first_1, mediumcom_third_1
**first_1   | eth0      Link encap:Ethernet  HWaddr 02:42:AC:11:00:02  
first_1   |           inet addr:172.17.0.2**  **Bcast:172.17.255.255  Mask:255.255.0.0**
**...**
**fourth_1  | ifconfig: eth0: error fetching interface information: Device not found
...
second_1  | eth0      Link encap:Ethernet  HWaddr 02:50:00:00:00:01  
second_1  |           inet addr:192.168.65.3  Bcast:192.168.65.255  Mask:255.255.255.0**
...
**third_1   | eth0      Link encap:Ethernet  HWaddr 02:42:AC:11:00:02  
third_1   |           inet addr:172.17.0.2  Bcast:172.17.255.255  Mask:255.255.0.0**
...
mediumcom_fourth_1 exited with code 1
mediumcom_second_1 exited with code 0
mediumcom_third_1 exited with code 0
mediumcom_first_1 exited with code 0
```

发生了什么事？让我们来分解一下:

*   `first`容器进入网桥模式，因此被分配了自己的`eth0`专用网络接口(IP 172.17.0.2，是 Docker 网桥子网的一部分)
*   `second`容器进入主机模式，这意味着它运行在与主机相同的网络级别，因此可以看到主机的所有网络接口，包括主机的 eth0 IP
*   `third`集装箱和`first`集装箱被赋予相同的网络
*   `fourth`容器没有网络，因此它无法找到`eth0`接口。

## **网络**

现在，回到上面的选项，如果您想将您的容器连接到一个用户定义的网络(现有的或新的)，该怎么办呢？嗯，用`networks`:

```
**$** docker network create cjdc-test
0f5e35bf6b8d396c45c19545540957fbc5fa15ba1ca0e5fbca4add3802c7db6d
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: sleep 30
    networks:
     - new-net second:
    image: alpine
    command: sleep 30
    networks:
     - cjdc-testnetworks:
  new-net:
  cjdc-test:
    external: true
    name: cjdc-testEOF
**$** docker-compose up -d
**Creating network "mediumcom_new-net**" with the default driver
Creating mediumcom_second_1 ... done
Creating mediumcom_first_1  ... done
**$** docker inspect --format '{{.NetworkSettings.Networks}}' mediumcom_first_1
map[**mediumcom_new-net**:0xc0004d8f00]
**$** docker inspect --format '{{.NetworkSettings.Networks}}' mediumcom_second_1
map[**cjdc-test**:0xc0003e8f00]
```

## 十进位计数制

它基本上让你为你的容器定义一个定制的 DNS 服务器。示例:

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: cat /etc/resolv.conf
    dns: 1.2.3.4
    network_mode: bridgeEOF
**$** docker-compose up
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | nameserver **1.2.3.4**
mediumcom_first_1 exited with code 0
```

**注意:**注意需要有一个桥接网络，因为对于用户定义的网络来说,`dns`选项不会有这样的行为。

## dns_search

这样，您可以设置自己的自定义 DNS 搜索域。

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: cat /etc/resolv.conf
    dns_search: my.custom.dns
    network_mode: bridgeEOF
**$** docker-compose up 
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | search **my.custom.dns**
first_1  | nameserver 192.168.65.5
mediumcom_first_1 exited with code 0
```

## 环境文件

您可能习惯于一次设置一个变量的容器环境，但是您可以使用一个文件来一次性完成这项工作:

```
**$** cat >my-env <<EOF
VAR1=value
MY_VAR_FROM_FILE=1
FOO=BAR
EOF**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: env
    env_file:
     - my-envEOF
**$** docker-compose up 
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
first_1  | HOSTNAME=157e34e4fc64
first_1  | **VAR1=value**
first_1  | **MY_VAR_FROM_FILE=1**
first_1  | **FOO=BAR**
first_1  | HOME=/root
mediumcom_first_1 exited with code 0
```

## 环境

这样，您可以直接在合成文件中一个接一个地为容器设置环境变量。

您也可以用它来覆盖通过`env_file`选项加载的变量。

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: env EOF
**$** docker-compose up 
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
first_1  | HOSTNAME=68e909013198
first_1  | **NEW_VAR=yes**
first_1  | HOME=/root
mediumcom_first_1 exited with code 0
```

## 揭露

与 Dockerfiles 非常相似，这个选项只是简单地标记一个容器的端口，并使它们对其他链接的容器可用。

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: sleep 20
    expose:
     - 9876EOF
**$** docker-compose up -d
Recreating mediumcom_first_1 ... done
**$** docker ps
CONTAINER ID   IMAGE     COMMAND      CREATED          STATUS          PORTS      NAMES
3be377f4bf6c   alpine    "sleep 20"   18 seconds ago   Up 16 seconds   **9876/tcp**   mediumcom_first_1
```

## 外部链接

*让我们跳过这一个原因 1)它是一个遗留选项，2)您可以使用网络来实现相同的结果(即，通过* `*external*` ***网络*** *，将您的容器连接到您的合成文件之外的其他容器)。*

## 额外主机

这基本上允许您向容器的`/etc/hosts`文件添加一个新条目。例如，如果你想让你的容器在内部给自己起一个别的名字，而不仅仅是“localhost ”,你可以这样做:

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: sh -c 'cat /etc/hosts; ping me -c 1'
    extra_hosts:
     - "myself:127.0.0.1"
     - "me:127.0.0.1"EOF
**$** docker-compose up 
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | 127.0.0.1 localhost
first_1  | ::1 localhost ip6-localhost ip6-loopback
first_1  | fe00::0 ip6-localnet
first_1  | ff00::0 ip6-mcastprefix
first_1  | ff02::1 ip6-allnodes
first_1  | ff02::2 ip6-allrouters
**first_1  | 127.0.0.1 myself
first_1  | 127.0.0.1 me**
first_1  | 172.22.0.2 01d566c4380a
first_1  | PING **me** (127.0.0.1): 56 data bytes
first_1  | 64 bytes from 127.0.0.1: seq=0 ttl=64 time=1.192 ms
first_1  | 
first_1  | --- me ping statistics ---
first_1  | **1 packets transmitted, 1 packets received, 0% packet loss**
first_1  | round-trip min/avg/max = 1.192/1.192/1.192 ms
mediumcom_first_1 exited with code 0
```

## 健康检查

正如所料，这与同名的 Dockerfile 选项非常相似。它允许您创建一个自定义检查，Docker 可以用它来评估您的容器是否“健康”。

因此，假设只有当在`/tmp`有一个名为`medium.txt`的文件时，我们的容器才是健康的。

```
**$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: sh -c 'touch /tmp/NOT_MY_FILE; sleep 10'
    healthcheck:
      test: "[ -f /tmp/medium.txt ]"
      # run asap
      interval: 1s
      timeout: 5s
      retries: 1
      start_period: 0s second:
    image: alpine
    command: sh -c 'touch /tmp/medium.txt; sleep 10'
    healthcheck:
      test: "[ -f /tmp/medium.txt ]"
      # run asap
      interval: 1s
      timeout: 5s
      retries: 1
      start_period: 0sEOF
**$** docker-compose up -d
Recreating mediumcom_first_1 ... done
Creating mediumcom_second_1  ... done
**$** docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS                     PORTS     NAMES
b8804cbb440d   alpine    "sh -c 'touch /tmp/N…"   4 seconds ago   Up 2 seconds (**unhealthy**)             **mediumcom_first_1**
604a6880f3c6   alpine    "sh -c 'touch /tmp/m…"   4 seconds ago   Up 2 seconds (**healthy**)               **mediumcom_second_1**
```

正如所料，`first`不正常，但`second`正常，因为所需的文件存在于正确的位置。

## 初始化

有了这个，你可以告诉 Docker 是否为你的容器使用一个`init`。如果是`true`，Docker 将使用基于[](https://github.com/krallin/tini)*的 init 调用`docker-init`。让我们来看看不同之处:*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: ps -a
    init: falseEOF
**$** docker-compose up 
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | PID   USER     TIME  COMMAND
first_1  |     1 root      0:00 **ps -a**
mediumcom_first_1 exited with code 0*
```

*但是如果您启用了一个`init`，那么您将得到这种流程包装器，并添加了 PID 1(在本例中是`docker-init`)功能:*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: ps -a
    init: trueEOF
**$** docker-compose up
Starting mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | PID   USER     TIME  COMMAND
first_1  |     1 root      0:00 **/sbin/docker-init** -- ps -a
first_1  |     8 root      0:00 ps -a
mediumcom_first_1 exited with code 0*
```

## *隔离*

*对于 Linux，这总是`default`。对于 Windows，你有更多的选择，但我恐怕我没有一台 Windows 机器来为你们运行一个例子😛对不起 Windows 用户！*

## *标签*

*顾名思义，使用该选项可以为容器设置标签:*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: sleep 10
    labels:
     - "foo=bar"EOF
**$** docker-compose up -d
Recreating mediumcom_first_1 ... done
**$ docker ps --filter 'label=foo=bar'** CONTAINER ID   IMAGE     COMMAND      CREATED          STATUS         PORTS     NAMES
94e844176d76   alpine    "sleep 10"   30 seconds ago   Up 7 seconds             **mediumcom_first_1***
```

## *链接*

**至于* `*external_links*` *我们就跳过这个* ***遗留*** *选项吧。容器之间的通信可以通过用户定义的网络轻松实现。**

## *记录*

*实际上，我发现这个选项非常关键，因为大多数用户通常没有足够重视他们的容器应用程序日志不断增长的大小。除非您希望您的主机随着时间的推移而填满，否则对于任何其他应用程序，您还必须控制应用程序记录其内容的方式。Docker 允许您通过使用这个`logging`选项来控制容器应用程序的标准输出日志。不仅是日志记录的类型，还有日志记录大小限制和循环参数:*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: sh -c 'while true; do echo `date`; done'
    logging:
      options:
        max-size: "50k"
        max-file: "10"EOF
**$** docker-compose up -d
Recreating tmp_first_1 ... done
**$** docker ps -f 'name=tmp_first_1'
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
**2ea63dd32607**   alpine    "sh -c 'while true; …"   2 minutes ago   Up 2 minutes             tmp_first_1
**$ 
$** du -h /var/lib/docker/containers/2ea63dd32607*/*log*
**20K** /var/lib/docker/containers/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60-json.log
**52K** /var/lib/docker/containers/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60-json.log.1
**52K** /var/lib/docker/containers/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60-json.log.2
**52K** /var/lib/docker/containers/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60-json.log.3
**52K** /var/lib/docker/containers/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60-json.log.4
**52K** /var/lib/docker/containers/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60-json.log.5
**52K** /var/lib/docker/containers/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60-json.log.6
**52K** /var/lib/docker/containers/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60-json.log.7
**52K** /var/lib/docker/containers/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60-json.log.8
**52K** /var/lib/docker/containers/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60/2ea63dd32607ea5c5b3238ed4da2233a049c41422cfff96b6a2e45c818f96f60-json.log.9*
```

*如您所见，在本例中，我们可以确认 Docker 最多只保存 10 个日志文件，它们的大小从未超过我们期望的 50k 字节(= 52K)。*

## *pid*

*根据请求，您可以让容器与主机共享 PID 地址空间。这是实现这一目标的选择:*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: ps -a
    pid: "host" second:
    image: alpine
    command: ps -a
EOF
**$** docker-compose up 
Creating network "mediumcom_default" with the default driver
Creating mediumcom_second_1 ... done
Creating mediumcom_first_1  ... done
Attaching to mediumcom_second_1, mediumcom_first_1
second_1  | PID   USER     TIME  COMMAND
**second_1**  |     1 root      0:00 ps -a
first_1   | PID   USER     TIME  COMMAND
**first_1**   |     1 root      0:07 /sbin/init
first_1   |     2 root      0:00 [kthreadd]
first_1   |     3 root      0:00 [rcu_gp]
first_1   |     4 root      0:00 [rcu_par_gp]
first_1   |     6 root      0:00 [kworker/0:0H-ev]
first_1   |     8 root      0:00 [mm_percpu_wq]
first_1   |     9 root      0:00 [rcu_tasks_rude_]
first_1   |    10 root      0:00 [rcu_tasks_trace]
first_1   |    11 root      0:22 [ksoftirqd/0]
first_1   |    12 root      0:10 [rcu_sched]
first_1   |    13 root      0:07 [migration/0]
first_1   |    15 root      0:00 [cpuhp/0]
first_1   |    16 root      0:00 [cpuhp/1]
...*
```

*请注意，这意味着您可以从容器内部管理(包括终止)主机进程，所以使用该选项时要小心。*

## *港口*

*在最流行的选项中，这允许您从容器内部向您的主机发布端口(除非您的容器已经在“主机”模式下运行)。*

*假设我要发布:
-端口 3000 到随机端口
-端口范围 3005 到 3010
-端口 80 到仅本地主机上的端口 80
-端口 443 到所有主机接口上的端口 443
-端口 3011 到主机上的端口 9876，但仅用于 UDP*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: sleep 10
    ports:
     - 3000
     - 3005-3010
     - 127.0.0.1:80:80
     - 443:443
     - 9876:3011/udpEOF
**$** docker-compose up -d
Removing orphan container "mediumcom_second_1"
Starting mediumcom_first_1 ... done
**$** docker ps --format '{{.Ports}}'
127.0.0.1:80->80/tcp, 0.0.0.0:443->443/tcp, 0.0.0.0:61290->3000/tcp, 0.0.0.0:61292->3005/tcp, 0.0.0.0:61293->3006/tcp, 0.0.0.0:61294->3007/tcp, 0.0.0.0:61295->3008/tcp, 0.0.0.0:61296->3009/tcp, 0.0.0.0:61291->3010/tcp, 0.0.0.0:9876->3011/udp*
```

## *轮廓*

*“配置文件”是一个很酷的控制机制，通过它您可以在您的撰写文件中分离职责。假设我们希望我们的组合文件中的一些容器只在非常特定的设置下运行，或者按需运行(也称为“概要”)。你可以这样做:*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: nginx second:
    image: alpine
    command: wget [http://first](http://first')
    profiles: ["debug"]EOF
**$** docker-compose up -d
Creating network "mediumcom_default" with the default driver
Creating **mediumcom_first_1** ... done**$** # see? only the nginx container was started. Let's enable "debug"
**$** docker-compose **--profile debug** up 
mediumcom_first_1 is up-to-date
Creating **mediumcom_second_1** ... done
Attaching to mediumcom_first_1, mediumcom_second_1
**second_1  | Connecting to first (172.26.0.2:80)
second_1  | saving to 'index.html'
second_1  | index.html           100% |********************************|   615  0:00:00 ETA
second_1  | 'index.html' saved**
first_1   | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
first_1   | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
first_1   | /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
first_1   | 10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
first_1   | 10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
first_1   | /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
first_1   | /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
first_1   | /docker-entrypoint.sh: Configuration complete; ready for start up
first_1   | 2022/02/01 15:07:21 [notice] 1#1: using the "epoll" event method
first_1   | 2022/02/01 15:07:21 [notice] 1#1: nginx/1.21.5
first_1   | 2022/02/01 15:07:21 [notice] 1#1: built by gcc 10.2.1 20210110 (Debian 10.2.1-6) 
first_1   | 2022/02/01 15:07:21 [notice] 1#1: OS: Linux 5.10.76-linuxkit
first_1   | 2022/02/01 15:07:21 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
first_1   | 2022/02/01 15:07:21 [notice] 1#1: start worker processes
first_1   | 2022/02/01 15:07:21 [notice] 1#1: start worker process 31
first_1   | 2022/02/01 15:07:21 [notice] 1#1: start worker process 32
**first_1   | 172.26.0.3 - - [01/Feb/2022:15:07:34 +0000] "GET / HTTP/1.1" 200 615 "-" "Wget" "-"**
**mediumcom_second_1 exited with code 0***
```

## *重新开始*

*这就是你如何为你的容器定义重启策略(从`no`、`always`、`unless-stopped`和`on-failure`中)。*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    restart: on-failure
    command: sleep 4 second:
    image: alpine
    command: echo Hi `date`
    restart: alwaysEOF
**$** docker-compose up 
Creating network "mediumcom_default" with the default driver
Creating mediumcom_first_1  ... done
Creating mediumcom_second_1 ... done
Attaching to mediumcom_first_1, mediumcom_second_1
second_1  | Hi Tue Feb 1 17:21:11 CET 2022
**mediumcom_second_1 exited with code 0** second_1  | Hi Tue Feb 1 17:21:11 CET 2022
second_1  | Hi Tue Feb 1 17:21:11 CET 2022
**mediumcom_second_1 exited with code 0** second_1  | Hi Tue Feb 1 17:21:11 CET 2022
second_1  | Hi Tue Feb 1 17:21:11 CET 2022
second_1  | Hi Tue Feb 1 17:21:11 CET 2022
**mediumcom_second_1 exited with code 0** second_1  | Hi Tue Feb 1 17:21:11 CET 2022
second_1  | Hi Tue Feb 1 17:21:11 CET 2022
second_1  | Hi Tue Feb 1 17:21:11 CET 2022
second_1  | Hi Tue Feb 1 17:21:11 CET 2022
mediumcom_first_1 exited with code 0*
```

## *安全性 _opt*

*该选项允许您控制容器的 SELinux 标签。*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: sleep 30
    security_opt:
     - label:user:USER second:
    image: alpine
    command: sleep 30EOF
**$** docker inspect mediumcom_first_1 --format '{{ .Id }}: SecurityOpt={{ .HostConfig.SecurityOpt }}'
2808e4b2ac6a6870b372ecf06da5ce0217efdd6d14df16cef4b332e1d4fb9b6a: SecurityOpt=**[label:user:USER]**
**$** 
**$** docker inspect mediumcom_second_1 --format '{{ .Id }}: SecurityOpt={{ .HostConfig.SecurityOpt }}'
ed4c417b27257cc9f793d5d3dad94672074859dba90effddd0cde49f12dd4115: SecurityOpt=**<no value>***
```

## *停止宽限期*

*你可能会发现，默认情况下，如果在收到一个 SIGTERM 后 10 秒内没有自然停止，Docker force 就会杀死一个容器(例如，在你试图用`docker stop`停止一个容器后)。这个时间段称为停止宽限期，您可以对其进行配置。*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: sh -c 'while true; do sleep 5 & done' second:
    image: alpine
    command: sh -c 'while true; do sleep 5 & done'
    stop_grace_period: 1sEOF
**$** docker-compose up -d
Recreating mediumcom_first_1  ... done
Recreating mediumcom_second_1 ... done
**$** time docker stop mediumcom_first_1
mediumcom_first_1**real 0m10.847s**
user 0m0.205s
sys 0m0.092s
**$** time docker stop mediumcom_second_1
mediumcom_second_1**real 0m1.883s** user 0m0.192s
sys 0m0.105s*
```

## *停止信号*

*从上面的选项中，您已经知道 SIGTERM 是停止 Docker 容器的默认信号。但是您可以改变这一点，这样`docker stop`就可以向您的容器应用程序发送您自己喜欢的信号:*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: sleep 60
    stop_signal: SIGUSR1EOF
**$** docker-compose up -d
Creating network "mediumcom_default" with the default driver
Creating mediumcom_first_1 ... done
**$** docker inspect mediumcom_first_1 --format '{{.Config.StopSignal}}'**SIGUSR1***
```

## *sysctls*

*该选项允许您在运行时为容器配置命名空间内核参数。*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: sleep 60
    sysctls:
      net.core.somaxconn: 1024
      net.ipv4.tcp_syncookies: 0EOF
**$** docker-compose up -d
Creating network "mediumcom_default" with the default driver
Creating mediumcom_first_1 ... done
**$** docker inspect mediumcom_first_1 --format '{{json .HostConfig.Sysctls}}'
**{"net.core.somaxconn":"1024","net.ipv4.tcp_syncookies":"0"}***
```

## *ulimits*

*允许您覆盖容器的默认 ulimits。*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: sleep 60
    ulimits:
      nproc: 65535
      nofile:
        soft: 20000
        hard: 40000EOF
**$** docker-compose up -d
Recreating mediumcom_first_1 ... done
**$** docker inspect mediumcom_first_1 --format '{{json .HostConfig.Ulimits}}'
**[{"Name":"nproc","Hard":65535,"Soft":65535},{"Name":"nofile","Hard":40000,"Soft":20000}]***
```

## *用户模式*

*不涉及太多细节，以防万一您已经在 Docker 守护进程中启用了`user`名称空间，这个选项让您只需执行`userns_mode: "host"`就可以禁用单个容器的用户名称空间。*

## *用户*

*您希望您的容器应用程序在您的容器中作为特定用户运行吗？*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: whoami
    user: nobodyEOF
**$** docker-compose up 
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | **nobody**
mediumcom_first_1 exited with code 0*
```

## *工作目录*

*你从档案里知道这个。有了它，你可以为你的容器重新定义登陆路径(PWD ),并可能影响你的入口点和命令的行为，以防它们没有使用绝对路径。*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: pwd
    working_dir: /my/custom/medium/dirEOF
**$** docker-compose up 
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | **/my/custom/medium/dir**
mediumcom_first_1 exited with code 0*
```

## *域名*

*如您所知，您的容器有一个主机名和一个域名。当**不是**在主机模式下运行时，主机名将等于容器的 ID，域名，正如所料，将是 none。如果您想设置它，请执行以下操作:*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: hostname -f
    domainname: cristovaocordeiro.medium.comEOF
**$** docker-compose up 
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | 6e900c697939.**cristovaocordeiro.medium.com**
mediumcom_first_1 exited with code 0*
```

## *主机名*

*显然，如果您可以设置一个域名，那么您也可以为您的容器设置一个主机名，这样它就可以采用默认的“难看的”ID 形式😜*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: hostname -f
    hostname: test-containerEOF
**$** docker-compose up 
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | **test-container**
mediumcom_first_1 exited with code 0*
```

## *工业程序控制（ industrial process control 的缩写）*

*您可以管理容器的进程间通信(用于性能调优)。通过让您为容器配置 IPC 名称空间，该选项让您可以做到这一点。*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: sleep 30
    ipc: shareableEOF
**$** docker inspect mediumcom_first_1 --format '{{json .HostConfig.IpcMode}}'
"**shareable**"*
```

## *mac 地址*

*你甚至可以为你的容器定义你自己的 mac 地址。*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: ifconfig eth0
    mac_address: 00:ab:cd:12:34:56EOF
**$** docker-compose up 
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | eth0      Link encap:Ethernet  HWaddr **00:AB:CD:12:34:56**  
first_1  |           inet addr:172.29.0.2  Bcast:172.29.255.255  Mask:255.255.0.0
first_1  |           UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
first_1  |           RX packets:2 errors:0 dropped:0 overruns:0 frame:0
first_1  |           TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
first_1  |           collisions:0 txqueuelen:0 
first_1  |           RX bytes:200 (200.0 B)  TX bytes:0 (0.0 B)
first_1  | 
mediumcom_first_1 exited with code 0*
```

## *特许的*

*我们已经在上面几次解决了“特权”容器的状态。总之如果你确定(！！)并且您想简单地向您的容器授予每一种可能的能力(以及对主机设备和更多设备的潜在访问权)，那么您可以使用这个选项。*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: sh -c 'ls /dev/ | wc -l' second:
    image: alpine
    command: sh -c 'ls /dev/ | wc -l'
    privileged: trueEOF
**$** docker-compose up 
Recreating mediumcom_first_1  ... done
Recreating mediumcom_second_1 ... done
Attaching to mediumcom_first_1, mediumcom_second_1
first_1   | 15
**second_1  | 150**
mediumcom_first_1 exited with code 0
mediumcom_second_1 exited with code 0*
```

## *只读*

*如您所知，运行容器时，会在容器的只读图像层之上创建一个可写层。但是，您也可以将这个可写层变为只读。*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: touch /tmp/test second:
    image: alpine
    command: touch /tmp/test
    read_only: trueEOF
**$** docker-compose up 
Recreating mediumcom_first_1  ... done
Recreating mediumcom_second_1 ... done
Attaching to mediumcom_first_1, mediumcom_second_1
**second_1  | touch: /tmp/test: Read-only file system**
mediumcom_first_1 exited with code 0
mediumcom_second_1 exited with **code 1***
```

## *shm_size*

*您可以指定容器可以使用的共享内存量。*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    command: df -h /dev/shm
    shm_size: 123MEOF
**$** docker-compose up 
Recreating mediumcom_first_1 ... done
Attaching to mediumcom_first_1
first_1  | Filesystem                Size      Used Available Use% Mounted on
first_1  | **shm**                     **123.0M**         0    123.0M   0% **/dev/shm**
mediumcom_first_1 exited with code 0*
```

## *标准输入 _ 打开*

*这相当于`docker run`中的`-i`。使用它，您可以为您的容器打开 STDIN，在您的主机和容器之间创建一个交互式会话。*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    stdin_open: trueEOF
**$** docker-compose up -d
Recreating mediumcom_first_1 ... done
**$** docker attach mediumcom_first_1 
echo Hello, I am sending commands to the container
Hello, I am sending commands to the container
exit*
```

*如您所见，我们正在与容器进行交互，但主机和容器之间并没有真正的终端会话…如果您想简化它，它基本上只是一个管道。*

## *电传打字机*

*这相当于`docker run`中的`-t`。它让你为你的容器分配一个伪 tty，当与`stdin_open`结合时，它会很有用。*

```
***$** cat >**docker-compose.yml** <<EOF
version: "3"services:
  first:
    image: alpine
    stdin_open: true
    tty: trueEOF
**$** docker-compose up -d
Recreating mediumcom_first_1 ... done
**$** docker attach mediumcom_first_1 
/ # echo now this is a real terminal
now this is a real terminal
/ # sleep 5
^C
/ # echo $?
130
/ # exit*
```

*现在我们有了一个真正的终端和一个交互式会话，可以在容器内部做我们想做的任何事情。*

*那是一篇大文章。很抱歉。从好的方面来说，你不需要在标签页之间跳来跳去，在多篇文章中搜索，只是为了找出不同的合成文件选项的作用*

*请注意，我已经忽略了一些选项(那些特定于 Swarm 模式中的服务的选项)和特殊的结构技巧，如变量替换和扩展字段。这些是可选的，我们可以在另一篇文章中介绍它们。*

*我希望这能提高你的 Docker 写作技巧*

```
***Want to Connect?**I hope this article was useful, and serves as a good complement to my [Docker course for beginners](https://www.udemy.com/course/docker-crash-course-learn-from-experience-for-beginners/?couponCode=B6FB29C107CC97ACFEFF) (get 75% discount from this link).*
```