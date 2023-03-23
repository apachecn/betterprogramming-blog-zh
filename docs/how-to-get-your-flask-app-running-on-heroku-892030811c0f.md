# 如何将 Flask 应用程序部署到 Heroku

> 原文：<https://betterprogramming.pub/how-to-get-your-flask-app-running-on-heroku-892030811c0f>

## 本教程假设您正在将一个现有的应用程序部署到 Heroku，该应用程序是使用 Flask 和 Python 3 开发的，没有数据库

![](img/e412bd276b5c9c5588bb855dea53ffa3.png)

照片由 [Unsplash](https://unsplash.com/s/photos/launch?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的[clment Falize](https://unsplash.com/@centelm?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄

部署是复杂的，需要了解许多移动部件。不幸的是，通常没有一个适合所有人的分步指南来部署给定的应用程序。在整篇文章中，我链接了重要的背景信息，如果出现问题，这些信息可能有助于您进行调试。

我和很多学生一起工作，将 Flask 项目部署到 Heroku，所以我非常熟悉边缘案例和您可能会忘记的事情。常见的遗漏步骤已被收录为**注释**，请密切关注！

本教程假设您正在将一个现有的应用程序部署到 Heroku，该应用程序是使用 Flask 和 Python 3 开发的，没有数据库。如果您的应用程序使用数据库，本教程可能会对非数据库部分有所帮助。如果你正在使用 Postgres，阅读关于这个主题的 Heroku 指南。

[](https://devcenter.heroku.com/articles/heroku-postgresql#provisioning-heroku-postgres) [## Heroku Postgres

### Heroku Postgres 是由 Heroku 直接提供的托管 SQL 数据库服务。您可以访问 Heroku Postgres…

devcenter.heroku.com](https://devcenter.heroku.com/articles/heroku-postgresql#provisioning-heroku-postgres) 

# 瓶

## 虚拟

如果你还没有在`virtualenv`中开发你的应用，现在就做一个(否则跳到下一部分):

在项目目录中，运行:

```
$ virtualenv -p python3 env
```

然后，激活它:

```
$ source env/bin/activate
```

**注意:**如果您关闭外壳/终端窗口，请确保重新激活 virtualenv！

检查以确保你的`virtualenv`和`pip`使用的是 Python 3:

```
$ pip --version && python --version
```

你不应该在任何地方看到 Python 2.7。如果你这样做了，你的虚拟能力就有问题，你需要在继续前进之前解决它。

运行您的应用程序。您可能会丢失依赖错误，所以您需要`pip install`那些。例如:

```
$ pip install flask
```

如果这是你第一次使用`virtualenv`，我强烈推荐你在这里阅读它们。

## 要安装的软件包

你将需要 [Gunicorn](https://gunicorn.org/) :

```
$ pip install gunicorn
```

Gunicorn 是一个 [WSGI](https://www.fullstackpython.com/wsgi-servers.html) HTTP 服务器，当你的应用被部署时，它将处理对你的应用的请求。大多数人都用它来部署 Python 应用程序。

完成后，确保 pip 安装了所有正确的依赖项，并且`virtualenv`处于活动状态，然后运行:

```
$ touch requirements.txt
$ pip freeze > requirements.txt
```

这个命令会将你的应用程序的所有依赖项添加到一个文件中，这个文件会告诉 Heroku 它需要安装什么。仔细检查您的新`requirements.txt`文件。确保 Flask 和 Gunicorn 在那里，并且你没有意外地`pip freeze`你的整个本地 Python 环境(如果你有，Heroku 会对你非常不满意)。

# 赫罗库的文件

将以下文件放在主应用程序所在的目录中:

## **过程文件**

```
web: gunicorn -w 4 app_file:app_name
```

这个档案是专门给 Heroku 的。将这里的`app_file`替换为你的主应用文件的名称。我通常只给我的取名为`app.py`，所以`app`可以。

`app_name`是你对 Flask 应用程序的命名，所以如果你的应用程序在一个名为`salmon.py`的文件中，有一行看起来像`aardvark = Flask(__name__)`，那么最后一行看起来像`salmon:aardvark`。

然而，您很可能有一个名为`app.py`的文件，其中有一行类似于`app = Flask(__name__)`的内容。在这种情况下，您的 Procfile 应该只包含:

```
web: gunicorn -w 4 app:app
```

`-w 4`指定我们想要四个[工作线程](https://en.wikipedia.org/wiki/Thread_pool)。一个 Procfile 也可以有很多其他的设置，你可以在这里读到[。](https://devcenter.heroku.com/articles/python-gunicorn)

## **runtime.txt**

```
python-3.6.6
```

Heroku 需要知道使用哪个 Python 版本。不幸的是，它并不支持每一个版本，所以你通常应该在开发中使用一个支持的版本。如何做到这一点超出了本教程的范围，所以我们只是要选择一个版本。要找出您本地有哪个运行时，请执行`python --version`。然后，检查 H [eroku 支持的运行时，](https://devcenter.heroku.com/articles/python-support#supported-runtimes)并选择最接近你的运行时(但不要使用 2.7)。

## **。gitignore**

```
env
*.pyc
```

拥有一个`.gitignore`并不是严格要求的，但是强烈推荐。如果你没有，你会从你的开发环境中上传不必要的东西。

**注意:**如果你在开发中使用 SQLite，你会很想添加那个文件，因为 Heroku 不需要它。另外，在您的生产环境中有您不需要的文件会导致奇怪的错误。

# 饭桶

如果您还没有使用 Git 进行版本控制，您将需要使用它来部署到 Heroku [(安装说明](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git))。

```
$ git init
$ git add -A
$ git commit -m "deploying to heroku"
```

# 赫罗库

[注册 Heroku，](https://www.heroku.com/)和[如果你还没有安装 CLI](https://devcenter.heroku.com/articles/heroku-cli) 。确保您位于正在部署的应用程序的顶层目录中，并运行:

```
$ heroku login
$ heroku create example-app-name
$ git push heroku master
```

很有可能出现了一些错误。如果有，查看底部的调试提示。一旦你得到这个工作，运行:

```
$ heroku open
```

希望这行得通，并且您已经部署了您的应用程序！请在评论中告诉我你哪里出了问题，以及你是如何解决的。

## 调试 Heroku

在你急着让你的应用在 Heroku 上运行之前，要确保你的应用在本地运行。

当我第一次部署 Python 应用程序时，最常见的错误消息似乎是`pip install`错误。它们看起来有点像这样:

```
**remote:          Could not find a version that satisfies the requirement pkg-resources==0.0.0 (from -**r/tmp/build_1b06c0323986c42337cfd16a42542c2a/requirements.txt (line 11)) (from versions: )
remote:        No matching distribution found for pkg-resources==0.0.0 (from -r /tmp/build_1b06c0323986c42337cfd16a42542c2a/requirements.txt (line 11))
remote:  !     Push rejected, failed to compile Python app.
```

如果发生这种情况，请尝试编辑您的`requirements.txt`文件中的版本号。上面的错误原来是与我的操作系统有关的错误，所以我只是从`requirements.txt`中删除了`pkg-resources==0.0.0`，然后再试一次。

**注:**另一种真的很常见(而且很难诊断！)错误是您的某个模块/文件/文件夹与 Heroku 正在使用的某个模块具有相同的名称。你可以通过跟随`traceback`来判断这正在发生。试着找出不是你写的代码抛出的错误，因为它试图调用你写的代码。不幸的是，解决这个问题的方法是重命名冲突的文件夹/文件。

如果您正在部署数据科学项目**(例如**像 tweet generators)，您可能会在某个时候看到这样的错误:

```
Error R14 (Memory quota exceeded)
```

这意味着你的应用程序使用了太多的 Heroku 内存，你要么需要优化你的代码，要么增加 Heroku 允许它使用的内存量。

**查看最近的错误信息:**

```
heroku logs --tail
```

Heroku 日志可能看起来令人困惑，但是请寻找那些熟悉的 Python 回溯。寻找类似于`Python can not find Flask`的消息，这些消息可能表明您的`requirements.txt`有问题。

您也可以尝试关闭并再次打开它。

**重启你的 app** :

```
heroku restart
```

当我想到更多的技巧时，我会更新这篇文章。