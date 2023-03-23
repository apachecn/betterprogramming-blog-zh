# 如何在 Node.js 中处理工作线程

> 原文：<https://betterprogramming.pub/how-to-handle-worker-threads-in-node-js-4657cb8c1adf>

## 揭开 Node.js 中线程的神秘面纱

![](img/9d9d66895020ba176c3c65ff1c86f8e8.png)

作者图片

我们经常谈论 Node 的单线程特性如何使 Node 应用程序易于扩展和节省资源。但是这也使得[节点不适合用于实现 CPU 密集型任务](https://livecodestream.dev/post/when-you-should-and-should-not-use-nodejs-for-your-project/)。

但是为了解决这个问题，Node 在其 v10.5.0 版本中引入了工作线程的概念。工作线程提供了一种生成新线程的机制，以处理节点程序中的 CPU 密集型任务。

因此，在本教程中，我们将向您简单介绍工作线程以及如何使用它们。然而，为了理解为什么工作线程很重要，让我们首先讨论为什么 Node 在处理 CPU 密集型任务时性能很差。

# 为什么 Node 不能处理 CPU 密集型任务？

我们称运行在节点程序中的单线程为事件循环。当事件被触发时，节点使用它来处理下面的执行。

但是如果事件的执行代价很高，比如 CPU 受限和 I/O 受限的任务，Node 无法在事件循环中运行它，除非长时间阻塞唯一可用的线程。

因此，事件循环不会等待代价高昂的操作完成，而是注册附加到事件的回调函数，并继续处理循环中的下一个事件。

昂贵的操作被卸载到一组叫做*工作线程池的辅助线程上。*工作池中的一个线程异步执行任务，一旦操作完成就通知事件循环。

然后，事件循环在其线程上执行为该操作注册的回调函数。

因为回调是在事件循环上运行的，如果它们中的任何一个包含 CPU 密集型操作，比如机器学习或大数据中使用的复杂数学计算，它就会在相当长的时间内阻塞事件循环。在此期间，应用程序不会执行事件循环中的任何其他任务，包括响应来自客户端的请求。

这种情况会大大降低节点应用程序的性能。因此，在很长一段时间里，Node 被认为不适合处理 CPU 密集型操作。

但是工作线程的引入为这个问题提供了一个解决方法。

# 工作线程是如何工作的？

在 v10 中，Node 引入了工作线程作为实验特性。在 v12 中变得稳定。工作线程的行为并不完全像传统的多线程系统，因为它们不是 JavaScript 本身内置的特性。

但是它们允许将昂贵的任务委托给单独的线程，而不是阻塞应用程序的事件循环。那么工作线程实际上是如何工作的呢？

工作线程的职责是运行由父线程或主线程指定的一段代码。每个工人与其他工人隔离运行。但是，工作线程及其父线程可以通过消息通道来回传递消息。

当 JavaScript 不支持多线程时，为了运行相互隔离的工作线程，工作线程使用一种特殊的机制。

我们都知道 Node 运行在 Chrome 的 V8 引擎之上。V8 支持创建独立的 V8 运行时。这些被隔离的实例被称为 [V8 Isolate](https://v8docs.nodesource.com/node-0.8/d5/dda/classv8_1_1_isolate.html) ，它们有自己的 JavaScript 堆和微任务队列。

工作线程在这些隔离的 V8 引擎上运行，每个工作线程都有自己的 V8 引擎和事件队列。换句话说，当 workers 处于活动状态时，一个节点应用程序有多个节点实例在同一个进程中运行。

即使 JavaScript 本身不支持并发，worker threads 也提供了一种在单个进程中运行多个线程的方法。

# 创建和运行新员工

在这个例子中，我们将执行一个任务来计算斐波那契数列的第 *n* 项。这是一个 CPU 密集型任务，如果在没有工作线程的情况下执行，它将阻塞我们的节点应用程序的单个线程，特别是随着第 *n* 项的增加。

我们将实现分成两个文件。第一个是`app.js`，这是主线程执行的代码，包括一个新工作线程的创建。第二个文件`worker.js`，包含我们将创建的 worker 运行的代码。这是 CPU 密集型斐波那契计算的代码应该在的地方。

让我们看看如何在父线程中处理新工作线程的创建。

```
const {Worker} = require("worker_threads");let num = 40;//Create new worker
const worker = new Worker("./worker.js", {workerData: {num: num}});//Listen for a message from worker
worker.once("message", result => {
  console.log(`${num}th Fibonacci Number: ${result}`);
});worker.on("error", error => {
  console.log(error);
});worker.on("exit", exitCode => {
  console.log(exitCode);
})console.log("Executed in the parent thread");
```

我们使用`Worker`类来创建一个新的工作线程。当创建一个新的`Worker`实例时，它接受以下参数。

```
new Worker(filename[, options])
```

这里，filename 参数指的是文件的路径，该文件包含应该由工作线程执行的代码。因此，我们需要传递`worker.js`文件的文件路径。

`Worker`构造器也接受一些选项，你可以参考 `[Worker](https://nodejs.org/api/worker_threads.html#worker_threads_class_worke)` [类](https://nodejs.org/api/worker_threads.html#worker_threads_class_worke)的[官方文档。然而，我们选择只使用`workerData`选项。当开始运行时，通过`workerData`选项传递的数据将可供工人使用。我们可以使用这个选项轻松地将 *n* 的值传递给斐波那契数列计算器。](https://nodejs.org/api/worker_threads.html#worker_threads_class_worke)

为了在执行完成时接收结果，父线程将一些事件侦听器附加到工作线程。

在这个实现中，我们选择监听三个事件。它们是:

*   `message`:当工作者向父母发布消息时，触发该事件。
*   `error`:当运行工人时发生错误时触发。
*   `exit`:当工人退出执行时触发。如果在`process.exit()`调用后退出，退出代码将被设置为`0`。如果用`worker.terminate()`终止执行，代码将是`1`。

在 message 事件中，worker 使用连接 worker 和 parent 的消息通道发送消息。我们将在后面的小节中看到消息传递是如何工作的。

一旦创建了工作线程，父线程就可以继续执行，而无需等待结果。当我们运行上面的代码时，在返回第 *n* 个斐波那契数列之前，字符串`Executed in the parent thread`被记录到控制台。

因此，我们看到这样的输出:

```
Executed in the parent thread
40th Fibonacci Number: 102334155
```

现在，让我们在`worker.js`文件中编写由 worker 实现的代码。

```
const {parentPort, workerData} = require("worker_threads");parentPort.postMessage(getFib(workerData.num))function getFib(num) {
    if (num === 0) {
      return 0;
    }
    else if (num === 1) {
      return 1;
    }
    else {
      return getFib(num - 1) + getFib(num - 2);
    }
}
```

这里，我们使用递归`getFib`函数来计算斐波纳契数列的第 *n* 项。

但是更有趣的是我们通过`workerData`选项从父节点接收数据的方式。通过这个对象，工人可以访问创建时传递的`num`的值。

然后，我们还使用这个`parentPort`对象向父线程发送一条包含斐波那契计算结果的消息。

# 父线程和工作线程之间的通信

正如您在前面的例子中看到的，`parentPort`对象允许工作线程与父线程通信。

这个`parentPort`是`MessagePort`类的一个实例。当父母或工作者使用`MessagePort`实例发送消息时，消息被写入`M[essageChannel](https://nodejs.org/api/worker_threads.html#worker_threads_class_messagechannel)`并触发`message`事件来通知接收者。

父进程可以使用相同的`postMessage`方法向工作进程发送消息。

```
worker.postMessage("Message from parent");
```

我们可以随时使用`MessageChannel`在父母和工作者之间来回发送多条消息。

# 对待工人的更好方法

在前面的例子中，我们创建的 worker 在一次 Fibonacci 计算结束时退出。有了这个实现，如果我们想计算另一个斐波那契数，我们就必须生成一个新的工作线程。

但是，使用独立的 V8 引擎和事件循环来生成工作线程是一项非常消耗资源的任务，尤其是考虑到在生产代码中遵循这种方法并不十分有效。

因此，我们可以重用同一个工作线程来执行每个计算，而不是为每个 Fibonacci 计算创建新的工作线程。这种行为是如何实现的。

```
//worker.js
const {parentPort} = require("worker_threads");parentPort.on("message", data => {
  parentPort.postMessage({num: data.num, fib: getFib(data.num)});
});function getFib(num) {
  if (num === 0) {
    return 0;
  }
  else if (num === 1) {
    return 1;
  }
  else {
    return getFib(num - 1) + getFib(num - 2);
  }
}
```

在`worker.js`中，我们设置 worker 监听来自其父节点的消息。

父节点没有通过`workerData`选项传递数据，而是选择通过消息传递数据。

```
//app.jsconst {Worker} = require("worker_threads");//Create new worker
const worker = new Worker("./worker.js");//Listen for a message from worker
worker.on("message", result => {
  console.log(`${result.num}th Fibonacci Number: ${result.fib}`);
});worker.on("error", error => {
  console.log(error);
});worker.postMessage({num: 40});
worker.postMessage({num: 12});
```

现在，我们可以使用同一个工作线程来运行多个 CPU 密集型操作。

值得注意的是，通过`postMessage`方法传递的数据，类似于作为`workerData`传递的数据，在发送给 worker 之前，使用[结构化克隆算法](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Structured_clone_algorithm)进行克隆。这可以防止两个线程修改同一个对象时出现争用情况。

# 在父母和员工之间共享内存

节点工作线程允许应用程序在父线程和工作线程之间共享内存，类似于传统的多线程系统。

共享内存比克隆数据并在父线程和工作线程之间来回传递更有效。

为了在线程之间共享内存，我们使用了一个`[SharedArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer)`。

像这样在两个线程之间共享内存可能会导致程序中出现竞争情况。[竞争条件](https://en.wikipedia.org/wiki/Race_condition)发生在两个线程试图同时读写同一内存位置的时候。

我们可以用[原子](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Atomics)来解决这个问题。Atomics 确保一个读或写操作在开始下一个操作之前完成。它防止了竞争情况的发生，因为现在在给定的时间只有一个线程可以访问共享内存。

让我们看看如何用`Atomics`和`SharedArrayBuffer`实现内存共享。

```
const {Worker} = require("worker_threads");let nums = [21, 33, 15, 40];//get size of the array buffer with int32 size buffer for each element in the array
const size = Int32Array.BYTES_PER_ELEMENT*nums.length;//create the buffer for the shared array
const sharedBuffer = new SharedArrayBuffer(size);
const sharedArray = new Int32Array(sharedBuffer);nums.forEach((num, index) => {
  Atomics.store(sharedArray, index, num);
})//Create new worker
const worker = new Worker("./worker.js");//Listen for a message from worker
worker.on("message", result => {
  console.log(`${result.num}th Fibonacci Number: ${result.fib}`);
});worker.on("error", error => {
  console.log(error);
});worker.postMessage({nums: sharedArray});
```

通过这种实现，父线程和工作线程都可以从共享数组对象读取数据和向其写入数据。

worker 执行的代码略有变化，因为现在我们传递的是一个数字数组，而不是单个数字。

```
parentPort.on("message", data => {
  data.nums.forEach(num => {
    parentPort.postMessage({num: num, fib: getFib(num)});
  });
})
```

工作线程会立即向父线程发送它计算的每个斐波那契数的消息，而不会等待整个过程完成。

在这个实现中，尽管我们使用了`postMessage`方法向 worker 发送数据，但是在将数据发送给 worker 之前，它不会克隆数据。相反，它传递对共享数组对象的引用。

在工作线程(如集群和子进程)之前提供的并行处理解决方案节点没有在线程之间共享内存的能力。这使得工作线程比这些解决方案更有优势。

# 线程工作池

正如我们之前所讨论的，创建一个工人是一项成本很高的任务。这就是为什么我们决定重用同一个工作者，而不让它在一个任务执行后退出。我们可以更进一步，创建一个等待执行任务的工人池。

当我们只有一个工作者时，应用程序必须等待，直到工作者被释放来接受新的任务。它会延迟应用程序对其他排队请求的响应时间。我们可以通过使用线程池来避免这种情况。

然后，该池可以从可用的工作线程中为我们提供一个非活动的工作线程，以便毫不延迟地执行任务。

不幸的是，工作线程还没有对工作池的本地支持。所以我们必须依靠第三方库来简化我们的任务。我们将使用`[node-worker-threads-pool](http://node-worker-threads-pool npm package)` npm 包。

下面是我们如何使用这个包创建一个静态的工人池(即固定数量的工人)。

```
const {StaticPool} = require("node-worker-threads-pool");let num = 40;//Create a static worker pool with 8 workers
const pool = new StaticPool({
  size: 8,
  task: "./worker.js"
});//Get a worker from the pool and execute the task
pool.exec({num: num}).then(result => {
  console.log(`${result.num}th Fibonacci Number: ${result.fib}`);
});
```

当我们运行`pool.exec()`函数时，池提供一个非活动的工作线程来运行我们的任务。

工作者的代码不需要通过任何改变来支持工作者池。

```
const {parentPort} = require("worker_threads");parentPort.on("message", data => {
  parentPort.postMessage({num: data.num, fib: getFib(data.num)});
})function getFib(num) {
  if (num === 0) {
    return 0;
  }
  else if (num === 1) {
    return 1;
  }
  else {
    return getFib(num - 1) + getFib(num - 2);
  }
}
```

# 摘要

在本教程中，我们讨论了如何使用节点工作线程创建新线程来运行 CPU 密集型任务。将这些任务卸载到单独的线程有助于运行我们的节点应用程序，即使在处理 CPU 密集型任务时，性能也不会显著下降。

尽管 Node 不支持传统方式的多线程，但 worker threads 为这个问题提供了一个很好的解决方法。因此，如果您认为节点应用程序不能有多个线程，那么是时候抛弃这种想法，尝试一下工作线程了。

感谢阅读！