# 对 Selenium 测试框架的一个简单添加，使其更加健壮和可靠

> 原文：<https://betterprogramming.pub/a-simple-addition-to-your-selenium-test-framework-that-makes-it-more-robust-and-reliable-e9cf97f52e78>

## 仅仅通过用我们自己的版本包装 selenium execute 函数，您就可以使您的测试框架更加健壮，测试更加可靠

![](img/990402e87718076367efda80528b6293.png)

由 [Unsplash](https://unsplash.com/@theshubhamdhage?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的 [Shubham Dhage](https://unsplash.com/@theshubhamdhage?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

尽管 selenium 目前在 UI 测试自动化和其他与浏览器相关的自动化工作中非常流行，但它相当不可靠，并且众所周知在与 DOM 交互时会抛出各种错误。

引发的常见异常包括:

```
ElementNotInteractableException
NoSuchWindowException
NoSuchElementException
NoSuchFrameException
NoSuchAttributeException
InvalidElementStateException
ElementNotVisibleException
```

当我们第一次遇到这些错误时，我们的解决方案是用显式等待来包装 selenium 方法。

虽然这种解决方案是可以接受的，但我认为在代码中到处都有太多显式等待确实会使代码库变得臃肿和难看。

因此，在本文中，我想建议一种非常简单的重试机制，它可以嵌入到您的 selenium 调用中，可以处理大多数错误，而不需要在代码中到处进行显式等待。

# 为什么重试会起作用？

在我们使用显式等待之前，我们应该考虑使用浏览器的随机性。意想不到的弹出窗口、资源没有完全加载、元素与其他元素重叠并阻碍交互等等，都会导致很多问题。

事实证明，对同一调用的简单重试在处理这种随机性方面惊人地强大和有效，如果在此之后测试仍然失败，您可以确定您正在处理非常极端的情况

# 让我们跳到解决方案

我将用 Python 来演示这个解决方案，但是你可以随意用任何你想要的语言来实现它。它的核心功能是 selenium 中的[执行](https://github.com/SeleniumHQ/selenium/blob/ff19ab8bdcffc93f7b3e07294833e4f074dc0236/py/selenium/webdriver/remote/webdriver.py#L410)功能，我们将用我们自己的版本包装它。

首先定义您希望在重试逻辑中处理的 selenium 异常

我已经编译了这个异常列表，因为其中大部分可以归因于我们讨论过的浏览器随机性，我们的重试逻辑应该处理这些。

如果你想看看 selenium 异常的完整列表，你可以点击[这里](https://www.selenium.dev/selenium/docs/api/py/common/selenium.common.exceptions.html#module-selenium.common.exceptions)。

接下来，我们将编写自己的执行函数

这个函数所做的就是引用 selenium 的 to [execute](https://github.com/SeleniumHQ/selenium/blob/ff19ab8bdcffc93f7b3e07294833e4f074dc0236/py/selenium/webdriver/remote/webdriver.py#L410) 函数并运行它，但是为上面的异常添加了一个处理程序，它只是在 1 秒钟的短暂延迟后再次运行 execute。就是这样！

现在，我们如何告诉 selenium 使用这个执行版本而不是它自己的版本呢？

就像我们用自己的 execute 版本(即`selenium_execute_with_retry`)包装 selenium execute 函数一样，我们将使用自己的版本`selenium_wrapper`包装整个 selenium，它只是用我们的版本替换了 execute 函数。

为了确保我们只在香草硒上这样做，我添加了一个`_patched`属性来表示这是我们的补丁硒而不是香草硒。

现在，要使用我们的 selenium 补丁版本，只需像平常一样实例化浏览器，并将其传递给包装器。

第 2 行产生的 browser 对象现在将拥有我们版本的 execute 函数，而不是普通版本。

# 结论

在开发我自己的自动化框架的这些年里，我现在在我所有的框架中使用这个解决方案来增加额外的一层健壮性，并过滤掉随机和奇怪的失败。事实证明这只是一次简单的重试。

## 资源

[](https://github.com/SeleniumHQ/selenium/blob/ff19ab8bdcffc93f7b3e07294833e4f074dc0236/py/selenium/webdriver/remote/webdriver.py#L410) [## selenium/web driver . py at ff 19 ab 8 BD cffc 93 f 7 B3 e 07294833 E4 f 074 DC 0236 selenium HQ/selenium

### 此文件包含双向 Unicode 文本，其解释或编译可能与下面显示的不同…

github.com](https://github.com/SeleniumHQ/selenium/blob/ff19ab8bdcffc93f7b3e07294833e4f074dc0236/py/selenium/webdriver/remote/webdriver.py#L410) 

```
**Want to Connect?**If you are into api test automation. Checkout my python course using Tavern-CI on udemy at [https://www.udemy.com/share/103Pqi/](https://www.udemy.com/share/103Pqi/)
```