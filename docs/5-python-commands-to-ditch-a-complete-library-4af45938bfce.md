# 抛弃完整库的 5 个 Python 命令

> 原文：<https://betterprogramming.pub/5-python-commands-to-ditch-a-complete-library-4af45938bfce>

## 其中四个出现在每一个 Python 基础训练中

![](img/7b0437eab27cdfdfedce7d65850c7539.png)

[穆利亚迪](https://unsplash.com/@mullyadii?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

从源代码中分离敏感信息的最佳方式是利用环境变量(EVs)。难道你从来没有把这些硬编码到你的文件里吗:

*   秘密密钥和 API 密钥
*   电子邮件和允许的 Web 主机地址
*   数据库和 SMTP 凭据

这些变量有些是字符串类型，有些是整数，有些是布尔型，列表等。但是，EVs 总是存储为字符串。这肯定会造成一片混乱，因为:

*   “假”并不等同于假
*   128 不能用“128”代替
*   "('用户'，'主机'，'地址')"是单个值，而不是三项列表

那么，如何将这些字符串值转换成应用程序可以识别和理解它们真正代表的变量:布尔值、整数、列表或字典？

简单的解决方案:安装 [Python-decouple](https://github.com/henriquebastos/python-decouple) ，学习它并在您的代码中使用。

另一个简单的解决方案是:继续阅读，并使用 Python 内置函数。

尽管这可能与代码重用的原则背道而驰，但我更喜欢在学习特定的库之前深入研究 Python 本身。我意识到自己找到解决方案是一次很有价值的学习经历。

# 声明环境变量

在开发阶段，您可能会使用一个虚拟环境( *pipenv* )来包装您的项目并管理依赖关系。创建一个名为“”的文件。env ",并声明所有变量。

```
#Folders Tree
Root-Folder\
   .git\
   .venv\
   app1\
   app2
   projectfolder\
   static\
   **.env ** 
```

”的例子。env "文件:

```
SECRET_EV = 'pass'
PORT_EV = '5432'
DEBUG_EV = 'True'
ALLHOSTS_EV = '.localhost,*'
DB_EV = '{"ENGINE": "django.db.backends.sqlite3", "NAME": "db.sqlite3"}'
```

当虚拟环境启动(`pipenv shell`)时。env”文件，并使这些值可用，以便应用程序可以检索它们。

# 检索变量

环境变量加载到操作系统中，所以需要用`import os`导入 OS 模块。

然后，您可以访问这些值，并将它们分配给 *settings.py* 文件中的可用变量，或者代码中任何您需要的地方。

## #1 —字符串变量

对于字符串变量，不需要转换，因为 ev 已经是字符串了。因此，使用一个简单的命令来检索变量，有一个改进:用*删除前导/尾随空格。strip()* 命令。

```
#.env
SECRET_EV = 'pass'#settings.py
import osSECRET_KEY = os.environ.get('SECRET_EV') #basic
SECRET_KEY = os.environ.get('SECRET_EV')**.strip()** #improved
```

## #2 —整数变量

连接端口需要整数值，字符串“5432”不同于整数 5432 …是吗？

对于 Heroku PostgreSQL 数据库(托管在 AWS 中)，无论您是将整数格式还是字符串格式解析为端口值，它都会接受。

但这并不一定适用于网络上的所有服务，所以最好使用内置函数 *int()* 将字符串转换为整数。

```
#.env
PORT_EV = '5432'#settings.py
import osDB_PORT = **int(**os.environ.get('PORT_EV')**)**
```

## #3 —布尔变量

调试需要一个布尔类型值(真或假)，由于[假阳性陷阱](https://medium.com/@renan.carlos/the-false-positive-trap-in-python-df804bd01b70)，字符串不能替换布尔类型变量。

由于这个陷阱，也不要使用`bool()`功能。见下文:

```
>>> a = 'True'
>>> bool(a)
True
>>>
>>> b = 'False'
>>> bool(b)
True
```

要正确地将字符串转换为布尔值，有两种方法:

当“false”是标准时(如 Django 中的 DEBUG)，我将字符串与“true”进行比较。如果它们匹配，比较返回 True。如果不是，则返回 False。

```
#.env
DEBUG_EV = 'True'#settings.py
import osDEBUG = os.environ.get('DEBUG_EV').strip().lower() == 'true'
```

而是，如果标准是‘真’，我就和‘假’比较，取反答案——`not`再比较。如果它们匹配，返回将是假的(反真)。如果不是，其他的都将返回 True(反 False)。

```
DEBUG = **not** os.environ.get('DEBUG').strip().lower() == 'false'
```

## #4 —列表和元组

允许的主机是需要列表的设置之一。内置的 *split()* 函数从一个字符串中创建一个列表，使用一个分隔符参数来识别一个项目的结束和另一个项目的开始。

```
#.env
ALLHOSTS_EV = '.localhost,*'#settings.py
import osALLOWED_HOSTS = os.environ.get('ALLHOSTS_EV').split(sep=",")
```

如果需要一个 Tuple 来代替，将环境变量 string 值转换成一个 List，然后将其包装在 Python 内置的 *tuple()* 函数中。

```
ALLOWED_HOSTS = **tuple(**os.environ.get('ALLHOSTS_EV').split(sep=",")**)**
```

## #5 —字典变量

最后，如果需要一个包含一组可变参数的字典，那么基本级别的 toolbelt 解决方案就不再有帮助了。例如，用于 SQLite3 的 Django SQL 数据库声明。

```
#settings.py (SQLite3)
DATABASES = {
   'default': {
      'ENGINE': 'django.db.backends.sqlite3',
      'NAME': 'db.sqlite3'
   }
}#DICT = {'key1': 'value1', 'key2': 'value2'}
```

注意，Python 字典只不过是一系列的键和值。如果用一个字符串表示，它就是**和**一样的 JSON 值。正因为如此，内置的 Python JSON 模块将非常有帮助。

JSON 模块有 *loads()* 方法。它理解 JSON 字符串的模式，提取带有相应值的键，并将它们解析到 Python 字典中——这正是我们所需要的。

```
#.env (SQLite3)
DB_EV = '{"ENGINE": "django.db.backends.sqlite3", "NAME": "db.sqlite3"}'#settings.py
import os
import jsonDATABASES = {'default': json.loads(os.environ.get('DB_EV').strip())}
```

一开始看起来很难，但这是一个简单而灵活的解决方案。上面的例子适用于我用于开发的 SQLite3。然而，对于生产，我使用 PostgreSQL，它需要额外的键/值对。

```
#.env (PostgreSQL)
DB_EV = '{"ENGINE": "django.db.backends.postgresql_psycopg2", "NAME": "database-name", "USER": "user-name", "PASSWORD": "user-password", "HOST": "host-address", "PORT": "port-number"}'#settings.py
import os
import jsonDATABASES = {'default': json.loads(os.environ.get('DB_EV').strip())}
```

代码( *settings.py* )里写的东西对于开发(SQLite3)和生产(PostgreSQL)是一样的。只有环境变量会根据每个工作环境而不同。

这确保了在这两种情况下，所有数据库参数都将被相应地声明，并建立正确的连接。