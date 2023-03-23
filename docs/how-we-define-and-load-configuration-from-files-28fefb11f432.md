# 满足归零——一种从文件定义和加载配置的方法

> 原文：<https://betterprogramming.pub/how-we-define-and-load-configuration-from-files-28fefb11f432>

## 我们如何简化配置文件的解析

![](img/f2cb50763705f6f919167ca7083211c4.png)

我们写应用的时候，基本上都是用配置文件，从各种`shell`到`nginx`等等。，都有自己的配置文件。虽然这并不太难，但是配置项相对来说比较繁琐，解析和验证起来会比较麻烦。

在本文中，我将讲述我们如何简化配置文件的定义和解析。

# 方案

如果我们想写一个`Restful API`服务，配置项大概会有以下内容。

*   `Host` —监听`IP`，如未填写，默认为`0.0.0.0`
*   `Port` —监听的端口，必选，只能是一个数字，大于等于 80，小于 65535
*   `LogMode` —测井模式，只能选择`file`或`console`
*   `Verbose` —查看是否输出详细日志，可选，默认为`false`
*   `MaxConns` —允许的最大并发连接数，默认`10000`
*   `Timeout` —超时设置，默认`3s`
*   `CpuThreshold` —设置`CPU`使用触发减载的阈值，默认为`900`，`1000m`表示`100%`

以前我们使用`json`作为配置文件，但是`json`有一个问题，我们不能添加注释，所以我们切换到`yaml`格式。

现在让我们看看用`go-zero`定义和解析这样一个配置文件是多么容易

# 定义配置

首先，我们需要将上述配置需求定义到一个 Go 结构中，如下所示。

```
RestfulConf **struct** {
    Host **string** `json:",default=0.0.0.0"`
    Port **int** `json:",range=[80,65535)"`
    LogMode **string** `json:",options=[file,console]"`
    Verbose **bool** `json:",optional"`
    MaxConns **int** `json:",default=10000"`
    Timeout time.Duration `json:",default=3s"`
    CpuThreshold **int64** `json:",default=900,range=[0:1000]"`
}
```

如您所见，我们对每个配置项都有特定的定义和限制，其中一些定义如下。

*   `default`，如果没有填写配置，则使用默认值。你可以看到里面的`3s`会自动解析成`time.Duration`类型
*   `optional` —您可以将该项目留空。如果没有，使用其类型的零值
*   `range` —将数字类型限制在给定的范围内
*   `options` —将配置值限制为仅一个给定值

而且，有些属性可以一起使用，比如。

*   `default`和`range`可以一起用于添加范围限制和提供默认值
*   `default`和`options`可以一起用于添加选项限制并提供默认值

# 配置文件

因为我们在定义配置时给出了很多默认值，以及使用`optional`指定为可选，所以我们的配置文件中有相对较少的配置项可以使用默认值而无需显式编写，如下。

```
# Because many of them have default values, you only need to write the ones
# that need to be specified and the ones that don't have default values
Port: 8080
LogMode: console
# You can read the values of environment variables
MaxBytes: ${MAX_BYTES}
```

这里需要注意的是，如果配置字符的`value`都是数字，而你定义的配置类型是`string`，比如有人经常用`123456`来测试密码，但密码一般会定义为`string`，那么配置应该是这样写的(只是举例，密码一般不建议写在配置文件中)

```
Password: "123456"
```

这里不能遗漏双引号，否则会报告`type mismatch`，因为`yaml`解析器会将`123456`解析为`int`。

# 加载配置文件

我们有了配置定义(`config.go`)和配置文件(`config.yaml`)，下一步是加载配置文件，这可以通过三种方式完成。

*   必须加载成功，否则程序退出，我们一般都这么用，如果配置不正确，程序将无法继续

```
// If error, exit the program directly
**var** config RestfulConf
conf.MustLoad("config.yaml", &config)
```

由`go-zero`的`goctl`工具生成的默认代码也使用`MustLoad`来加载配置文件

*   加载配置并自己处理`error`

```
// handle errors on your own
**var** config RestfulConf
// For simplicity, LoadConfig will be changed to Load later,
// LoadConfig has been marked as Deprecated.
**if** err := conf.LoadConfig("config.yaml", &config); err ! = **nil** {
    log.Fatal(err)
}
```

*   加载配置并读取环境变量

```
// Automatically read environment variables
**var** config RestfulConf
conf.MustLoad(configFile, &config, conf.UseEnv())
```

这就是为什么我们需要显式指定`conf.UseEnv()` ——因为如果默认使用，你可能需要在配置中写入特定字符时使用`escape`，所以默认不读取环境变量。

# 实施原则

我们通常在实现`yaml/json`解析时使用`encoding/json`或者对应的`yaml`库。但是对于`go-zero`，我们需要对`unmarshal`有更精确的控制，这导致我们自己定制`yaml/json`解析。完整的代码在这里:

*   配置文件编码:[https://github.com/zeromicro/go-zero/tree/master/core/conf](https://github.com/zeromicro/go-zero/tree/master/core/conf)
*   `yaml/json`解析代码:[**https://github . com/zero micro/go-zero/tree/master/core/mapping**](https://github.com/zeromicro/go-zero/tree/master/core/mapping)

这也是一个很好的例子，说明了如何使用`reflect`以及如何使用单元测试来确保复杂场景中的正确性。

# 摘要

我一直推荐`Fail Fast`的想法。

我们在加载配置文件时做同样的事情。一旦出现错误，立即退出，这样`ops`在部署服务时会及时发现问题，因为进程无法启动运行。

`go-zero`服务的所有配置项都是这样加载并自动验证的，包括我写的很多工具的配置也是基于此，希望有帮助！

欢迎使用`[go-zero](https://github.com/zeromicro/go-zero)`和明星来支持我们！