# 3 个 Python 包来隐藏代码中的密码和秘密

> 原文：<https://betterprogramming.pub/hiding-passwords-secrets-in-python-397ecd929050>

## 保持密码和秘密与代码分离，使它们对每个人都隐藏起来。

![](img/3477de465d8ff071f23a59274ad39af8.png)

照片由[马頔·佩丘林](https://unsplash.com/@pechka?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

每当任何项目启动时，项目中都会使用各种机密和密码，例如令牌、AWS 密钥、第三方 API 机密、数据库配置和密码。通常我们会将它们作为常量硬编码在代码中。

将它们硬编码在代码中并不是一个好主意，因为这会降低应用程序的安全性，需要为 QA 和 prod env 更改这些值。此外，如果代码被上传到 GitHub 或任何 VCS 云上，那么秘密就会暴露给所有人。

为了使应用程序更加安全，并使这些秘密对每个人都隐藏起来，最好将这些秘密用作环境变量或配置文件。在 Python 中，有各种各样的包可以用来做这件事，比如:`[python-decouple](https://pypi.org/project/python-decouple/)`、`[python-dotenv](https://pypi.org/project/python-dotenv/)`、`[jproperties](https://pypi.org/project/jproperties/)`。

让我们深入了解它们中的每一个，以了解流程和用法。

# Python-dotenv

Python-dotenv 从一个`.env`文件中读取键值对，并可以将它们设置为环境变量。它有助于遵循 [12 因素](http://12factor.net/)原则的应用程序开发。

## **安装**

```
pip install python-dotenv
```

## **用途**

在项目中创建一个`.env`文件，把所有的秘密和密码保存在里面。`python-dotenv`支持的`.env`文件的语法类似于 Bash:

```
# Comment goes here
USERNAME=testusername
ADMIN_USERNAME=admin@${USERNAME}
PASSWORD=1234567sa
```

将`.env`文件包含为:

```
from dotenv import load_dotenv

load_dotenv()  # take environment variables from .env.

# Code of your application, which uses environment variables (e.g. from `os.environ` or
# `os.getenv`) as if they came from the actual environment.
username=os.getenv('USERNAME')
```

并且永远不要忘记在`.gitignore.`中加上`.env`

如果我们想在代码中改变这些值，那么不要直接使用`dotenv_values`，它给出了所有值的值。然后我们可以根据我们的需求来消费它

```
import os
from dotenv import dotenv_values

config = {
    **dotenv_values(".env.shared"),  # load shared development variables
    **dotenv_values(".env.secret"),  # load sensitive variables
    **os.environ,  # override loaded values with environment variables
}
```

实际上，`python-dotenv`是使用`.env`变量的最简单的方法，不用将它们声明为`os`变量。

# python-解耦

顾名思义，它将秘密文件从代码中完全分离出来，类似于`python-dotenv`，但有一些附加功能。*解耦*帮助您组织您的设置，以便您可以更改参数，而无需重新部署您的应用程序。

## **安装**

```
pip install python-decouple
```

## **用法**

它还可以从`.env`或`.ini`文件中获取秘密，但是它有一个优势，即可以转换值或输入默认值，如果我们直接使用`os environment or with python-dotenv.`的话，这在现实中是没有的

```
# .env file
USERNAME=testusername
ADMIN_USERNAME=admin@${USERNAME}
PASSWORD=1234567sa
NO_USERS=10
IS_DEV=False
```

在主 python 脚本中，将其用作:

```
from decouple import configusername = config('USERNAME')
admin_username = config('ADMIN_USERNAME',default='admin')
password = config('PASSWORD')
no_users = config('NO_USERS',default=10,cast=int)
is_dev = config('IS_DEV',default=False,cast=bool)
```

默认情况下，解耦返回的所有值都是字符串，毕竟它们是从文本文件或`envvars`中读取的。为了将它们转换成期望的类型，config 函数接受一个 cast 参数，该参数接收任何可调用的，这将用于将字符串值转换成其他值。

*解耦*总是按此顺序搜索*选项*:

1.  环境变量。
2.  资源库:`ini`或`.env`文件。
3.  传递给配置的默认参数。

为什么不直接使用环境变量呢？

Env 变量可以工作，但是因为`os.environ`只返回字符串，所以很棘手。假设你有一个*env var*。如果你跑步:

```
if os.environ['IS_DEV']:
    print True
else:
    print False
```

它将打印`True`，因为`os.environ[“IS_DEV”]`返回字符串`False`。因为它是非空字符串，所以将被计算为`True`。解耦提供了一个看起来不像变通办法的解决方案:

```
config(“IS_DEV”, cast=bool)
```

# j 属性

`jProperties`是一个用于 Python 的 Java 属性文件解析器和编写器。它旨在提供与 [Java 的属性类](http://docs.oracle.com/javase/7/docs/api/java/util/Properties.html)相同的功能。

我们还可以使用这些 java 属性文件来保存秘密，并使用`jproperties`将它们加载到 python 代码的开头。

## **安装**

```
pip install jproperties
```

## **用法**

首先，创建一个包含所有机密和密码的属性文件，如下所示:

```
# test.properties file
USERNAME=testusername
ADMIN_USERNAME=admin@${USERNAME}
PASSWORD=1234567sa
NO_USERS=10
IS_DEV=False
```

现在，如果我们想在代码中使用它们，那么我们首先需要加载文件并获取秘密，如下所示:

```
from jproperties import Propertiessecrets = Properties() # create a property objectwith open("test.properties", "r") as f:
    secrets.load(f, "utf-8") # load the test.properties fileusername = secrets["USERNAME"].data
password = secrets["PASSWORD"].data
```

不仅变量值支持元数据信息。请访问[元数据支持](https://pypi.org/project/jproperties/)的官方文档。

不要按代码中的`.env`、`.ini`或`.properties`。同时确保在`.gitignore.`中标记它们

如果你正在使用上面的任何一个包，那么在`Readme.md`中记录好关于制作`.env`文件或属性文件的细节，这样无论是谁使用你的代码都知道在运行代码之前创建了这些文件。

继续学习，继续编码谢谢。

接下来阅读 [Python 代码格式化程序黑色。](https://link.medium.com/byx9SAuammb)