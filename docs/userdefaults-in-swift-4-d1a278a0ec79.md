# Swift 中的用户默认值

> 原文：<https://betterprogramming.pub/userdefaults-in-swift-4-d1a278a0ec79>

## 从 UserDefaults 开始|如何使用 UserDefaults？

![](img/85dd6a87be04810db4c980b82b766a81.png)

在保存小数据的 iOS 应用中，我们使用的是`[UserDefaults](https://developer.apple.com/documentation/foundation/userdefaults)`。

这里的专业解决方案可以轻松处理这些事情。

# 用户默认值

为什么是`UserDefaults`？因为有时我们需要在应用程序中保存少量数据，如设置、状态或记录，我们可以用它来代替核心数据。

可以支持`Bool`、`Dictionary`、`Int`、`String`、`Data`、`Array`等保存数据类型。

## 保存字符串

```
**UserDefaults**.standard.**set**("Anand", forKey: "name")
```

## 正在检索字符串

```
let name = **UserDefaults**.standard.**string**(forKey: “name”) ?? “”
```

为什么我在这里用了`name`？这是用于保存和检索数据的密钥。我们可以用唯一的`key`存储每一条数据，否则，它会用新数据替换旧数据。

再举一个`Bool`的例子...

## 保存布尔值

```
**UserDefaults**.standard.**set**(true, forKey: “userlogin”)
```

## 正在检索布尔值

```
let status = **UserDefaults**.standard.**bool**(forKey: “userlogin”) ?? false
```

# 一个管理用户详细信息的小会话的小例子

在这个例子中，我们将介绍使用`Dictionary`进行保存，以及通过`Model`类检索用户数据。

用户默认值示例([github.com](https://gist.github.com/ANSCoder/d7faaa8d7211663ef797ce0207e11688))

## 如何使用它

保存详细信息:

```
**Defaults**.save("Anand", address: "Bengalore")
```

正在检索详细信息:

```
let name = **Defaults**.getNameAndAddress.name
```

清楚细节:

```
**Defaults**.clearUserData()
```

# 结论

这是处理应用程序少量数据并存储在`UserDefaults`中的最简单方法。它易于使用，并且几乎支持所有类型的值类型，如`Dictionary`、`Array`、`Any`、`String`、`Int`、`Float`、`Double`、`Bool`、`URL`。

*关于用户默认值还有一件重要的事情:*

`UserDefaults`类是线程安全的。

*遗产:*

避免使用`UserDefaults.standard.synchronize()`

> 你可以在这里查看如何通过 **Propertywrapper** 使用 UserDefaults。

*来源-* [*苹果文档。*](https://developer.apple.com/documentation/foundation/userdefaults/1414005-synchronize)

# 感谢阅读！