# 在多个软件环境中管理凭证的一种方法

> 原文：<https://betterprogramming.pub/one-way-to-manage-credentials-in-multiple-software-environments-b2d725a73777>

## 保护您的 API

![](img/f88b94c2ffb92cdbb3b16e376b322055.png)

参考:[https://commons.wikimedia.org/wiki/File:Steal_password.jpg](https://commons.wikimedia.org/wiki/File:Steal_password.jpg)

在维护凭证和访问凭证的奇妙世界中，没有人能够在单一方法上达成一致。

在一个完美的世界里，没有人会窃取密码并试图窃取信息，但不完美正是软件有趣的地方。

总的来说，我将详细说明当无法从本地设置访问凭证存储时，我用来管理凭证的一种方法。

首先，让我解释一下这个方法有意义的场景。我正在开发的一个 API 可以访问一个数据库，并且需要一个密码来认证为一个特定的用户。作为该过程的一部分，我必须将密码的加密版本交给 IT 部门，他们将它存储到一个解决方案中。

但是，这个解决方案不能通过任何员工的笔记本电脑访问，只能在部署了容器的集群上访问。因此，自然的问题是:我如何用一个实时数据库在本地测试这一点？

因此，每个人都可以想出几种方法来访问凭证，但我们希望有一种可重复的方法来做到这一点，而不必记住 4-5 个不同的命令来设置它。通常，容器/服务的环境变量是一个好主意，但是，我们如何以一种简单的方式多次设置它呢？可以添加 bash 别名，但是如果其他人有该项目的票怎么办？给他们相同的 bash 别名？我们可以做得更好。

以下是我对本地环境的要求:

*   能够在机器环境中运行(`venv`、`nodenv`等)。)
*   能够在我的机器上的 docker 容器中运行

以下是项目所需的一些文件:

*   配置文件(本例中为 YAML)
*   配置模块(在这个例子中是 Python)
*   生成文件
*   Dockerfile 文件
*   头脑清楚

我有的是一个配置模块，它打开一个带有 YAML 扩展名的配置文件，扩展名如下:

```
db:
  host: the-host
  port: 5432
  database: my-database
```

每个项目还存在其他一些配置，但是，这是一个简单的设置，目前没有凭证。在下面的代码片段中，config 模块以类似的方式打开:

```
# requires install of pyyaml
import yamldef read():
    with open("config.yaml", "r") as stream:
        try:
            return yaml.safe_load(stream)
        except yaml.YAMLError as exc:
            print(exc)
```

我做的第一件事是复制并修改同一个目录下的配置文件，并将其命名为`config-local.yaml`

```
db:
  host: the-host
  port: 5432
  database: my-database
  password: ultra-secret-password
```

现在，执行以下步骤 **:**

*   将上述文件添加到`.gitignore`

```
<PATH>/<TO>/config-local.yaml
```

*   然后用`git status`仔细检查，确保文件没有被意外地提交到存储库中。
*   接下来，创建一个`Dockerfile.dockerignore`文件，用于忽略将该文件添加到主 docker 构建中。这应该等同于将它添加到`.gitignore`中，并构建/检查容器文件以确保它没有被添加。
*   现在我将修改上面的模块来处理自定义文件名:

```
# requires install of pyyaml
import yaml
import os# I only have to worry about local vs every environment so this is     # the only check I needCONFIG_NAME = 'config.yaml' if not os.getenv('LOCAL_ENV') else \ 'config-local.yaml'.format(os.getenv('LOCAL_ENV'))def read():
    with open(CONFIG_NAME, "r") as stream:
        try:
            return yaml.safe_load(stream)
        except yaml.YAMLError as exc:
            print(exc)
```

因此，现在每当我在 Python 的虚拟环境中运行时，我都可以执行以下操作(下面的示例是针对 FastAPI 设置的):

```
PYTHONPATH=src/ LOCAL_ENV=true uvicorn main:app --reload
```

我将它转换成了 make 目标(这是一个很好的技巧，所以您不必记住命令):

```
# The source keyword doesn't work so a period is needed
# Source: [https://stackoverflow.com/a/27801227](https://stackoverflow.com/a/27801227)
ACTIVATE_VENV =. path/to/venv/activate.PHONY: run-local
run-local:
    $(ACTIVATE_VENV); PYTHONPATH=src/ LOCAL_ENV=true uvicorn main:app --reload
```

注 **:** 这是一个激活虚拟环境的技巧，这样当您将目录更改为存储库时，就不必每次都运行源命令。

这太棒了！对意外提交明文密码的担忧消失了，密码的本地副本被添加到配置模块，可以快速访问该模块以进行本地测试(不要在 zoom 会议期间打开)。这并不理想，但确实在不中断代码进度的同时简化了对凭证的访问。

剩下的最后一件事是设置 docker 在本地运行。现在，由于基本 docker 文件忽略了新文件，让我们创建另一个文件。可以创建一个名为`Dockerfile.local`的新文件，它不必跟在`.dockerignore`后面(不幸的是，docker 中没有忽略参数)。

`Dockerfile.local`和`Dockerfile`的唯一区别是这条线:

```
ENV LOCAL_ENV=true
```

现在让我们添加一个 make 目标:

```
.PHONY: run-docker-local
run-docker-local:
   DOCKER_BUILDKIT=1 docker build -f Dockerfile.local -t <IMAGE_NAME>:local .
   docker run <IMAGE_NAME>:local
```

注意:匹配 dockerfiles 的`.dockerignore`命名需要将`DOCKER_BUILDKIT`变量设置为 1 才能工作。

这张图片不能被推送到任何环境中(我建议使用本地标签来避免意外推送)。

最后，现在我们有两种方法来运行我们的服务，同时避免凭据被推送到在线存储库。我并不认为这是处理凭证的最佳方式，但这是我在本地测试并与流程保持一致的同时不断改进服务的一种方法。请随意批评这个过程或提出改进建议，因为我想看看其他人做了什么。

感谢阅读！