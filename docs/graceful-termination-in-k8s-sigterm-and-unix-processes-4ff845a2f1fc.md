# 如何在基于 Unix 和基于云的系统中使用 SIGTERM 信号

> 原文：<https://betterprogramming.pub/graceful-termination-in-k8s-sigterm-and-unix-processes-4ff845a2f1fc>

## K8S 中的优美终止

![](img/a98edbef4e64ea996ba1090e3cd8f2d1.png)

照片由 [Growtika 开发商营销机构](https://unsplash.com/@growtika?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在本文中，我们将讨论`UNIX`过程信号，尤其是“SIGTERM”信号。我们将通过使用 Node、 [TypeScript](https://www.typescriptlang.org/) 、 [Docker](https://www.docker.com/) 和 [Kind](https://kind.sigs.k8s.io/) local cluster 的实际例子来介绍如何处理它们。

## UNIX 信号和 SIGTERM

基于 Unix 的操作系统(OS)有多个进程。操作系统使用软件[中断](https://en.wikipedia.org/wiki/Interrupt)(又名信号)作为与正在运行的进程通信的方式，这些信号表示某种事件已经发生，并且它们的意图和目的可能不同。

以下是我的机器上可用的信号列表:

```
$ kill -l
 1) SIGHUP  2) SIGINT  3) SIGQUIT  4) SIGILL
 5) SIGTRAP  6) SIGABRT  7) SIGEMT  8) SIGFPE
 9) SIGKILL 10) SIGBUS 11) SIGSEGV 12) SIGSYS
13) SIGPIPE 14) SIGALRM 15) SIGTERM 16) SIGURG
17) SIGSTOP 18) SIGTSTP 19) SIGCONT 20) SIGCHLD
21) SIGTTIN 22) SIGTTOU 23) SIGIO 24) SIGXCPU
25) SIGXFSZ 26) SIGVTALRM 27) SIGPROF 28) SIGWINCH
29) SIGINFO 30) SIGUSR1 31) SIGUSR2
```

有几个，但是我们要把重点放在`SIGTERM`。

`**S**IGTERM`信号是操作系统优雅地终止程序的一种方式。所谓优雅，是指程序在关闭前有时间进行最后的清理。根据应用程序的不同，清理任务会有所不同。有趣的是，Unix 进程可以阻塞和忽略`SIGTERM`。但是，如果我们想要高质量的流程/服务，我们需要按照它们的意图处理这些信号，否则我们的流程将被迫关闭。

## 作为 HTTP 服务器的 Unix 进程

为了进行演示，我们将使用 [TypeScript](https://www.typescriptlang.org/) 和 [hapi](https://hapi.dev/) 创建一个示例 HTTP 服务器。

让我们创建 [NPM](https://www.npmjs.com/) 项目，并按照提示操作:

```
$ npm init
…
```

安装依赖项:

```
$ npm install [@hapi/hapi](http://twitter.com/hapi/hapi) typesript [@types/node](http://twitter.com/types/node) [@types/node](http://twitter.com/types/node) [@types/hapi__hapi](http://twitter.com/types/hapi__hapi)
```

在这里，我们不会为开发和生产依赖的分离而烦恼。

创建一个本地文件，并将其命名为`index.ts`:

```
import { Server } from "@hapi/hapi";
function sleep(ms: number, value: string) {
 return new Promise((resolve) => setTimeout(() => resolve(value), ms));
}
export async function startServer(host: string, port: number): Promise<Server> {
 const server = new Server({ port, host });
server.route({
 method: "GET",
 path: "/work",
 handler: async () => sleep(10 * 1000, `done something for 10 seconds\n`),
 });
await server.start();
 console.log(`Server running at: ${server.info.uri}, PID: ${process.pid}`);
 return server;
}
startServer("0.0.0.0", 3000);
```

这里，我们在 0.0.0.0 主机和 3000 端口上启动本地服务器。我们还配置了一个端点 GET `/work`,它模拟了一些需要时间来计算的东西——在我们的例子中是 10 秒。在实际场景中，这可能是执行某种数据库查询所需的时间。

> 我喜欢 node，它太酷了，你可以用 27 行代码定义一个服务器，你实际上需要的更少，因为我增加了睡眠功能等等。但是，是的，很棒。

让我们运行我们的服务器:

```
 $ ./node_modules/.bin/ts-node ./index.ts
Server running at: [http://0.0.0.0:3000](http://0.0.0.0:3000), PID: 16544 
```

一切都好。我们有一台服务器在运行！

现在让我们向端点发送`GET /work` HTTP 请求。选择你喜欢的网络工具，我将使用 [CURL](https://curl.se/) :

```
 $ curl [http://0.0.0.0:3000/work](http://0.0.0.0:3000/work)
done something for 10 seconds 
```

到目前为止一切顺利。但是，如果服务器突然被终止，或者换句话说，不再存在，那么 HTTP 请求会发生什么呢？我们在客户端会得到什么样的响应？我们会得到什么吗？在服务器死亡之后，什么将服务于请求？很多问题！让我们试一试:

向服务器发送另一个请求:

```
$ curl [http://0.0.0.0:3000/work](http://0.0.0.0:3000/work)
done something for 10 seconds
```

但是这一次，当服务器正在做 10 秒钟的模拟工作时——让我们`KILL`它！

```
$ kill -15 19346
```

> 注意:我使用的是从 index.ts 输出中获得的进程 Id (PID )!你当地的 PID 我们肯定会做些别的事情！如果不是，那就是命中注定，一定要给我发邮件。

有人可能会问，为什么不按下`CTRL+C`键来终止服务器的 shell 进程呢？嗯，那会发出一个`SIGINT`信号，但是我们想要`SIGTERM`！

好的，那么当服务器关闭时，我们的客户端连接发生了什么？事情就是这样:

```
$ curl [http://0.0.0.0:3000/work](http://0.0.0.0:3000/work)
curl: (52) Empty reply from server
```

它得到了一个空的答复，这意味着它没有得到任何信息，什么也没有。让我们在 CURL 命令中添加 **-v** (代表 verbose)标志来查看更多信息。

```
$ curl -v [http://0.0.0.0:3000/work](http://0.0.0.0:3000/work)
* Trying 0.0.0.0:3000…
* Connected to 0.0.0.0 (127.0.0.1) port 3000 (#0)
> GET /work HTTP/1.1
> Host: 0.0.0.0:3000
> User-Agent: curl/7.84.0
> Accept: */*
> 
* Empty reply from server
* Closing connection 0
curl: (52) Empty reply from server
```

看起来服务器突然关闭了连接👀。这可不好。如果这种情况发生在生产中的服务器客户机上，会怎么样呢？如果您在某个云编排工具中运行您的应用程序，容器被关闭可能并不罕见。

我用的是 [Kubernetes (K8S)](https://kubernetes.io/) ，所以我要谈谈这个。K8S 就像是云的操作系统。K8S 可能会随意终止正在运行的 [Pods](https://kubernetes.io/docs/concepts/workloads/pods/) (又名容器)，毕竟，K8S 的全部目的是协调分布式系统。如果其中一个 pod 请求了太多的资源，或者如果应用程序正在被缩减，容器可能会从 K8S 收到一个信号，表示该走了。

这就是为什么我们有信号的概念来优雅地终止我们的容器。

## 服务优雅终止

在上一节中，我们讨论了我们的进程或容器如何接收不同的信号。

当 K8S 需要终止一个 Pod 时，会发送`SIGTERM`。这样，我们的服务不仅仅是被切断了资源，相反，它将有一些时间来执行终结任务。

让我们在节点服务器中实现这个逻辑:

```
import { Server } from "@hapi/hapi";
function sleep(ms: number, value: string) {
 return new Promise((resolve) => setTimeout(() => resolve(value), ms));
}
export async function startServer(host: string, port: number): Promise<Server> {
 const server = new Server({ port, host });
server.route({
 method: "GET",
 path: "/work",
 handler: async () => sleep(10 * 1000, `done something for 10 seconds\n`),
 });
process.on("SIGTERM", async function () {
 console.log(`Received SIGTERM`);
 await server.stop({ timeout: 10 * 1000 });
 console.log(`Server stopped.`);
 process.exit(0);
 });
 await server.start();
 console.log(`Server running at: ${server.info.uri}, PID: ${process.pid}`);
 return server;
}
startServer("0.0.0.0", 3000);
```

我们向`SIGTERM`事件添加了一个监听器。当这样的事件发生时，我们会停止服务器，但不仅仅是终止它。在`SIGTERM`到达的时间和指定的‘超时’参数之间，我们的服务器将拒绝接受任何新的请求，并将终止正在进行的请求。

我们可以检验这种说法！再次启动服务器:

```
$ ts-node ./index.ts
Server running at: [http://0.0.0.0:3000](http://0.0.0.0:3000), PID: 67116
```

运行`CURL`请求并终止服务器:

```
$ curl -v [http://0.0.0.0:3000/work](http://0.0.0.0:3000/work)
```

我们应该看到请求已经完成，并且响应被发送回客户机——不再出现“(52)来自服务器的空回复”错误。

如果我们试图在`SIGTERM`和实际关机之间的时间段内访问服务器，我们将得到:

```
$ curl -v [http://0.0.0.0:3000/work](http://0.0.0.0:3000/work)
* Trying 0.0.0.0:3000…
* connect to 0.0.0.0 port 3000 failed: Connection refused
* Failed to connect to 0.0.0.0 port 3000 after 3 ms: Connection refused
* Closing connection 0
curl: (7) Failed to connect to 0.0.0.0 port 3000 after 3 ms: Connection refused 
```

如果根本没有服务器，我们会得到同样的错误！

但这就是我们为节点进程的优雅终止所需要做的一切！

## 向 K8S 传送信号

正如我们提到的，每当 K8S Pod 被终止时，它都会被发送一个`SIGTERM`信号。

与我们对本地进程使用`kill`命令的方式类似，我们可以使用`delete`命令终止 pod:

```
$ kubectl delete pod my-pod-qgldf
```

当 K8S 出于某种原因决定终止 Pod 时，`SIGTERM`信号将被发送给它，然后发送给 Docker 容器，最终发送给正在运行的进程。

你不必相信我，试试看。

## K8S 示例应用程序

我们将重用服务器代码，但是将其容器化并部署在本地 K8S 集群上。

我们的文档:

```
FROM node:19-bullseye
WORKDIR /app
COPY index.ts package.json package-lock.json /app/
RUN npm install
EXPOSE 3000
ENTRYPOINT [“/app/node_modules/.bin/ts-node”, “index.ts”]
```

构建 docker 容器:

```
$ docker build -t poc -f ./Dockerfile .
```

首先，让我们运行它，看看是否能获得`SIGTERM`相关日志:

```
$ docker run -t poc:latest
```

获取码头工人 id:

```
$ docker ps
CONTAINER ID IMAGE 
86b0a46730ba poc:latest 
```

停下来:

```
$ docker stop 86b0a46730ba 
```

我们应该看到 docker run 命令被终止，以及我们的 docker 容器，其输出与我们对流程的输出相同。

```
$ docker run -t poc:latest
Server running at: [http://0.0.0.0:3000](http://0.0.0.0:3000), PID: 1
Received SIGTERM
Server stopped.
```

现在我们来做几个 k8！

首先，我们需要将容器映像加载到集群中:

```
 $ kind load docker-image poc:latest — name my-cluster 
```

K8S 部署清单:

```
apiVersion: v1
kind: Namespace
metadata:
 name: poc-namespace
 — -
apiVersion: apps/v1
kind: Deployment
metadata:
 name: poc-deployment
 namespace: poc-namespace
spec:
 selector:
 matchLabels:
 app: poc-app
 template:
 metadata:
 labels:
 app: poc-app
 spec:
 containers:
 — name: poc
 image: poc:latest
 ports:
 — containerPort: 3000
```

部署到我们的集群:

```
$ kubectl apply -f ./deployment.yaml 
namespace/poc-namespace created
deployment.apps/poc-deployment created
```

如果您流式传输 pod 日志:

```
$ k logs -f poc-deployment-bf749f576-wfmv9 
```

然后删除 pod:

```
$ kubectl delete pod poc-deployment-bf749f576-wfmv9
```

应该得到相同的结果:

```
$ k logs -f poc-deployment-bf749f576-wfmv9
Server running at: [http://0.0.0.0:3000](http://0.0.0.0:3000), PID: 1
Received SIGTERM
Server stopped.
```

就这样结束了！

# 摘要

在本文中，我们介绍了`SIGTERM`信号，它们是如何在基于 Unix 和基于云的系统中使用的，并展示了处理这些信号的实际应用及其潜在的 it 影响。

`SIGTERM`处理对于[可水平扩展的](https://en.wikipedia.org/wiki/Scalability#Horizontal_(scale_out)_and_vertical_scaling_(scale_up))云应用来说至关重要。它允许应用程序按需伸缩，而不会影响客户端的稳定性。