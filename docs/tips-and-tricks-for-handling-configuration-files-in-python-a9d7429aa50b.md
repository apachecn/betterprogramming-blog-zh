# 用 Python 处理配置文件的技巧和诀窍

> 原文：<https://betterprogramming.pub/tips-and-tricks-for-handling-configuration-files-in-python-a9d7429aa50b>

## 利用 ConfigParser 在 Python 中读写配置文件

![](img/ee5650f63b5c2f91a1843d3a1fee030d.png)

由[杯先生/杨奇煜·巴勒](https://unsplash.com/@iammrcup?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/file?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

通过阅读本文，您将了解用 Python 管理配置文件所需的基本步骤。本教程重点介绍一个名为`ConfigParser`的 Python 模块。基于官方[文档](https://docs.python.org/3/library/configparser.html) , `ConfigParser`是一个:

> “…实现基本配置语言的类，该语言提供类似于 Microsoft Windows INI 文件中的结构。您可以使用它来编写 Python 程序，最终用户可以轻松地对其进行定制。”

本教程有四个部分:

1.  设置
2.  文件格式
3.  基本 API
4.  结论

让我们进入下一部分，安装该模块。

# 1.设置

`ConfigParser`模块是 Python 3 库的一部分，当你安装 Python 3 的时候就应该有了。您可以使用以下代码轻松导入它:

```
import configparser
```

如果您使用的是 Python 2.6–3.5，可以尝试以下导入语句:

```
from backports import configparser
```

除此之外，如果您想编写同时适用于 Python 2/3 的代码，请使用以下代码:

```
try:
    import configparser
except:
    from six.moves import configparser
```

# 2.文件格式

您可以根据自己的喜好来命名配置，但是强烈建议将扩展名保持为`ini`。

一个配置文件由一个或多个基于开始和结束括号标题`[section_name]`识别的部分组成。每个部分将包含由`=`或`:`符号分隔的键值条目。我将在本教程中使用等号。允许在键值条目之间有空格。您甚至可以使用`#`或`;`前缀添加评论。

让我们来看一个配置文件的例子:

```
[default]
host = 192.168.1.1
port = 22
username = username
password = password[dev_database]
port = 22
forwardx11 = no
name = db_test
```

配置文件中有两个部分，每个部分都有自己的键值条目。您可以创建自己的配置文件或通过代码生成它。让我们进入下一部分，了解更多信息。

# 3.基本 API

我们需要做的第一件事是初始化一个`ConfigParser`对象。

```
config = configparser.ConfigParser()
```

## 写

我们可以很容易地创建默认节，通过用字典初始化该节来使用。

```
config['default'] = {
        "host" : "192.168.1.1",
        "port" : "22",
        "username" : "username",
        "password" : "password"
    }
```

*   `default` —部分的名称
*   `“host” : “192.168.1.1”` —配置文件的键值对条目

此外，您可以将它初始化为一个空字典，然后逐行添加条目。这为我们提供了更多的灵活性。让我们来看看如何为`dev_database`部分做这件事。

```
config['dev_database'] = {}
    config['dev_database']['port'] = "22"
    config['dev_database']['forwardx11'] = "no"
    config['dev_database']['name'] = "db_test"
```

*   `dev_database` —区间名称
*   `port` —条目的键
*   `22` —条目的值

一旦完成，就可以开始将内容写入配置文件。

```
with open('test.ini', 'w') as configfile:
        config.write(configfile)
```

一旦运行代码，您应该能够看到一个生成的`test.ini`文件。

## 阅读

让我们试着读取您刚刚生成的配置文件。

```
config.read('test.ini')
```

您可以使用下面的 API 调用来标识配置文件中出现的部分。

```
config.sections()
```

接下来，我们将尝试获取数据库的名称。

```
config['dev_database']['name']
```

*   `dev_database` —部分的名称
*   `name` —用于输入的键

你应该可以得到`db_test`作为输出。如果您想获得一个部分的所有键值条目，该怎么办？让我们用下面的代码试一试，将所有的值打印到`default`部分。

```
for key in config['default']:
        print(config['default'][key])
```

## 数据类型

默认情况下，模块会在内部将数据解析为字符串。您需要手动或者使用 getter 方法来转换它们。

```
config['default'].getint('port')
```

有三种 getter 方法可用于转换:

*   `getint`
*   `getboolean`
*   `getfloat`

让我们用另一种方法来试试:

```
config.getboolean('dev_database', 'forwardx11')
```

请注意，第二个参数是条目的键，而不是回退结果。

## 撤退

如果您想要一个回退结果，您可以将其定义如下:

```
config.get('default', 'database', fallback='prod_database')
```

如果找不到数据库关键字或找不到节，将返回结果`prod_database`。

# 4.结论

让我们回顾一下今天所学的内容。首先，我们通过编写几行导入代码开始设置。

接下来，我们详细探讨了配置文件的格式，它由部分和键值条目组成。建议保留扩展名为`ini`。

然后，我们测试了模块中可用的一些基本 API。这包括将配置写入文件和读取现有的配置文件。我们甚至可以使用 getter 方法将数据转换成特定的数据类型。fallback 参数可用于在找不到节或键的情况下提供回退响应。

感谢阅读，希望在下一篇文章中再次见到您。

# 参考

1.  [https://docs.python.org/3/library/configparser.html](https://docs.python.org/3/library/configparser.html)
2.  【https://github.com/jaraco/configparser 号