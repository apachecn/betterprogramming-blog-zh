# 初学者技巧:异步/等待

> 原文：<https://betterprogramming.pub/beginner-tips-async-await-b4ee13e71f7f>

## 异步 JavaScript 入门

![](img/f30fa29722c05a3a6a1d8bd88e6bc4d5.png)

图为[陶黎黄](https://unsplash.com/@h4x0r3?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

作为一名 Flatiron 学生，我的一个项目涉及制作一个单页面应用程序，该应用程序利用 Rails API 后端和 JavaScript、CSS 和 HTML 前端。我最终制作了一个(简化)版本的韩国卡牌游戏 Hwatu，在日语中也被称为 Hanafuda。在项目开始时，我一点也不知道我很快就会一头扎进 async/await 的世界。希望我从奋斗中获得的知识对未来的学生和有抱负的软件工程师有用。

# 什么是异步/等待？

Async 只是承诺的语法糖。如果你不确定什么是承诺，这里有一篇非常有用的文章。您可以通过在函数前添加`async`来创建一个`async`函数。

```
const someAsyncFunction = async () => {
  console.log('Hello!');
};
```

将`async`添加到函数中后，您就可以使用`await`关键字来等待一个履行的承诺，然后再继续使用该函数。请注意，`await`必须加在返回承诺的函数之前(换句话说，另一个异步函数)。

# Async 不能与一起使用。为每一个

我遇到的第一个问题是将`async`与`.forEach`一起使用。不幸的是，我花了一段时间才意识到它们并不兼容。这是因为`.forEach`只调用一个函数，这意味着它不会等待函数结束就继续下一次迭代。有几个选择，当谈到补救这一点。一种选择是使用`.reduce`。

`.reduce`允许你遍历一个数组，在给定的元素上调用一个`async`函数，等待解析，然后继续迭代。

# Await 不会阻塞执行堆栈

`await`只暂停使用它的函数中的代码。它不会一直阻塞调用堆栈。这是什么意思？让我们通过重构第一个代码块来看看。

输出顺序让你吃惊吗？这是因为在`sayHelloAndGreet()`中调用`greeting()`之前没有使用`await`。因为没有`await` , `sayHelloAndGreet()`只是简单地调用`greeting()`却不等待它履行承诺。

操作顺序可以理解如下:

1.  该函数通过调用`await sayHello()`开始。这告诉 JavaScript 等待`sayHello()`实现它的承诺，然后继续执行其余的代码。
2.  当`sayHello()`开始时，它首先执行`await timeout(4000)`。四秒钟后，它打印出“你好！”，兑现承诺。
3.  现在`sayHello()`已经完成了，`sayHelloAndGreet()`可以继续执行代码了。下一行调用 `greeting()`。但是因为`greeting()`之前没有`await`，所以不会等待`greeting()`完成，它把`greeting()`解释为`Promise {pending}`。(记住这并不意味着`greeting()`停止执行！)
4.  `sayHelloAndGreet()`中的一切完成后，执行下面的`.then`，打印出“打招呼和问候完毕！”。
5.  然而，`greeting()`仍然在后台执行。一旦完成，它就会打印“你好吗？”到控制台。