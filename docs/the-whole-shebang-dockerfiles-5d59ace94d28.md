# 整个事情:Dockerfiles

> 原文：<https://betterprogramming.pub/the-whole-shebang-dockerfiles-5d59ace94d28>

## 在实践中学习，这个实用的教程通过例子解释了所有的 docker 文件说明

![](img/7edcc1ea1a121d68db56e95e435ba0b6.png)

照片由[纳扎尔·穆罕默德](https://unsplash.com/@nazahery?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

假设你开始了你的码头之旅。你想做的第一件事是:“构建我自己的容器应用”。

在部署容器应用程序之前，您需要将该应用程序打包成一个可执行的软件单元，也就是 Docker 映像。

为此，您需要一种 Docker 可以在构建时读取的配方文件。这些配方文件被称为 Dockerfiles！

这些包含由`docker build`执行的指令，并遵循一个你可以在[https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/)找到的文档化的语法。

为了帮助你构建 docker 文件，这里有所有文档中支持的说明，但是有真实的例子。

## 从

它为您的构建奠定了基础。此后的每条指令(从到另一个**)都将在该基础上运行。**

示例:

```
**$** echo '
# This is the Dockerfile
FROM ubuntu
# End of the Dockerfile' | docker build -t test-image -**$** docker run test-image cat /etc/os-release
```

输出将是:

```
NAME=”Ubuntu”
VERSION=”20.04.2 LTS (Focal Fossa)”
…
```

这意味着最终的容器非常像一个临时的 Ubuntu 文件系统。

## 奔跑

它允许您在构建时在前一层之上执行命令，并将结果提交给下一层和最终图像。

示例:

```
**$** echo '
# This is the Dockerfile
FROM ubuntu# shell syntax 
RUN ls /
# exec syntax
RUN ["touch", "/foo"]
# End of the Dockerfile' | docker build -t test-image -
```

输出显示以下内容:

```
Step 2/3 : RUN ls /
 — -> Running in 8dc25e5916f1
bin
boot
dev
etc
home
lib
lib32
…
```

所以这意味着:

1.  在映像构建期间，每个**运行**指令的输出被打印到您的终端，
2.  无论你用 **RUN** 做了什么改变，都会保存在最终的 Docker 镜像中，即`docker run test-image ls /foo`会找到`/foo`文件。

## 入口点

顾名思义，它允许您为容器执行定义一个默认的入口点。如果你愿意，这个入口点可以通过 **CMD** 参数获得额外的参数。如果没有入口点，你的容器将只依靠 **CMD** ，如果没有 **CMD** ，那么容器将不知道运行什么。

例如:

```
**$** echo '
# This is the Dockerfile
FROM ubuntu# unset Ubuntu's default CMD
CMD []
# End of the Dockerfile' | docker build -t test-image -
```

检查这个图像，你会发现**入口点**和 **CMD** 都是`null`:

```
**$** docker image inspect test-image --format 'CMD: {{.Config.Cmd}} , ENTRYPOINT: {{.Config.Entrypoint}}'
CMD: [] , ENTRYPOINT: []
```

所以如果你试着运行它，你会得到:

```
**$** docker run iii
docker: Error response from daemon: No command specified.
See 'docker run --help'.
```

所以让我们定义一下**入口点:**

```
**$** echo '
# This is the Dockerfile
FROM ubuntu# unset Ubuntu's default CMD
CMD []# and set the entrypoint
ENTRYPOINT ["/bin/ls"]
# End of the Dockerfile' | docker build -t test-image -
```

然后你会得到:

```
**$** docker run test-image
bin
boot
dev
etc
home
lib
lib32
lib64
libx32
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
**$** # and you can also give args to the default entrypoint
**$** docker run test-image -l
total 48
lrwxrwxrwx   1 root root    7 Oct  6 16:47 bin -> usr/bin
drwxr-xr-x   2 root root 4096 Apr 15  2020 boot
drwxr-xr-x   5 root root  340 Dec 22 18:17 dev
drwxr-xr-x   1 root root 4096 Dec 22 18:17 etc
drwxr-xr-x   2 root root 4096 Apr 15  2020 home
lrwxrwxrwx   1 root root    7 Oct  6 16:47 lib -> usr/lib
lrwxrwxrwx   1 root root    9 Oct  6 16:47 lib32 -> usr/lib32
lrwxrwxrwx   1 root root    9 Oct  6 16:47 lib64 -> usr/lib64
lrwxrwxrwx   1 root root   10 Oct  6 16:47 libx32 -> usr/libx32
drwxr-xr-x   2 root root 4096 Oct  6 16:47 media
drwxr-xr-x   2 root root 4096 Oct  6 16:47 mnt
drwxr-xr-x   2 root root 4096 Oct  6 16:47 opt
dr-xr-xr-x 172 root root    0 Dec 22 18:17 proc
drwx------   2 root root 4096 Oct  6 16:58 root
drwxr-xr-x   5 root root 4096 Oct  6 16:58 run
lrwxrwxrwx   1 root root    8 Oct  6 16:47 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 Oct  6 16:47 srv
dr-xr-xr-x  13 root root    0 Dec 22 18:17 sys
drwxrwxrwt   2 root root 4096 Oct  6 16:58 tmp
drwxr-xr-x  13 root root 4096 Oct  6 16:47 usr
drwxr-xr-x  11 root root 4096 Oct  6 16:58 var
```

注意:和`RUN`一样，`ENTRYPOINT`可以写成*shell**和 *exec* 两种形式。*

## *煤矿管理局*

*简而言之，`CMD` 让你为一个正在执行的容器提供默认值。这是什么意思？嗯，`CMD`与`ENTRYPOINT` 并排上场。事实上，许多开发人员通常会将这两者混为一谈。帮助你弄清楚这两者如何协同工作的最佳信息可以在这里找到。此表向您展示了定义这些参数的不同形式是如何组合在一起的，以及它们的最终形式是什么样的。*

*在我们这个简单的例子中，举个例子:*

```
***$** echo '
# This is the Dockerfile
FROM ubuntu# set the entrypoint
ENTRYPOINT ["/bin/ls"]# set a default arg for ls
CMD ["-l"]
# End of the Dockerfile' | docker build -t test-image -*
```

*如您所料，您将获得与上一节相同的输出，但是在启动容器时不必指定命令:*

```
***$** docker run test-image
total 48
lrwxrwxrwx   1 root root    7 Oct  6 16:47 bin -> usr/bin
drwxr-xr-x   2 root root 4096 Apr 15  2020 boot
drwxr-xr-x   5 root root  340 Dec 22 18:17 dev
drwxr-xr-x   1 root root 4096 Dec 22 18:17 etc
drwxr-xr-x   2 root root 4096 Apr 15  2020 home
lrwxrwxrwx   1 root root    7 Oct  6 16:47 lib -> usr/lib
lrwxrwxrwx   1 root root    9 Oct  6 16:47 lib32 -> usr/lib32
lrwxrwxrwx   1 root root    9 Oct  6 16:47 lib64 -> usr/lib64
lrwxrwxrwx   1 root root   10 Oct  6 16:47 libx32 -> usr/libx32
drwxr-xr-x   2 root root 4096 Oct  6 16:47 media
drwxr-xr-x   2 root root 4096 Oct  6 16:47 mnt
drwxr-xr-x   2 root root 4096 Oct  6 16:47 opt
dr-xr-xr-x 172 root root    0 Dec 22 18:17 proc
drwx------   2 root root 4096 Oct  6 16:58 root
drwxr-xr-x   5 root root 4096 Oct  6 16:58 run
lrwxrwxrwx   1 root root    8 Oct  6 16:47 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 Oct  6 16:47 srv
dr-xr-xr-x  13 root root    0 Dec 22 18:17 sys
drwxrwxrwt   2 root root 4096 Oct  6 16:58 tmp
drwxr-xr-x  13 root root 4096 Oct  6 16:47 usr
drwxr-xr-x  11 root root 4096 Oct  6 16:58 var*
```

*请注意，如果您在启动容器时传递了一个命令，它将覆盖 docker 文件中的默认 **CMD** 。*

## *标签*

*这只是元数据。将您的 Docker 图像标记为非常有用，这将为其最终用户提供关于该图像的附加信息。例如，假设您想要构建一个 Docker 映像，用户可以从中获得关于其维护者、支持电子邮件、文档页面、各自的 git 提交以及可能的友好消息的信息。*

*你应该这样做:*

```
***$** echo '
# This is the Dockerfile
FROM ubuntu# set the labels
LABEL maintainer="cristovaocordeiro"
LABEL "support contact"="[cristovaocordeiro@medium.com](mailto:cristovaocordeiro@medium.com)"
LABEL docs="[https://medium.com/@cristovaocordeiro](https://medium.com/@cristovaocordeiro)" commit="xyz"
LABEL message="I hope you enjoy this tutorial. \
Make sure you subscribe for more content like this"# End of the Dockerfile' | docker build -t test-image -*
```

*然后，每个用户都将能够访问这些元数据，而无需运行映像，方法是:*

```
***$** docker image inspect test-image --format '{{json .Config.Labels}}' | jq
{
  "commit": "xyz",
  "docs": "[https://medium.com/@cristovaocordeiro](https://medium.com/@cristovaocordeiro)",
  "maintainer": "cristovaocordeiro",
  "message": "I hope you enjoy this tutorial Make sure you subscribe for more content like this",
  "support contact": "[cristovaocordeiro@medium.com](mailto:cristovaocordeiro@medium.com)"
}*
```

## *揭露*

*这通常被误解为可以用来从容器中自动发布端口。*不是！尽管如此，区别还是很明显的。使用`EXPOSE`，您可以通知 Docker 您的 Docker 映像已经标记了哪些端口用于发布。那不一样。**

*所以让我们假设如下:*

```
***$** echo '
# This is the Dockerfile
FROM ubuntu# expose port
EXPOSE 80# End of the Dockerfile' | docker build -t test-image -*
```

*现在运行它，你会看到事实上没有任何端口被发布，除非**，**你使用`docker run`的`-P`选项！*

```
***$** docker run -d test-image sleep 10
**$** docker ps
CONTAINER ID   IMAGE        COMMAND       CREATED        STATUS        PORTS     NAMES
8f7af69c15dc   test-image   "sleep 10"   1 second ago   Up 1 second   **80/tcp**    pensive_goodall
**$
$** # now publish it
**$** docker run -d **-P** test-image sleep 10
CONTAINER ID   IMAGE        COMMAND       CREATED         STATUS        PORTS                   NAMES
2509758c86b3   test-image   "sleep 10"   2 seconds ago   Up 1 second   **0.0.0.0:55000->80/tcp**   flamboyant_einstein*
```

## *包封/包围（动词 envelop 的简写）*

*这个很简单。顾名思义，它允许您在 Docker 图像中设置环境变量。*

```
***$** echo '
# This is the Dockerfile
FROM ubuntu# set env vars
ENV A=1
ENV B="two"
ENV C three
ENV D=4 E="five"# End of the Dockerfile' | docker build -t test-image -*
```

*然后您会发现这些 env 变量是您从这个 Docker 映像创建的所有容器的固有部分(除非您使用`env`选项和`docker run`命令覆盖它们):*

```
***$** docker run test-image env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=df7db5ae7bb1
A=1
B=two
C=three
D=4
E=five
HOME=/root*
```

## *复制*

*它允许您将文件或目录从构建环境复制到 Docker 映像上。*

*所以让我们假设在我们的工作环境中有几个文件(`a.txt`和`b.txt`)和一个文件夹(`c`):*

```
***$** echo '
# This is the Dockerfile
FROM ubuntu# copy files and folder 
COPY a.txt /tmp/
COPY --chown=1000:1001 b.txt /tmp/
COPY ["c", "/tmp/c"]# End of the Dockerfile' > Dockerfile
**$** 
**$** docker build -t test-image .*
```

*注意:使用`**COPY**` 或`ADD`时，不能从`stding`开始构建图像，因为需要构建上下文。*

*因此，在这种情况下，我们可以通过以下方式仔细检查复制的文件:*

```
***$** docker run test-image ls -Rl /tmp
/tmp:
total 4
-rw-r--r-- 1 root root    0 Dec 23 18:52 a.txt
-rw-r--r-- 1 **1000 1001**    0 Dec 23 18:52 b.txt
drwxr-xr-x 2 root root 4096 Dec 23 19:06 **c**/tmp/c:
total 0
-rw-r--r-- 1 root root 0 Dec 23 18:55 c.txt*
```

## *注意缺陷障碍 (Attention Deficit Disorder)*

*很像`COPY`。事实上，它做得差不多，但是它也允许您从本地工作环境之外的其他来源复制文件:*

*   *URL:将资源从 URL 直接下载到 Docker 映像中的目标路径*
*   *tarballs:获取一个压缩的 tarball，并将其自动解压缩到 Docker 映像中的目标目录*

*示例(假设我们的本地环境中有一个 tarball `foo.tar`，包含上一个示例中的文件和文件夹):*

```
***$** echo '
# This is the Dockerfile
FROM ubuntu# add files from URL and tarball
ADD foo.tar /tmp
ADD [https://miro.medium.com/fit/c/262/262/1*Z22Wr8H1voxC2Ze25qwJ4Q.png](https://miro.medium.com/fit/c/262/262/1*Z22Wr8H1voxC2Ze25qwJ4Q.png) /tmp# End of the Dockerfile' > Dockerfile
**$** 
**$** docker build -t test-image .*
```

*然后你会有:*

```
***$** docker run test-image ls -Rl /tmp
/tmp:
total 16
-rw------- 1 root root    6696 Jan  1  1970 1*Z22Wr8H1voxC2Ze25qwJ4Q.png
-rw-r--r-- 1  501 dialout 2560 Dec 23 19:14 a.txt
-rw-r--r-- 1  501 dialout    0 Dec 23 18:52 b.txt
drwxr-xr-x 2  501 dialout 4096 Dec 23 19:00 c/tmp/c:
total 0
-rw-r--r-- 1 501 dialout 0 Dec 23 18:55 c.txt*
```

## *卷*

*这让您可以告诉 Docker，每当从您的 Docker 映像启动一个容器时，就自动创建一个挂载点。假设我希望从我的映像启动所有容器，在 Docker 卷中保存一个文件`foo.txt`:*

```
***$** echo '
# This is the Dockerfile
FROM ubuntuRUN echo "subscribe for more content like this" > /tmp/foo.txt# create a mountpoint on /tmp
VOLUME /tmp# End of the Dockerfile' > Dockerfile
**$** 
**$** docker build -t test-image .*
```

*在从这个映像运行容器之前，我可以仔细检查系统中是否没有卷，但是一旦我运行了容器，我将有一个新的卷(由 Docker 随机命名)，其中包含我的`foo.txt`文件:*

```
***$** docker volume ls
DRIVER    VOLUME NAME
**$
$** docker run test-image **$
$** docker volume ls
DRIVER    VOLUME NAME
local     28d64c77b5bb56b0fab699fd291ff71a776be6b5e893a3c482d962dc7894d465 **$
$** docker volume inspect 28d64c77b5bb56b0fab699fd291ff71a776be6b5e893a3c482d962dc7894d465
[
    {
        "CreatedAt": "2021-12-23T19:55:20Z",
        "Driver": "local",
        "Labels": null,
        **"Mountpoint": "/var/lib/docker/volumes/28d64c77b5bb56b0fab699fd291ff71a776be6b5e893a3c482d962dc7894d465/_data",**
        "Name": "28d64c77b5bb56b0fab699fd291ff71a776be6b5e893a3c482d962dc7894d465",
        "Options": null,
        "Scope": "local"
    }
]**$
$** ls -l /var/lib/docker/volumes/28d64c77b5bb56b0fab699fd291ff71a776be6b5e893a3c482d962dc7894d465/_datatotal 4
-rw-r--r-- 1 root root 37 Dec 23 19:38 foo.txt*
```

## *用户*

*不，这不会让您创建新用户。相反，这让您**为 docker 文件中的所有后续指令设置**用户，并在从映像启动指令时设置容器。*

*示例:*

```
***$** echo '
# This is the Dockerfile
FROM ubuntu# need to create the user first
RUN adduser --disabled-password --gecos "" medium# set the user
USER mediumRUN whoami# End of the Dockerfile' | docker build -t test-image -*
```

*在这个构建的输出中，您会发现`whoami`的结果如下:*

```
*...
Step 4/4 : RUN whoami
 ---> Running in b9c344af5969
**medium**
Removing intermediate container b9c344af5969
...*
```

*如果您从它运行一个容器，您会发现相同的默认用户:*

```
***$** docker run test-image whoami
medium*
```

## *工作方向*

*这可能是 docker 文件中最简单的指令。它只是让您为 docker 文件和未来容器的所有后续指令设置工作目录。您可以在 docker 文件中多次使用它(非常像一个`cd <path>`)，注意无论最后出现的`WORKDIR`是什么，都将在运行的容器中生效。*

*示例:*

```
***$** echo '
# This is the Dockerfile
FROM ubuntu# let's change the working dir a few times
RUN pwd
WORKDIR /my/dir
RUN pwdWORKDIR subdir
RUN pwdWORKDIR /newdir
RUN pwd# End of the Dockerfile' | docker build -t test-image -*
```

*通过这次构建，您会发现`pwd`正在发生变化:*

```
*...
Step 2/8 : RUN pwd
 ---> Running in 41d16b2a91ea
/
Removing intermediate container 41d16b2a91ea
 ---> b2aee049b0a5
Step 3/8 : WORKDIR /my/dir
 ---> Running in 9c7d0e17c797
Removing intermediate container 9c7d0e17c797
 ---> 34371fd7f7f7
Step 4/8 : RUN pwd
 ---> Running in 7b7ed0a84e9f
**/my/dir**
Removing intermediate container 7b7ed0a84e9f
 ---> 306f967c5ba4
Step 5/8 : WORKDIR subdir
 ---> Running in 2a3915cb09e9
Removing intermediate container 2a3915cb09e9
 ---> 25e00401902a
Step 6/8 : RUN pwd
 ---> Running in d1e5de3c1842
**/my/dir/subdir**
Removing intermediate container d1e5de3c1842
 ---> c35828117d72
Step 7/8 : WORKDIR /newdir
 ---> Running in e4b450689a85
Removing intermediate container e4b450689a85
 ---> f36a27bed479
Step 8/8 : RUN pwd
 ---> Running in 6e6c61b1419d
**/newdir** Removing intermediate container 6e6c61b1419d
...*
```

*当运行一个容器时，您也会在`/newdir`找到您的工作目录:*

```
***$** docker run test-image pwd
/newdir*
```

## *银*

*这条有用的指令允许您设置运行`docker build`命令时 Dockerfile 期望您提供的编译时参数。如果您需要参数化您的构件，您可以这样做:*

```
***$** echo '
# This is the Dockerfile
FROM ubuntuRUN echo $local_var# only now this arg becomes available
ARG local_var=default_local_valueRUN echo $local_var# End of the Dockerfile' > Dockerfile*
```

*现在，当构建这个图像时，我们可以随意定义`local_var`,如果我们不这样做，它将采用默认值:*

```
***$** docker build -t test-image .
Sending build context to Docker daemon  1.336MB
Step 1/4 : FROM ubuntu
 ---> ba6acccedd29
Step 2/4 : RUN echo $local_var
 ---> Running in 6162ed110103Removing intermediate container 6162ed110103
 ---> d0c3f0bdc3f0
Step 3/4 : ARG local_var=default_local_value
 ---> Running in c037666f9bb1
Removing intermediate container c037666f9bb1
 ---> 1d3922646297
Step 4/4 : RUN echo $local_var
 ---> Running in 3512775af26f
**default_local_value**
Removing intermediate container 3512775af26f
 ---> b20b82d78c89
Successfully built b20b82d78c89
Successfully tagged test-image:latest
**$ 
$** docker build -t test-image --build-arg local_var=FOO --no-cache .
Sending build context to Docker daemon  1.336MB
Step 1/4 : FROM ubuntu
 ---> ba6acccedd29
Step 2/4 : RUN echo $local_var
 ---> Running in a389271fe6efRemoving intermediate container a389271fe6ef
 ---> a811d17ee724
Step 3/4 : ARG local_var=default_local_value
 ---> Running in a02ee27d7862
Removing intermediate container a02ee27d7862
 ---> e430886a587a
Step 4/4 : RUN echo $local_var
 ---> Running in fb459bebe516
**FOO**
Removing intermediate container fb459bebe516
 ---> 65f3b5a18911
Successfully built 65f3b5a18911
Successfully tagged test-image:latest*
```

## *ONBUILD*

*这种指导可能永远都不需要，这取决于你的图像是为哪些目标受众制作的。*

*简而言之，这允许您定义一个指令，当您的或某人使用您的映像作为不同构建的基础映像时，就执行该指令。*

*所以让我们假设下面的 Dockerfile:*

```
***$** echo '
# This is the Dockerfile
FROM ubuntu# for future child builds, run this
ONBUILD RUN echo "this image is being built from [@cristovaocordeiro](http://twitter.com/cristovaocordeiro)'s tutorial"# End of the Dockerfile' | docker build -t test-image -*
```

*然后，对于这个`test-image`成为基础的所有图像，您将得到:*

```
***$** echo 'FROM test-image' | docker build - 
Sending build context to Docker daemon  2.048kB
Step 1/1 : FROM test-image
# Executing 1 build trigger
 ---> Running in 7ed28561ef91
**this image is being built from** [**@cristovaocordeiro**](http://twitter.com/cristovaocordeiro)**'s tutorial
Removing intermediate container 7ed28561ef91**
 ---> 318be0653ae4
Successfully built 318be0653ae4*
```

## *停止信号*

*简单明了:它让您重新定义将导致容器退出的系统调用。默认情况下，所有容器都设置为`SIGTERM`。但是您可以做以下事情来使您的容器仅在收到`SIGKILL`时退出:*

```
***$** echo '
# This is the Dockerfile
FROM ubuntu# redefine exit system call
STOPSIGNAL SIGKILL# End of the Dockerfile' | docker build -t test-image -*
```

*要确认:*

```
***$** docker inspect test-image --format '{{.Config.StopSignal}}'
SIGKILL*
```

## *健康检查*

*健康检查是有用的机制，通过它您可以定义自己的探测器来验证您的容器是否健康。Docker 将获取这些探测器的输出，并将所述信息添加到容器的状态中。*

*假设我们相信我们的容器是健康的，当且仅当路径`/tmp_custom`存在！此外，让我们还使我们的探测器在 1s 后启动，每秒运行一次(`--interval`)，3s 后超时(`--timeout`)，如果探测器在容器生命周期的前 2s 失败，不认为容器不健康(`--start-period`)，并重试探测器命令 3 次(`--retries`):*

```
***$** echo '
# This is the Dockerfile
FROM ubuntu# container are healthy only if
HEALTHCHECK --interval=1s --timeout=3s --start-period=2s --retries=3 CMD [ -d /tmp_custom ]# End of the Dockerfile' | docker build -t test-image -*
```

*让我们运行一下，看看会发生什么:*

```
***$** docker run -d test-image sleep 20
588bdfb7a633a465859510d6850b6b78326d15ca8b642a768465ebc2ef37edfd
**$
$** docker ps # immediately after starting it
CONTAINER ID   IMAGE        COMMAND      CREATED         STATUS                           PORTS     NAMES
588bdfb7a633   test-image   "sleep 20"   2 seconds ago   Up 1 second (health: **starting**)             nervous_booth
**$
$** docker ps # a few seconds later
CONTAINER ID   IMAGE        COMMAND      CREATED         STATUS                     PORTS     NAMES
588bdfb7a633   test-image   "sleep 20"   5 seconds ago   Up 4 seconds (**unhealthy**)             nervous_booth*
```

*所以容器是不健康的，导致`tmp_custom`文件夹似乎不存在。检查容器将为您提供更多关于探头故障的信息:*

```
*docker inspect nervous_booth --format '{{json .State.Health}}' | jq
{
  "Status": "unhealthy",
  "FailingStreak": 17,
  "Log": [
    {
      "Start": "2021-12-23T20:49:18.8034984Z",
      "End": "2021-12-23T20:49:18.9115695Z",
      "ExitCode": 1,
      "Output": ""
    },
    {
      "Start": "2021-12-23T20:49:19.9204635Z",
      "End": "2021-12-23T20:49:20.0230435Z",
      "ExitCode": 1,
      "Output": ""
    },
    {
      "Start": "2021-12-23T20:49:21.034769Z",
      "End": "2021-12-23T20:49:21.1171521Z",
      "ExitCode": 1,
      "Output": ""
    },
    {
      "Start": "2021-12-23T20:49:22.1307374Z",
      "End": "2021-12-23T20:49:22.2147348Z",
      "ExitCode": 1,
      "Output": ""
    },
    {
      "Start": "2021-12-23T20:49:23.2238803Z",
      "End": "2021-12-23T20:49:23.3248102Z",
      "ExitCode": 1,
      "Output": ""
    }
  ]
}*
```

*现在，让我们确保我们的容器是健康的:*

```
***$** docker run -d test-image sh -c 'mkdir /tmp_custom && sleep 20'
327440b2758c40f4058f1ea86e9ee9e9874eed482d4f9b3f60ef5fc0ea2f101e
**$** docker ps
CONTAINER ID   IMAGE        COMMAND                  CREATED         STATUS                   PORTS     NAMES
327440b2758c   test-image   "sh -c 'mkdir /tmp_c…"   4 seconds ago   Up 4 seconds (**healthy**)             nervous_heisenberg*
```

## *壳*

*Linux 的默认 shell 是`/bin/sh`，但是您可以使用 **SHELL 来更改它。**对于可以有多个 shells 的 Windows 容器来说可能更有意义(比如`sh`、`cmd`、`powershell`)。例如，如果`powershell`应该是您的默认 shell，那么只需将`SHELL ["powershell", "-command"]`添加到您的 docker 文件中。*

*瞧啊！我认为这给了你一个来自 Docker 文档的总结版本，并带有一些实际的例子。你可以找到[我的另一篇文章](https://levelup.gitconnected.com/the-whole-docker-shebang-part-1-docker-build-470f0c265702)，里面有关于如何使用`docker build`及其所有选项的详尽解释。*

```
***Want to Connect With the Author?**I hope the above article is useful for you, and serves as a good complement to my [Docker course for beginners](https://www.udemy.com/course/docker-crash-course-learn-from-experience-for-beginners/).*
```