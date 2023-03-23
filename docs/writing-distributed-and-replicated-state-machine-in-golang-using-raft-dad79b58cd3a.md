# 用 Raft 编写 Golang 分布式复制状态机

> 原文：<https://betterprogramming.pub/writing-distributed-and-replicated-state-machine-in-golang-using-raft-dad79b58cd3a>

## 嗯，你可能会听到很多关于“分布式系统”或者“raft”本身的说法。但是，你可能想知道如何使用它。

![](img/bc4cf715db29685249afebb15d62d36a.png)

约书亚·厄尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本教程中，我们将讨论如何使用 Golang 和 [Raft 库](https://github.com/shaj13/raft)处理以集群模式运行的 kv 数据库。本教程更侧重于代码和使用 raft 集群，而不是讨论 raft 算法。

# **什么是筏？**

Raft 是一种协议，通过它，节点集群可以维护一个复制的状态机。

状态机通过使用复制日志保持同步。然而，Raft 协议的细节超出了本教程的范围，关于 Raft 的更多细节，参见[寻找可理解的共识算法](https://raft.github.io/raft.pdf)

## **戈朗木筏的实施**

Raft 算法是在寻找一种可以理解的共识算法。不幸的是，大多数 go 库都需要对它们的实现和 API 有深入的了解。

我们将在本教程中使用的 raft 库是为了与可理解性 raft 原则保持一致而诞生的，它的唯一目的是为极简、简单、干净和惯用的 API 提供共识。

`Etcd Raft`是生产中使用最广泛的 Raft 库。但是，它遵循极简主义的设计哲学，只实现核心 raft 算法，留下了缺口和歧义。

因此，`[shaj13/raft](https://github.com/shaj13/raft)`库使用`etcd raft`作为其核心，而不是重新发明轮子。

这就是你如何受益于`etcd raft`的强大和稳定性，以及一个可理解的 API。事实上，它让你专注于构建优秀的软件。

# 创建我们的项目

我们将从创建我们的项目开始。

```
mkdir raft && cd raft && go mod init raft && touch raft.go
```

这将创建一个名为`raft`的新目录，并用`go.mod`初始化项目。

# 我们的第一行代码

在我们写任何代码之前，我们需要写一些强制性的代码来使程序运行。

```
package main
import (
  "log"
)
func main() {
  log.Println("Raft !!")
}
```

# 创建我们的流程命令行

我们将删除打印出`Raft!!`的行，添加标志包并初始化它。

```
package main 
import "flag"
func main() {
   addr := flag.String("raft", "", "raft server address")
   join := flag.String("join", "", "join cluster address")
   api := flag.String("api", "", "api server address")
   state := flag.String("state_dir", "", "raft state directory (WAL, Snapshots)")
   flag.Parse() 
}
```

# 创建我们的 kv 数据库

我们将实现一个名为`stateMachine`的结构来定义`kv`数据库，该数据库读取并应用一个键值，同时获取`database`快照并恢复它。

# 创建我们的端点

我们将添加 gorilla mux 包，并在主函数中初始化路由器。

```
router := mux.NewRouter()
```

现在，我们将建立 API 的端点，我们将在 main 函数中创建所有端点，每个端点都需要一个函数来处理请求，我们将在 main 函数下定义这些端点。

```
router.HandleFunc("/", http.HandlerFunc(save)).Methods("PUT", "POST")
router.HandleFunc("/{key}", http.HandlerFunc(get)).Methods("GET")
router.HandleFunc("/mgmt/nodes", http.HandlerFunc(nodes)).Methods("GET")
router.HandleFunc("/mgmt/nodes/{id}", http.HandlerFunc(removeNode)).Methods("DELETE")
```

# 路线处理程序

现在我们只需要定义处理请求的函数。
在开始之前，我们需要声明两个变量来允许 routes 访问数据。

*   节点表示 raft 过程
*   FSM 代表 raft 进程状态机

# 创建我们的 raft 节点和服务器

在主函数中和路由器下面，我们需要声明我们的 raft 节点和`gRPC`服务器，以允许当前 raft 节点与其他 raft 节点通信。

您的文件现在应该看起来像这样:

# 测试我们的代码

## 建筑筏形集群

```
go mod tidy && go build raft.go 
```

## 运行单节点筏

首先，启动 raft 的单成员集群:

```
./raft -state_dir=$TMPDIR/1 -raft :8080 -api :9090
```

每个 raft 进程维护一个 raft 实例和一个键值服务器。

raft 服务器地址(`-raft`)、状态目录(`-state_dir`)和 http 键值服务器地址(`-api`)通过命令行传递。

接下来，将一个值(“`medium`”)存储到一个键(“`hello`”):

```
curl -L http://127.0.0.1:9090/ -X PUT -d '{"Key":"hello", "Value":"medium"}'
```

最后，检索存储的密钥:

```
curl -L [http://127.0.0.1:9090/hello](http://127.0.0.1:9090/hello)
```

## 运行本地集群

让我们带来两个额外的 raft 实例。

```
./raft -state_dir $TMPDIR/2 -raft :8081 -api :9091 -join :8080
./raft -state_dir $TMPDIR/3 -raft :8082 -api :9092 -join :8080
```

现在可以向集群的任何成员写入一个键-值对，同样也可以从任何成员检索它。

## 容错

要测试集群恢复，请将值“`foo`”写入关键字“T10”:

```
curl -L [http://127.0.0.1:9090/](http://127.0.0.1:9090/) -X PUT -d '{"Key":"foo", "Value":"foo"}'
```

接下来，停止一个节点(9092)并将值替换为“bar ”,以检查集群可用性:

```
curl -L [http://127.0.0.1:9090/](http://127.0.0.1:9090/) -X PUT -d '{"Key":"foo", "Value":"bar"}'
curl -L [http://127.0.0.1:9090/foo](http://127.0.0.1:9090/foo)
```

最后，重新启动该节点，并验证它是否使用更新后的值“`bar`”进行了恢复:

```
curl -L [http://127.0.0.1:9092/foo](http://127.0.0.1:9092/foo)
```

## 集群重构

可以从正在运行的群集中添加、删除或更新节点。让我们使用对 REST API 的请求来删除节点。

首先，列出所有 raft 集群节点，并获取节点 id。

```
curl -L [http://127.0.0.1:9090/mgmt/nodes](http://127.0.0.1:9090/mgmt/nodes)
```

然后使用删除请求删除节点:

```
curl -L http://127.0.0.1:9090/<ID> -X DELETE
```

一旦群集处理了此请求，节点将自行关闭。

关于`raft`库的更多信息可以在 [GitHub](https://github.com/shaj13/raft) 和 [GoDoc](https://pkg.go.dev/github.com/shaj13/raft) 上找到。非常感谢您的阅读。我希望这篇文章对你有所帮助。