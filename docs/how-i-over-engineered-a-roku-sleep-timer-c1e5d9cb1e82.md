# 我是如何过度设计 Roku 睡眠定时器的

> 原文：<https://betterprogramming.pub/how-i-over-engineered-a-roku-sleep-timer-c1e5d9cb1e82>

## 我在使用 Docker、Python 和 Flask 创建自托管微服务 API 时做出某些设计决策的思考过程

![](img/3942d3a41d8bc7911890cd1de4702e16.png)

Roku 睡眠计时器的运行——图片由作者提供

虽然问题本身相当琐碎，但这里的真实故事是我为自托管定制服务创建一个[可重用模板](https://github.com/nschenone/docker-python-flask-template)并将它们安全地公开为 API 端点的旅程。这篇博客不是 Flask 和 Docker 等底层技术的教程，而是概述了我如何构建应用程序以及我做出某些设计决策的思维过程。

# 动机——一个奇怪的缺失事件

第一世界的问题和不必要的解决方案——说出一个更好的组合。虽然这些问题并不重要，但它们可以带来有趣的解决方案，解决超出最初预期范围的问题。一个这样的例子是，网飞上的几集缺失导致了在我的网络上运行的许多定制微服务 API 控制各种设备和服务。

2019 年，我买了第一台便宜的 Insignia 32 英寸电视和一个 Roku 流媒体棒。这绝不是一个家庭影院，但它肯定完成了工作。因此，我养成了利用电视内置的睡眠定时器在看节目时睡着的习惯。一切看起来都很棒…直到我意识到我比我最初意识到的更深入我的表演。经过大量调查后，发现虽然电视的睡眠定时器会自动关闭电视，但网飞仍然整夜在后台播放。

# 解决方案——制作我自己的睡眠计时器

利用我的 Python 和 Docker 经验，我决定自己动手。最终结果可以在[这个 GitHub repo](https://github.com/nschenone/roku-sleep-timer) 中找到，看起来如下:

![](img/bdd90a2ed3fc12c48f7fe10b87839264.png)

Roku 睡眠定时器架构—图片由作者提供

我的要求包括:

*   能够在指定的时间段后“睡眠”Roku 和电视(例如，关闭当前运行的应用程序并关闭电视)
*   取消当前运行的睡眠定时器的能力
*   从我的手机轻松安排/取消睡眠定时器
*   向应用程序不可知的 API 公开睡眠定时器的控制
*   以安全的方式在我的家庭网络外公开 API

我想出了一个解决方案，它包括几个主要部分:

*   应用逻辑(Roku 控制、作业调度、Flask)
*   包装(Docker，Docker 撰写)
*   部署(自托管、Cloudflare 代理、NGINX 代理管理器)
*   调用(苹果快捷键)

# 应用逻辑一:Roku 控制

![](img/b67192e1ac1890b088cfbc0505e065c4.png)

照片由[格伦·卡斯滕斯-彼得斯](https://unsplash.com/@glenncarstenspeters?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

作为第一步，我找到了优秀的库 [python-roku](https://github.com/jcarbaugh/python-roku) ，它允许你通过 Python SDK 控制你的 roku。有一个方便的功能可以自动发现本地网络上的 Roku 并模拟远程控制:

```
from roku import Roku

timeout = 10 # 10 second timeout
my_roku = Roku.discover(timeout)[0] # returns a list of Roku devices

my_roku.home()
my_roku.right()
my_roku.select()
```

以这种方式用你的笔记本电脑控制你的电视是令人难以置信的满足感(然而效率很低)。此外，如果您的电视支持 [HDMI-CEC](https://en.wikipedia.org/wiki/Consumer_Electronics_Control) ，您还可以使用 Roku 遥控器来控制音量和电源。这也适用于 Python SDK:

```
my_roku.poweron()
my_roku.poweroff()
```

经过一些试验，我发现最佳的命令组合是关闭当前运行的应用程序，等待 2 秒钟播放动画，然后关闭电视:

```
from time import sleep

# Prevents Roku from waking up TV if timer is triggered while TV is off
if my_roku.active_app.id != "None":
    my_roku.home()

    # Gives enough time to close open apps before turning off
    sleep(2)

# Power off TV once app is closed
my_roku.poweroff()
```

我还添加了一项检查，以确保回家前 Roku 上的应用程序当前处于打开状态。这可以防止电视在睡眠定时器意外启动时被打开。

虽然这些命令正是我所寻找的，但我不想立即运行它们。相反，我想在一段特定的时间后运行它们。

# 应用逻辑 II:作业调度

![](img/cece7b2cfa994f6329df31398146c9ea.png)

照片由 [insung yoon](https://unsplash.com/es/@insungyoon?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

接下来，我找到了`[APScheduler](https://apscheduler.readthedocs.io/en/3.x/)`或高级 Python 调度程序库。这个健壮的库允许使用各种调度器、作业存储、执行器和触发器来调度一次性或重复的命令。

对于我的简单应用程序，我所需要的就是将一个作业排队，在 X 分钟后运行一个给定的 Python 函数，并清除当前的作业队列。这可以使用内存中的`BackgroundScheduler`轻松完成。对于更重要的应用程序，有更多的容错方法来存储和执行作业队列。

以下示例在 5 分钟后运行`trigger_sleep` Python 函数:

```
from datetime import datetime, timedelta
from apscheduler.schedulers.background import BackgroundScheduler

def trigger_sleep():
    print("ZzZzZz")

scheduler = BackgroundScheduler()
scheduler.start()

scheduler.add_job(
    trigger_sleep,
    run_date=(timedelta(minutes=5) + datetime.now())
)
```

此外，您可以像这样清除当前作业队列:

```
for job in scheduler.get_jobs():
    job.remove()
```

这就是我对来自 [python-roku](https://github.com/jcarbaugh/python-roku) 的命令进行排队所需要的一切，但是只在它们被调度的时候执行它们。

# 应用逻辑 III:将所有内容整合在一起

![](img/6ca3376cb444f95fd1b6b588118bcf00.png)

上下文中的 RokuSleepTimer Python 脚本-图片由作者提供

最后，我将 Roku 控件和作业调度放在一个专用类中，以便更容易地从 Flask 应用程序中进行控制。最后一个`RokuSleepTimer`类处理睡眠和调度逻辑:

```
from datetime import datetime, timedelta
from time import sleep

from apscheduler.schedulers.background import BackgroundScheduler
from roku import Roku

class RokuSleepTimer:
    """
    Sleep timer for Roku. Will interact with Roku via Python SDK
    and schedule sleep job using APScheduler. Automatically connects
    to the first Roku it finds on the network.
    """

    def __init__(self):
        # Start background scheduler for sleep jobs
        self.scheduler = BackgroundScheduler()
        self.scheduler.start()

        # Find first roku on network
        self.discover()
        print(f"Using roku at IP: {self.host}")

    def on(self) -> str:
        """
        Turn TV on via Roku.

        :return: On message
        """
        self.roku.poweron()
        return "on"

    def off(self) -> str:
        """
        Turn TV off via Roku.

        :return: Off message
        """
        self.roku.poweroff()
        return "off"

    def discover(self, timeout: int = 10) -> None:
        """
        Find the first Roku on the network.

        :param timeout: Number of seconds before search times out.
        """
        self.roku = Roku.discover(timeout=timeout)[0]

    def trigger_sleep(self) -> None:
        """
        Sleep mechanism. If an app is open, go to the
        home screen, wait, and power off.
        """
        # Prevents Roku from waking up TV if timer is triggered while TV is off
        if self.roku.active_app.id != "None":
            self.roku.home()

            # Gives enough time to close open apps before turning off
            sleep(2)
        self.roku.poweroff()

    def stop_sleep(self) -> str:
        """
        Stop sleep timer by removing all jobs in scheduler.

        :return: Stop message
        """
        for job in self.scheduler.get_jobs():
            job.remove()
        return "Stopped sleep timer"

    def schedule_sleep(self, minutes: int) -> str:
        """
        Schedule sleep job after the specified number of minutes.

        :param minutes: Number of minutes to wait before sleeping

        :return: Job schedule success message
        """
        self.scheduler.add_job(
            self.trigger_sleep, run_date=(timedelta(minutes=minutes) + datetime.now())
        )
        return (
            f"Started sleep timer for {minutes} minutes. Enjoy the show, sleep tight!"
        )

    @property
    def host(self) -> str:
        """
        Helper to get IP address of Roku device.

        :return: IP address of Roku
        """
        return self.roku.host
```

既然休眠/调度逻辑已经完成，我需要一种方法来调用作为 API 的`RokuSleepTimer`。

# 应用逻辑四:烧瓶

![](img/2b660473380b0a3be8588865f0a8e0cf.png)

上下文中的 Flask Python 脚本-图片由作者提供

由于这个应用程序相对简单，我决定使用[Flask](https://flask.palletsprojects.com/en/2.2.x/)——一个经典的轻量级 web 框架。它很简单，不固执己见，使用起来非常简单。

就设计 API 而言，睡眠定时器只需要两条路线:

*   GET — `/start/<int:minutes>`:启动睡眠定时器 X 分钟
*   GET — `/stop`:取消任何正在运行的睡眠定时器

我还添加了一些生活质量路线，以使调试更容易:

*   GET — `/`:确保服务运行的健全性测试
*   GET — `/on`:手动打开 Roku + TV
*   GET — `/off`:手动关闭 Roku + TV
*   GET — `/discover`:通过搜索网络重新连接 Roku
*   GET — `/host`:获取 Roku 设备的 IP 地址

最终的烧瓶应用程序如下所示:

```
import os

from flask import Flask

from roku_sleep_timer import RokuSleepTimer

app = Flask(__name__)
sleep_timer = RokuSleepTimer()

@app.route("/")
def home() -> str:
    """
    Test connection to app.

    :return: Successful connection message
    """
    return "Connected"

@app.route("/host")
def host() -> str:
    """
    Get IP address of Roku.

    :return: IP address of Roku
    """
    return sleep_timer.host

@app.route("/on")
def on() -> str:
    """
    Turn TV on via Roku.

    :return: On message
    """
    return sleep_timer.on()

@app.route("/off")
def off() -> str:
    """
    Turn TV off via Roku.

    :return: Off message
    """
    return sleep_timer.off()

@app.route("/discover")
def discover() -> str:
    """
    Reset Roku connection to re-find first
    Roku on the network.

    :return: IP address of Roku
    """
    sleep_timer.discover()
    return sleep_timer.host

@app.route("/start/<int:minutes>")
def schedule_sleep(minutes: int):
    """
    Schedule sleep job after the specified number of minutes.

    :param minutes: Number of minutes to wait before sleeping

    :return: Job schedule success message
    """
    return sleep_timer.schedule_sleep(minutes=minutes)

@app.route("/stop")
def stop_sleep() -> str:
    """
    Stop sleep timer by removing all jobs in scheduler.

    :return: Stop message
    """
    return sleep_timer.stop_sleep()

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=int(os.getenv("FLASK_PORT")))
```

既然我能够通过 Flask API 调用`RokuSleepTimer`，我对应用程序逻辑感到满意。下一个任务是使用 Docker 和 Docker Compose 将所有东西打包。

# 包装一:码头工人

![](img/0650f8f1a68742f0265c077eb30295f9.png)

照片由[张秀坤·吕克曼](https://unsplash.com/@exdigy?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

首先，为什么我使用 [Docker](https://docs.docker.com/get-started/overview/) 来打包应用程序？

虽然我可以简单地运行 Flask 应用程序，但 Docker 允许环境之间的可移植性/一致性，与主机设备隔离，以及对包和硬件资源的粒度控制。

这也使得未来使用相同的技术创建更多服务变得非常简单——剧透警报——[我为此创建了自己的可重用微服务模板](https://github.com/nschenone/docker-python-flask-template)。

为了打包应用程序，首先我像这样组织目录结构:

```
├── Dockerfile           # Recipe for building Docker image
├── docker-compose.yml   # Builds image and deploys Docker container
├── requirements.txt     # Python dependencies
├── .env                 # Environment file for secret management
├── Makefile             # Helper to start, stop, and restart app
└── src                  # Source code
    ├── app.py                # Flask application
    └── roku_sleep_timer.py   # Roku control and job scheduling
```

`Dockerfile`完成大部分工作——它负责复制源代码、安装 Python 依赖项和启动 Flask 服务器:

```
#Download Python from DockerHub and use it
FROM python:3.11

#Set the working directory in the Docker container
WORKDIR /code

#Copy the dependencies file to the working directory
COPY requirements.txt .

#Install the dependencies
RUN pip install -r requirements.txt

#Copy the Flask app code to the working directory
COPY src/ .

#Run the container
CMD [ "python", "./app.py" ]
```

Python 依赖关系在`requirements.txt`文件中指定——最佳实践规定固定特定版本的库，以防止由于更新导致的意外行为:

```
Flask==2.0.1
roku==4.1.0
APScheduler==3.7.0
```

最后，`src`目录中的`app.py`和`roku_sleep_timer.py`文件使用前面几节中的代码填充

# 包装二:Docker 撰写

![](img/cf452738f35caf4e2ff16ddebfaf2f5c.png)

上下文中的 Docker 容器—按作者排序的图像

那么`docker-compose.yml`、`.env`、`Makefile`文件是干什么用的呢？他们将实际使用`Dockerfile`来启动应用程序容器。

[Docker Compose](https://docs.docker.com/compose/) 比 Docker 高一级——它允许创建在 YAML 文件(`docker-compose.yml`)中定义的多容器应用程序:

```
version: "3.3"

services:
  flask:
    restart: unless-stopped
    build: .
    image: roku_sleep
    container_name: roku_sleep
    network_mode: "host"       # This was necessary to find Roku on network
    environment:
      - FLASK_PORT=${FLASK_PORT}
```

虽然这个应用程序只有一个容器，但是还有其他好处。Docker Compose 允许轻松构建 Docker 映像并从一个`.env`文件注入环境变量。这对于在应用程序中添加任何 API 键或其他秘密非常有用，无需硬编码任何值。在这种情况下，烧瓶端口通过`.env`文件添加。

也许最重要的是，这意味着不再有任何笨拙的`docker run`命令带有许多许多参数。要构建 Docker 映像并运行应用程序，只需运行`docker-compose up -d --build`。这将使用您更新的源代码构建映像，并作为后台进程旋转容器。同样，您可以用`docker-compose down`停止容器。

最后，我为生活质量添加了一个助手`Makefile`:

```
.PHONY: help

help:
 @echo "Usage:"
 @echo "    up:       Start app"
 @echo "    down:     Stop app"
 @echo "    restart:  Restart app"

up:
 docker-compose up -d --build

down:
 docker-compose down

restart: down up
```

所有这些意味着我可以在开发时快速试验和迭代——编辑源代码并运行`make up`来重新部署应用程序。这整个目录和文件结构被构建到我自己的可重用微服务模板中，用于创建未来的服务。

现在应用程序已经和 Docker 和 Docker Compose 打包在一起了，我需要一个地方来存放它。

# 部署 I:自托管

![](img/ff3c58d8d9ea7661872594c9eec12998.png)

照片由[i̇smail·埃尼斯·艾汉](https://unsplash.com/@ismailenesayhan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

因为`RokuSleepTimer`需要直接连接到 Roku，所以应用程序需要托管在我的家庭网络上。我选择将它托管在我的个人服务器上——我办公桌下运行 Ubuntu 的计算机塔，它负责我的媒体库、数据科学工作台、定制微服务 API 和其他杂项 Docker 服务。

也就是说，Raspberry Pi、台式电脑或笔记本电脑都可以很好地工作——唯一的要求是 Docker 容器正在运行。在`docker-compose.yml`文件中的参数`restart: unless-stopped`将确保容器在底层主机重启或关闭时重启。

虽然 API 现在可以通过`localhost`在我的家庭网络中使用，但我最初的需求之一是能够以安全的方式访问网络外部的 API。

# 部署 II: Cloudflare 代理

![](img/60d9a59c11768520a6a6a03533595b02.png)

连接客户端和家庭网络的 Cloudflare 图片由作者提供

为了通过公共互联网连接到我的 API，我需要在一个定制的域上公开它。你可以从 Freenom 获得一个免费域名，但是，我在使用它的时候遇到了一些问题。如果你这样做了，请务必及时更新你的域名——我的域名过期了，我无法取回。我选择从 [Namecheap](https://www.namecheap.com/) 购买我的域名，它相对便宜，到目前为止运行良好。

从那里， [Cloudflare](https://www.cloudflare.com/) 负责管理 DNS，并作为代理隐藏我的家庭公共 IP 地址:

![](img/e3cf2487e8ace827a6458a0258f40321.png)

Cloudflare DNS 条目—作者图片

代理功能不能被低估——在线暴露您的公共 IP 地址是邀请不需要的客人进入您的网络的一个很好的方式。使用 Cloudflare DNS 和代理，我可以在`sleeptimer.mydomain.com`访问我的 API，同时只暴露一个 Cloudflare IP 地址。

Cloudflare 还提供了一些其他安全选项，如使用 WAF (Web 应用程序防火墙)按地理位置阻止传入请求的能力。我添加了一条规则来阻止任何来自美国以外的请求:

![](img/7b5762c28933abdacc1f153a1c37733f.png)

Cloudflare WAF 防火墙规则—图片由作者提供

我发现这是最有助于阻止不需要的请求到达我的网络的设置。Cloudflare 甚至有一些出色的分析仪表盘，用于显示每个国家/地区拦截了多少威胁:

![](img/00d6e07454e50a202c9447d1edc5f752.png)

按国家/地区阻止的威胁-按作者分类的图片

这种方法对于将传入请求路由到我的网络非常有效——但是，DNS 记录只指向我的公共 IP。一旦一个请求进入网络，没有关于做什么的指导。

# 部署 III: NGINX 代理管理器

![](img/108c4da36c11a5fd844286cabc020872.png)

NGINX 架构中的代理管理器—图片由作者提供

把 [NGINX 代理管理器](https://nginxproxymanager.com/) (NPM)想象成剧院的引座员——它根据域和子域将传入的请求定向到所需的服务。这是确保进入`sleeptimer.mydomain.com`的请求被路由到网络上特定的 IP 地址和端口的机制。我主要是跟着这个优秀的[视频](https://www.youtube.com/watch?v=cI17WMKtntA)通过 Docker 设置 NPM，连接到 Cloudflare:

使用您的域设置超级简单的 Cloudflare 和 Nginx 代理管理器——极客

以下 NPM 配置将我的域连接到在我的本地服务器上运行的服务，IP 地址为`192.168.1.168`，端口为`5001`:

![](img/944e831b711e941f050edc15683084fd.png)

NGINX 代理管理器设置—作者图片

这是因为 HTTP (80)和 HTTPS (443)的端口在路由器上被转发到运行 NPM 的机器——在本例中，是在`192.168.1.168`的同一台服务器:

![](img/363b168b9d94eb842e757f476c2f5cc0.png)

路由器端口转发设置—作者图片

还有其他安全设置，如自动添加 SSL 证书的能力，以及在发出请求时要求用户/密码验证。这些设置在上面链接的[视频](https://www.youtube.com/watch?v=cI17WMKtntA)中有更全面的解释。

![](img/c9681d8a96ff6bf52587b1c21a8294b1.png)

SSL 证书设置-按作者分类的图像

所有这些都意味着我的应用程序终于可以被某个最终用户客户端调用了。

# 调用:苹果快捷键

![](img/dfc7104b97099b54da8d638a44239e0e.png)

架构中的 HTTP(S)客户端—作者图片

以这种方式公开 API 的好处在于它是客户端不可知的——任何发出 HTTP(S)请求的方法都可以。

下面是一个使用 Python `requests`库的简单示例，其中授权信息`username:password`以 base64 编码格式在头中提供:

```
import requests
requests.get(
    url="https://sleeptimer.mydomain.com",
    headers={"Authorization": "Basic dXNlcm5hbWU6cGFzc3dvcmQ"}
)

# returns "Connected"
```

这种一般模式可以用任何方法复制。因为我有一部 iPhone，所以我选择使用所有更新的苹果产品自带的原生[苹果快捷方式](https://support.apple.com/guide/shortcuts/welcome/ios)应用。它是一个简单的基于 GUI 的工具，用于 Apple 生态系统中的通用自动化。非苹果用户的另一个很好的选择是 [IFTTT](https://ifttt.com/) 。

以下快捷方式提示用户输入分钟数，并使用提供的数字和授权信息启动睡眠定时器。此处[提供了一个模板](https://www.icloud.com/shortcuts/7e516d9a588c468580bd7580064b9595)，您可以根据自己的需要进行修改:

![](img/2dc2e3aa278aceb4737e3ed9be638521.png)

苹果睡眠定时器快捷方式——作者图片

从那里，可以通过主屏幕上的小工具或通过 Siri 的语音控制轻松调用睡眠定时器:

![](img/3942d3a41d8bc7911890cd1de4702e16.png)

Roku 睡眠计时器的运行——图片由作者提供

# 结束语

总的来说，这是为了解决一个简单而不重要的问题而付出的大量工程努力。然而，这里真正的故事是用于在家中自托管服务并将它们安全地公开为面向公众的 API 的整体方法和技术。

这个项目是在我的网络上创建附加服务的门户。我利用从 Roku 项目中学到的知识创建了我自己的可重用微服务模板，比如托管网站、向我的个人 Slack 工作区发送通知、通过 YNAB 自动生成支出报告以及从数据库中检索任务。

向系统添加新服务非常简单:

*   使用[模板](https://github.com/nschenone/docker-python-flask-template)将新的 Dockerized 应用程序部署到服务器上的给定端口
*   在 Cloudflare 中为新子域名添加 DNS 路由(例如`service.mydomain.com`)
*   在 NGINX 代理管理器中添加 route，将传入请求路由到新服务

我希望本指南对您有所帮助，并激励您为任何对您有用的东西创建自己的自托管服务！

# 参考

*   [苹果快捷方式用户指南](https://support.apple.com/guide/shortcuts/welcome/ios)
*   [APScheduler(高级 Python 调度程序)](https://apscheduler.readthedocs.io/en/3.x/)
*   [CEC(消费电子控制)](https://en.wikipedia.org/wiki/Consumer_Electronics_Control)
*   [云闪](https://www.cloudflare.com/)
*   [码头工人](https://docs.docker.com/get-started/overview/)
*   [Docker 撰写](https://docs.docker.com/compose/)
*   [Docker Python 烧瓶模板 GitHub Repo](https://github.com/nschenone/docker-python-flask-template)
*   [烧瓶](https://flask.palletsprojects.com/en/2.2.x/)
*   [IFTTT (If This Then That)](https://ifttt.com/explore)
*   [NGINX 代理管理器](https://nginxproxymanager.com/)
*   [Python Roku](https://github.com/jcarbaugh/python-roku)
*   [Roku 睡眠定时器 GitHub Repo](https://github.com/nschenone/roku-sleep-timer)
*   [使用您的域名设置超级简单的 Cloudflare 和 Nginx 代理管理器](https://www.youtube.com/watch?v=cI17WMKtntA)