# WebAssembly、Docker 和 Alpine 入门

> 原文：<https://betterprogramming.pub/getting-started-with-wasm-webassembly-docker-alpine-b8652f82ce5e>

## 今天就开始体验 WASM

![](img/f64b1efd1f578755ef7d800828f515b0.png)

卢卡斯·范·奥尔特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

[Go](https://golang.org/) 内置的支持使得 WASM (WebAssembly)变得简单可行。让我们第一次尝试使用 WASM。在您的系统上运行之前，需要几个包。

首先创建一个. Docker 文件，并在您的主机上安装 Docker:

在这里，我们有一个[高山](https://alpinelinux.org/)为基础的围棋。dockerfile 文件

这是一个最小尺寸的 Docker 镜像，已经安装了 Go。

我们将创建一个名为`/app`的工作目录，然后使用带有参数`--no-cache`的命令`apk add`安装`git`。

这将为我们的 Alpine 实例做好准备，以允许稍后在流程中引入 Git 模块。

# 编译 WASM 文件的必要步骤

注意我们的运行命令:

```
RUN cp "$(go env GOROOT)/misc/wasm/wasm_exec.js" .
```

该命令从 Go 包中复制一个必需的文件，该文件将用于编译到 WASM。接下来，让我们看看编写的服务器命令。

# 在 Docker 中提供 WASM 文件

这基本上是一个 Go 运行时，我们将把它用作静态服务器:

```
RUN go get -u github.com/shurcooL/goexec
```

这个包需要安装，并且是我们的服务器命令所必需的:

```
CMD ["goexec", "http.ListenAndServe(`:8080`, http.FileServer(http.Dir(`.`)))"] 
```

# 在 Docker 中构建 WASM 文件

现在我们有了。dockerfile 文件设置完毕，让我们从这个文件开始构建:

```
docker build -t wasm .
```

让我们最终开始使用用 Go 编写的 WASM 应用程序。

创建一个名为`main.go`的文件。这将是我们的主要应用程序(又名前端),稍后将被写入 WASM 文件:

我们在 Go 语法中定义了一些函数(vanilla-js-looking)。

WASM 是一种用于浏览器的本机汇编语言。

请记住，某些 Go 功能可能不兼容。由于 web 浏览器的沙箱特性，一些试图与主机交互的功能可能无法工作。

现在，让我们创建一个`index.html`文件，它可以触发这些函数并运行我们的程序。

这里，我们有一个函数调用一个`onClick` HTML 按钮元素。

在我们编译`.wasm`文件之前，这个`add()`函数还不存在。

让我们回顾一下 docker 文件中编译发生的位置:

```
RUN GOOS=js GOARCH=wasm go build -o main.wasm
```

在我们加载服务器之前,`RUN`命令将编译成`.wasm`。

`GOOS=js`和`GOARCH=wasm`需要建立一个`.wasm`文件。

当使用带有`-o`参数的`go build`来设置输出文件名时，我们的 Go 应用程序将被编译成 WASM。

让我们使用参数`-d`在分离模式下运行一个容器:

```
docker run -d -p 8180:8080 wasm
```

如你所见，我在端口`8080`前使用端口`8180:`。

这是从主机到容器的重新映射。

您可以将其更改为主机上可用的任何端口号。甚至`80`都可以工作。

# 结论

我希望你发现你第一次使用 WASM 和我一样令人兴奋。作为一名独立的开发人员继续学习和成长，你可以用 WebAssembly 制作出很棒的工具、产品和体验。

编码快乐！