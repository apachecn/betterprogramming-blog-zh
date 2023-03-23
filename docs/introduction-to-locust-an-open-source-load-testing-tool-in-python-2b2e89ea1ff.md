# Locust 简介:Python 中的一个开源负载测试工具

> 原文：<https://betterprogramming.pub/introduction-to-locust-an-open-source-load-testing-tool-in-python-2b2e89ea1ff>

## Tsung 和 JMeter 的基于 Python 的替代方案，用于对服务器进行负载测试

![](img/7ee50731d6e70da7901b4b89cab37111.png)

图片来源:官方[蝗虫网站](https://locust.io/)

受够了对现有服务器进行基准测试和负载测试的现有工具，我最近在我的一个项目中找到了一个名为`Locust`的开源工具。

通过阅读这篇文章，您将能够使用这个负载测试工具。这为您提供了服务器每秒支持多少请求(RPS)的粗略概念。根据[官方文献](https://docs.locust.io/en/stable/)记载，蝗虫是:

> “…一个易于使用的分布式用户负载测试工具。它旨在对网站(或其他系统)进行负载测试，并计算出一个系统可以处理多少并发用户。
> 
> 这个想法是在测试期间，一群蝗虫会攻击你的网站。每个蝗虫(或者测试用户，如果你愿意的话)的行为是由你定义的，群集过程是由一个 web 用户界面实时监控的。这将有助于您在让真正的用户进入之前测试和识别代码中的瓶颈。
> 
> Locust 是完全基于事件的，因此可以在一台机器上支持成千上万的并发用户。与许多其他基于事件的应用相比，它不使用回调。相反，它通过 gevent 使用轻量级流程。每一个蜂拥而至的蝗虫实际上都运行在自己的进程中(准确地说，是 greenlet)。这允许您用 Python 编写非常有表现力的场景，而不会因为回调而使代码变得复杂。"

本教程有四个部分:

1.  **设置**
2.  **基本用法**
3.  **命令行界面**
4.  **结论**

我们开始吧！

# 1.设置

安装非常简单，因为 3.6、3.7 和 3.8(从版本 0.14 开始)都支持`Locust`。您可以根据您的 Python 版本运行适当的 pip 命令来安装它。强烈建议先设置一个虚拟环境。

请注意蝗虫已经从`locustio`改名为`locust`。

## Python 3

```
pip install locust
```

## 来自 git 的最新主分支

```
pip install -e git://github.com/locustio/locust.git@master#egg=locust
```

上面提到的步骤应该适用于 Linux 和 Windows。如果事情没有按预期进行，请尝试以下方法。

## Windows 的可选修复程序

首先，前往以下[网站](https://www.lfd.uci.edu/~gohlke/pythonlibs/)下载预构建的二进制包:

*   pyzmq
*   gevent
*   一种绿色小鸟

您应该有 wheel 文件，可以通过运行以下命令来安装每个文件:

```
pip install name-of-the-package.whl
```

完成后，只需运行 pip 安装。

```
pip install locust
```

## 马科斯

MacOS 要求在安装 pip 之前安装`gevent`。最简单的方法是安装[自制软件](https://brew.sh/):

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

然后，安装`gevent`的依赖项`libev`:

```
brew install libev
```

然后通过`pip install`安装蝗虫。

## 测试蝗虫装置

完成后，您可以通过在命令行中运行以下代码来测试它:

```
locust --help
```

您应该会看到以下输出:

![](img/db2d9fc05c8aee01573678cb41294e94.png)

作者图片

在下一节中，我们将探索该模块提供的基本功能，并了解如何编写一个测试脚本来对您自己的服务器进行负载测试。

# 2.基本用法

## Flask 服务器

假设您有下面的 Flask 开发服务器文件，`myapp.py`，它已经准备好进行负载测试了:

```
from flask import Flaskserver_port = 5000
app = Flask(__name__)[@app](http://twitter.com/app).route('/')
def hello_world():
    return 'Hello, World!'if __name__ == "__main__":
    app.run('0.0.0.0',port=server_port)
```

只需在终端中通过以下代码运行服务器:

```
python myapp.py
```

您应该能够通过 localhost:5000 访问服务器，并看到`Hello World`输出。

## 蝗虫脚本

创建一个名为`locustfile.py`的新文件。向其中添加以下导入语句:

```
from locust import HttpUser, task, between
```

继续编写继承了`HttpUser`类的类。您可以根据自己的喜好命名该类。

```
class WebsiteTestUser(HttpUser):
```

在该类中，您可以定义自己的函数作为 locust 的任务。默认情况下，它提供了两个基类，将在`Locust`调用的开始和停止期间调用:

*   开启 _ 启动
*   开 _ 停

如果你在一个函数上添加`@task`装饰器，它将被视为一个 Locust 的任务。下面的例子显示了一个测试`localhost:5000` API 的简单任务:

```
[@task](http://twitter.com/task)(1)
def hello_world(self):
    self.client.get("[http://localhost:5000](http://localhost:5000)")
```

您必须修改后续任务的编号，如下所示:

```
[@task](http://twitter.com/task)(1)
def hello_world(self):
    self.client.get("[http://localhost:5000](http://localhost:5000)")[@task](http://twitter.com/task)(2)
def index(self):
    self.client.get("[http://localhost:5000](http://localhost:5000)/index")
```

如果你想设置一个`POST`请求任务，你可以使用下面的代码:

```
self.client.post("/login", {"username":"admin", "password":"admin"})
```

一旦你完成了它，你可以在类中添加额外的特性，比如`wait_time`:

*   `wait_time` —指任务执行之间的等待时间。现在，让我们用`between(0.5, 3.0)`来表示 0.5 到 3 秒之间的等待时间。其他[内置功能](https://docs.locust.io/en/stable/api.html#module-locust.wait_time)也可用，如`constant`和`constant_pacing`。对于负载测试，我们稍后会将这个数字更改为 0。

```
wait_time = between(0.5, 3.0)
```

您的最终代码应该与此类似:

`on_start`和`on_stop`功能特意留空以供参考。一个主要的用例是添加登录和注销调用来测试网站的认证。

## 试验

让我们通过打开另一个命令行并指向您的`locustfile.py`所在的目录来测试它。如果您使用完全相同的名称，只需运行:

```
locust
```

如果您使用的不是这个名称，您必须使用以下命令(相应地修改文件名):

```
locust -f personal_directory/your_file.py
```

您应该能够看到以下输出:

![](img/f8e3918b1c779224a86e55581ff3c751.png)

作者图片

打开浏览器，输入以下 URL:

```
[http://localhost:8089/](http://localhost:8089/)
```

将出现以下界面。最佳实践是使用实际主机的实际 IP 或主机名。现在，只要填写你喜欢的任何东西，因为我们不做任何分布式测试。

![](img/57d453448e66975587e31191d0ca130f.png)

作者图片

准备好之后点击`Start swarming`按钮。该过程将开始，您将注意到如下变化:

![](img/31a025ae4e283a870ae68eb4e272f5f1.png)

作者图片

*   `Request` —迄今提出的请求总数
*   `Fails` —失败的请求数
*   `Median`—50%的响应速度，单位为毫秒
*   `90%ile`—90%的响应速度，单位为毫秒
*   `Average` —平均响应速度，单位为毫秒
*   `Min` —最小响应速度，单位为毫秒
*   `Max` —最大响应速度，单位为毫秒
*   `Average bytes` —以字节为单位的平均响应大小
*   `Current RPS` —当前每秒请求数
*   `Current Failure/s` —每秒失败的总次数

转到图表选项卡，您应该会看到以下图表:

![](img/5459bec5b632330f88c30f47e700fe31.png)

作者图片

![](img/3d581d94f58af1c133c8d9be9dcc2742.png)

作者图片

![](img/350dbba0aa104114756c3559a8ee2caf.png)

作者图片

其余的菜单用于故障和异常。您甚至可以通过`Download Data`选项卡下载 csv 格式的数据。你会注意到此时`RPS`非常低。这主要是因为我们已经将`wait_time`设置在 0.5 到 3 秒之间。对于实际负载测试，将这两个值都更改为 0。您也可以根据自己的喜好使用`constant(0)`功能。重启您的测试，您应该能够看到您的服务器能够处理的最大值`RPS`。请随意使用不同的`number of users`和`hatch rate`进行试验。

下一节将直接通过命令行而不是 web UI 来模拟相同的功能。

# 3.命令行界面

该模块还为我们提供了一种通过命令行界面运行负载测试的方法。这允许我们轻松地实现自动化负载测试流程。运行 locust 时只需添加无 web 参数。您还必须包括主机、客户端数量和孵化率。

```
locust -f locustfile.py --no-web --host wfng -c 1000 -r 100
```

*   `f` —文件的路径
*   `no-web` —在没有网络界面的情况下运行模拟
*   `c` —客户数量
*   `r` —孵化率

运行大约 30 秒，然后使用`Ctrl-C`命令停止。您应该会看到以下输出:

![](img/79d633d5764f1b34ccaa63210e2f4a50.png)

作者图片

还有其他有用的参数，例如:

*   `t` —特定时间后停止。30s，5m，1h，1h30m。
*   `csv` —将结果保存为 CSV 格式的文件。

请随时通过以下方式检查所有可用参数:

```
locust --help
```

# 4.结论

让我们回顾一下今天所学的内容。首先，根据所使用的平台，我们从模块的简单安装开始。

然后，我们为负载测试创建了一个测试 Flask 服务器。同时，我们通过编写一个简单的 Locust 脚本探索了该模块提供的功能。我们运行了测试，并在 web UI 界面中获得了结果。有图表和数据可以很容易地以 CSV 格式下载。

此外，我们尝试仅通过命令行界面运行相同的模拟。这允许我们运行自动化的负载测试流程。

感谢阅读，我希望你喜欢这篇教程。下一篇再见！

# 参考

1.  [https://docs.locust.io/en/stable/installation.html](https://docs.locust.io/en/stable/installation.html)
2.  [https://locust.io/](https://locust.io/)