# 用 GraphQL 启动 Django 项目的正确方法(第 1 部分)

> 原文：<https://betterprogramming.pub/proper-way-to-start-a-django-project-with-graphql-6e230d4b4d2c>

## *创建带认证的简单待办应用程序—基本设置*

![](img/5c63acf00277c662fb96b2ff7fafea0e.png)

照片由[克里斯里德](https://unsplash.com/@cdr6934?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/python-code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

*这是一系列文章中的第一篇，我们将创建一个现代的 web 应用程序，提供一个简单的带身份验证的待办事项应用程序，包括一个公开 GraphQL API 端点的 Django 后端，以及一个具有渐进式 web 应用程序功能的 Next.js、React 和 Material UI 前端。*

今天我们将创建一个基本的 Django 应用程序，它公开了一个 GraphQL 端点。在我们深入细节之前，让我们回顾一下几个概念:

*   [Django](https://www.djangoproject.com/) 是一个高级 Python Web 框架，鼓励快速开发和干净、实用的设计—[djangoproject.com](http://djangoproject.com)
*   [GraphQL](https://graphql.org/) 是一种用于 API 的查询语言，也是一种用现有数据完成这些查询的运行时语言——[graphql.org](http://djangoproject.com)
*   [Graphene](https://graphene-python.org/) 是一个在 Python 中快速构建 GraphQL APIs 的库。 [Graphene-Django](https://docs.graphene-python.org/projects/django/en/latest/) 构建在 Graphene 之上，提供了一些额外的抽象，使得将 GraphQL 功能添加到您的 Django 项目变得很容易——[graphene-python.org](http://djangoproject.com)

让我快速解释一下为什么为后端开发 Django 加石墨烯堆栈是有意义的。Django 是一个流行的 web 框架。事实上，在最近几年排名前五的 web 框架(Spring、Django、Rails、Laravel 和 Express)中，Django 是仅次于 Spring 的第二强。GraphQL 被认为是表述性状态转移(REST)的下一个迭代，它有其局限性(后面会有更多介绍)。Graphene 是开发人员想用 Django 公开 GraphQL 端点时使用的事实上的库。将所有这些放在一起，我们就有了一个现代而强大的后端起点。

现在无聊的东西已经过时了，让我们开始研究代码吧！

# 开始之前

在深入 Django 之前，让我们先安装一个现代版本的 Python 和一个虚拟环境[来运行我们的应用程序。为此，我们首先安装](https://docs.python.org/3/tutorial/venv.html) [Homebrew](https://brew.sh/) ，一个用于 macOS(或 Linux)的包管理器:

```
**$** /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

然后我们安装 Python 3.x:

```
**$** brew install python3
```

接下来，我们将安装一个名为`[virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/)`的库，这使得使用虚拟环境变得容易:

```
**$** pip install virtualenvwrapper
```

然后我们给`~/.bashrc`(或`~/.zshrc`)添加必要的配置:

要激活`virtualenvwrapper` *，*只需退出你的终端然后重新打开，或者运行`source ~/.zshrc`。现在，让我们创建一个虚拟环境来应对:

```
**$** mkvirtualenv -p python3 mybackend
```

一旦该命令运行，您应该会看到您的提示更改为:

```
(mybackend) $
```

这意味着您的虚拟环境是活动的，您运行的任何 pip/Python 命令都将使用该环境。您可以通过运行`deactivate`来取消激活当前环境，并通过`workon mybackend`重新激活它。恭喜你，你现在已经准备好开始你的 Django 项目了！

# Django 入门

如果你对 Django 完全陌生，你应该遵循他们的[入门指南](https://www.djangoproject.com/start/)。但是很快，我们首先安装 Django:

```
(mybackend) **$** pip install Django
```

然后我们创建我们的项目:

```
(mybackend) **$** django-admin startproject mybackend
```

您应该得到这样的文件夹结构:

```
mybackend/
    manage.py
    mybackend/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
```

接下来要做的是运行数据库迁移并启动开发服务器:

```
(mybackend) **$** cd mybackend
(mybackend) mybackend **$** python manage.py migrate
(mybackend) mybackend **$** python manage.py runserver
```

现在访问 [http://127.0.0.1:8000/](http://127.0.0.1:8080/) ，Django 应该上了。如果一切都按预期运行，那么继续冻结您的 pip 依赖项:

```
(mybackend) mybackend **$** pip freeze > requirements.txt
```

现在，任何签出您的代码(包括您的部署脚本)的人都可以运行`pip install -r requirements.txt`来复制您的环境。

# 设置代码样式工具

我知道，现在您已经启动并运行了后端服务器，您很高兴能够加入 GraphQL 支持！但是在我们进行任何实际的编码之前，建立一些通过林挺和自动格式化来加强代码质量的自动化工具是一个好主意。

我坚信使用这样的工具，即使你是一个项目中唯一的开发者。他们解决了在微小的代码风格决策上花费时间的麻烦，使您的代码在整个项目中保持一致，并偶尔通过静态分析捕捉 bug。所有这些都使得在时机成熟时，让下一个开发人员加入项目变得更加自然。它也很容易设置。

我们将使用三个公共库来实现这个目标:`[flake8](https://flake8.pycqa.org/en/latest/)`、`[isort](https://timothycrosley.github.io/isort/)`和`[black](https://black.readthedocs.io/en/stable/)`。`flake8`是 Python 的林挺工具(类似于 JS 的`ESLint`),`black`是代码格式化程序(类似于 JS 的`Prettier`),`isort`是一个漂亮的小库，它将根据相关性对你的导入进行排序。当我们谈论最佳实践时，让我们也初始化一个 git 存储库，并通过一个方便命名的库`[pre-commit](https://pre-commit.com/)`将这些林挺工具与预提交挂钩:

```
(mybackend) mybackend **$** pip install isort flake8 black pre-commit seed-isort-config flake8-bugbear
(mybackend) mybackend **$** pip freeze > requirements.txt
(mybackend) mybackend **$** git init
```

并创建一个`.gitignore`文件来忽略特定的文件:

接下来，在项目根目录下创建一个`.pre-commit-config.yaml`,使用:

项目根目录下的`.flake8`文件包含:

然后运行以下命令:

```
(mybackend) mybackend **$** pre-commit install
(mybackend) mybackend **$** pre-commit run --all-files
```

下次您将文件提交给 Git 时，这些工具将自动运行并抱怨您的代码(或者在可能的情况下自动修复)。

# 集成石墨烯

既然我们已经启动并运行了应用程序，让我们来集成石墨烯。假设这是你第一次将 Graphene 用于 Django plus GraphQL，浏览一下他们的文档是个好主意。要安装，只需运行:

```
(mybackend) mybackend **$** pip install graphene-django
(mybackend) mybackend **$** pip freeze > requirements.txt
```

然后，在`mybackend/settings.py`处将`graphene_django`添加到`INSTALLED_APPS`中:

然后将`graphql` URL 添加到您的`mybackend/url.spy`中:

最后，在`mybackend/settings.py`中定义您的模式位置，如下所示:

并在`mybackend/schema.py`创建最基本的模式:

现在，如果您通过`python manage.py runserver`运行您的开发服务器并访问[http://localhost:8000/graph QL](http://localhost:8000/graphql)，您应该能够看到 GraphQL API 浏览器。

# 后续步骤

现在，您有了一个能够服务于 GraphQL 端点的适当的 Django 设置。

接下来，我们将开始实现项目中著名的`[TodoMVC](http://todomvc.com/)`的查询和变化。请务必[看一看](https://medium.com/@cansinyildiz/introduction-to-graphql-with-django-and-graphene-part-2-5fe08f2d637a)！