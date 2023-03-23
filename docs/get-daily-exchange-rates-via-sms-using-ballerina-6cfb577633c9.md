# 使用芭蕾舞演员通过短信获取每日汇率

> 原文：<https://betterprogramming.pub/get-daily-exchange-rates-via-sms-using-ballerina-6cfb577633c9>

## 轻松掌握最新财务信息

![](img/cde7e23ecc1c0839b1e1a778e9ea578a.png)

由[玛加·桑托索](https://unsplash.com/@margabagus)在 [Unsplash](https://unsplash.com/s/photos/currency-exchange?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

# 介绍

我开发了一个使用芭蕾舞演员接收法定货币每日汇率的服务。它允许您订阅任何转换的汇率，并在每天早上收到订阅转换的短信。以下视频描述了该应用程序的功能:

应用程序功能

我将带你完成这个应用程序的实现，这样你就可以利用这种经验和 Ballerina 一起创建很酷的东西。好吧！让我们首先开始查看用例。

# 用例

该服务有两个 HTTP 端点。一个用于预订服务，另一个用于取消预订。订阅时，用户应该提供一个手机号码和要订阅的转换列表。当取消订阅时，用户只需提供带有 HTTP 请求的移动电话号码。该应用程序应该每天早上发送短信到每个订阅的移动电话号码与各自的转换。

# 背景

在尝试实现应用程序之前，我们应该了解芭蕾舞演员的基础知识。我们先来了解一下芭蕾中的`services``recurrent tasks`**`client objects`。Ballerina 中的这些构造对我们来说非常重要，因为我们需要每天早上定期通知订户，同时为订阅管理提供两个 HTTP 端点。**

# **芭蕾舞演员服务**

**Ballerina 中的一个服务声明表示一个可远程访问的资源方法的集合，这些方法附加到一个特定的侦听器上。在我们的用例中，服务应该包含订阅和取消订阅的两种方法，并且应该附加到一个 HTTP 侦听器，如下所示:**

**示例转换服务**

**下图描述了我们如何运行和测试端点。**

**芭蕾舞女演员提供了一堆对应各种交通工具的监听器。详细指南请参考此[示例](https://ballerina.io/learn/by-example/?utm_source=me&utm_medium=link&utm_campaign=me_link_smsballerinamalintha_220825)。**

# **芭蕾舞演员的任务**

**在上面的用例中，需要查找转换率并在每天早上通知订户。这可以在芭蕾舞中使用`[recurrent jobs](https://lib.ballerina.io/ballerina/task/2.2.2/functions#scheduleJobRecurByFrequency).`来完成**

**要创建周期性任务，需要提供类型`Job`的值。`Job`是一只`[object type](https://lib.ballerina.io/ballerina/task/2.2.2/objectTypes/Job)`。Ballerina 中的对象类型有点类似于 java 中的接口。一个对象类型可以用来定义一个`class` 使用类型包含类似于实现一个接口。**

**但是需要注意的是，芭蕾舞者不是 OOP 语言。芭蕾舞演员类型的系统是结构性的。它使用的概念叫做`shapes`。详细指南请参考[芭蕾舞演员类型系统](https://ballerina.io/why-ballerina/flexibly-typed/)。**

**Ballerina 中的类类型用于创建特定类类型的值(该类的对象实例)。在下面的代码段中，我定义了一个名为`MyJob`的类，它可以由调度程序作为一次性任务或循环任务来调度和执行。**

**示例作业**

**当创建类型为`MyJob`的值时，执行`init`方法，这类似于 java 类中的`constructor`。`execute`方法是上面提到的`task:Job` 对象类型中未实现的方法`execute`的实现。**

# **芭蕾舞女中的顾客物品**

**我们可以用`client objects`和芭蕾里的`remote services`互动。远程服务可以是在特定传输上可用的任何服务。例如，Ballerina 中的`http client`允许我们与 HTTP 远程服务进行交互。我们可以使用`http client`来检索两种货币的汇率，如下所示:**

**示例客户端对象**

**在上面的实现中，我们使用了`http client`来调用由[https://apilayer.com](https://apilayer.com)提供的[远程 HTTP 服务](https://apilayer.com/marketplace/exchangerates_data-api)。应该获取 API 密钥来调用端点。API 键可以在 Ballerina 中指定为可配置变量。示例参见[可配置变量](https://ballerina.io/learn/by-example/configurable-variables/?utm_source=me&utm_medium=link&utm_campaign=me_link_smsballerinamalintha_220825)。**

**因为我们只对结果感兴趣，所以可以改进上面的程序，用类型定义克隆响应。代码如下:**

**客户端对象的改进示例**

# **履行**

**现在，让我们看看如何使用我们在背景部分看到的内容来实现最初的需求。我们可以进一步改进`Job`类，如下所示:**

**用例的作业类**

**`Job`类应该维护一组每天早上必须向其发送消息的订户。此外，它还应该定义添加或删除订户的逻辑。`getRatesAndSendSMS()`方法负责最重要的任务，即从外部`HTTP`服务中检索汇率并通知用户。**

**现在，困难的部分已经完成了。让我们用这段代码改进一下我们的`/conversions` HTTP 远程服务:**

**用例的改进转换服务**

**服务对象初始化时调用的服务的`init`方法。在`init`方法中，我们创建了一个`Job`对象，并安排了一个每分钟执行一次的循环任务(一分钟模拟一天)。实现到此为止。**

**万岁！！！你可以试试这个，就像我在上面的视频中展示的那样。**

**在本文中，我们看到了 Ballerina 编程语言的优势，以及为集成场景编写应用程序是多么容易。**

**下一步是使用适当的身份验证和授权来部署这个应用程序。让我们在一篇新的文章中见面，我将讨论我们如何在 [Choreo](https://wso2.com/choreo/) 上改进和部署这个应用程序。**

**在[https://github.com/malinthar/daily_exchange_rates](https://github.com/malinthar/daily_exchange_rates)找到源代码。**

***感谢您的阅读！***

***如有任何疑问，请随时联系我！***