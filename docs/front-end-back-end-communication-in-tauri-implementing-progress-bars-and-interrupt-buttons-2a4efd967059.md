# Tauri 中的前端后端通信:实现进度条和中断按钮

> 原文：<https://betterprogramming.pub/front-end-back-end-communication-in-tauri-implementing-progress-bars-and-interrupt-buttons-2a4efd967059>

## 简化异步通信

![](img/87f514ae55ec55594fa27ad7a861abf0.png)[](https://medium.com/@applied-math-coding/membership) [## 通过我的推荐链接加入 Medium-applied . math . coding

### 获得我所有的故事，以及其他作家在媒体上发表的数千篇故事。我坚信，媒介是…

medium.com](https://medium.com/@applied-math-coding/membership) 

在这个小故事中，我想简单介绍一下 Tauri 是如何支持前端和后端之间的异步通信的。当后端通知 UI 有关变化(如长时间运行的任务的进度消息或 UI 受其影响的状态变化)时，通常需要这些特性。我们会看到，通常对 Tauri 来说，我们的目标并不难实现。Tauri 基于其`[Window](https://tauri.app/v1/api/js/window)` [-API](https://tauri.app/v1/api/js/window) 提供了非常易于使用的异步消息传递工具。

*对于不熟悉 Tauri 或者 Rust 又想快速总结一下的，建议看这里的*[](https://medium.com/@applied-math-coding/list/an-introduction-into-rust-22c99777c5e5)**[*这里的*](/building-a-desktop-app-with-tauri-8e281b8c4f64) *。***

# **从后端发送**

**假设在前端，我们有一些组件通过调用 Tauri 命令来触发运行一个长时间运行的任务。任务可以定期发送其进度状态的更新，并作为一个`[Event](https://tauri.app/v1/api/js/event)`发送给客户机。作为回报，客户端可以显示进度条或任何指示器。为此，客户端必须注册一个事件侦听器，如下所示:**

```
**import { **appWindow** } from '@tauri-apps/api/window';const **unlistenProgress** = await **appWindow**.listen(
  '**PROGRESS**',
  ({event, payload}) => console.log(payload)
);**
```

**我们导入代表 Tauri 对当前窗口的抽象的实例`appWindow`。在这种情况下，我们可以在一个命名事件上注册一个监听器(这里是`'PROGRESS'`)。**

**每当具有此名称的事件到达时，监听器本身就会被调用，并在特定的有效负载中被传递。后者的内容是自定义的，我们可以在后端指定它。这个方法，也就是`listen`，返回一个`Promise`，用一个重要的函数`unlistenProgress`解析。对此的调用，如`unlistenProgress()`，将监听器从事件中断开。例如，当我们的组件在导航过程中被破坏时，这尤其有用。**

**为了让后端在这个`appWindow`实例上发出事件，我们需要在命令调用中将它作为参数传递:**

```
**const result = await invoke('**do_some_long_task**', {    
    window: **appWindow**
});**
```

**注意，即使不考虑这个参数，Tauri 也将它作为一个参数在带有签名`(..., window: Window)`的`#[tauri::command]`注释函数中传递。**

**在后端，为了触发我们的`'PROGRESS'`事件，我们必须做以下事情:**

**我们定义了一个客户`Payload`，它需要是可克隆和可序列化的。然后我们只需在传递的带有有效负载的`window`实例上调用`emit`方法。就这些了。这确保了将我们的事件发送到特定的窗口。**

# **从前端发送**

**向服务器发送消息的一个常见场景是通知长时间运行的任务停止。为此，在后端我们注册了一个合适的处理程序来监听客户端发送的`STOP`-事件:**

**简单的想法是有一些可变标志`stop`，当信号到达时，它从`false`变为`true`。信号本身被注册为由`window.once("STOP", ...)`监听。**

**返回的`handler`在这个方法的最后用`window.unlisten(...)`取消注册。正如你所看到的，我们必须注意这样一个事实，即注册的处理程序将在一个单独的线程中运行。**

**出于这个原因，我们必须传递一个同步的 ref-counter ( `Arc`)，它包装了一个`RwLock`来读写共享值。**

**对于这种情况，前端代码看起来非常简单:**

```
**import { appWindow } from '[@tauri](http://twitter.com/tauri)-apps/api/window';async function doStop() {
 await appWindow.**emit**('STOP');
 console.log('all stopped');
}**
```

**这只是向当前窗口发出一个名为`'STOP'`的事件。**

**上面的机制我已经用于一个关于计算牛顿分形的小应用，你可能从我的故事[这里](https://applied-math-coding.medium.com/producing-newton-fractals-in-rust-and-zoom-into-them-3e12785f5f3d)知道。**

**感谢阅读！**