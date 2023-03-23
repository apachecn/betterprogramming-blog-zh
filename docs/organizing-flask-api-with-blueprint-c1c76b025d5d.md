# 用蓝图组织 Flask API 应用程序

> 原文：<https://betterprogramming.pub/organizing-flask-api-with-blueprint-c1c76b025d5d>

## 准备申请的指南

![](img/08318c59ecd501e7a2ee5e2ebc68965c.png)

烧瓶应用

应用程序设计可能具有挑战性，尤其是当您刚接触一个框架时。对于 Flask 应用程序，我们可以使用蓝图将 Python 应用程序设计成可重用的组件/模块。

我们将从理解我们的 flask 应用程序的结构开始。

```
.
├── govt_structure/
│   └── mainapp/
│       ├── constituencies/
│       │   ├── __init__.py
│       │   ├── constituencies.py
│       │   └── route.py
│       ├── counties/
│       │   ├── __init__.py
│       │   ├── counties.py
│       │   └── route.py
│       ├── database/
│       │   ├── __init__.py
│       │   └── db.py
│       └── __init__.py
├── app.py
├── appentry.sh
├── Pipfile
├── Pipfile.locl
├── .gitignore
└── Dockerfile
```

让我们花些时间来理解这个结构。应用程序文件夹名为`govt_structure`,包含`mainapp`的内容，我们的代码将位于其中，并包含与我们的代码通信的条目文件。第一个入口文件是`app.py`，它将调用`mainapp`包。每个包含`__init__.py`文件的文件夹在 python 中都被解释为一个包。`app.py`是我们的入口文件，它将被`appentry.sh`脚本用来启动一个 gunicorn WSGI HTTP 服务器。

在`mainapp`中，我们将使用 __init__ 文件用蓝图注册我们的模块。我们的应用程序中有三个模块:县、选区和数据库。

因为这三个模块是作为包启动的(存在 __init__)，所以它们可以很容易地相互引用，或者从我们应用程序中的任何位置进行寻址。

无论是在本地环境中还是在容器化时，都将通过运行`appentry.sh`脚本来启动应用程序。

现在我们已经有了一个结构，让我们开始准备 python 环境并创建所有文件。

有了合适的结构，我们将从初始化文件`mainapp/__init__.py`中的主应用程序开始。

```
from flask import Flask#function to initiate the application
def create_app()
    app = Flask(__name__)

    return app
```

接下来将在`mainapp/counties/__init__.py`进行县包初始化。

```
from flask import Blueprint#create a counties module blueprint
counties_bp = Blueprint('counties',__name__)#import counties module routes
from mainapp.counties import route
```

在`mainapp/counties/counties.py`创建 counties 类和 counties 模式(这将有助于序列化和反序列化)。

在`mainapp/database/db.py`创建数据库类来为我们执行所有数据库事务。

我们将为用户提供访问文件`mainapp/counties/route.py`中的 counties 模块的路径。

我们的 counties 模块已经可以导入到应用程序中了。我们将通过在 mainapp __init__ 文件中注册国家蓝图来实现。我们还将初始化数据库，开始接受请求。

```
from flask import Flaskfrom mainapp.database.db import DB#function to initiate the application
def create_app():

    app = Flask(__name__)

    #initiate DB connection
    DB.init()

    #call the below method to register modules
    register_modules(app)

    return app#import and register modules as blueprints here.
#you can register as many modules you have
def register_modules(app):
    from mainapp.counties import counties_bp
    app.register_blueprint(counties_bp)
```

应用入口文件是 app.py，它将调用 mainapp 包方法`create_app`来运行应用。

```
from mainapp import create_appapp = create_app()
#add host as 0.0.0.0 to be accessible from outside when running as a #container
if __name__ == "__main__":
   app.run(host='0.0.0.0')
```

我们需要一个 wsgi 服务器来运行这个 python 应用程序。为此，我们将使用 gunicorn。脚本`appentry.sh`将用于启动这个 wsgi 服务器。脚本是指`app.py`中的 app(文件)和可调用 app。

该脚本将在`port 8000`中公开应用程序，并将有两个工人和两个线程。这些可以根据您的硬件能力进行更改。

```
#!/bin/bash
python3 -m gunicorn app:app -w 2 --threads 2 -b 0.0.0.0:8000
```

要启动应用程序，请执行以下操作:

```
#run the application by calling appentry.sh script
$ ./appentry
```

通过调用不需要数据库连接的/sayhello API 路由来测试应用程序。

```
$ curl [http://localhost:8000/sayhello](http://localhost:8000/sayhello)
{"greetings":"Hello there"}
```

现在，您可以添加任意数量的模块，并且结构清晰。你可以在我的 git [库](https://github.com/Wainaina3/publications.git)中找到上面的代码。