# Docker 提示:关于构建上下文的所有内容

> 原文：<https://betterprogramming.pub/docker-tips-about-the-build-context-dbc76505e178>

## 理解什么是构建上下文以及如何优化它

![](img/eca4dbd8749c87fcbb9f799791d60da8.png)

让我们来看看这个构建上下文的东西！

# 什么是构建上下文？

让我们从用于构建 Docker 映像的命令开始:

```
$ docker build [OPTIONS] PATH | URL | -
```

构建上下文是位于指定路径或 URL 的一组文件。这些文件在构建过程中被发送到 Docker 守护进程，这样它就可以在映像的文件系统中使用它们。

让我们来说明这一点。

## 使用路径

假设我在文件夹`/Users/luc/src/github.com/lucj/genx`中，其中包含了 genx 应用程序的源代码(简单的 [Go](https://golang.org/) 应用程序，生成虚拟数据)。

通常，我们使用如下所示的命令来构建图像，Dockerfile 位于项目文件夹的根目录下:

```
$ docker image build -t genx:1.0 .
```

在这种情况下，构建上下文是当前文件夹的内容(“.”指定为命令的最后一个元素)。

## 使用 URL

同一个 genx 项目在 [GitLab](http://gitlab.com/lucj/genx) 中管理，因此可以在本地构建引用 GitLab 存储库的映像:

```
$ docker image build -t genx:1.0 [git@gitlab.com](mailto:git@gitlab.com):lucj/genx.git
```

在这种情况下，构建上下文是`gitlab.com/lucj/genx`中的一组文件。

基本上，构建上下文至少包含将被复制到映像文件系统的应用程序代码，但是它通常包含映像中可能需要或不需要的许多其他内容。

# 我应该过滤构建上下文吗？

是的，我们最好确保构建上下文只包含它真正需要的文件和文件夹。

在一个由 Git 处理源代码的项目中，我们使用一个`.gitignore`文件来确保私有数据保存在本地，而不是发送到 GitHub/GitLab/BitBucket/etc。

同样的事情也适用于 Docker 映像的构建阶段，因为守护进程使用一个`.dockerignore`文件来过滤掉在构建上下文中不应该考虑的文件和文件夹。

# 如果我不使用. dockerignore 呢？

然后，您将向 Docker 守护进程发送大量它不需要的内容，这些内容可以复制到映像文件系统中。

## 构建上下文中的巨大文件

让我们考虑下面的 Dockerfile。它使用一个`nginx:1.14.0`基础映像，将当前文件夹的内容(*index.html*、 *css* 、 *js、img* )复制到[NGINX](https://www.nginx.com/)(*/usr/share/NGINX/html*)提供的默认位置。

```
FROM nginx:1.14.0
COPY . /usr/share/nginx/html/
```

当前文件夹的内容如下:

```
$ tree -ah
.
├── [  48]  Dockerfile
├── [  64]  css
├── [  64]  images
├── [  39]  index.html
├── [  64]  js
└── [1.8G]  ubuntu-18.04.1-desktop-amd64.iso
```

注意:你有没有注意到这个文件夹里的 Ubuntu 安装 ISO(误放在这里)？当构建映像时，这个巨大的家伙被发送到守护进程并复制到映像中…显然，这不是我们想要的。

![](img/ee4f826f48d631dbbb953abb586c9551.png)

当前文件夹内容被发送到守护程序(2G 被发送到守护程序并被复制到图像层中)

注意:我不建议遵循这个例子。Docker 守护进程一点也不喜欢它…*。(后来我甚至需要在 MacBook Pro 上重启它)。*

*但是，如果我们真的需要这个文件(只是为了好玩)，我们只需要创建一个`.dockerignore`文件并在其中添加它的名字。我们应该加上`*.iso`以防我们下载其他 iso。*

*这样做，我们可以确保没有 ISO 文件将被发送到守护进程。*

*好的，一个 2 GB 的 ISO 文件不太可能出现在当前的文件夹中，但是……该项目的 Git 历史呢，它也可能非常大。*

## *Git 历史*

*让我们删除 ISO 文件，开始用 Git 处理这个项目。*

```
***$ git init****$ tree -a**
.
├── .git
│   ├── HEAD
│   ├── branches
│   ├── config
│   ├── description
│   ├── hooks
│   │   ├── applypatch-msg.sample
│   │   ├── commit-msg.sample
│   │   ├── fsmonitor-watchman.sample
│   │   ├── post-update.sample
│   │   ├── pre-applypatch.sample
│   │   ├── pre-commit.sample
│   │   ├── pre-push.sample
│   │   ├── pre-rebase.sample
│   │   ├── pre-receive.sample
│   │   ├── prepare-commit-msg.sample
│   │   └── update.sample
│   ├── info
│   │   └── exclude
│   ├── objects
│   │   ├── info
│   │   └── pack
│   └── refs
│       ├── heads
│       └── tags
├── Dockerfile
├── css
├── images
├── index.html
└── js*
```

*然后我们创建图像:*

```
***$ docker image build -t www:1.0 .** Sending build context to Docker daemon 40.96kB
Step 1/2 : FROM nginx:1.14.0
 — -> 86898218889a
Step 2/2 : COPY . /usr/share/nginx/html/
 — -> 973188e5d7a3
Successfully built 973188e5d7a3
Successfully tagged www:1.0*
```

*看看里面有什么:*

```
***$ docker run -ti www:1.0 bash** root@5d91b258bdc3:/# cd /usr/share/nginx/html/
root@5d91b258bdc3:/usr/share/nginx/html# ls
50x.html Dockerfile css images index.html js
root@5d91b258bdc3:/usr/share/nginx/html# find .git/
.git/
.git/description
.git/config
.git/refs
.git/refs/tags
.git/refs/heads
.git/hooks
.git/hooks/applypatch-msg.sample
.git/hooks/pre-push.sample
.git/hooks/pre-rebase.sample
.git/hooks/prepare-commit-msg.sample
.git/hooks/post-update.sample
.git/hooks/pre-applypatch.sample
.git/hooks/update.sample
.git/hooks/fsmonitor-watchman.sample
.git/hooks/pre-commit.sample
.git/hooks/commit-msg.sample
.git/hooks/pre-receive.sample
.git/objects
.git/objects/pack
.git/objects/info
.git/HEAD
.git/branches
.git/info
.git/info/exclude
root@5d91b258bdc3:/usr/share/nginx/html#*
```

*那里有一个`.git`文件夹，包含了项目的版本历史，这可能是一个很大的文件夹。我们需要图像中的整个 Git 历史吗？不这么认为。然后我们应该创建一个`.dockerignore`，并在里面添加`.git`。*

## *资格证书*

*假设我在一个 [Node.js](https://nodejs.org/) 应用程序上工作，该应用程序需要连接到一个外部 [MongoDB](https://www.mongodb.com/) 数据库。*

*当应用程序部署在 swarm 或 Kubernetes 集群上时，建议通过 secret 提供连接字符串。(如果想了解更多，可能会对这篇文章感兴趣: [*从 env 变量到 Docker 秘密*](https://medium.com/lucjuggery/from-env-variables-to-docker-secrets-bc8802cacdfd) )。)*

*但是，在开发阶段，我们可能在当前文件夹中有这些凭证来测试应用程序。*

*在我们的项目中有一个`creds`文件夹怎么样？这很难看，但可以帮助，对不对？好吧，至少如果我们确保它不会到处复制。*

```
***$ tree**
.
├── .git
├── .gitignore
├── Dockerfile
├── app.js
├── creds
│ ├── mongo-preprod <-- mongodb://user:pass@prep.db.com:27017/mydb
│ ├── mongo-prod    <-- mongodb://user:pass@prod.db.com:27017/mydb
│ └── mongo-test    <-- mongodb://user:pass@test.db.com:27017/mydb
├── node_modules
│ ├── ...
└── package.json*
```

*我有下面的`.gitignore`文件*，*没问题，我的密码不会去 GitHub:*

```
*creds
node_modules*
```

*但是，我没有任何`.dockerignore` *。然后，我的凭证文件将被传送到映像中。**

```
*# Building the image
**$ docker build -t myapp:1.0 .**# Checking what's inside the image's filesystem
**$ docker run -ti myapp:1.0 sh** /app # ls
Dockerfile         app.js             creds              node_modules       package-lock.json  package.json
/app # find creds/
creds/
creds/mongo-test
creds/mongo-prod
creds/mongo-preprod
/app # cat creds/mongo-prod
mongodb://user:[pass@prod.db.com](mailto:pass@prod.db.com):27017/mydb
/app #*
```

*我们应该已经创建了一个`.dockerignore`文件，并在其中添加了我们不想公开的文件夹的名称(`creds`)。*

# *摘要*

*这些简单(且夸张)的例子表明，用一个`.dockerignore`文件过滤构建上下文的内容非常简单，也非常重要。你总是确保你的构建环境被正确处理吗？*