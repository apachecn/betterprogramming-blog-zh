# 使用 Pyjnius 访问 Python 中的 Java 类

> 原文：<https://betterprogramming.pub/accessing-java-classes-in-python-using-pyjnius-6122bcaad49a>

## 建立一个强大的 Python 到 Java 的桥

![](img/6d0adff789e036489429ee667b0058a0.png)

由 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上的 [AltumCode](https://unsplash.com/@altumcode) 拍照。

# 什么是桥接？

桥接意味着在不同的运行时环境中使用一个运行时环境的行为和数据类型。

换句话说，就是在不同的语言(比如 Python)中使用最初用一种语言(比如 Java)编写的类、方法、函数和数据字段。

在我们的例子中，我们将用 Java 创建一个简单的代码，然后我们将编写一个 Python 程序，在运行时使用 Java 代码。

# 为什么？

理想情况下，我们希望所有的东西都只使用一种编程语言，但是 ideal 并不总是可用的。

很多时候，我们可能会为一个不是用我们选择的语言编写的问题找到一个很好的解决方案。

一个解决方案是部署一个服务器，用 API 调用来“服务”这些实现。

但是维护一台服务器可能成本很高。

这不仅需要开发服务器端应用程序并公开所需的行为和 API，还需要托管和部署服务器，这是有成本的。

此外，如果所需的行为拥有某种内部状态，那么服务器现在必须为每个特定的消费者维护该状态。这可能是极其错误和不稳定的。

因此，在某些情况下，在本地桥接这些行为会是一个更好的选择。

# 从简单开始

下面是一个简单的 Java 应用程序:

这个类有两个方法:一个是计算一组数字的简单平均值，另一个是有状态方法。

状态是每次调用`calculateFactoredMean`时递增的因子，平均值乘以因子。

因此，典型的主应用程序如下所示:

很直白。main 方法实例化`Calculator`类，初始因子为 1，步长增量为 2，然后调用`calculateFactoredMean`三次。

该应用程序的输出将是:

```
Mean of list is: 5.50
Mean of list is: 16.50
Mean of list is: 27.50
```

现在，让我们将这段代码应用到 Python 应用程序中。

# 介绍 pyjnius

`[Pyjnius](https://pyjnius.readthedocs.io/en/stable/)`基于 Java 原生接口和反射将 Java 类提供给 Python 运行时。

## 先决条件

*   您必须安装 Java。在我的例子中，我使用的是 openJDK 11。
*   您还需要安装 Javac(例如，`apt-get install openjdk-11-jdk-headless`)。

## 安装 pyjinus

```
pip install pyjnius==1.4.1
```

## 你好世界

这段代码生成一个对 Java `System` 类的引用，然后使用输入“Hello world”调用`out.println`静态方法。

让我们使用以下命令来运行它:

```
python simple.py
```

以下是输出结果:

```
Hello world
```

酷毙了。

## 盛装图书馆

为了使用我们定制的库，我们首先需要将 Java 代码编译成一个 Jar 文件。

使用 maven 很容易做到这一点，可以使用以下命令安装 maven:

```
mvn clean install
```

Jar 将位于`target`目录中。

现在，在我们的 Python 代码中，我们应该将 jar 文件添加到 Java 类路径中，如下所示:

首先，我们推断 jar 文件在哪里，然后使用`jnius_config.set_classpath`将它添加到 Java 类路径中。

现在，我们的 main 部分做了与 Java 中的 main 方法完全相同的事情。

它实例化了一个从 1 到 10 的整数列表。

**注意:**这是一个 Java 列表；Java 类不能接受 Python 数据类型！

然后它得到一个对`Calculator`类的引用，然后用它实例化一个对象。记住，Python 中的类也是对象！

然后它使用了`calculateFactoredMean`行为，三次调用列表中的方法。

让我们使用以下命令来运行它:

```
python some-python-app
```

以下是输出结果:

```
Mean of list is: 5.5
Mean of list is: 16.5
Mean of list is: 27.5
```

厉害！

# 桥接的注意事项

语言之间的桥接可能涉及进程间的通信，这对于某些应用程序来说是很昂贵的。

它要求对母语和桥接语言都有很好的了解。

建立项目可能会很困难，对进入设置障碍，用桥运行应用程序可能会增加依赖性，这可能会使它难以执行。