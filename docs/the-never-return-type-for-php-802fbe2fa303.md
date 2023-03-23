# PHP 的“永不”返回类型

> 原文：<https://betterprogramming.pub/the-never-return-type-for-php-802fbe2fa303>

## 这不是一个大的增加，但它是一个受欢迎的

![](img/8d33b84d6819abca256f6e438d47e796.png)

[engin akyurt](https://unsplash.com/@enginakyurt?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

哇，PHP 列车不停啊？最近有一个针对 PHP 语言本身的新功能的 RFC(征求意见稿)关闭了投票，它叫做 `[never](https://wiki.php.net/rfc/noreturn_type)`。

PHP 8.1 计划在今年年底发布，除了[纤程](/a-look-at-the-new-php-8-1-fibers-feature-979489399918)之外，新的`never`关键字将允许你将一个函数标记为“永不”返回。该函数将通过抛出一个异常、永远循环或者简单地用`die()`告诉 PHP 停止来停止正常的脚本执行。

# 使用新的“从不”

说明为什么`never`返回类型有意义的最好方法是用一个简单的例子来说明函数中的返回类型。不要过多考虑例子。这只是为了说明。

假设我们有一个类，`Credentials`，用户可以在他们的项目中扩展使用。这个类保存有意义的数据，并使其可用于其他类，比如连接到不同外部 API 的 HTTP 客户端。

如果我们将其扩展到包含敏感数据，我们可以避免意外泄露。要做到这一点，我们可以很容易地在`__toString()`方法中放入一个异常来阻止它的泄漏:

返回类型在这里没有多大意义。我们不仅*不会* 返回一个`string`，而且*永远不会*返回任何东西！我们总是不得不在某个地方记录它，因为 PHP 永远不会意识到代码中的这个中断。

多亏了新的 RFC，我们可以通过返回协方差来“覆盖”它，这在代码级别就很清楚了:这个函数*永远不会*返回任何东西——这就是新关键字的名字。

```
public function swim(): never
{
    throw new RuntimeError("Cats are very bad swimmers!")
}
```

你可能会说`void`也一样，其实不然。当你使用`void`时，你的意思是函数*返回空值*，所以脚本仍然可以继续正常执行，而`never`则不能。

对于永远不会返回任何东西的函数，你应该使用`never`(例如，当它们永远循环的时候):

# 这对我的应用程序意味着什么？

好吧，如果你正在使用一个函数，只有*为了方便起见*抛出一个异常或者它停止脚本执行，一旦 PHP 8.1 登陆你的服务器，你可以使用`never`。在野外很难看到无尽的循环，但如果你看到了，你也可以使用`never`。

一件很酷的事情是，如果一个*从未*返回的函数实际上返回了其他东西，基于 PHP 的 [pull 请求，PHP 将会恐慌。](https://github.com/php/php-src/pull/6761)

```
function thisWillError(): never
{
    return 'oops!';
}
```

如果您使用静态分析代码工具，如果您传递一个从不返回并期望正常执行的函数，您可能会捕捉到一个错误，因为捕捉和修复它会更容易。