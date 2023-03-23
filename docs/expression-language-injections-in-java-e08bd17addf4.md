# Java 中的表达式语言注入

> 原文：<https://betterprogramming.pub/expression-language-injections-in-java-e08bd17addf4>

## 如何使用 CodeQL 检测 JEXL 进样

![](img/e3fd43000d46c20ef8bb7c76592fc556.png)

[科兹贾特](https://unsplash.com/@kozjat?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片。

在本文中，我将讨论一个用于检测 JEXL 表达式语言注入漏洞的 CodeQL 查询。

首先，我将简要概述表达式语言，特别是 JEXL。我还会解释什么是表达式语言注入，如何防范。然后，我将描述 CodeQL 查询是如何工作的。此外，我将展示通过查询发现的一些漏洞。

# 什么是表情语言？

表达式语言(EL)是一种通用编程语言，主要用于在运行时嵌入和计算表达式。大多数情况下，ELs 是解释性语言。换句话说，有一个解释器准备执行上下文并在其中运行表达式。根据特定的应用程序，开发人员、管理员甚至普通用户都可以出于各种目的创建表达式。嵌入式表达式通常有助于定制和配置应用程序。

EL 有多种实现方式。以下是几个例子:

*   Java 服务器页面(JSP)允许在许多标签中使用表达式。
*   Spring 框架提供了 [Spring 表达式语言](https://docs.spring.io/spring-framework/docs/3.2.x/spring-framework-reference/html/expressions.html) (SpEL)，这在 Spring 标签、配置和其他上下文中是允许的。
*   Apache Struts 允许在多种上下文中使用表达式。它使用[对象图形导航库](https://commons.apache.org/proper/commons-ognl/) (OGNL)。

通常，表达式语言提供许多简单的操作，如加法、乘法、字符串连接等等。除此之外，一些 EL 解释器允许您调用评估上下文中可用的对象上的方法。此外，一些解释器允许您访问 JVM 中的任何类并调用它们的方法。但这可能相当危险。

# JEXL 是什么？

[JEXL](https://commons.apache.org/proper/commons-jexl/) 代表 Java 表达式语言。这是一个提供用于评估 JEXL 表达式的 API 的库。该项目属于 Apache 软件基金会。

对我们来说，最有趣的事实是 JEXL 足够强大，可以让我们调用 JVM 中可用的类和方法。下面的代码调用`ProcessBuilder`来启动一个外部进程:

# 什么是表情语言注入？

如果应用程序允许表达式访问 JVM 中可用的类和方法，并且如果恶意用户可以向应用程序提供(或注入)任意表达式，则通常会导致任意代码执行。那叫表达式语言注入漏洞。

这种问题的影响通常很大，因为攻击者可以在 JVM 内部甚至外部运行几乎任何代码。成功攻击的可能性取决于攻击者如何注入恶意表达式。在最坏的情况下，应用程序从未经身份验证的远程用户接收的请求中读取并运行表达式(例如，使用 HTTP)。这通常会导致远程代码执行(RCE)。除此之外，应用程序可以从文件、数据库和其他地方获得表达式。

下面是一个易受攻击的代码示例。它只是从 HTTP 请求中获取一个表达式并立即运行它:

# 预防 JEXL 注射

首先，我建议在应用程序中引入表达式之前要三思。它们可能真的没有必要，可能有更好(或更简单)的设计。如果引入表达式是不可避免的，那么表达式应该在一个限制性的沙箱中运行。

幸运的是，JEXL 提供了两种实现沙箱的方法。第一种方法是使用`JexlSandbox`类。类别可让您指定允许或禁止从 JEXL 运算式呼叫哪些类别。确保将`false`传递给构造函数。这意味着所有方法调用都被禁止，除非它们被明确允许。这里有一个例子:

第二种方法是通过实现`JexlUberspect`接口来创建一个自定义沙箱。这个类的主要目的是自省，但是它也可以用于创建沙箱。这种方法更加灵活，因为我们不受`JexlSandbox`提供的 API 的限制。这里有一个例子:

# 用于检测 JEXL 注入的 CodeQL 查询

[CodeQL](https://securitylab.github.com/tools/codeql) 是一个代码分析引擎。它允许您为代码编写查询来检测各种问题，包括安全问题。让我们看看它如何帮助我们检测 JEXL 注射。

CodeQL 可以查找从源到我们指定的接收器的数据流。在 JEXL 注入的情况下，接收器是触发评估 JEXL 表达式的方法调用，比如`JexlExpression.evaluate()`、`JexlScript.execute()`和其他几个表达式。最有趣的数据源是远程用户可用的数据源(例如网络套接字和 HTTP 请求)。从远程数据源到其中一个 JEXL 接收器的数据流可能是一个潜在的 JEXL 注入。

我已经在一个 CodeQL 查询中实现了这个想法。让我们来看看它的主要组成部分。

为了搜索数据流，我编写了一个配置来跟踪从远程数据源到 JEXL 接收器的污染数据。它看起来是这样的:

它有三个主要部分:

*   `isSource()`谓词定义了数据源。这里，它使用了描述网络套接字、HTTP 请求和 CodeQL 核心库知道的其他远程数据源的`RemoteFlowSource`类。
*   `isSink()`谓词定义了数据接收器。这里，它使用了`JexlEvaluationSink`类，该类列出了触发评估 JEXL 表达式的方法调用。
*   `isAdditionalTaintStep()`谓词告诉 CodeQL 引擎被感染的输入可以通过其他方式传播。

让我们仔细看看`JexlEvaluationSink`:

它定义了两组可以触发评估 JEXL 表达式的方法调用:

*   立即计算表达式的方法。比如`JexlExpression.evaluate()`和`JexlEngine.getProperty()`。
*   表达式延迟求值的方法:`JexlExpression.callable()`和`JexlScript.callable()`。那些方法不会马上计算表达式。该查询假设稍后将调用触发评估的`Callable.call()`方法。

`isAdditionalTaintStep()`谓词定义了另外两种传播受感染数据的方式:

*   `TaintPropagatingJexlMethodCall`类定义了编译 JEXL 表达式、脚本和模板的方法。
*   `returnsDataFromBean()`谓词定义了对可能保存被污染数据的 beans 上的 getters 的调用。

最后，该查询检查是否使用了沙盒 JEXL 引擎。它在`isUnsafeEngine()`谓词和一个单独的数据流配置(`SandboxedJexlFlowConfig`)中实现，该配置检查是否已经为 JEXL 引擎设置了`JexlSandbox`或`JexlUberspect`。

当前查询有几个已知问题:

*   当数据存储在复杂的结构中时,`returnsDataFromBean()`谓词有助于识别问题。然而，它没有考虑数据在 bean 中是如何流动的。因此，它可能会导致误报。
*   如果查询检查沙箱是否是实现的属性就好了。不幸的是，我找不到一个可靠的解决方案。目前，这个查询不能确定一个`JexlUberspect`是否真的实现了一个沙箱，或者一个`JexlSandbox`是否过于宽松。相反，它只是假设沙箱得到了正确的实现。因此，它可能会导致假阴性。

该查询发现了几个有趣的问题。

# CVE-2021–3396:OpenNMS 中的 RCE

第一个发现是在 [OpenNMS/newts](https://github.com/OpenNMS/newts) 项目中的一个 RCE([CVE-2021–3396](https://nvd.nist.gov/vuln/detail/CVE-2021-3396))。服务器提供`/measurements/{resource}` [端点](https://github.com/OpenNMS/newts/blob/d706eb9e12783b31f4745c232a3cfa212474e68a/rest/src/main/java/org/opennms/newts/rest/MeasurementsResource.java#L62):

```
[@POST](http://twitter.com/POST)
[@Path](http://twitter.com/Path)("/{resource}")
[@Timed](http://twitter.com/Timed)
public Collection<Collection<MeasurementDTO>> getMeasurements(
        ResultDescriptorDTO descriptorDTO,
        [@PathParam](http://twitter.com/PathParam)("resource") Resource resource,
        [@QueryParam](http://twitter.com/QueryParam)("start") Optional<TimestampParam> start,
        [@QueryParam](http://twitter.com/QueryParam)("end") Optional<TimestampParam> end,
        [@QueryParam](http://twitter.com/QueryParam)("resolution") Optional<DurationParam> resolution,
        [@QueryParam](http://twitter.com/QueryParam)("context") Optional<String> contextId) {
```

端点接受带有许多参数的 POST 请求。大部分来自 URL 路径和查询字符串。除此之外，`getMeasurements()`方法接受了一个`ResultDescriptorDTO`的实例。这个参数没有任何注释，但是它来自于
POST 请求的主体。`ResultDescriptorDTO`类保存一个 JEXL
表达式数组。当端点被调用时，这些 JEXL 表达式会转到`JexlEngine`并立即执行。

引擎未配置沙箱。因此，远程用户可以在服务器上运行任意代码。该问题已在 newts 1.5.3 中通过[沙盒](https://github.com/OpenNMS/newts/pull/49) JEXL 表达式[修复。](https://www.opennms.com/en/blog/2021-02-16-cve-2021-3396-full-security-disclosure/)

# 特拉卡的 RCE

第二个发现是 Traccar 项目中的一个 [RCE。服务器有一个未记录的](https://github.com/traccar/traccar/issues/4624)[端点](https://github.com/OpenNMS/newts/blob/d706eb9e12783b31f4745c232a3cfa212474e68a/rest/src/main/java/org/opennms/newts/rest/MeasurementsResource.java#L62) ( `/attributes/computed/test`)，它接受一个设备 ID 和一个带有属性的结构:

```
[@POST](http://twitter.com/POST)
[@Path](http://twitter.com/Path)("test")
public Response test(
        [@QueryParam](http://twitter.com/QueryParam)("deviceId") long deviceId, Attribute entity) {
```

该属性有一个包含 JEXL 表达式的`expression`字段。表达式直接进入一个不使用沙箱的`JexlEngine`。此问题可能难以利用，因为攻击者必须是管理员才能访问端点。因此，项目维护人员没有把这个问题当回事。

该查询可以扩展到考虑更多的数据源，如文件。那肯定会发现更多的问题。然而，这样的问题并不有趣，因为攻击者很难利用它们。例如，Apache JMeter 不支持来自测试计划的 JEXL 表达式。实现沙箱被认为是一种安全[增强](https://bz.apache.org/bugzilla/show_bug.cgi?id=65151)。

# 参考

*   [表情语言注射(OWASP)](https://owasp.org/www-community/vulnerabilities/Expression_Language_Injection)
*   [阿帕奇 JEXL](https://commons.apache.org/proper/commons-jexl/)
*   [CodeQL 文档](https://codeql.github.com/docs/)