# 如何屏蔽 NLog 中的敏感数据

> 原文：<https://betterprogramming.pub/how-to-mask-sensitive-data-in-nlog-12a368f13f61>

## 使日志文件中的用户配置文件数据更加安全

![](img/a6fdb1f29a1aedc934fddeadac30aed9.png)

由[丹·尼尔森](https://unsplash.com/@danny144?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

敏感数据不可避免地存在于我们的日志内容中，如电话号码、身份证号码、信用卡号码等。

我们必须屏蔽日志中的敏感数据，以保护用户的配置文件数据。

没人希望自己的电话或信用卡号被日志文件泄露，对吧？

有两种方法可以屏蔽敏感数据。

我会解释它们的优缺点。

不同的日志内容有不同的场景。

1.  结构化日志

`LogTarget`类是一个包含两个属性`PhoneNo`和`IdentityNo`的 POCO。

```
var target = new LogTarget();

_logger.LogInformation("---Structured {@Target}---",target);
```

我们将在日志文件中获得以下文本。

```
---Structured {"PhoneNo":"12345678", "IdentityNo":"A33123123"}---
```

2.纯文本内容(例如，查询字符串或 JSON 序列化内容)

```
var queryString = QueryString.Create(new List<KeyValuePair<string, string?>>
{
    new("PhoneNo","12345678"),
    new("IdentityNo","A33123123")
});

_logger.LogInformation($"---Other {queryString}---");
```

在日志文件中获取以下文本。

```
---Other ?PhoneNo=12345678&IdentityNo=A33123123---
```

我们的目标是将`PhoneNO`屏蔽到 123***78，将`IdentityNo`屏蔽到 A33***123。

在接下来的两个部分中，我将解释如何以不同的方式做到这一点。

# 方法 1 `RegisterObjectTransformation`扩展方法

在 NLog 4.7 中引入了扩展方法`RegisterObjectTransformation`。

[](https://nlog-project.org/2020/03/28/nlog-4-7-has-been-released.html) [## NLog 4.7 已经发布了！

### NLog 4.7 标志着新功能和性能改进的又一年。推出了新的流畅 api…

nlog-project.org](https://nlog-project.org/2020/03/28/nlog-4-7-has-been-released.html) 

我们可以通过下面的代码轻松地使用方法来屏蔽日志内容。

我们得到以下内容。

```
---Structured {"PhoneNo":"123***78", "IdentityNo":"A33***123"}---
---Other ?PhoneNo=12345678&IdentityNo=A33123123---
```

如您所见，这种方法只适用于结构化日志，不适用于纯文本。

尽管这种方法很简单，但它不适用于一般的场景。

让我们尝试另一种方法。

# 方法 2 自定义渲染

如果不使用方法 1，获得想要的屏蔽值是最大的问题。

我将编写一个字符串操作逻辑来处理这个棘手的问题。

我的第一个想法是将所有日志内容作为纯文本处理，并在不同种类的日志内容中找到共同的规则。

然后按照特定的规则字符串分割日志内容，以获得实际的`value`。

例如，结构化日志或 JSON 序列化文本的特定规则字符串是`"`；URL 查询字符串的特定规则是`&`。

下面是一个 JSON 序列化文本的演示。

```
{"key1":"value1","key2":"value2"}
```

当我们用`"`分割文本时，我们得到下面的字符串数组。

`{``key1``:``value1``,``key2``:``value2``}`

假设我们屏蔽的目标是键`key1`的值。

找到`key1`的索引是 1，加上 2 到 3，字符串数组中的索引 3 就是键`key1`的值。

用想要的屏蔽值替换字符串数组中的索引 3。

【T12`key1``:``maskedvalue1``,``key2``:``value2``}`

重新连接字符串数组和分割规则，以获得完整的日志内容。

```
{"key1":"maskedvalue1","key2":"value2"}
```

当我们替换日志内容时，在日志写入日志文件之前，我们编写了一个自定义的`SensitiveMaskLayoutRenderer`来渲染日志。

## 让我们看一下实现代码

1.我们需要一个字典来存储哪些类型的字符串是我们屏蔽的目标(在这个例子中，是`PhoneNo`和`IdentiyNo`)。

这本字典是通用的。

字典的关键字是我们的屏蔽目标，值是我们想要的屏蔽值的函数。

创建类`NLogSensitiveDataMaskOptions`和创建属性`IDicionary<string, Func<string,string>>` `MaskDataDic`。

2.创建类`SensitiveMaskLayoutRenderer`和继承`LayoutRenderer`。

```
Remark:In this code example, I don't implement the code of MaskSensitiveData.Only append the *masked* string behind the original log content.
```

3.创建扩展方法以将布局呈现注册到 NLog。

4.将屏蔽的目标注册到服务容器中

登记到容器中

5.将`sensitive-mask`渲染添加到 nlog.config 的内部消息之外

## 打印日志！

检查您的日志文件，我们将获得屏蔽日志；无论您的日志内容是结构化的还是其他的，这种方法都可以实现目标。

```
---Structured {"PhoneNo":"12345678", "IdentityNo":"A33123123"}--- *masked*---Other ?PhoneNo=12345678&IdentityNo=A33123123--- *masked*
```

# 结论

方法 1 的优点是可以快速屏蔽敏感数据，但缺点只适用于结构化日志。

在方法 2 中，优点是您可以使它适用于所有情况，但缺点是当添加新规则时，您需要编写新代码。

你可以选择方法 1。如果你的日志内容总是结构化的，那就不要犹豫。

如果你的日志内容比较复杂，也许你可以参考我的方法 2。

希望这篇文章能解决你的问题。