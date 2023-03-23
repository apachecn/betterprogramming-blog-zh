# 创建自己的 API 的 3 种方法

> 原文：<https://betterprogramming.pub/3-ways-to-create-your-own-api-f99f7fb739b1>

## 从头开始创建 API 的三种初学者友好的方法

![](img/fe429b99158d412bb1bef0e651d3cbea.png)

由 [Lorenzo Herrera](https://unsplash.com/@lorenzoherrera?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

API 无处不在，它们在现代技术中扮演着至关重要的角色。从支付平台到股市更新，如今一切都是 API，学习自己创建一个 API 是成为全栈开发人员的基本步骤。

*注意:如果你不确定什么是 API，我有* [*一个方便的介绍给你*](https://www.elletownsend.co.uk/blog/posts/top-5-apis/) *。*

在本文中，我将讨论三种初学者友好的创建 API 的方法:

1.  JSON 服务器
2.  Node.js 和 Express
3.  Python 和 Flask RESTful

每种方法都有不同的用例，并使用不同的技术。你想和哪一个一起去由你决定。

# JSON 服务器

*要求:* [*节点安装*](https://nodejs.org/)

JSON Server 是一个可以通过 NPM 获得的包，它允许我们仅使用一个 JSON 文件创建一个带有 CRUD(创建、读取、更新、删除)操作的 REST API。

首先，我们需要通过在终端中运行以下命令来安装该包(全局地):

```
npm install -g json-server
```

现在我们需要一些数据来服务。用一些数据创建一个`db.json`文件(这可以是您希望 API 提供的任何内容):

```
{ 
  "dogs": [
    { "id": 1,
      "breed": "pembroke-welsh-corgi",
      "name": "Charles"
    },
    { "id": 2,
      "breed": "pug",
      "name": "cheese"
    },
    { "id": 3,
      "breed": "dachshund",
      "name": "ralph"
    }
  ],
  "cats": [
    { "id": 1,
      "breed": "ragdoll",
      "name": "mimi"
    },
    { "id": 2,
      "breed": "siamese",
      "name": "mocha"
    },
    { "id": 3,
      "breed": "russian-blue",
      "name": "bean"
    }
  ]
}
```

为了启动并运行我们的服务器，请运行:

```
json-server --watch db.json --port 8080
```

如果我们现在向[http://localhost:8080/dogs/1](http://localhost:8080/dogs/1)发出请求，我们应该得到:

```
{ "id": 1, "breed": "pembroke-welsh-corgi", "name": "charles" }
```

JSON 服务器也将自动创建以下 HTTP 端点:

```
GET /dogs 
GET /dogs/{id} 
GET /cats 
GET /cats/{id} POST /dogs 
POST /cats PUT /dogs/{id} 
PUT /cats/{id} PATCH /dogs/{id} 
PATCH /cats/{id} DELETE /dogs/{id} 
DELETE /cats/{id}
```

使用 JSON Server，我们可以通过向请求添加查询参数来过滤结果。如果我们打 [http://localhost:8080/cats？breed=siamese](http://localhost:8080/cats?breed=siamese) ，它应该返回数据中符合给定标准的所有 cat 对象的数组。

我们也可以用`p`参数做全文搜索: [http://localhost:8080/cats？q=m](http://localhost:8080/cats?q=m) 。

所有这些信息以及更多信息都可以在 JSON 服务器 GitHub repo 中找到。

如果您想更深入地了解这一点，并找出如何部署您的 JSON 服务器 API，我推荐 [Ania Kubow 的 YouTube 教程](https://www.youtube.com/watch?v=FLnxgSZ0DG4&feature=youtu.be)关于如何创建和部署模拟 API。

# Node.js 和 Express.js

*要求:* [*节点安装*](https://nodejs.org/)

对于这种方法，我们将创建一个新的快速应用程序。为此，创建一个名为`node-api`的项目文件夹，并在其中创建一个`server.js`文件。完成后，在我们的`node-api`文件夹中打开一个新的终端并运行:

```
npm init
```

这将初始化我们的项目并创建一个`package.json`文件(对于设置问题，只需按回车键并保留所有内容为空/默认即可)。

现在我们需要安装 Express！我们可以通过运行以下命令来实现 NPM:

```
npm install express
```

在我们的`server.js`文件中，我们现在可以如下创建我们的服务器:

```
var express = require("express");var app = express();app.listen(8080, () => {
 console.log("Server running on port 8080!");
});
```

我们需要的东西都用快递准备好了。现在我们将为我们的请求创建端点。在本教程中，我们将仅为 GET 请求创建端点。

我们可以使用两个包含数据对象的常量变量将数据添加到文件中，如下所示:

在`var app = express();`之后，让我们添加一些函数来处理 GET 请求，用“200 OK”状态代码发送回所请求资源上的所有数据:

为了通过 ID 获取特定的条目，我们必须使用`:id` URL 参数来搜索我们的数组，以找到匹配给定 ID 的条目。在我们的标准 GET 请求下，添加以下内容:

我们现在可以访问[http://localhost:8080/dogs/](http://localhost:3000/posts/1)和[http://localhost:8080/dogs/1](http://localhost:3000/posts/1)或[http://localhost:8080/cats/](http://localhost:3000/posts/1)(等等。)并看到我们的 API 正常工作。我们已经创建了一个非常简单的节点 API！

如果你想进一步开发它，并为你的节点 API 添加更多的端点和功能，我推荐 [Ankit Maheshwari 的 CRUD Rest API 教程](https://medium.com/javascript-in-plain-english/create-rest-api-web-services-using-node-js-and-express-js-with-crud-operations-ff790d6ae030)用简单的英语从 JavaScript 开始。

# Python 和 Flask RESTful

*需求:*[*Python 3*](https://www.python.org/downloads/)*和 PIP 安装*

首先，我们需要通过运行以下命令来安装 [Flask-RESTful](https://flask-restful.readthedocs.io/en/latest/) :

```
pip install flask-restful
```

[Flask RESTful](https://flask-restful.readthedocs.io/en/latest/) 是一个 Python 模块，它扩展了 Python Flask 模块的功能，帮助我们快速构建 REST APIs。

首先，在一个新的 Python 项目文件中，创建一个`api.py`文件，并在顶部导入以下内容:

```
from flask import Flask
from flask_restful import Api, Resource
```

然后我们必须创建一个`Flask`类的实例，并将其传递给一个`Flask` RESTful API 的实例:

```
app = Flask(__name__)
api = Api(app)
```

接下来，让我们创建一些列表来存储我们的数据:

现在来看看更复杂的东西。根据 [Flask RESTful 文档](https://flask-restful.readthedocs.io/en/latest/)，我们需要创建资源类来定义我们的 HTTP 方法。我们必须为猫/狗列表创建一个资源，为单个猫/狗创建一个资源(按 ID):

为了将这些资源添加为我们的 API 的端点，我们将使用`add_resource()`方法并传入我们的资源和我们希望从其访问这些资源的各个端点:

```
api.add_resource(DogList, '/dogs')
api.add_resource(Dog, '/dogs/<int:dog_id>')

api.add_resource(CatList, '/cats')
api.add_resource(Cat, '/cats/<int:cat_id>')
```

最后，我们在文件的末尾添加这一行:

```
if __name__ == '__main__':
    app.run(debug=True)
```

这里，我们在运行代码时启用 Flask 调试模式，这样当发生更改时它会重新加载(像 nodemon 一样),并在出错时给出更详细的错误消息。

现在如果你尝试[http://localhost:5000/dogs](http://localhost:5000/dogs)、[http://localhost:5000/](http://localhost:5000/dogs)cats、[http://localhost:5000/dogs/1](http://localhost:5000/dogs)等。，您将看到您的 API 完美地工作！

如果你想进一步开发你的 Python Flask API 项目，我推荐 [RapidAPI 的 Python APIs 指南](https://rapidapi.com/blog/how-to-build-an-api-in-python/)。

# 结论

现在你已经知道了:REST APIs 有三种不同的方式！我希望这篇文章能帮助你开始开发自己的 API。

这三种方法的所有示例文件都可以在 GitHub 上找到[。](https://github.com/elletownsend/create-an-api)