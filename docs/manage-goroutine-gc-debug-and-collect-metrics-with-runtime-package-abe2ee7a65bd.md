# 使用运行时包管理 Goroutine、GC、Debug 和收集指标

> 原文：<https://betterprogramming.pub/manage-goroutine-gc-debug-and-collect-metrics-with-runtime-package-abe2ee7a65bd>

## 何时以及如何使用 Go 运行时包

![](img/86747810a35f62fc75c3ac7fb2d5b9ff.png)

来自 https://unsplash.com/photos/QVD3Xht9txA 的

Go [runtime](https://pkg.go.dev/runtime) 包及其子包 [debug](https://pkg.go.dev/runtime/debug) 对于管理 metrics、debug、goroutine、GC 非常实用，也可以方便我们理解 Go 本身的实现。

本文旨在通过一些使用案例和示例代码来回答以下五个“如何做”。

*   如何使用运行时和运行时/调试包公开的环境变量
*   如何使用运行时和运行时/调试包公开的 Go 指标
*   如何用运行时和运行时/调试包管理 goroutines
*   如何用运行时和运行时/调试包操作 GC
*   如何使用运行时和运行时/调试包进行调试

运行时包提供了许多我们可以在开发中调用的内置环境变量。

# 包封/包围（动词 envelop 的简写）

最常见的环境变量有`GOARCH`、`GOOS`、`GOPATH`、`GOROOT,`，分别针对基于不同底层操作系统的不同实现。以下输出与我们在 CLI 中执行`go env`得到的结果相同。

```
fmt.Printf(“OS: %s, GOARCH: %s”, runtime.GOOS, runtime.GOARCH)
```

此外，还有一些与 GC、debug 和 trace 相关的环境变量。

*   `GOGC`设置垃圾收集的初始目标百分比，并根据剩余垃圾对象的比例决定是否触发 GC。默认情况下，`debug.SetGCPercent`函数在运行时修改这个百分比，并在`GOGC=100\. GOGC=off`时完全禁用垃圾收集。
*   `GODEBUG`控制运行时的调试变量，由一组`name=val`，逗号分隔的字符串，以及`allocfreetrace=1, clobberfree=1`等支持配置组成。更多信息请参考这张[清单](https://pkg.go.dev/runtime#hdr-Environment_Variables)。
*   `GORACE`用于通过在`build`时添加`-race`标志来配置[竞争检测器](https://golang.org/doc/articles/race_detector.html)。
*   `GOMAXPROCS` 限制并发执行用户级 Go 代码的操作系统线程数量，但不限制系统调用中阻塞的线程数量。
*   `GOTRACEBACK`控制 Go 程序因未处理的异常或意外运行条件而失败时的输出量。默认情况下，当出现错误时，将打印当前 goroutine 的堆栈跟踪，忽略内部运行时函数，并使用`exit 2`退出。
    如果没有当前的 goroutine，或者故障是运行时内部的，则在发生错误时打印所有 go routine 的堆栈跟踪，支持值包括`none(0)`、`all(1)`、`system(2)`、`single`、 `crash`，其中`all`和`system`是最常用的。`all`为所有用户创建的 goroutines 添加堆栈跟踪信息。和`system`类似，但是添加了运行时函数的堆栈跟踪信息，并显示运行时内部创建的 goroutines。

# 戈罗廷

Goroutine 是 Go 的核心特性之一。通常，我们使用`go func()`来创建 goroutines，但是只要最终结果是正确的，我们就不太关心后续的调度和执行。

但是作为高水平的 Go 开发者，我们需要了解 goroutine 的调度，比如 GMP 模型，每个 goroutine 可以使用的堆栈大小，可以并行执行的 Go routine 的最大数量等等。在某种程度上，这些会受到运行时包中的方法的影响。

## 运行时。GOMAXPROCS(n)

`runtime.GOMAXPROCS(n)`设置一个进程中并行执行的 goroutines 的最大数量(`P`)，与`NumCPU()`相同，最大 256。但是修改这个值会触发 STW(停止世界)，所以我们应该避免频繁调用它。当工作池中的工作线程数量(或`waitGroups`)可配置时，此方法主要用于确保`GOMAXPROCS()`少于工作线程池。

## 运行时。戈舍德

调用`runtime.Gosched()`的 goroutine 将被`runtime.Gosched()`挂起，重置为`Runnable`，并放入调度器的可运行 G 队列中。它可以与 runnable worker pool 结合使用，监控当前正在运行的任务数量，并在任务数量超过线程池大小时，将当前的 goroutine 修改为下次执行的`Runnable`状态。一个完美的例子如下:

## 运行时。Goexit

`runtime.Goexit()`在执行完该 goroutine 中的`defer`语句后，立即终止调用它的 goroutine 的运行，但不影响其他 go routine。在错误处理中，`runtime.Goexit()`可以保持当前 goroutine 中的异常，保证整个 Go 程序处于正常状态。

## 运行时。LockOSThread 和运行时。解锁线程

`runtime.LockOSThread()`锁:goroutine 用运行它的`M`调用它，而`runtime.UnlockOSThread()`解锁锁。请记住以下几点:

*   多次调用`runtime.LockOSThread()`不会导致任何问题，但最后一次调用将被记录。
*   即使没有调用`runtime.LockOSThread()`也不会影响`runtime.UnlockOSThread()`的调用。

在调用非 Go 程序(如 C 代码或 OS 服务)之前使用它，可以使相关的 goroutine 在程序失败时发现错误。在 Postman 的代码中，你可以找到很多[访问不同 OS 时的例子](https://sourcegraph.com/search?q=context:global+runtime.LockOSThread%28%29+lang:Go+repo:%5Egithub%5C.com/containers/podman%24+&patternType=standard)。例如:

```
runtime.LockOSThread()
defer runtime.UnlockOSThread()// Call unix functions(non-Go)
fds, err := unix.Socketpair(unix.AF_UNIX, unix.SOCK_DGRAM, 0)
 if err != nil {
  return false, -1, err
 }
 r, w := os.NewFile(uintptr(fds[0]), "sync host"), os.NewFile(uintptr(fds[1]), "sync child")
```

此外，运行时/调试包中还有一些方法会影响 goroutines 的运行。

## 调试。SetMaxStack

`debug.SetMaxStack()`限制单个 goroutine 可以申请的堆栈空间的最大大小。​

## 调试。SetMaxThreads

`debug.SetMaxThreads()`设置运行时使用的内核线程数(`M`)。此方法通常与队列和工作池大小设置一起调用。

# 韵律学

运行时和运行时/调试包中有两种类型的数据。

*   `profiles`，包括`BlockProfile`、`MemProfile`、`GoroutineProfile`等。，当前栈信息的所有封装都可以用`pprof`工具获得，Go 官方不建议在代码中直接调用。
*   `stats`，直读数据，非常直观。它们通常用于直接在线分析当前程序。

## NumCPU，NumCgoCall，NumGoroutine

这三种方法都直接返回实时数据。但是需要注意的是`runtime.NumGoroutine()`在被调用后会返回特定状态下的 goroutines 的数量。这里的特定状态指的是`Grunnable\Gruning\Gsyscall\Gwaition,`，其中 goroutines 被认为是活动的或预定的。

## ReadMemStats，ReadGCStats

`​ReadMemStats()`读取当前内存，包括堆占用、堆释放、栈占用、缓存状态、GC 数量、下次 GC 时间等。打印出来看看。

`ReadGCStats()`在调试包中，它提供的部分数据与`READMemStats`有重复，但都集中在 GC 上，比如`LastGc`、`NumGc`、`Pause`、`PauseTotal`等。比如普罗米修斯的代码中就应用了 [GCStats](https://github.com/kubernetes/kubernetes/blob/22f3e6403904ba54bb7bb3b9a743367909669f08/vendor/github.com/prometheus/client_golang/prometheus/go_collector.go#L251) 。

我们还可以在开发 Go server 服务时添加 API 来返回这些数据，以方便在线调试。

# 车底距地高(Ground Clearance)

Go 封装 GC 非常出色，即使成千上万个 goroutines 同时运行，程序也能有效地执行，几乎不需要用户干预。但是运行时包提供了一些函数来弥补默认 GC 的缺点。

## 运行时。车底距地高(Ground Clearance)

`runtime.GC()`在运行时执行强制 GC，不同于`GOGC`配置触发的非强制 GC。

通常，我们在分析之前手动触发 GC，就像我们在 [Kubernetes](https://github.com/kubernetes/kubernetes/blob/e8449012e2909ba55e5636bc912a16f98f42196d/staging/src/k8s.io/kubectl/pkg/cmd/profiling.go#L90) 中所做的那样。代码如下:

```
func flushProfiling() error {
 switch profileName {
 case "none":
  return nil
 case "cpu":
  pprof.StopCPUProfile()
 case "heap":
  runtime.GC()
  fallthrough
...
}
```

## 运行时。SetFinalizer

`SetFinalizer()`截取特定对象的 GC，用`Finalizer`增加额外的清理操作，比如 Go 的 crypto 包中的[终结器](https://github.com/golang/go/blob/3ba3b4893f3630b2bd78ec6f4f366d60e16bd636/src/crypto/internal/boring/hmac.go#L102)调用 C 来清理对象。

## 运行时。保持活跃

> `*​KeepAlive*`将其参数标记为当前可达。这确保了在程序中调用`*KeepAlive*`之前，对象没有被释放，并且它的终结器没有运行。

```
runtime.SetFinalizer(p, func(p *File) { syscall.Close(p.d) })
var buf [10]byte
n, err := syscall.Read(p.d, buf[:])
// Ensure p is not finalized until Read returns.
runtime.KeepAlive(p)​
```

一些重对象像文件描述符和空队列在 GC 被触发前使用 KeepAlive，比如在 Kubernetes 中获得的 windows `os.File` [对象](https://github.com/kubernetes/kubernetes/blob/e8449012e2909ba55e5636bc912a16f98f42196d/vendor/github.com/Microsoft/go-winio/fileinfo.go#L26)。

```
func GetFileBasicInfo(f *os.File) (*FileBasicInfo, error) {
 bi := &FileBasicInfo{}
 if err := windows.GetFileInformationByHandleEx(windows.Handle(f.Fd()), windows.FileBasicInfo, (*byte)(unsafe.Pointer(bi)), uint32(unsafe.Sizeof(*bi))); err != nil {
  return nil, &os.PathError{Op: "GetFileInformationByHandleEx", Path: f.Name(), Err: err}
 }
 runtime.KeepAlive(f)
 return bi, nil
}​​​
```

# 调试

一旦出现错误，我们还可以通过运行时包获得异常打印和堆栈信息。

## 运行时。呼叫者

`Caller()`反映堆栈帧的绝对路径和语句所在文件的行号。`skip`是用`0`标识`Caller`的堆栈帧数。而要打印所有堆栈信息，可以直接使用`debug.PrintStack()`。

上面的例子还使用了`FuncForPC`函数，它通常用于

*   获取函数的名称，`.Name()`
*   获取函数的文件行，`.FileLine()`

这两种方法最常用于在定制打印日志时获取更多信息，例如`SetV`功能在`klog`包中扮演的角色。

## 追踪

运行时包还支持打印`trace`信息，使用`StartTrace()`和`StopTrace()`方法开始和结束跟踪，使用`ReadTrace()`读取信息。

Go 本身使用这些函数来跟踪运行时的活动。

# 结论

这篇文章总结了运行时包中的大多数方法，其中与 goroutine 和 metrics 相关的方法在 Go 工具开发中非常有用。它们提供了一些功能，比如在更高的层次上管理 Go 的内存使用、监控性能和保护程序健康。与调试相关的功能有助于构建对意外错误提示的默认支持，这可以显著加快我们定位错误的速度。

感谢阅读！