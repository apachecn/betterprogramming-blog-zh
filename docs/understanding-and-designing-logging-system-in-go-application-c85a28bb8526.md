# 如何在你的 Go 应用中设计一个基本的日志系统

> 原文：<https://betterprogramming.pub/understanding-and-designing-logging-system-in-go-application-c85a28bb8526>

## 更好的日志记录，更好的调试

![](img/18a34e8c69f1940da83b986db9721029.png)

照片由[威廉·艾文](https://unsplash.com/@firmbee?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/design?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

# **简介**

由于 Go 的可组合性、可伸缩性和并发性，以及其他非常有趣的特性，许多公司都将赌注压在了它身上。

简单的 web 服务和微服务应用程序需要一个经过深思熟虑的日志记录策略来帮助我们理解用户行为、定位错误并监控应用程序的性能。

在本文中，我们将探索跟踪、度量和日志记录。尤其是日志记录，将会得到最大的关注。

# 跟踪/监控/记录

## **什么是追踪？**

与日志记录相比，跟踪覆盖了更广泛、更连续的应用程序视图。实现跟踪的主要原因是为了能够监督应用程序的流程和进出应用程序的数据进展。因此，跟踪扮演的角色比日志记录更脏。

跟踪代表单个用户在整个应用程序中的旅程。这使得工程师能够识别瓶颈，并专注于改善和优化应用程序的性能。

运行时/跟踪为程序提供了为 Go 执行跟踪器生成跟踪的工具。当问题确实发生时，跟踪允许您查看您是如何到达那里的:哪个函数、函数的持续时间、传递的参数以及用户可以到达函数的多深。在理想的 Go 应用程序中，每个函数都支持跟踪。尽管 Stackdriver(可以通过 OpenCensus Go 包使用)等云技术可以帮助在您的应用程序中检测追踪过程，但由此产生的数据量可能会太多而无法排序。

**示例**:以下代码记录了执行跟踪中的四个区域，以跟踪卡布奇诺制作操作中连续步骤的持续时间(来自`trace`官方包):

```
trace.WithRegion(ctx, “makeCappuccino”, func() { // orderID allows to identify a specific order // among many cappuccino order region records. trace.Log(ctx, “orderID”, orderID) trace.WithRegion(ctx, “steamMilk”, steamMilk) trace.WithRegion(ctx, “extractCoffee”, extractCoffee) trace.WithRegion(ctx, “mixMilkCoffee”, mixMilkCoffee)})
```

## **什么是监控？**

术语*监控*可用于跟踪、记录或其他一些活动。然而，监视要具体得多:它检测应用程序，然后收集、汇总和分析指标，以便更好地理解应用程序的行为。

这种类型的监控主要是诊断性的——例如，当系统没有正常工作时，向开发人员发出警报。在理想情况下，成本不是问题，您可以检测和监控您的所有服务。在 Go 应用程序中实现监控的一个极好的工具是 OpenTracing(请查看它们)。

## **什么是日志记录？**

*日志*以一种战略性的集中格式为我们提供错误报告跟踪和相关数据。

可以为不同模式的小型和大型应用程序实现日志记录。日志文件服务器被认为是在应用程序中获取离散事件的第一站。离散事件可以是应用程序中的失败、错误或状态转换，可以指示事件的主要原因。

# 在 Go 应用程序中实现日志记录的工具

## **内置的‘日志’库**:

可以使用 Golang 内置的[日志库](https://golang.org/pkg/log/)，命名为`log`。这个包附带了一个默认的记录器，它可以编写一个标准错误并添加一个时间戳，而不需要进行配置。就我个人而言，当我需要从代码中获得快速反馈，而不必担心生成丰富的结构化日志时，我发现这个包对本地开发更有用。

```
package mainimport (“log” “errors” “fmt”) func add(f int64, s int64) (int64, error) {
    if f < 0 && s < 0 {
         return 0, errors.New(“cannot add signed numbers”)
    } return f * s, nil
} func main() {
    var first, second int64
    first, second = 10, 20 //first, second = -10, -20

    result, err := add(first, second)

    if err != nil{
       log.Print(err)
    } fmt.Println(result)
}
```

## **浩浩荡荡**[**Logrus**](https://github.com/sirupsen/logrus)**包**:

您可以将 Logrus 用于格式化日志。这是一个强烈推荐的包，因为它是为 JSON 等合适格式的结构化日志设计的。由于许多语言都支持 JSON 格式，这使得机器可以轻松解析 Golang 日志。下面的代码片段为我们提供了一个示例和预期结果:

```
package mainimport (log “github.com/sirupsen/logrus”)func main() { log.SetFormatter(&log.JSONFormatter{}) coreFields := log.Fields{ “gopher_lagos”: “staging-1”, “meetup”: “foo-app”, “session”: “1ce3f6v”, } log.WithFields(coreFields).WithFields(log.Fields{“product_type”: “ticket”, “quantity”: 3, “price”:100.0}).Info(“This is for Gophers Lagos Meetup”)}
```

上面的代码片段会导致如下结果:

```
*{“gopher_lagos”:”staging-1",”level”:”info”,”meetup”:”foo-app”,”msg”:”This is for Gophers Lagos Meetup”,”number”:3,”price”:100,”product_type”:”ticket”,”session”:”1ce3f6v”,”time”:”2009–11–10T23:00:00Z”}*
```

# **设计和存储 Golang 日志的最佳实践(Datadog，2019 年):**

## 避免为日志记录声明“goroutines”

避免创建自己的`goroutine`来处理写日志有两个原因。首先，它会导致并发问题，因为日志记录器的副本会试图访问同一个`io.Writer`。第二，日志库通常自己启动`goroutine`，在内部管理任何并发问题，启动自己的`goroutine`只会造成干扰。

## 将您的日志写入文件

当您从应用程序中收集日志时，建议您直接将它们写入本地文件(而不是直接写入远程文件)。您需要确保您的日志在本地始终可用，并且不会在网络中丢失。

## **实现标准日志接口**

在一个开发人员/工程师团队中，很可能会看到成员使用不同的属性名称来描述同一件事情。这可能会导致日志中不可解决的不一致，并使应该构成同一画面一部分的日志之间的关联变得非常激烈。例如，当以不同的方式处理上传时，两个开发人员可能会记录相同的错误—缺少客户端名称。实现标准日志接口是避免这种灾难的唯一方法。

让我们看一个实际的例子。下面的结构定义了我们希望记录的对象/事件。当然，我们可以向列表中添加更多事件:

```
type LogEvent struct {
    id int
    message string
}
```

这些变量将我们的日志消息存储为新事件:

```
var (
     invalidArgMessage = LogEvent{1, “Invalid arg: %s”} invalidArgValueMessage = LogEvent{2, “Invalid value for argument: %s: %v”} missingArgMessage = LogEvent{3, “Missing arg: %s”}
)
```

标准错误消息和 init():

```
// Standard “InvalidArg error messagefunc (m *MainLogger) InvalidArg(argumentName string){
    m.Errorf(invalidArgMessage.message, argumentName)
}// Standard “InvalidArgValue error message
func (m *MainLogger) InvalidArgValue(argumentName string, argumentValue string){
    m.Errorf(invalidArgValueMessage.message, argumentName, argumentValue)
}// “Standard “MissingArg error message”
func (m *MainLogger) MissingArg(argumentName string){
    m.Errorf(missingArgMessage.message, argumentName)
}// MainLogger enforces specific log message formats (we are using logrus, but you are free to use other libraries)type MainLogger struct {
   *logrus.Logger
}func init() {
    // Log as JSON instead of the default ASCII formatter.
    logrus.SetFormatter(&logrus.JSONFormatter{})
}
```

创建一个构造器/初始化器来返回我们的`MainLogger`结构:

```
func NewLogger() *MainLogger {
     f, err := os.OpenFile(“logrus.log”,      os.O_APPEND|os.O_CREATE|os.O_RDWR, 0666) if err != nil {
         fmt.Printf(“error opening file: %v”, err)
     } var baseLogger = logrus.New() var standardLogger = &MainLogger{baseLogger} standardLogger.Formatter = &logrus.JSONFormatter{} mw := io.MultiWriter(os.Stdout, f) standardLogger.SetOutput(mw) return standardLogger
}
```

我们已经完成了基本标准接口的设计，可以开始调用 Go 文件中的方法了，如下所示:

```
var standardLogger = logger.NewLogger()// call method when an argument is missing
standardLogger.MissingArg(“imgModel.ImageData”)// call method when an invalid argument is supplied
standardLogger.InvalidArg(“imgModel.ImageData”)
```

## **4。集中 Golang 日志**

一些 Go 应用程序部署在一个主机集群上。

当您需要检查/调查这些主机中的日志时，您将被迫 SSH 到其中的每一个主机。这无论如何都没有效率！

一个可扩展且高效的解决方案是将日志从本地文件传递到一个中央平台，然后您可以根据需要处理这些日志。

有许多方法可以实现这一点；一种方法是使用 [Golang syslog 包](https://golang.org/pkg/log/syslog/)将日志从整个基础设施转发到一个 syslog 服务器。另一种方法是使用日志管理解决方案。例如，Datadog 和 OpenTracing 可以跟踪您的日志文件，并将日志转发到中央平台进行处理和分析。

# 摘要

在本文中，我们学习了跟踪、监控和日志记录以及它们的各种用法。此外，我们深入探讨了日志记录，讨论了标准和第三方日志记录库。然后，我们研究了在 Go 应用程序中实现日志记录的最佳实践，并提供了代码示例。

我真的希望你喜欢读这篇文章，就像我喜欢写它一样。整个代码库可以在[这里](https://github.com/D-sense/logger-api-demo/blob/master/log/logger.go)找到。

让我们继续前进！