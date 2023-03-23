# 在 Node.js 中使用事件(第 2 部分)

> 原文：<https://betterprogramming.pub/using-events-in-node-js-part-2-50d26d817b26>

## 如何附加和移除事件侦听器

![](img/fb3dfdce259ae6f2c9ed2a244069bcbd.png)

由 [Pablo Heimplatz](https://unsplash.com/@pabloheimplatz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

如果你还没有阅读第一部分，我建议你在继续阅读之前先阅读一下。

[Node.js](https://nodejs.org/) 的核心特性是异步编程。这意味着 Node.js 中的代码可能不会按顺序执行。因此，数据可能无法在固定的时间内确定。

这意味着，为了获得我们需要的所有数据，我们必须在获得数据时在应用程序中传递数据。这可以通过在 Node.js 应用程序中发出、侦听和处理事件来实现。

当发出具有给定名称的事件时，如果侦听器被指定侦听具有该名称的事件，则该事件可以侦听侦听器。事件发射器函数被同步调用。事件侦听器代码是一个回调函数，它接受数据的参数并对其进行处理。

Node.js 有一个`EventEmitter`类，可以通过我们创建的一个新类来扩展，以发出可以被事件监听器监听的事件。在本文中，我们从第 1 部分继续。

在第 1 部分中，我们向您展示了如何定义`EventEmitters`，附加事件监听器，并使用`once`和`removeListener`函数控制它们何时监听。

在本文中，我们将从第 1 部分继续，探索可用于控制事件发出和侦听方式的其他函数。`EventEmitter`也有许多 getter 函数，用于获取我们定义的`EventEmitter`的数据。

为了获得被监听事件的名称，我们可以使用`eventNames`函数。该函数不带参数，返回一个事件标识符数组，其中可能包含字符串或符号。

例如，我们可以在下面的代码中使用它:

```
const EventEmitter = require('events');
const eventEmitter = new EventEmitter();
eventEmitter.on('event1', () => {});
eventEmitter.on('event2', () => {});const sym = Symbol('event3');
eventEmitter.on(sym, () => {});console.log(eventEmitter.eventNames());
```

当我们运行上面的代码时，我们记录了以下内容:

```
[ 'event1', 'event2', Symbol(event3) ]
```

为了获得可以附加到单个事件的最大数量的侦听器，我们可以使用`getMaxListeners`函数。它不接受任何参数，并返回当前可附加到一个事件的最大侦听器数量。

可以附加到事件的事件监听器的最大数量可以由`setMaxListeners`函数设置，默认值是 10。例如，我们可以在下面的示例中使用它:

```
const EventEmitter = require('events');
const eventEmitter = new EventEmitter();
console.log(eventEmitter.getMaxListeners());
```

我们可以使用`listenerCount`函数来获取附加到一个事件的事件侦听器的数量。它采用一个字符串或符号参数作为事件名称，并返回一个整数，其中包含附加到事件的事件侦听器的数量，事件名称传递到参数中。

例如，我们可以在下面的代码中使用它:

```
const EventEmitter = require('events');
const eventEmitter = new EventEmitter();for (let i = 1; i <= 5; i++) {
  eventEmitter.on('event', () => { });
}console.log(eventEmitter.listenerCount('event'));
```

如果运行上面的代码，我们会得到 5 个日志，因为我们将 5 个事件侦听器附加到了`event`事件。

要将事件监听器函数附加到给定的事件，我们可以使用`listeners`函数。它接受带有事件名称的字符串或符号，并返回事件侦听器函数的数组。

它返回侦听器的副本，而不是原始副本。例如，我们可以在下面的例子中使用它:

```
[ '() => console.log(`Listener ${i} for \'event\' event invoked`)',
  '() => console.log(`Listener ${i} for \'event\' event invoked`)',
  '() => console.log(`Listener ${i} for \'event\' event invoked`)',
  '() => console.log(`Listener ${i} for \'event\' event invoked`)',
  '() => console.log(`Listener ${i} for \'event\' event invoked`)' ]
```

要将一个事件监听器附加到给定事件的数组监听器的开头，我们可以使用`prependListener`函数。

该函数将事件名称作为第一个参数，将事件侦听器函数作为第二个参数。例如，我们可以编写以下代码:

```
const EventEmitter = require('events');
const eventEmitter = new EventEmitter();for (let i = 1; i <= 5; i++) {
  eventEmitter.on('event', () => console.log(`Listener ${i} for 'event' event invoked`));
}eventEmitter.prependListener('event', () => console.log('Prepended listener invoked'))console.log(eventEmitter.listeners('event').map(f => f.toString()));
```

如果我们运行上面的代码，我们会记录以下内容:

```
[ '() => console.log(\'Prepended listener invoked\')',
  '() => console.log(`Listener ${i} for \'event\' event invoked`)',
  '() => console.log(`Listener ${i} for \'event\' event invoked`)',
  '() => console.log(`Listener ${i} for \'event\' event invoked`)',
  '() => console.log(`Listener ${i} for \'event\' event invoked`)',
  '() => console.log(`Listener ${i} for \'event\' event invoked`)' ]
```

正如我们所看到的，预先考虑的事件侦听器在数组的第一个槽中。如果我们添加`eventEmitter.emit('event')`来发出`event`事件，如下面的代码所示:

```
const EventEmitter = require('events');
const eventEmitter = new EventEmitter();for (let i = 1; i <= 5; i++) {
  eventEmitter.on('event', () => console.log(`Listener ${i} for 'event' event invoked`));
}eventEmitter.prependListener('event', () => console.log('Prepended listener invoked'))console.log(eventEmitter.listeners('event').map(f => f.toString()));
```

然后，我们用`console.log`语句记录了以下内容:

```
[ '() => console.log(\'Prepended listener invoked\')',
  '() => console.log(`Listener ${i} for \'event\' event invoked`)',
  '() => console.log(`Listener ${i} for \'event\' event invoked`)',
  '() => console.log(`Listener ${i} for \'event\' event invoked`)',
  '() => console.log(`Listener ${i} for \'event\' event invoked`)',
  '() => console.log(`Listener ${i} for \'event\' event invoked`)' ]
Prepended listener invoked
Listener 1 for 'event' event invoked
Listener 2 for 'event' event invoked
Listener 3 for 'event' event invoked
Listener 4 for 'event' event invoked
Listener 5 for 'event' event invoked
```

我们还看到，我们添加到事件侦听器数组中的侦听器首先被调用，因为侦听器的处理顺序与它们添加到数组中的顺序相同。

如果我们想预先设置一个只处理一次发出事件的事件监听器，我们可以使用`prependOnceListener`来代替。例如，我们可以在下面的代码中使用它:

```
const EventEmitter = require('events');
const eventEmitter = new EventEmitter();
for (let i = 1; i <= 5; i++) {
  eventEmitter.on('event', () => console.log(`Listener ${i} for 'event' event invoked`));
}
eventEmitter.prependOnceListener('event', () => console.log('Prepended once listener invoked'))eventEmitter.emit('event');
eventEmitter.emit('event');
```

如果我们运行上面的代码，我们可以看到在`console.log`语句中记录了以下内容:

```
Prepended once listener invoked
Listener 1 for 'event' event invoked
Listener 2 for 'event' event invoked
Listener 3 for 'event' event invoked
Listener 4 for 'event' event invoked
Listener 5 for 'event' event invoked
Listener 1 for 'event' event invoked
Listener 2 for 'event' event invoked
Listener 3 for 'event' event invoked
Listener 4 for 'event' event invoked
Listener 5 for 'event' event invoked
```

正如我们所看到的，我们用`prependOnceListener`前置的监听器只运行一次。如果我们用`prependOnceListener`函数附加一个`event`监听器，这就是我们所期望的。

如果我们想要删除给定事件的所有事件监听器，我们可以使用`removeAllListeners`函数。它采用一个字符串或符号参数作为事件标识符。

注意，当在程序的其他组件中创建`EventEmitter`时，删除附加在代码中其他地方的事件监听器是一种不好的做法。它返回一个对`EventEmitter`的引用，这样调用就可以被链接起来。

例如，我们可以编写以下代码来删除`event`事件的所有事件侦听器:

```
const EventEmitter = require('events');
const eventEmitter = new EventEmitter();
for (let i = 1; i <= 5; i++) {
  eventEmitter.on('event', () => console.log(`Listener ${i} for 'event' event invoked`));
}
eventEmitter.prependOnceListener('event', () => console.log('Prepended once listener invoked'))console.log('Before remove all listeners\n', eventEmitter.listeners('event').map(f => f.toString()));eventEmitter.removeAllListeners('event');console.log('After remove all listeners\n', eventEmitter.listeners('event'));
```

当上面的代码运行时，我们记录了以下内容:

```
Before remove all listeners
 [ '() => console.log(\'Prepended once listener invoked\')',
  '() => console.log(`Listener ${i} for \'event\' event invoked`)',
  '() => console.log(`Listener ${i} for \'event\' event invoked`)',
  '() => console.log(`Listener ${i} for \'event\' event invoked`)',
  '() => console.log(`Listener ${i} for \'event\' event invoked`)',
  '() => console.log(`Listener ${i} for \'event\' event invoked`)' ]
After remove all listeners
 []
```

要删除给定事件的单个事件监听器，我们可以使用`removeListener`函数。它接受一个带有事件标识符的字符串或符号的参数，以及一个对附加到给定事件的侦听器的引用。

它返回一个对`EventEmitter`的引用，这样调用就可以被链接起来。我们可以在下面的代码中使用它:

```
const EventEmitter = require('events');
const eventEmitter = new EventEmitter();const listener1 = () => console.log('listener1 invoked');
const listener2 = () => console.log('listener2 invoked');eventEmitter.on('event', listener1);
eventEmitter.on('event', listener2);console.log('Before remove listeners\n', eventEmitter.listeners('event').length);eventEmitter
  .removeListener('event', listener1)
  .removeListener('event', listener2);console.log('After remove listeners\n', eventEmitter.listeners('event').length);
```

当我们运行上面的代码时，我们用`console.log`语句记录了以下内容:

```
Before remove listeners
 2
After remove listeners
 0
```

正如我们所见，`removeListener`可以被链接起来，让我们一次删除一个监听器。被删除的侦听器不再侦听事件。

如果一个侦听器被多次附加到一个事件，那么它可能被称为事件侦听器被附加以移除所有事件侦听器的次数。

如果在这个场景中调用一次`removeListener`函数，它将删除最近添加的事件监听器。移除事件侦听器后，将更新给定事件的事件侦听器数组。

为了获得一个事件的事件监听器数组，包括包装器，我们可以使用`rawListeners`函数。例如，我们可以将其用作以下代码:

```
const EventEmitter = require('events');
const eventEmitter = new EventEmitter();const listener1 = () => console.log('listener1 invoked');
const listener2 = () => console.log('listener2 invoked');eventEmitter.once('event', listener1);
eventEmitter.on('event', listener2);const rawListeners = eventEmitter.rawListeners('event');
rawListeners[0].listener();
```

如果我们运行上面的代码，我们会看到`listener1 invoked`被记录。这是因为`eventEmitter.once`包装了传递给它的监听器函数。

这将与实际的事件侦听器一起返回。因此，我们可以通过运行`rawListeners[0].listener();`来运行实际的事件监听器函数。

这对第二个侦听器不起作用，因为当我们调用`eventEmitter.on`来附加一个侦听器时，没有包装器来包装侦听器函数。

Node.js 的一个重要特性就是异步编程。这意味着 Node.js 中的代码可能不会按顺序执行。因此，数据可能无法在固定的时间内确定。

这意味着，为了获得我们需要的所有数据，我们必须在获得数据时在应用程序中传递数据。我们可以发出事件并在 Node.js 应用程序中处理它们。

当发出具有给定名称的事件时，如果侦听器被指定侦听具有该名称的事件，则该事件可以侦听侦听器。事件发射器函数被同步调用。

事件侦听器代码是一个回调函数，它接受数据的参数并对其进行处理。Node.js 有一个`EventEmitter`类，可以通过我们创建的一个新类来扩展，以发出可以被事件监听器监听的事件。

使用`EventEmitter`类，我们可以添加和删除带有内置函数的事件监听器。我们可以同时或一次移除一个。

然而，我们不应该从不是我们编写的事件发射器中移除监听器，因为这会给其他正在编写代码的人造成混乱，因为其他程序员不希望从其他模块中移除内置监听器。

# 资源

*   [使用 Node.js 中的事件(第 1 部分)](https://medium.com/better-programming/using-events-in-node-js-the-basics-9a9c44dcbe30)