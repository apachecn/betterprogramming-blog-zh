# 《长生不老药》中的断路器模式

> 原文：<https://betterprogramming.pub/circuit-breaker-pattern-in-elixir-fff06199083c>

## Elixir 中有趣的架构模式

![](img/1d79bc1dd5e7742e07e252c6abc9bbf9.png)

Adrien Olichon 在 [Unsplash](https://unsplash.com/s/photos/domino-effect?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

断路器用于检测故障，并封装逻辑以防止故障在维护、临时外部系统故障或意外系统故障期间不断重复出现。

在微服务时代，我们更有可能拥有正在调用并依赖于不受控制的外部服务的服务。

远程服务可能会挂起、失败或无响应。我们如何防止这些故障在系统中蔓延？获取关键资源？

进入断路器模式。该模式在书[中得到推广，发布吧！迈克尔·尼加德和像马丁·福勒这样的思想领袖。](https://www.oreilly.com/library/view/release-it/9781680500264/)

![](img/b0eee1bbca6ce1dc99e06e7df4f1e308.png)

这种模式背后的想法非常简单:失败是不可避免的，试图完全防止失败是不现实的。

处理这些失败的一种方法是将这些操作封装到某种代理中。该代理负责监控最近的故障，并使用该信息来决定是允许操作继续进行还是返回早期故障。

该代理通常被实现为模拟物理断路器的功能的状态机，该物理断路器可以具有三种状态:

*   **闭合:**在此状态下，断路器让所有请求通过，同时记录最近的故障次数，如果故障次数在特定时间范围内超过特定阈值，它将切换到断开状态。
*   **打开:**在此状态下，任何请求都不会发送到外部服务。相反，我们要么立即失败，返回一个扩展，要么回退到一个二级系统，如缓存。
*   **半开:**在这种状态下，来自应用程序的有限数量的请求被允许通过并调用我们的外部服务。根据这些请求的结果，断路器将翻转到闭合状态或回到断开状态，在试图再次断开之前重置计数器。

断路器模式提供了一些值得注意的关键优势:

*   **半开**状态使外部系统恢复而不会被淹没。
*   **Open**-状态实现给出了我们希望如何处理故障的选项，无论是立即故障还是退回到缓存层或辅助系统。
*   这种模式还可以通过快速拒绝可能失败或超时的呼叫来帮助保持响应时间。

# 例子

对于我们的示例，假设我们有以下场景:

我们正在运行一个工作板聚合器，它将消耗来自 GitHub 和其他来源的职位发布。然而，由于我们正在使用一些 API，我们面临着 API 达到极限或停机的风险。

让我们从创建一个 Github 作业的示例 API 连接器开始，该连接器检索最近发布的 50 个作业:

```
defmodule CircuitBreaker.Api.GithubJobs do 
    ...
    @spec get_positions :: none
    def get_positions do
        case HTTPoison.get(url()) do
        {:ok, response} -> {:ok, parse_fields(response.body)}
        {:error, %HTTPoison.Error{id: _, reason: reason}} -> {:error, reason}
        end
    end
    ...
end
```

**文件**:[lib/circuit _ breaker/API/github _ jobs . ex](https://github.com/amacgregor/circuit_breaker_example/blob/main/lib/circuit_breaker/api/github_jobs.ex)

这个连接器所做的就是请求`jobs.github.com`检索 JSON，解析并返回作业列表。如果我们想测试这个，我们可以在控制台上手动调用`get_positions`:

```
iex(1)> CircuitBreaker.Api.GithubJobs.get_positions
{:ok,
 ["Software Engineer", "Backend Engineer (w/m/d)",
  "Senior Frontend Engineer (f/m/d)", ...]}
```

# 断路器开关

现在，我们已经能够通过调用来获取招聘信息，我们需要构建断路器来包装 API 适配器。让我们来看看我们的交换机的框架。

```
defmodule CircuitBreaker.Api.Switch do
  use GenStateMachine, callback_mode: :state_functions

  @name :circuit_breaker_switch
  @error_count_limit 8
  @time_to_half_open_delay 8000

  def start_link do
    GenStateMachine.start_link(__MODULE__, {:closed, %{error_count: 0}}, name: @name)
  end

  def get_positions do
    GenStateMachine.call(@name, :get_positions)
  end
  ...
end
```

**文件**:[lib/circuit _ breaker/API/switch . ex](https://github.com/amacgregor/circuit_breaker_example/blob/main/lib/circuit_breaker/api/switch.ex)

为了实现我们的断路器，我们可以直接使用`gen_statem`行为。或者在这种情况下，我们可以利用 GenStateMachine 包，它为我们提供跟踪和错误报告，并将与监督树一起工作。

我们添加的前两个函数是:

*   `start_link`:这将启动具有初始状态和特定名称的断路器。
*   `get_positions`:这是我们的公共客户端 API，包装了我们刚刚构建的 GitHub 作业适配器。

这里需要注意的一点是第一行:

```
use GenStateMachine, callback_mode: :state_functions
```

在这种回调模式下，每次你执行`call/3`或`cast/2`时，消息将由与当前状态同名的`state_name/3`函数处理。在这种情况下，我们的`state_name`函数将是`open`、`closed`和`half_open`。

让我们继续，从添加我们的关闭状态代码开始:

```
def closed({:call, from}, :get_positions, data) do
    case CircuitBreaker.Api.GithubJobs.get_positions() do
      {:ok, positions} ->
        {:keep_state, %{error_count: 0}, {:reply, from, {:ok, positions}}}
      {:error, reason} ->
        handle_error(reason, from, %{ data | error_count: data.error_count + 1 })
    end
  end
```

**文件**:[lib/circuit _ breaker/API/switch . ex](https://github.com/amacgregor/circuit_breaker_example/blob/main/lib/circuit_breaker/api/switch.ex)

我们所做的就是调用 API 适配器`get_positions`，根据结果，我们要么返回位置列表，要么处理错误。

让我们直接进入终端，尝试通过断路器获取位置列表:

```
iex(1)> CircuitBreaker.Api.Switch.start_link
{:ok, #PID<0.231.0>}
iex(2)> CircuitBreaker.Api.Switch.get_positions
{:ok,
 ["Software Engineer", "Backend Engineer (w/m/d)",
  "Senior Frontend Engineer (f/m/d)", ...]}
```

让我们添加另外两种状态的函数，并回顾电路状态变化的工作原理:

```
def half_open({:call, from}, :get_positions, data) do
    case CircuitBreaker.Api.GithubJobs.get_positions() do
      {:ok, positions} ->
        {:next_state, :closed, %{count_error: 0}, {:reply, from, {:ok, positions}}}
      {:error, reason} ->
        open_circuit(from, data, reason, @time_to_half_open_delay)
    end
  end

  def open({:call, from}, :get_positions, data) do
    {:keep_state, data, {:reply, from, {:error, :circuit_open}}}
  end

  def open(:info, :to_half_open, data) do
    {:next_state, :half_open, data}
  end
```

让我们添加几个私有的实用函数:

```
defp handle_error(reason, from, data = %{error_count: error_count}) when error_count > @error_count_limit do
      open_circuit(from, data, reason, @time_to_half_open_delay)
  end

  defp handle_error(reason, from, data) do
    {:keep_state, data, {:reply, from, {:error, reason}}}
  end

  defp open_circuit(from, data, reason, delay) do
    Process.send_after(@name, :to_half_open, delay)
    {:next_state, :open, data, {:reply, from, {:error, reason}}}
  end
```

大多数神奇的事情都发生在`open_circuit`函数上，它做了两件事:

*   首先，我们安排一条消息，在指定的延迟后将断路器状态设置为`half_open`。
*   第二，我们返回一个新状态，将断路器完全设置`open`。

8000 毫秒后，现在处于`open`状态的断路器将收到我们预定的消息，并将状态改为`half_open`。

最后，在`half_open`状态期间，我们将尝试调用 API 端点。如果失败，我们将自动切换回完全`open`并重试。

# 结论

断路器是我们的武器库中的一个有价值的模式，因为它们可以帮助增加系统的稳定性，并给我们一个更可靠的方法来处理远程服务的错误。

这个例子只是触及了断路器的表面。有很多机会可以进一步扩展这种模式，例如，

*   通过查看错误类型和频率，改进断路器跳闸的逻辑
*   一旦断路器改变状态，增加监控和记录
*   在返回故障之前，退回到辅助服务或缓存层

最后，与任何模式一样，记住用例并决定这种行为是否是所期望的是很重要的。

该示例的完整代码可在[电路断路器示例](https://github.com/amacgregor/circuit_breaker_example)中找到

# 进一步阅读

*   [微软—断路器模式](https://docs.microsoft.com/en-us/azure/architecture/patterns/circuit-breaker)
*   [大容量分布式系统中的容错](https://netflixtechblog.com/fault-tolerance-in-a-high-volume-distributed-system-91ab4faae74a)作者:Ben Christensen
*   [微服务架构—断路器模式](https://microservices.io/patterns/reliability/circuit-breaker.html)