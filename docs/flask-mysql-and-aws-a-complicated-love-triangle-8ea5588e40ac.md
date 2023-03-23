# Flask、MySQL 和 AWS:一个复杂的三角恋

> 原文：<https://betterprogramming.pub/flask-mysql-and-aws-a-complicated-love-triangle-8ea5588e40ac>

## 将 Flask 应用程序连接到 AWS MySQL 数据库

![](img/aa49b6eb8d7ad2a36a0aa04d274eae49.png)

照片由 [Miha Arh](https://unsplash.com/@arhmi?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/love-triangle?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 介绍

我最近花了很多时间使用 [Flask](https://palletsprojects.com/p/flask/) 构建简单的 web 门户，SQL 后端运行在 Amazon Web Services 上( [AWS](https://aws.amazon.com/) )。原因是 AWS 提供了一个非常慷慨的免费层，我的大多数应用程序都是短期和小型的。

这样做了很多次后，我觉得写一篇简短的帖子，详细说明如何使用 Python 中的 Flask 设置一个应用程序，启动一个 AWS MySQL 数据库，并将两者连接起来，会很有用。

这篇文章假设你对 Flask 有一些初级的经验，但是我会一步一步的教你。我们不会讨论为一个网站创建页面；创建 HTML 表单，CSS 和所有其他前端 web 开发风格的工作。

这个教程可以被认为是你网站后端的一个练习。教程本身是模仿 Miguel Grinberg 的伟大教程，他是 Flask-Migrate 包的作者。

# 烧瓶应用

我将创建一个名为`flask_backed_with_aws`的基本 Flask 应用程序。应用程序的文件夹结构如下:

从这里我们可以看到，我们只有四个脚本(和一个环境文件)，我可以用文字来描述:

*   `flask_backend_with_aws.py`:启动你的 Flask 应用程序，这是你将要设置的`FLASK_APP`变量。
*   `config.py`:配置 Flask 应用程序的参数，特别是数据库的位置。
*   `.env`:保存`config.py`调用的环境变量。
*   `__init__.py`:启动 Flask 应用程序时运行。这将创建名为`create_app`的函数，它将初始化我们的数据库和迁移报告。
*   `models.py`:创建您的数据库模式。在这个名为`testDB`的文件中只创建了一个表。

让我们快速浏览一下如何在 Python 虚拟环境中创建这个应用程序。要逐步执行这段代码，Python 必须可以从命令行调用，并且必须有`[venv](https://docs.python.org/3/library/venv.html)`包。

您需要为您的项目创建一个目录，然后从该目录中启动虚拟环境。命令行(或 shell 命令)的任何代码都将在灰色框中突出显示:

```
# ~ Make directory and launch virtual environment ~ #                       mkdir flask_backend_with_aws                       
python3 venv venv # ~ Launch virtual environment (on Windows) ~ #                       venv\Scripts\activate
```

对应用程序结构最重要的两个文件是`config.py`和`__init__.py`。我们先来关注一下`__init__py`。

我们将使用 [SQLAlchemy](https://www.sqlalchemy.org/) 和 Migrate，两者都来自 Flask 特定的模块。

这两个模块的好处在于，它们允许我们在 Flask 应用程序中轻松地创建数据库，而且我们在脚本中对数据库所做的任何更改都可以轻松地推送到数据库所在的位置。

我们可以在上面的代码中看到，我们正在从`config.Config`中提取一些配置。这是 Flask 中的一个很好的方法，它允许我们在 Flask 知道要找到的一个单独的文件中指定配置。

我们在这里关心的具体配置将是数据库的统一资源标识符(URI)或地址。我们可以为测试数据库和生产数据库指定不同的地址。下面，我有我的`config.py`，我在这里做的唯一特别的事情是使用`dotenv`包从我的`.env`文件加载环境变量。

我们只需要再创建一个脚本，它是为这个数据库创建我们想要的表的脚本。

这个练习不是关于 SQL 的，所以我要做的表相对简单。注意，为了创建一个`testDB`，我们扩展了`db.Model`类。

这是 Flask 的另一个很好的特性，它已经有了一个预建的 table 类，每个表都简单地扩展了这个类。

我们最后编写一个脚本来启动我们的应用程序:

要启动我们的应用程序，在命令行中，您必须将`FLASK_APP`变量设置为启动我们的应用程序的文件，然后调用`flask run`。

```
# ~ Set flask application location ~ #
set FLASK_APP=flask_backend_with_aws# ~ Launch flask ~ #
flask run
```

您的 Flask 应用程序现在应该正在运行。要创建数据库，然后将任何更改迁移到数据库，您必须运行三个命令。

这些命令类似于 Git 命令，用于暂存、提交和推送对存储库的更改:

*   `flask db init`:创建一个 Python 脚本，用于对`models.py`中的数据库进行所有更改。
*   `flask db migrate`:创建迁移脚本。
*   `flask db upgrade`:执行迁移脚本升级数据库模式。

您可以看到这三个调用在起作用:

```
# ~ Initialize database changes ~ #
flask db init# ~ Migrate db changes ~ #
flask db migrate -m "testDB table"# ~ Upgrade changes ~ #
flask db upgrade
```

# 向数据库添加数据

我们已经成功地在您的计算机上创建了一个本地可用的数据库，我们可以使用任何能够与您的本地 [SQLite](https://www.sqlite.org/) 数据库对话的程序来访问该数据库。

下面我有一个示例脚本来添加，然后删除，然后添加，然后读入数据到一个 [pandas](https://pandas.pydata.org/) dataframe。

# 引入 AWS

最后一部分是关于简单地创建我们的数据库。该部分的好处在于，一旦我们在 AWS *上正确地*设置了 MySQL 数据库，那么我们所要做的就是更改环境文件中的“URI ”,我们基本上就完成了。

事实证明，虽然 AWS 令人惊叹，但在设置数据库时，您应该知道一些问题。正如我之前所说，我将建立一个 MySQL 数据库。

鉴于我是一名学生，没有钱，我将在这个练习中使用 AWS 免费层。整个过程可以在我的 GitHub 中的一系列截图中找到。不过，我可以总结一下你需要遵循的确切流程。

你首先需要登录你的 AWS 控制台，导航到亚马逊的关系数据库系统服务(简称 RDS)。

# 在数据库创建期间

要创建数据库，您需要确保完成以下两件事:

*   创建一个“初始数据库名”——这不同于“数据库实例标识符”。
*   公开您的数据库—这在“连接”选项卡中。
*   创建主密码。

我对我的设置进行了如下设置:

*   主用户名= admin
*   主密码= flaskexample。
*   数据库实例标识符= flaskexampledatabase2。
*   初始数据库名= testFlask。

一旦创建了数据库，这些操作就无法撤消。如果做不到这两点，就必须重新创建数据库。

# 数据库创建后

## 设置安全组

一旦创建了数据库，您需要更改您的安全组设置，以允许您的机器(或服务器)与 AWS 上的数据库进行对话。

这仅仅意味着改变`inbound`和`outbound`规则，要么包含您的 IP (AWS 有一个选项)，要么包含所有 IP 流量。

# 在 Flask Python 代码中设置 URI 变量

一旦创建了数据库并正确更改了安全组设置，您所要做的就是为数据库设置正确的位置。要设置您的 URI 变量，它将采用以下形式:

```
mysql+pymysql://MasterUsername:MasterPassword@URL/databaseName
```

关于上面要注意的一些事情，`MasterUsername`可能不是你用来登录 AWS 的用户名。通常情况下，`MasterUsername`=‘admin’。

接下来要知道的是`MasterPassword`要么是在*创建数据库*期间随机生成的，要么是在创建数据库时设置的。

一旦这些都设置好了，您现在就可以运行`flask db init`，进行迁移，并再次更新。

请注意，您还必须更改 Flask 迁移目录的位置。现在这个数据库存在于 AWS 上！要访问它，我可以使用与上面类似的 MySQL 脚本。

# 与 AWS 上的数据进行连接和交互

下面是一个使用`pymysql`与 AWS 数据库交互的简短脚本。正如您从上面看到的，我们使用了来自`DATABASE_URL`变量的所有信息，外加端口号。

一旦这些都设置好了，您现在就可以运行`flask db init`，进行迁移，并再次升级。现在这个数据库存在于 AWS 上！要访问它，我可以使用上面类似的 MySQL 脚本。

这个新脚本使用`pymysql`，一个 Python 库来与 MySQL 数据库交互。

# 结论

这种将应用程序从数据库中分离出来的框架并不新颖，因为这是大多数现代 web 应用程序架构的运行方式。

话虽如此，学习如何为个人和定制的过程这样做是一个很少网络开发经验的人有点难。

希望这能为将来尝试类似方法的人提供一个蓝图。