# 常见的围棋陷阱

> 原文：<https://betterprogramming.pub/common-go-pitfalls-a92197cd96d2>

## 一些常见错误以及如何诊断和修复它们

![](img/ca6a91c4d965bf01fcbdf81769bb5197.png)

编写简单、可靠的 Go 代码时避免陷阱

我喜欢 Golang 有几个原因:

*   这是一种超级小语种(它只有[25 个保留关键字](https://golang.org/ref/spec#Keywords)
*   交叉编译轻而易举
*   本地支持创建可靠的 HTTP(s)服务器

从本质上来说，它是一种枯燥的语言，这可能就是为什么像 [Docker 和 Kubernetes](https://github.com/avelino/awesome-go) 这样的优秀项目是用它编写的，而像 [Cloudflare](https://blog.cloudflare.com/tag/go/) 这样对性能和弹性要求高的公司也在使用它。

尽管它很容易使用，但 Go 确实需要注意细节。如果你不按预期使用语言，它可能会崩溃。很难诊断和修复错误。

以下是我在产品代码库中，在代码评审过程中，以及我自己所犯的一些常见错误。希望这能让你在遇到同样的问题时更容易诊断出来。

# HTTP 超时

第一期有一整篇关于它的文章，但它仍然值得一提，因为最佳解决方案可能需要一些思考。它必须使用默认的 HTTP 客户端发出 HTTP 请求。

为了说明这个问题，这里有一个向 google.com 发出`GET`请求的基本示例:

```
package mainimport (
    "io/ioutil"
    "log"
    "net/http"
)var (
    c = &http.Client{}
)func main() {
    req, err := http.NewRequest("GET", "google.com", nil)
    if err != nil {
        log.Fatal(err)
    } res, err := c.Do(req)
    if err != nil {
        log.Fatal(err)
    }
    defer res.Body.Close()
    b, _ := ioutil.ReadAll(res.Body)
    ...
} 
```

正如不要使用 Go 的默认 HTTP 客户端文章中指出的，默认客户端实际上没有超时。这意味着根据服务器的不同，代码可能会无限期挂起，或者直到应用程序重新启动。

那么解决这个问题的最好方法是什么呢？

虽然总是用合理的超时来定义 HTTP 客户端是一个好主意，但是`&http.Client{Timeout: time.Minute}`您也可以考虑为您的请求附加一个上下文，以获得一些额外的好处:

*   取消正在进行的请求的能力
*   您可以针对特定请求调整超时

第二个好处尤其重要，因为如果您知道有几个请求需要很长时间，比如说一个多小时，您不希望每个请求都等待一个小时才超时。

在上面的例子中，添加一个上下文看起来像这样:

```
ctx, cancel := context.WithTimeout(context.Background(), time.Minute)
defer cancel()req = req.WithContext(ctx)res, err := c.Do(req)
...
```

如果超过了分配的时间，对`c.Do`的调用将导致一个`DeadlineExceeded`错误，使得处理或重试变得容易。有关上下文包的更多信息，请查看[文档](https://golang.org/pkg/context/)。

# 数据库连接

在我参与的几乎每个 Go 项目中，我都遇到过数据库连接问题。我认为对于新的地鼠来说，难以理解的是,`sql.DB`对象是一个并发安全的连接池，而不是单个数据库连接。这意味着，如果您忘记将连接返回到池中，您很容易耗尽连接的数量，并且您的应用程序可能会陷入停顿。

例如，连接池既包含`Open`连接，也包含`Idle`连接，它们是通过以下方式配置的:

*   `[SetConnMaxLifetime](https://golang.org/pkg/database/sql/#DB.SetConnMaxLifetime)`:连接可以重复使用的最长时间
*   `[SetMaxIdleConns](https://golang.org/pkg/database/sql/#DB.SetMaxIdleConns)`:空闲连接池中的最大连接数
*   `[SetMaxOpenConns](https://golang.org/pkg/database/sql/#DB.SetMaxOpenConns)`:数据库的最大打开连接数

请注意，即使将最大打开连接数配置为 200，应用程序仍会耗尽数据库将接受的打开连接数，从而导致必须关闭或重新启动。您需要检查数据库设置或与任何有权限的人协调，以确保您正确地设置了这些限制。

如果不配置限制，您的应用程序可以很容易地使用数据库将接受的所有连接。

回到耗尽连接池。当查询数据库时，许多开发人员忘记关闭`*sql.Rows`对象。这导致达到最大连接数限制，并导致死锁或高延迟。以下代码片段展示了这一点:

```
package mainimport (
    "context"
    "database/sql"
    "fmt"
    "log"
)var (
    ctx context.Context
    db  *sql.DB
)func main() {
    age := 27
    ctx, cancel := context.WithTimeout(context.Background(), time.Minute)
    defer cancel() rows, err := db.QueryContext(ctx, "SELECT name FROM users WHERE age=?", age)
    if err != nil {
        log.Fatal(err)
    } for rows.Next() {
        var name string
        if err := rows.Scan(&name); err != nil {
            log.Fatal(err)
        }
        fmt.Println(name)
    }
    ...}
```

您会注意到，就像您可以向 HTTP 请求添加上下文一样，您也可以向数据库查询(或预准备语句的执行、ping 等)添加带有超时的上下文。)但这不是问题。

如上所述，我们需要关闭 rows 对象以防止进一步枚举，并将连接释放回连接池:

```
rows, err := db.QueryContext(ctx, "SELECT name FROM users WHERE age=?", age)
if err != nil {
    log.Fatal(err)
}
defer rows.Close()
```

如果您在函数和包之间传递开放的连接，这变得特别难以发现。

# Goroutine 或内存泄漏

这里我要介绍的最后一个常见错误是 Goroutine 泄漏。这些可能很难检测，但通常是由用户错误造成的。

这种情况在使用通道时经常发生。例如:

```
package main func main() {
    c := make(chan error)
    go func() {
        for err := range c {
            if err != nil {
                panic(err)
            }
        }
    }() c <- someFunc()
    ...}
```

如果我们不关闭通道 c 或者如果`someFunc()`没有返回错误，我们已经初始化的 Goroutine 将会挂起，直到程序终止。

我没有列举可能导致 Goleaks 的案例数量，而是使用了两种方法来检测和消除它们。

第一种方法是在测试中使用泄漏检测器，像[优步的 goleak 库](https://github.com/uber-go/goleak)。实际上是这样的:

```
func TestA(t *testing.T) {
    defer goleak.VerifyNone(t)
    // test logic here.
}
```

这将在 30 秒钟的宽限期(允许正常关闭)后验证在测试结束时没有意外的 Goroutines 运行。

另一种方法是在应用程序的一个运行实例上使用 [Go profiler](https://blog.golang.org/profiling-go-programs) ，查看活动 Goroutines 的数量。一种方法是添加`[net/http/pprof](https://golang.org/pkg/net/http/pprof/)` [库](https://golang.org/pkg/net/http/pprof/)并点击 Goroutine 概要文件。

您可以通过添加以下内容来启用它:

```
import _ "net/http/pprof"func someFunc() {
    go func() {
        log.Println(http.ListenAndServe("localhost:6060", nil)) }
}
```

这将启用端口 6060 上的`pprof`。对于特别严重的泄漏，您可以刷新并看到 goroutines 的数量增加。对于更细微的泄漏，通读概要文件并寻找函数在不应该的时候滞留的实例。个人资料页面将如下所示:

```
goroutine profile: total 39
2 @ 0x43cf10 0x44ca6b 0x980600 0x46b301
#	0x9805ff	database/sql.(*DB).connectionCleaner+0x36f	/usr/local/go/src/database/sql/sql.go:950

2 @ 0x43cf10 0x44ca6b 0x980b18 0x46b301
#	0x980b17	database/sql.(*DB).connectionOpener+0xe7	/usr/local/go/src/database/sql/sql.go:1052

2 @ 0x43cf10 0x44ca6b 0x980c4b 0x46b301
#	0x980c4a	database/sql.(*DB).connectionResetter+0xfa	/usr/local/go/src/database/sql/sql.go:1065...
```

如果您的应用程序处于空闲状态，并且您看到了大量的总 Goroutine，这是一个很好的迹象，表明有什么地方出错了。在确定了泄漏的位置之后，我仍然建议在测试中使用泄漏检测器来确保问题得到解决。

# 结论

希望了解和看到这些常见错误的一些例子将帮助您更快地识别和修复它们。显然还有许多其他常见的错误，例如:

*   竞赛条件
*   僵局
*   吞咽错误

这些可以通过类似的技术找到并修复，比如使用 [go race 检测器、](https://blog.golang.org/race-detector)编写测试，或者使用 go profiler。