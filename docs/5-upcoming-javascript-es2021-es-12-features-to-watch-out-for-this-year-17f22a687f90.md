# 今年值得关注的 5 个即将到来的 JavaScript ES2021 (ES 12)特性

> 原文：<https://betterprogramming.pub/5-upcoming-javascript-es2021-es-12-features-to-watch-out-for-this-year-17f22a687f90>

## 承诺。任何，WeakRef，和更多

![](img/33ae52f3069b59f0b76d05c0f86663cc.png)

照片由 [Kira auf der Heide](https://unsplash.com/@kadh?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

重要的是，本文开头要提到 ES2021 或 ES12 正处于该过程的第 4 阶段。这意味着它还没有发布。计划在 2021 年 6 月发布 ES12。尽管如此，我们可以看看它将带来的功能。

ES12 有什么新功能？

# 1.数字分隔符

处理大量数据会很快变得令人困惑。例如，考虑数字`92145723`。你要仔细看才知道是 9200 万什么的。

有了 ES2021 的新增功能，您可以重写相同的数字，如下所示:`92_145_723`。本质上，您可以使用下划线来提高可读性。已经比较好理解了。现在，你可以清楚地看到它是 9200 万左右。

因此，数字分隔符功能只是为了提高可读性。它不会对应用程序的性能产生正面或负面的影响。

```
// previous syntax before ES12
const number = 92145723;// new syntax coming with ES12
const number = 92_145_723;
```

# 2.String.prototype.replaceAll()

新的`replaceAll`方法没有带来突破性的变化，但它是一个小而好的补充。顾名思义，使用方法，可以替换字符串中的所有匹配项。

有一个例子总是更容易，所以让我们来看看实际使用的方法:

```
// replacing all occurrences of x with a
// jxvxscript becomes javascript
'jxvxscript'.replaceAll('x', 'a');
```

在`replaceAll`之前，您必须使用正则表达式替换所有出现的字符/单词。因此，这是一个受欢迎的附加功能，它允许你更容易、更快速地替换文本。

# 3.逻辑赋值运算符

使用新的逻辑赋值操作符(`&&=`、`||=`和`??=`)，您可以根据逻辑运算给变量赋值。也就是说，它结合了逻辑运算符和赋值表达式。

把它们想象成类似于`+=`、`-=`、`*=`和`/=`操作符。不要担心:一旦您看到每个操作符的一些例子，这将更有意义。

## And 和 equals (&&=)

`and and equals`运算符仅在左操作数为真时执行赋值。让我们看一个例子:

```
let first = 10;
let second = 15;first &&= second;
```

上面表达式的等价形式是`first && (first = second)`。如果很难掌握运算符，可以这样想:

```
let first = 10;
let second = 15;if (first) {
    first = second;
}
```

这段代码展示了如何在 ES2021 之前做同样的事情。让我们更深入地看看`first &&= second`是什么意思:

*   如果`first`为真，那么变量`second`被赋值给`first`。
*   否则，如果`first`是 falsy ( `false`、`0`、`-0`、`0n`、`""`、`null`、`undefined`和`NaN`，那么它什么也不做。`second`未分配给`first`。

## 或或等于(||=)

在`&&=`的相反频谱上，逻辑`OR`仅在左操作数为 falsy 时执行赋值。像往常一样，我们来看一个例子:

```
let first = null;
let second = 15;first ||= second; // first is 15 now
```

与上面的表达式等价的是`first || (first = second)`。这意味着只有当`first`为假值时，变量`first`才会被赋值`second`。如果变量`first`为真，则不执行赋值。

运行代码后，变量`first`将被赋予编号`15`。如果将`let first = null`替换为`let first = 10`，则分配不会发生。变量`first`保持`10`。

带有`if`语句的等效代码如下:

```
let first = null;
let second = 15;if (!first) {
    first = second;
}
```

## 问题问题等于(？？=)

与[无效合并运算符](https://catalins.tech/nullish-coalescing-operator-in-javascript-what-is-it-and-how-to-use-it)类似，只有当左操作数为`nullish`或`undefined`时才执行赋值:

```
let first = null;
let second = 15;first ??= second;
```

`first ?? (first = second)`相当于上面的表达式。只有当`first`为`null`或`undefined`时，`first`变量才会被赋予`second`变量。在这个例子中，`first`就是`15`。如果我们用`let first = 20`替换`let first = null`，赋值就不会发生。变量`first`保持`20`。

带有`if`语句的等效代码如下:

```
let first = null;
let second = 15;if (first == null || first == undefined) {
    first = second;
}
```

对于这个操作符，重要的是要注意它不检查其他假值。它只检查`null`和`undefined`，就像 Nullish 合并运算符一样。也就是说，无论何时当你使用这些操作符时，看看带有`if`语句的替代代码。在你使用一段时间后，你会习惯的。

# 4.承诺。任何

我们有了一个新的承诺方法:`Promise.any()`。新方法接受多个承诺，并在任何承诺被解析后进行解析。`Promise.any()`取其先定，故名:`any`。

```
try {
    const firstPromiseResolved = Promise.any(promisesArray);// do more work with the first promise resolved
catch(e) {
   // catch the error
}
```

另一方面，如果没有承诺解决，`Promise.any()`抛出一个`AggregateError`异常。如果所有承诺都被拒绝，它还会说明拒绝的原因。这就是`Promise.any`的全部内容。请随意使用它！

# 5.WeakRef

是弱引用的简写，它的主要用途是保存对另一个对象的弱引用。这意味着它不会阻止垃圾收集器收集对象。当我们不想将对象永远保存在内存中时，弱引用是有用的。

但是为什么我们首先需要这个`WeakRef`？在 JavaScript 中，只要存在对该对象的引用，垃圾收集器就不会收集该对象。因此，它将对象保存在内存中，这样会减少内存。`WeakRef`实现允许您避免这种情况。

可以使用`new WeakRef`创建一个弱引用，通过调用`deref()`方法可以读取一个引用。一个简单的例子是:

请注意，这个示例只是为了展示如何访问和读取弱引用。

使用时要小心。尽管`WeakRef`在某些情况下可能有用，但 [TC39 提案](https://github.com/tc39/proposal-weakrefs#a-note-of-caution)建议尽可能避免使用它。

# 结论

在本文中，您了解了 ES12 中 JavaScript 的新特性。更多信息，请关注 [ECMAScript 完成提案](https://github.com/tc39/proposals/blob/master/finished-proposals.md)。

*如果你对 JavaScript 教程感兴趣，我推荐* [*前端高手*](https://catalins.tech/frontend-masters-membership-is-it-worth-it) *！*

*如果你想用技术写作赚钱，就去查查* [*那些付钱让你写*](https://catalins.tech/websites-that-pay-you-to-write-technical-articles) *技术文章的网站吧！*

*如果你想学习 JavaScript，我推荐这些* [*5 资源作为初学者学习 JavaScript*](https://catalins.tech/5-best-resources-to-learn-javascript-as-a-beginner)*！*

*谈判你的工资是必不可少的——学习* [*作为一名开发者如何谈判你的工资*](https://catalins.tech/how-to-negotiate-your-salary-as-a-developer) *！*

*用* [*加速你的开发 Git 别名*](https://catalins.tech/git-aliases-what-are-they-and-how-to-use-them) *。*

*如果你想* [*以开发者的身份开博*](https://catalins.tech/how-to-start-your-blog-as-a-developer) *，我推荐你阅读《* [*如何以开发者的身份开博*](https://catalins.tech/how-to-start-your-blog-as-a-developer) *》一文！*

你是否很难跟上科技领域的最新消息？参见 [*作为开发者保持最新状态的一种方法*](https://catalins.tech/one-way-to-stay-up-to-date-as-developer) *！*

*学习* [*如何在 JavaScript*](https://catalins.tech/how-to-use-asyncawait-in-javascript) *中使用 Async/Await！*

GitHub 简介目前风靡一时。了解 [*如何创建 GitHub 个人资料页面*](https://catalins.tech/how-to-create-a-kickass-github-profile-page) *！*

*查看这 7 个* [*资源，帮你通过求职面试*](https://catalins.tech/7-github-repositories-to-help-you-crush-your-job-interviews) *！*

*查看*[*JavaScript ECMAScript 2021 es 2021*](https://catalins.tech/javascript-es2021-you-need-to-see-these-ecmascript-2021-features)*即将推出的新功能！*

*你是初学程序员吗？查看这些* [*编程项目思路适合初学者*](https://catalins.tech/10-programming-project-ideas-for-beginners) *！*

你是在学习编码还是打算做编码？查看 [*免费学习编程的最佳去处*](https://catalins.tech/20-best-places-to-learn-programming-for-free) *！*

[*用这 9 个浏览器扩展提高你的开发者生产力*](https://catalins.tech/my-9-must-have-browser-extensions-for-increased-developer-productivity) *！*

*如果你是 Node.js 的开发者，我建议你去 Node.js* *查看这些* [*4 个创造性的设计模式！*](https://catalins.tech/the-4-creational-design-patterns-in-nodejs-you-should-know)

*查看这些惊人的*[*JavaScript ECMAScript 2020 特性*](https://catalins.tech/javascript-es2020-the-features-you-should-know) *！*