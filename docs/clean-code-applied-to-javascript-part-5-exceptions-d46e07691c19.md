# 应用于 JavaScript 的干净代码:第 5 部分—异常

> 原文：<https://betterprogramming.pub/clean-code-applied-to-javascript-part-5-exceptions-d46e07691c19>

## 关于编写和维护干净代码的惊人好处的系列文章

![](img/1db26b1e844d92b3e0525f55bacfd3a7.png)

尼克·麦克米伦在 [Unsplash](https://unsplash.com/s/photos/team?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

本文是深入研究干净代码主题(应用于 JavaScript)的系列文章中的第四篇。

在本系列中，我们将讨论每个程序员都应该知道的关于干净代码的经典技巧，并将它们应用于特定的 JavaScript/TypeScript 语言。

*   [第 1 部分:开始之前](https://medium.com/better-programming/clean-code-applied-to-javascript-part-1-9f3badd5715)
*   [第二部分:变量](https://medium.com/better-programming/clean-code-applied-to-javascript-part-ii-variables-8302f01e539c)
*   [第 3 部分:功能](https://medium.com/@ccaballero/clean-code-applied-to-javascript-part-3-functions-f9f26b221736)
*   [第 4 部分:注释](https://medium.com/better-programming/clean-code-applied-to-javascript-part-4-3f6db21ee4e5)
*   [第 5 部分:例外情况](https://medium.com/better-programming/clean-code-applied-to-javascript-part-5-exceptions-d46e07691c19)
*   [第 6 部分:避免条件复杂性](https://medium.com/better-programming/clean-code-applied-to-javascript-part-6-avoid-conditional-complexity-5ee9cbb1b26a)

# 介绍

异常是高质量软件开发中的一个重要部分，因为我们将控制意想不到的或未实现的情况。

因此，开发人员有时会混淆错误处理和软件流处理。异常应该被用来处理我们软件中不受控制或发展的情况，而不是作为一种模拟我们的业务逻辑回归的方式，以从一个方向或另一个方向导出软件流。

在这篇文章中，我们将提供一些与处理异常相关的建议，让你的代码在使用异常时保持整洁

# 首选异常，而不是返回错误代码

当编程语言有异常处理时，使用异常而不是错误代码。

这种说法似乎是显而易见的，但事实并非如此，因为许多程序员学习的编程语言缺乏这一特性，或者没有看到它的潜力，并忽略了它的用途。然而，使用异常会产生比在代码本身中管理错误代码更干净的代码。

下面的代码显示了一个没有使用异常的类，并且必须通过`if`语句手动执行不受控制的情况的管理。相反，我们必须通过异常将所有这些单调乏味的任务委托给语言。

观察第二段代码，其中业务逻辑已经与错误管理分离。该代码具有以下优点:

1.非耦合的业务逻辑和错误控制。它们是需要解决的两个不同的问题，必须分开，区别对待。
2。减少代码的冗长，使其更易于阅读。
3。错误代码的责任已经委托给编程语言，它必须为我们服务，而不是相反。

```
class Laptop {
 sendShutDown() {
   const deviceID = getID(DEVICE_LAPTOP);
   if (deviceID !== DEVICE_STATUS.INVALID) {
     const laptop = DB.findOne(deviceID); 
     if (laptop.getStatus() !== DEVICE_SUSPENDED) {
       pauseDevice(deviceID);
       clearDeviceWorkQueue(deviceID);
       closeDevice(deviceID);
     } else {
       logger.log(‘Device suspended. Unable to shut down’);
     }
    } else {
     logger.log(‘Invalid handle for: ‘ + DEVICE_LAPTOP.toString());
    }
  }
}// Clean
/* 
 The code is better because the algorithm 
 and error handling, are now separated. 
*/
class Laptop {
  sendShutDown() {
    try {
      tryToShutDown();
    } catch (error) {
      logger.log(error);
    }
  }
  tryToShutDown() {
    const deviceID = getID(DEVICE_LAPTOP);
    const laptop = DB.findOne(deviceID);
    pauseDevice(deviceID);
    clearDeviceWorkQueue(deviceID);
    closeDevice(deviceID);
  }

  getID(deviceID) {
    throw new DeviceShutDownError(‘Invalid handle for: ‘ +   deviceID.toString());
   }
}
```

# 不要忽略捕获的错误

## **请不要做鸵鸟术**

鸵鸟技术包括将你的头藏在地下。这就是我们每次出错时所做的，我们什么也不做。

非常重要的一点是，你要知道对错误做`console.log`或`system.out.println`不是做某事。事实上，这更危险，因为在我们做这种错误控制的情况下，当异常发生时，我们会看到它出现。因此，不要忽视异常的管理。异常是由意外情况引起的，必须正确处理。

第一段代码演示了初级程序员或应用鸵鸟技术的程序员的通常处理方式——这很容易，因为错误已经停止中断应用程序。但真正应该做的是第二个例子，在这个例子中我们做出了正确的处理。当然，我知道做一个错误处理需要时间和精力。

```
try {
 functionThatMightThrow();
} catch (error) {
 console.log(error);
}try {
 functionThatMightThrow();
} catch (error){
 console.error(error);
 notifyUserOfError(error);
 reportErrorToService(error);
}
```

# 不要忽视被拒绝的承诺

就像之前我们忽略错误处理的情况一样，在 JavaScript 中我们有异步，我们必须处理异步的工具之一是承诺。

承诺可以被拒绝(并不是说它本身是一个错误)，因此，我们必须像对待错误一样对待它们。

在这种情况下，我们看到与前一种情况相同的例子，但适用于承诺。

```
getData() 
 .then(data => functionThatMightThrow(data))
 .catch(error => console.log);getData()
   .then(data => functionThatMightThrow(data))
   .catch(error => {
     console.log(error);
     notifyUserOfError(error);
     reportErrorToService(error);
   }); 
```

# 例外层次结构

创建例外的层次结构。每种编程语言都有一套自己的底层异常:`NullPointerException`或
`ArrayIndexOutOfBoundsException`。

这些例外没有谈到我们的业务逻辑，也没有给我们任何东西。使用这些异常来控制代码中出现的错误是没有意义的，因为我们的代码正在对业务逻辑进行建模。

因此，我们必须创建自己的异常层次结构，它代表我们的业务逻辑，并在业务逻辑中出现意外情况时触发。

在下面的例子中，创建了两个名为`UserException`和`AdminException`的异常。这些异常发生在两种类型的用户上，但不再发生在数据结构上。现在我们有了业务逻辑——事实上，这两个异常太普通了，我们可以定义如下类型的异常:`UserRepeatException`、`UserNotFoundException`等。

我们对异常的语义价值做出了贡献，这是我们无法获得的。

```
export class UserException extends Error {
  constructor(message) {
    super(`User: ${mesage}`);
  }
}export class AdminException extends Error {
 constructor(message) {
   super(`Admin: ${message}`);
 }
}// Client code
const id = 1;
const user = this.users.find({ id });
if(user){
 throw new UserException(‘This user already exists’);
}
```

# 提供带有异常的上下文

虽然异常有一个堆栈跟踪，允许我们在异常发生时看到链调用，但理解起来很复杂。

因此，向异常添加上下文来改进这个特性。通常，会添加一条消息，解释我们软件中失败的操作的意图。请不要使用人类无法理解的代码。

应该注意的是，我们提供的信息不应该是最终用户看到的。我们应该适当地管理这个异常，这样这些代码就不会显示在用户界面上，而是对用户更有用的东西。

如果我们开发一个异常的层次结构，我们将为异常提供上下文。

# 结论

在本文中，我们提出了一些创建异常的建议。

异常是高质量软件开发中的一个基本部分，但是在许多情况下，它们被忽略或者只是被错误地保存，从而重定向了应用程序的流程。

在任何情况下，如果编程语言提供了这个特性，我们就必须利用它，并把它委托给专注于业务逻辑的语言。

最后，我们讨论的要点如下:

*   优先选择异常，而不是返回错误代码
*   不要忽略捕获的错误
*   不要忽视被拒绝的承诺
*   使用例外层次结构
*   提供带有异常的上下文