# 在 Go 中探索过滤器

> 原文：<https://betterprogramming.pub/exploring-filter-in-go-158abc3926>

## 我使用 Go 创建函数数组过滤器的旅程

![](img/79599e05e749ad4be1ff43838694f525.png)

来源:[Flaticon.com](https://www.flaticon.com/free-icon/filter_408317?term=filter&page=1&position=33)

假设一个数组对象有`N`个条目。你想过滤它，只得到带有`flagged = true`或`flagged = false, amount <= 500`的条目。你开始写代码，`imperatively`。

*   这里，我们需要读取循环内部的所有代码，只是为了知道它的意图。
*   如果过滤每个条目需要`x`的时间，那么`n`个条目的总时间就是`t = n * x`。
*   给定`x=1s`和`n=1000`然后`t=1000*1s = 1000s`。

# Go 中的并行滤波

想象一个叫做`ParallelFilter`的函数。它需要两个参数:

*   `Array`指任何一种物体。姑且称之为`source`。
*   `Function`获取`Array`的每个条目并返回一个`boolean`。姑且称之为`filter`。

```
*// ParallelFilter an array using go routine*
*// This function will not guarantee order of results*
func ParallelFilter(arr, filter interface{}) (interface{}, error) {
    *//TODO:*
}
```

# 通过单元测试确保 ParallelFilter 行为

# TODO-1。确保源的类型

# TODO-2。确保 F `ilter`不为零并且是一个函数

# 待办事项-3。正在准备结果队列

基本思路是这样的。每次我们发现条目满足`filter`函数，我们就把值放入`queue`。然后还有另一个进程等待`queue`并收集所有结果。

# TODO-4 进行循环并返回结果

# 将它们缝合在一起

测试结果显示如下:

```
Running tool: /usr/local/opt/go/libexec/bin/go test -timeout 30s github.com/bastianrob/go-experiences/filter -run ^(TestParallelFilter)$ok       github.com/bastianrob/go-experiences/filter    0.007s
Success: Tests passed.
```

# 基准平行过滤器

首先，我们将尝试模拟慢速运行的过滤器功能:

基准测试结果:

```
BenchmarkParallelFilter-4            100      22920607 ns/op       18678 B/op         451 allocs/op
PASS
ok      github.com/bastianrob/go-experiences/filter    2.322s
Success: Benchmarks passed.pkg: github.com/bastianrob/go-experiences/filter
BenchmarkImperative-4                  1    2289902017 ns/op        1240 B/op           7 allocs/op
PASS
ok      github.com/bastianrob/go-experiences/filter    2.295s
Success: Benchmarks passed.
```

`ParallelFilter`在处理大量条目时速度更快，而`filter`处理速度较慢。但是跑得更快的`filter`呢？

基准测试结果:

```
BenchmarkParallelFilterFast-4        10000          184717 ns/op       11216 B/op         347 allocs/op
PASS
ok      github.com/bastianrob/go-experiences/filter    1.870s
Success: Benchmarks passed.BenchmarkImperativeFast-4         50000000            40.7 ns/op           0 B/op           0 allocs/op
PASS
ok      github.com/bastianrob/go-experiences/filter    2.079s
Success: Benchmarks passed.
```

这说明`ParallelFilter`绝对是被正常的`imperative`滤镜谋杀的。

# ParallelFilter 使用稍微不同的方法

如果我们把收集过滤结果的责任倒置给函数调用方会怎么样？这在 Go 中通过使用`channel`是可能的，所以让我们做一个`DeferredFilter`函数:

基准测试:

结果是:

```
BenchmarkDeferredFilterFast-4          10000        144723 ns/op        9060 B/op         304 allocs/op
PASS
ok      github.com/bastianrob/go-experiences/filter    1.471s
Success: Benchmarks passed.
```

测试显示时间/操作稍快。

# 如果我们不用围棋套路呢？

让我们来制作刚才制作的`Filter`函数:

基准测试:

结果是:

```
BenchmarkFilterFast-4          50000         33171 ns/op        7864 B/op         244 allocs/op
PASS
ok      github.com/bastianrob/go-experiences/filter    2.008s
Success: Benchmarks passed.
```

当然，它比`ParallelFilter`快，但它还是被`imperative`环路谋杀了。

# 结论

不幸的是，除非您正在处理大块的数组并过滤每个条目，这需要很长时间，否则使用`ParallelFilter`、`DeferredFilter`或仅仅使用`Filter`几乎没有任何好处。

我想如果我们也能流式传输输入源，而不是发送整个数组，那会更好。但那是`Pipeline Processing`，一个我在这篇文章里不会触及的话题！

在我的 Github 中找到所有这些恶作剧:

[](https://github.com/bastianrob/go-experiences/tree/master/filter) [## Bastian rob/go-体验

### 假设一个数组对象有 N 个条目。您希望对其进行过滤，以便只获得带有 flagged = true 的条目，或者…

github.com](https://github.com/bastianrob/go-experiences/tree/master/filter)