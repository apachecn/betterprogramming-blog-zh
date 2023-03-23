# 在 Node.js 中使用事件(第 1 部分)

> 原文：<https://betterprogramming.pub/using-events-in-node-js-the-basics-9a9c44dcbe30>

## 如何发出和监听事件

![](img/9f4bf76dd790698251a2a6d18622cc4d.png)

来源:[维基共享资源](https://www.google.com/url?sa=i&url=https%3A%2F%2Fcommons.wikimedia.org%2Fwiki%2FFile%3ANode.js_logo.svg&psig=AOvVaw2fYyIekeh0RMO3t7TO8irB&ust=1575656878456000&source=images&cd=vfe&ved=0CA0QjhxqFwoTCPCbkbiRn-YCFQAAAAAdAAAAABAD)

Node.js 的核心特性是异步编程。这意味着 Node.js 中的代码可能不会按顺序执行。因此，数据可能无法在固定的时间内确定。这意味着，为了获得我们需要的所有数据，我们必须在获得数据时在应用程序中传递数据。这可以通过在 Node.js 应用程序中发出、侦听和处理事件来实现。当发出具有给定名称的事件时，如果侦听器被指定侦听具有该名称的事件，则该事件可以侦听侦听器。事件发射器函数被同步调用。事件侦听器代码是一个回调函数，它接受数据的参数并对其进行处理。Node.js 有一个`EventEmitter`类——可以通过创建一个新类来扩展它，以发出可以被事件监听器监听的事件。

# 定义事件发射器

下面是一个创建和使用`EventEmitter`类的简单例子:

```
const EventEmitter = require('events');class Emitter extends EventEmitter {}const eventEmitter = new Emitter();
eventEmitter.on('event', () => {
  console.log('event emitted!');
});eventEmitter.emit('event');
```

我们应该得到“事件发射！”在控制台日志中。在上面的代码中，我们创建了扩展`EventEmitter`类的`Emitter`，该类具有我们在最后一行中调用的`emit`函数。`emit`函数的参数是事件的名称，我们在下面的代码块中听到了这个名称:

```
eventEmitter.on('event', () => {
  console.log('event emitted!');
});
```

在上面的`'event'`参数之后的回调函数是事件处理函数，在接收到事件时运行。

在上面的代码中，我们发出了一个事件。然而，这不是很有用，因为当我们发出事件时，我们没有传递任何数据，所以它没有做太多。因此，我们希望用事件发送数据，这样我们就可以传递数据，这样我们就可以在事件侦听器中做一些有用的事情。为了在发出事件时传递数据，我们可以在第一个参数(即事件名称)之后传入额外的参数。例如，我们可以编写以下代码:

```
const EventEmitter = require('events');
class Emitter extends EventEmitter {}
const eventEmitter = new Emitter();eventEmitter.on('event', (a, b) => {
  console.log(a, b);
});eventEmitter.emit('event', 'a', 'b');
```

如果我们运行上面的代码，我们会在事件处理程序回调函数的`console.log`语句中得到‘a’和‘b’。正如我们看到的，我们可以用`emit`函数传递多个参数，将数据传递给订阅事件的事件处理程序。在第一个之后，`emit`函数调用中的参数都作为参数传递给事件监听器的回调函数，因此可以在事件监听器回调函数中访问它们。

我们还可以访问事件监听器回调函数中的事件发射器对象。我们所要做的就是将回调的箭头函数更改为传统函数，如以下代码所示:

```
const EventEmitter = require('events');
class Emitter extends EventEmitter {}
const eventEmitter = new Emitter();eventEmitter.on('event', function(a, b){
  console.log(a, b);
  console.log(`Instance of EventEmitter: ${this instanceof EventEmitter}`);
  console.log(`Instance of Emitter: ${this instanceof Emitter}`);
});eventEmitter.emit('event', 'a', 'b');
```

如果我们运行上面的代码，我们会在事件监听器回调函数内的`console.log`语句中记录这一点:

```
a b
Instance of EventEmitter: true
Instance of Emitter: true
```

另一方面，如果我们有以下情况:

```
const EventEmitter = require('events');
class Emitter extends EventEmitter {}
const eventEmitter = new Emitter();eventEmitter.on('event', (a, b) => {
  console.log(a, b);
  console.log(`Instance of EventEmitter: ${this instanceof EventEmitter}`);
  console.log(`Instance of Emitter: ${this instanceof Emitter}`);
});eventEmitter.emit('event', 'a', 'b');
```

然后，我们在事件监听器回调函数中的`console.log`语句中记录这一点。：

```
a b
Instance of EventEmitter: false
Instance of Emitter: false
```

这是因为箭头功能不会改变其内部的`this`对象。然而，传统函数*会改变`this`对象的内容。*

`EventEmitter`按照注册的顺序同步调用所有监听器。这消除了竞争条件和其他逻辑错误的可能性。为了异步处理事件，我们可以使用`setImmediate()`或`process.nextTick()`方法:

```
const EventEmitter = require('events');
class Emitter extends EventEmitter {}
const eventEmitter = new Emitter();eventEmitter.on('event', (a, b) => {
  setImmediate(() => {
    console.log('event handled asychronously');
  });
});
eventEmitter.emit('event', 'a', 'b');
```

在上面的代码中，我们将`console.log`放在`setImmediate`函数的回调函数中，该函数将异步而不是同步运行事件处理代码。

事件在每次发出时都会被处理。例如，如果我们有:

```
const EventEmitter = require('events');
class Emitter extends EventEmitter {}
const eventEmitter = new Emitter();
let x = 1;eventEmitter.on('event', (a, b) => {
  console.log(x++);
});for (let i = 0; i < 5; i++){
  eventEmitter.emit('event');
}
```

因为我们发出了“event”事件五次，所以我们得到:

```
1
2
3
4
5
```

如果我们想发出一个事件，并且只在第一次发出时处理它，那么我们使用`eventEmitter.once()`函数，如下面的代码所示:

```
const EventEmitter = require('events');
class Emitter extends EventEmitter {}
const eventEmitter = new Emitter();
let x = 1;eventEmitter.once('event', (a, b) => {
  console.log(x++);
});for (let i = 0; i < 5; i++){
  eventEmitter.emit('event');
}
```

正如所料，我们只在上面的事件处理程序的`console.log`语句中记录了这一点:

```
1
```

# 错误处理

如果在出现错误的情况下发出错误事件，它将被视为 Node.js 中的特殊情况。如果`EventEmitter`没有至少一个错误事件监听器寄存器，并且发出了错误，则抛出该错误，并且将打印错误的堆栈跟踪，该流程将退出。例如，如果我们有以下代码:

```
const EventEmitter = require('events');
class Emitter extends EventEmitter {}
const eventEmitter = new Emitter();eventEmitter.emit('error', new Error('Error occured'));
```

然后我们得到这样的结果，程序退出:

```
Error [ERR_UNHANDLED_ERROR]: Unhandled error. (Error: Error occured
    at evalmachine.<anonymous>:5:28
    at Script.runInContext (vm.js:133:20)
    at Object.runInContext (vm.js:311:6)
    at evaluate (/run_dir/repl.js:133:14)
    at ReadStream.<anonymous> (/run_dir/repl.js:116:5)
    at ReadStream.emit (events.js:198:13)
    at addChunk (_stream_readable.js:288:12)
    at readableAddChunk (_stream_readable.js:269:11)
    at ReadStream.Readable.push (_stream_readable.js:224:10)
    at lazyFs.read (internal/fs/streams.js:181:12))
    at Emitter.emit (events.js:187:17)
    at evalmachine.<anonymous>:5:14
    at Script.runInContext (vm.js:133:20)
    at Object.runInContext (vm.js:311:6)
    at evaluate (/run_dir/repl.js:133:14)
    at ReadStream.<anonymous> (/run_dir/repl.js:116:5)
    at ReadStream.emit (events.js:198:13)
    at addChunk (_stream_readable.js:288:12)
    at readableAddChunk (_stream_readable.js:269:11)
    at ReadStream.Readable.push (_stream_readable.js:224:10)
```

为了防止 Node.js 程序崩溃，我们可以使用新的事件侦听器来侦听错误事件，并在错误事件处理程序中优雅地处理错误。例如，我们可以写:

```
const EventEmitter = require('events');
class Emitter extends EventEmitter {}
const eventEmitter = new Emitter();eventEmitter.on('error', (error) => {
  console.log('Error occurred');
});eventEmitter.emit('error', new Error('Error occurred'));
```

然后我们得到“错误发生”记录。我们也可以用事件处理程序回调函数的`error`参数获取错误内容。如果我们记录它，如下面的代码所示:

```
const EventEmitter = require('events');
class Emitter extends EventEmitter {}
const eventEmitter = new Emitter();eventEmitter.on('error', (error) => {
  console.log(error);
});eventEmitter.emit('error', new Error('Error occurred'));
```

我们会得到这样的结果:

```
Error: Error occurred
    at evalmachine.<anonymous>:7:28
    at Script.runInContext (vm.js:133:20)
    at Object.runInContext (vm.js:311:6)
    at evaluate (/run_dir/repl.js:133:14)
    at ReadStream.<anonymous> (/run_dir/repl.js:116:5)
    at ReadStream.emit (events.js:198:13)
    at addChunk (_stream_readable.js:288:12)
    at readableAddChunk (_stream_readable.js:269:11)
    at ReadStream.Readable.push (_stream_readable.js:224:10)
    at lazyFs.read (internal/fs/streams.js:181:12)
```

# 更多处理事件的方法

`Node.js`将在不编写任何代码的情况下发出一个特殊事件:T2。在将侦听器添加到内部侦听器数组之前，会发出`newListener`事件。例如，如果我们有以下代码:

```
const EventEmitter = require('events');
class Emitter extends EventEmitter {}
const eventEmitter = new Emitter();
eventEmitter.on('newListener', (event, listener) => {
  console.log(event);
});
```

然后我们得到这样的记录:

```
Emitter {
  _events: [Object: null prototype] { newListener: [Function] },
  _eventsCount: 1,
  _maxListeners: undefined }
```

即使没有事件发出，也会发生这种情况。对于任何其他事件，处理程序中的任何内容都将在事件处理程序中的代码之前运行。

`removeListener`函数可以用来阻止事件监听器函数监听事件。这需要两个参数:第一个是表示事件名称的字符串，第二个是您想要停止使用来监听事件的函数。例如，如果我们想用监听器函数停止监听“event”事件，那么我们可以这样写:

```
const EventEmitter = require('events');
class Emitter extends EventEmitter { }
const eventEmitter = new Emitter();const listener = () => {
  console.log('listening');
}eventEmitter.on('event', listener)setInterval(() => {
  eventEmitter.emit('event');
}, 300);setTimeout(() => {
  console.log("removing");
  eventEmitter.removeListener('event', listener);
}, 2000);
```

然后我们在输出中得到这样的结果:

```
Timeout {
  _called: false,
  _idleTimeout: 2000,
  _idlePrev: [TimersList],
  _idleNext: [TimersList],
  _idleStart: 1341,
  _onTimeout: [Function],
  _timerArgs: undefined,
  _repeat: null,
  _destroyed: false,
  [Symbol(unrefed)]: false,
  [Symbol(asyncId)]: 10,
  [Symbol(triggerId)]: 7 }listening
listening
listening
listening
listening
listening
removing
```

事件发射器每 300 毫秒发出一次上面代码中的“event”事件。这由`listener`函数监听，直到通过在`setTimeout`函数的回调中调用事件名称为`listener`事件监听器函数的`removeListener`函数来阻止它再次监听。

多个事件侦听器可以注册一个事件。默认情况下，事件侦听器的最大数量限制为十个。我们可以用`EventEmitter`类中的`defaultMxListeners`函数来改变这一点。我们可以把它设为任何正数。如果不是正数，那么抛出一个`TypeError`。如果注册的侦听器超过了限制，则会输出一个警告。例如，如果我们运行以下代码为“event”事件注册 11 个事件侦听器:

```
const EventEmitter = require('events');
class Emitter extends EventEmitter { }
const eventEmitter = new Emitter();const listener = () => {
  console.log('listening');
}for (i = 1; i <= 11; i++){
  eventEmitter.on('event', listener);
}eventEmitter.emit('event');
```

当我们运行上面的代码时，我们得到这样的结果:

```
listening
listening
listening
listening
listening
listening
listening
listening
listening
listening
listening
(node:345) MaxListenersExceededWarning: Possible EventEmitter memory leak detected. 11 event listeners added. Use emitter.setMaxListeners() to increase limit
```

但是，如果我们调用`setMaxListeners`将其设置为`getMaxListeners() + 1`，即 11 个侦听器，如下面的代码所示:

```
const EventEmitter = require('events');
class Emitter extends EventEmitter { }
const eventEmitter = new Emitter();
eventEmitter.setMaxListeners(eventEmitter.getMaxListeners() + 1);const listener = () => {
  console.log('listening');
}for (i = 1; i <= 11; i++){
  eventEmitter.on('event', listener);
}eventEmitter.emit('event');
```

然后，我们记录了以下内容:

```
listening
listening
listening
listening
listening
listening
listening
listening
listening
listening
listening
```

Node.js 的一个重要特性就是异步编程。这意味着 Node.js 中的代码可能不会按顺序执行。数据可能无法在固定的时间内确定。这意味着，要获得我们需要的所有数据，我们必须在获得数据时在应用程序周围传递数据。我们可以发出事件并在 Node.js 应用程序中处理它们。

当发出具有给定名称的事件时，如果侦听器被指定侦听具有该名称的事件，则该事件可以侦听侦听器。事件发射器函数被同步调用。事件侦听器代码是一个回调函数，它接受数据的参数并对其进行处理。Node.js 有一个`EventEmitter`类，可以通过我们创建的一个新类来扩展，以发出可以被事件监听器监听的事件。使用`EventEmitter`类，我们可以创建新的`EventEmitter`类来发出和监听事件。我们可以在一个事件上附加多个事件监听器，它们可以做不同的事情。此外，我们可以根据需要设置事件侦听器的最大数量。最后，我们可以选择异步处理事件，而不是同步。