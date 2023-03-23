# 带有 Flask 和 PostgreSQL 的 CRUD API

> 原文：<https://betterprogramming.pub/crud-api-with-flask-and-postgresql-15548d9ee48e>

## 了解如何创建和测试 Flask API

![](img/b4f75fa2598d2b71018b43c262d6681e.png)

由 [Marvin Meyer](https://unsplash.com/@marvelous?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/internet-connection?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

本教程将构建一个 Flask CRUD 应用程序，允许用户使用 API 创建、读取、更新和删除数据库条目。API 将能够:

*   添加新的宠物条目
*   查看数据库中的所有宠物
*   更新宠物的详细信息
*   删除宠物

## 先决条件

您将使用以下工具和技术。

*   Python3
*   瓶
*   烧瓶化学
*   一种数据库系统
*   邮递员

# 什么是 API

API(应用程序编程接口)是软件程序之间通信的中间件。API 用于在应用程序中的客户端和数据库服务器之间进行通信。

# 超文本传输协议

我们需要理解的另一个重要概念是 HTTP 协议，它将实现 API。HTTP 协议是互联网上数据通信的基础。它描述了数据分布的方式。下图显示了使用 HTTP 的标准通信。

![](img/fb1cc7d3d8fae03437b80329b255ade8.png)

## HTTP 请求方法

HTTP 定义了一组指示要执行什么操作的方法。最常见的 HTTP 请求方法包括:

*   `GET` : Get 请求用于检索资源数据。
*   `POST` : POST 请求用于向服务器发送数据资源。
*   `PUT`:上传请求用于替换现有数据资源
*   `DELETE`:删除请求用于删除数据资源
*   `PATCH`:补丁请求用于修改数据资源

## HTTP 响应

当客户端向服务器发送请求时，服务器返回一个 HTTP 响应，该响应由以下元素组成:

*   `STATUS CODES`
*   `STATUS MESSAGE`

考虑一个简单的对 Google 的 GET 请求:

```
import requestsresponse_obj = requests.get('https://www.google.com/')print(response_obj)print(response_obj.status_code)
```

当您运行上面的代码时，您将得到`<Response [200]>` 和一个状态代码 `200`，这是对成功请求的响应。

```
200
<Response [200]>
```

常见的 HTTP 状态代码包括:

*   200:好的
*   201:已创建
*   304:未修改
*   400:错误请求
*   401:未经授权
*   404:未找到
*   405:不允许的方法
*   500:内部服务器错误

# 使用 Postgresql 的 Flask CRUD 应用程序

Flask 是一个简单的 python 微框架，允许开发人员轻松创建应用程序，因为它不需要工具和库。

创建一个项目目录，

```
mkdir flaskcrud
```

创建并激活虚拟环境

```
cd flaskcrud
python3 -m venv env
source env/bin/activate
```

其中`env`是虚拟环境的名称。虚拟环境确保在一个地方管理项目依赖关系。用 pip 安装`Flask`和`Flask-Sqlalchemy`

```
pip install flask
pip install -U Flask-SQLAlchemy
```

> Flask-SQLAlchemy 是对 [Flask](http://flask.pocoo.org/) 的扩展，为您的应用程序增加了对 [SQLAlchemy](https://www.sqlalchemy.org/) 的支持。它旨在通过提供有用的缺省值和额外的帮助器来简化 SQLAlchemy 与 Flask 的使用，从而更容易完成常见任务。

由于我们将使用 [PostgreSQL](https://www.postgresql.org/) ，对于数据库，让我们用 pip 安装 psycopg2 驱动程序库。

```
pip install psycopg2
```

您应该已经在开发环境中安装了 [PostgreSQL](https://www.postgresql.org/) 。这本[指南](https://www.postgresql.org/)告诉你如何在不同的操作系统上安装它。

在项目目录下，创建一个文件`app.py`

```
touch app.py
```

打开 `app.py`，导入`Flask`，创建一个 flask 应用实例。

```
from flask import Flask
app = Flask(__name__)
```

# 创建新的数据库

PostgreSQL 附带一个 psql，这是一个交互式终端程序，允许您与 PostgreSQL 数据库服务器进行交互。连接到 PostgreSQL 数据库服务器。

```
sudo su postgres
psql
```

该命令在您的操作系统中会有所不同，但原则是相同的。您将看到一个类似这样的`psql`终端

```
psql (9.5.14)
Type "help" for help.postgres=#
```

创建数据库:

```
CREATE DATABASE pets;
```

创建角色

```
CREATE ROLE john WITH LOGIN PASSWORD ‘password’;
```

向您在上面创建的用户授予数据库访问权限。

```
GRANT ALL PRIVILEGES ON DATABASE pets TO john;
```

# 数据库配置

让我们配置数据库配置。通过传递`DATABASE_URI`来加载数据库配置，然后通过将它传递给应用程序来创建 [SQLAlchemy](https://flask-sqlalchemy.palletsprojects.com/en/2.x/api/#flask_sqlalchemy.SQLAlchemy) 对象。

```
from flask_sqlalchemy import SQLAlchemy
app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql+psycopg2://john:password@localhost/pets'
db = SQLAlchemy(app)
```

`app.py`到目前为止应该是这样的:

# 数据库模型

模型代表应用程序中的对象。在这种情况下，对象指的是我们希望存储在应用程序中的宠物。创建一个类 Pet 并声明以下字段:

*   `id`
*   `pet_name`
*   `pet_type`
*   `pet_description`

`db`实例提供了用于创建声明模型的基类`Model`。`__repr__`函数创建了模型对象的人类可读版本。

要创建实际的数据库，用`db.create_all()`命令初始化数据库。

```
db.create_all()
```

或者，您也可以从终端创建数据库。

```
python3.8
from app import db
db.create_all()
```

# 运行服务器。

在开发环境中运行应用程序。

```
export FLASK_APP=app
export FLASK_en=development
flask run
```

`Flask_APP=app`，指定应用程序所在的文件夹。`FLASK_ENV=development`允许服务器在我们每次修改时重启，这样我们就不用重启服务器了。然后 flask run 在 [127.0.0.1:5000](http://127.0.0.1:5000) 启动一个开发服务器。你应该在`flask run`之后看到这个

如果你访问 [127.0.0.1:5000](http://127.0.0.1:5000) ，你应该得到`Not Found`错误。这是因为我们没有创建任何端点

![](img/6017f133acff3fc03bc8c43344672186.png)

未找到错误

# API 端点

flask 中一个简单的 API 端点如下所示。

```
from flask import Flask,jsonify@app.route('/') def index(): return jsonify({"message":"Welcome to my site"})
```

我们使用 `[@app](http://twitter.com/app).route`装饰器来处理请求，并定义一个返回 jsonified 消息的函数索引。

![](img/3edd15ecb0b32ddc12e3a2ecf6a9c9de.png)

# CORS(跨产地资源共享)

您可能在开发应用程序时遇到过这种错误。

```
No 'Access-Control-Allow-Origin' header is present on the requested resource
```

CORS 是一种安全措施，可以阻止来自不同域名或不同协议的资源的请求。它的作用是防止来自可能嵌入在请求中的 javascript 脚本的恶意攻击。只有当一个网页和另一个网页共享同一个域名时，CORS 才会允许它们访问数据。

## 弗拉斯克-CORS

Flask 附带了 FLASK-CORS 扩展，我们将在本教程中使用它来实现 CORS。要安装扩展，请使用 pip。

```
pip install -U flask-cors
```

通过将它传递给应用程序实例，将其添加到应用程序中。

```
from flask-cors import CORS
CORS(app)
```

要在特定的路线上添加跨起点，只需在路线前添加`@cross-origin()`装饰器，如下所示。

```
@cross-origin()
@app.route('/pets')
    def pets()
        pass
```

## 终点后

创建一条路线，用于将宠物条目添加到数据库中。

在上面的代码中，我们指定了一个`POST`方法。`create_pet` 方法将从请求体中获取`pet_name`、`pet_type`、`pet_age`和`pet_description`，然后将记录插入数据库。

现在为了测试 API，我们将使用 Postman。

![](img/b5cdb4f59c5e61da97b8591564c57b43.png)

终点后

## 获取端点

我们将使用 GET 方法返回所有的宠物条目。

下面是使用 Postman 的 GET API。

![](img/66c05767784b7cafbecf4d16f767a225.png)

## 补丁端点

我们将使用`PATCH`方法来更新宠物的详细信息。只能更新宠物的年龄和描述。我们使用`query.get()`获得一个宠物的实例，然后在数据库中更新它。

下面是使用 Postman 的补丁 API。

![](img/0cae6c5cc2b95265d28da9569f6d4103.png)

删除 API

## 删除端点

我们将使用 DELETE 方法从数据库中删除一只宠物。首先，我们将宠物的 id 传递给 URL。然后我们使用`query.get()`获得一个宠物的实例，然后从数据库中删除它。`query.get()`仅返回单个对象。

下面是使用 Postman 的删除 API。

![](img/0cae6c5cc2b95265d28da9569f6d4103.png)

删除 API

# 结论

本教程介绍了如何用 Flask 构建 CRUD API。如果您希望了解如何在 flask 应用程序中处理异常，可以跟随本教程学习:

[](/how-to-handle-exceptions-in-flask-b1d9c151875b) [## 如何处理 Flask 中的异常

### 更好的警报处理有助于更好的编程

better 编程. pub](/how-to-handle-exceptions-in-flask-b1d9c151875b)