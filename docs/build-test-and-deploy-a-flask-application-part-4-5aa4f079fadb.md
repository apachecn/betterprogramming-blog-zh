# 构建、测试和部署 Flask 应用程序:第 4 部分

> 原文：<https://betterprogramming.pub/build-test-and-deploy-a-flask-application-part-4-5aa4f079fadb>

## 重组测试

![](img/fda9f05d78464b27aef221a3ef24b2c0.png)

照片由 [Yomex Owo](https://unsplash.com/@yomex4life?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 关于本教程的元信息

## 学习目标

*   单元测试

注:本教程是 [*科学学烧瓶*](https://medium.com/@neohao/learn-flask-in-a-scientific-way-baf4d8055f6e) *系列的一部分。*

## 源代码

*   本教程的源代码可以在 [GitHub](https://github.com/Neo-Hao/mean-review-collector) 上获取。
*   该演示可在 https://pacific-fortress-91193.herokuapp.com/的[获得。](https://pacific-fortress-91193.herokuapp.com/)
*   仅涵盖本教程的版本可在此处访问[。](https://github.com/Neo-Hao/mean-review-collector/tree/249a754ec97fcbb984d529aa834058b0ce0b250f)

# 将应用程序作为软件包安装

随着我们转向使用应用工厂和蓝图，我们测试代码也需要一些升级。为了使采用应用工厂和蓝图的应用程序对不同文件夹下的测试代码可见，我们需要首先将应用程序作为一个包安装。实现这一点有三个步骤。

首先，我们需要记录所有的包依赖关系。这一点很重要，尤其是当您需要在不同的机器/虚拟环境中复制您所拥有的东西时(例如，部署)。为此，通过终端导航到目录的根目录(`mean-review-collector`)，然后键入:

```
(env) $ pip freeze >requirements.txt
```

您将看到一个在根目录下生成的文件`requirements.txt`,它包含了包名和它们的确切版本号。

```
Flask==1.1.1
Jinja2==2.10.3
...
wcwidth==0.1.7
Werkzeug==0.16.0
zipp==0.6.0
```

当您想要在不同的虚拟环境中构建副本时，只需键入:

```
(env) $ pip install -r requirements.txt MANIFEST.in
```

其次，我们需要描述我们的项目。描述依赖于两个文件:`setup.py`和`MANIFEST.IN`。这两个文件是使我们的`app`可安装所需要的。`setup.py`包含关于我们申请的基本信息:

```
from setuptools import find_packages, setupsetup(
    name='app',
    version='0.1',
    packages=find_packages(),
    include_package_data=True,
    zip_safe=False,
    install_requires=[
        'flask',
    ],
)
```

`include_package_data=True`表示我们希望为此项目包含额外的数据。`MANIFEST.IN`包括本项目数据的基本信息，如模板和`sql`文件；

```
include app/schema.sql
graft app/static
graft app/templates
global-exclude *.pyc
```

第三，我们需要将自己的代码作为一个包安装在`app`中。使用`pip install`，安装只有一行:

```
(env)$ pip install -e .
```

然而，`install`值得一些详细的解释。通常，当你通过`pip nstall`安装 Python 包时，`pip`会在 Python 包索引中寻找目标包。但是，pip 也可以查找其他地方的包(例如，本地机器上的代码)。

上面的一行代码实际上是将您的代码复制到`env`文件夹中，并像您在这个虚拟环境中安装的任何其他包一样对待它。圆点表示您当前的目录。标志`-e`使它成为一个可编辑的安装——如果您对项目文件夹中的文件做进一步的更改，这些更改也会自动反映在`env`文件夹中。

现在，您可以检查该虚拟环境中所有已安装的软件包:

```
(env)$ pip list
```

您将看到如下内容:

```
Package            Version Location                               
------------------ ------- ---------------------------------------
app                0.1     /home/qiang/repos/mean-review-collector
attrs              19.3.0  
Click              7.0     
Flask              1.1.1   
...
```

# 单元测试

我们要做的下一件事是重写我们的单元测试用例。我们使用`unittest`只是为了方便。从现在开始，我们将从`unittest`切换到`pytest`。虽然`pytest`不在 Python 的标准库中，但是在很多方面都比`unittest`好。要安装`pytest`和另一个工具`coverage`(测量你的测试的代码覆盖率)，我们只需要通过你的终端输入:

```
(env)$ pip install pytest coverage
```

默认情况下，`pytest`只标识名称以`test_`开头或以`_test`结尾的测试文件或方法。我们至少要测试三个方面，包括工厂、数据库和认证，所以我们需要在`tests`目录下有三个文件:

```
mean-review-collector
 - env
 - app
 - instance
 - tests   
   - test_auth.py
   - test_db.py
   - test_factory.py
```

当所有的测试代码都准备好了，您只需在您的终端上输入 pytest 就可以运行所有的测试代码，您可能会看到类似如下的内容:

```
(env)$ pytest
=============== test session starts ===========================
platform linux -- Python 3.7.4, pytest-5.3.1, py-1.8.0, pluggy-0.13.1
rootdir: /home/qiang/repos/mean-review-collector
collected 7 itemstests/test_auth.py .. [ 42%]
tests/test_db.py .. [ 71%]
tests/test_factory.py .. [100%]
```

在我们急于在每个文件中编写测试代码之前，我们应该认识到，在任何单元测试之前，某些代码总是需要首先执行，例如连接到数据库和启动一个应用程序对象。`pytest`使用`fixture`来处理这样的代码。fixtures 的目的是提供一个固定的基线，测试可以在这个基线上可靠地重复执行。为了实现这一点，我们将把这样的代码放在一个名为`conftest.py`的不同文件中。例如，我们当然需要一些代码来帮助我们启动一个应用程序对象:

```
#conftest.py@pytest.fixture
def app():
  db_fd, db_path = tempfile.mkstemp() app = create_app({
        'TESTING': True,
        'DATABASE': db_path,
  }) with app.app_context():
        init_db() yield app os.close(db_fd)
  os.unlink(db_path)
```

`pytest`使用装饰器将一个函数标识为 fixture——一个应该在每次测试之前首先执行的函数。

尽管对数据库和认证的测试与我们使用`unittest`的旧测试代码非常相似，但我们仍然想强调两个不同之处。

首先，parametrize 可用于测试函数的各种输入:

```
# test_auth.py@pytest.mark.parametrize(('username', 'password', 'message'), (
  ('', '', b'Username is required.'),
  ('[a@c.com](mailto:a@c.com)', '', b'Password is required.'),
  ('[a@b.com](mailto:a@b.com)', 'test', b'already registered'),
))
def test_register_validate_input(client, username, password, message):
  response = client.post(
    '/register',
    data={'username': username, 'password': password}
  )
  assert message in response.data
```

这是通过使用`pytest.mark`decorator 测试函数来实现的。

其次，monkeypatch 可能是一个有用的测试工具。以下代码基本上遵循了 [Flask 官方教程](https://flask.palletsprojects.com/en/1.1.x/tutorial/tests/)的指导:

```
def test_init_db_command(runner, monkeypatch):
  class Recorder(object):
    called = False def fake_init_db():
    Recorder.called = True monkeypatch.setattr('app.db.init_db', fake_init_db)
  result = runner.invoke(args=['init-db'])
  assert 'Initialized' in result.output
  assert Recorder.called
```

然而，官方教程并没有真正解释 monkeypatch。monkeypatch 在运行时动态地改变一个软件(例如，模块、对象、方法或函数)。Pytest 使用这个特性来测试您不想实际执行的函数或方法。

例如，我们有一个名为 get_info 的函数，如下所示:

```
def get_info():
  *"""
  call GET for http://XXX/get
  returns status code and url in HTTP response
  """* r = requests.get(base_url + 'get')

  if r.status_code != 200:
    return r.status_code, ''
  else:
    response_data = r.json()
    return r.status_code, response_data["url"]
```

我们可以使用 monkeypatch 测试这个函数，而不需要让它实际发送 HTTP 请求:

```
def test_get_info(monkeypatch):class ResponseMock(object):
    def __init__(self):
      self.status_code = 200
      self.url = ''
      self.headers = {}

    def json(self):
      return {'account': '123','url': 'http://test.com'}

    def get_patched(url):
        return ResponseMock()

    monkeypatch.setattr(requests, 'get', get_patched)
    assert get_info() == (200, 'http://test.com')
```

monkeypatch 在这里实现的本质上是用您在`get_patched`下定义的内容替换`requests.get()`的行为。同样，在我们的`test_init_db_command`方法中:

```
monkeypatch.setattr('app.db.init_db', fake_init_db)
```

我们本质上给`app.db.init_db`附加了一些新的行为。新行为正在`fake_init_db`下定义。这个方法实际实现的唯一事情是添加一个新的字段记录器，调用`app.db.init_db`来跟踪它是否被执行。

请点击查看我们重组后的单元测试[的最终代码。](https://github.com/Neo-Hao/mean-review-collector/tree/249a754ec97fcbb984d529aa834058b0ce0b250f)

# 教程列表

*   [构建并测试迷你烧瓶应用程序](https://medium.com/@neohao/build-test-and-deploy-a-mini-flask-application-1d9ca6c45115)
*   [构建、测试和部署 Flask 应用程序:第 1 部分——模板](https://medium.com/better-programming/build-test-and-deploy-an-interactive-flask-application-part-i-templates-53a7b0cbe760)
*   [构建、测试和部署 Flask 应用程序:第 2 部分——认证](https://medium.com/better-programming/build-test-and-deploy-a-flask-application-part-2-53f2c8df3ebc)
*   [构建、测试和部署 Flask 应用程序:第 3 部分——应用程序工厂和蓝图](https://medium.com/@neohao/build-test-and-deploy-a-flask-application-part-3-3a2abfe4be21)
*   [构建、测试和部署 Flask 应用程序:第 4 部分——重构测试](https://medium.com/@neohao/build-test-and-deploy-a-flask-application-part-4-5aa4f079fadb)
*   [构建、测试和部署 Flask 应用程序:第 5 部分—认证(续)](https://medium.com/@neohao/build-test-and-deploy-a-flask-application-part-5-4a3c0bc36b8e)
*   [构建、测试和部署 Flask 应用程序:第 6 部分——评审系统](https://medium.com/@neohao/build-test-and-deploy-a-flask-application-part-6-952a1b29a02a)
*   [构建、测试和部署 Flask 应用程序:第 7 部分——部署](https://medium.com/@neohao/build-test-and-deploy-a-flask-application-part-7-60dde9080330)