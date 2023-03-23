# Pydantic 初级指南

> 原文：<https://betterprogramming.pub/the-beginners-guide-to-pydantic-ba33b26cde89>

## 用于解析和验证数据的 Python 包

![](img/7e0f015eef91ab4420bac9e168a36d31.png)

马克·巴宾在 [Unsplash](https://unsplash.com/s/photos/filter?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

今天的主题是关于使用 Python 类型提示的数据验证和设置管理。我们将使用一个名为`pydantic`的 Python 包，它在运行时强制执行类型提示。它提供用户友好的错误，允许您捕捉任何无效数据。根据[官方文件](https://pydantic-docs.helpmanual.io/)，Pydantic 是

> “…主要是一个解析库，而不是一个验证库。验证是达到目的的一种手段:构建一个符合所提供的类型和约束的模型。
> 
> 换句话说，pydantic 保证输出模型的类型和约束，而不是输入数据。"

本教程分为三个部分:

1.  设置
2.  履行
3.  结论

让我们继续下一部分，开始安装必要的模块。

# 1.设置

强烈建议您在继续安装之前创建一个虚拟环境。

## 基本安装

打开一个终端，运行下面的命令来安装`pydantic`

```
pip install pydantic
```

## 升级现有包

如果您已经有了一个现有的软件包，并且想要升级它，请运行以下命令:

```
pip install -U pydantic
```

## 蟒蛇

对于 Anaconda 用户，您可以按如下方式安装它:

```
conda install pydantic -c conda-forge
```

## 可选依赖项

`pydantic`根据您的需求提供以下可选依赖项:

*   `email-validator` —支持邮件验证。
*   `typing-extensions` —支持使用 Python 3.8 之前的`Literal`。
*   `python-dotenv` —支持带设置的`dotenv`文件。

您可以手动安装它们:

```
# install email-validator
pip install email-validator# install typing-extensions
pip install typing_extensions# install python-dotenv
pip install python-dotenv
```

或与`pydantic`一起如下:

```
# install email-validator
pip install pydantic[email]# install typing-extensions
pip install pydantic[typing_extensions]# install python-dotenv
pip install pydantic[dotenv]# install all dependencies
pip install pydantic[email,typing_extensions,dotenv]
```

# 2.履行

在这一部分，我们将探索`pydantic`中一些有用的功能。

在`pydantic`中定义一个对象就像创建一个继承自`BaseModel`的新类一样简单。当您从类中创建一个新的对象时，`pydantic`保证结果模型实例的字段将符合模型上定义的字段类型。

## 导入

在 Python 文件的顶部添加以下导入声明。

```
from datetime import datetime
from typing import List, Optional
from pydantic import BaseModel
```

## 用户级

如下声明一个继承了`BaseModel`的新类:

```
class User(BaseModel):
    id: int
    username : str
    password : str
    confirm_password : str
    alias = 'anonymous'
    timestamp: Optional[datetime] = None
    friends: List[int] = []
```

`pydantic`使用内置的类型提示语法来确定每个变量的数据类型。让我们一个一个地探究幕后发生了什么。

*   `id` —一个整数变量代表一个 ID。由于未提供默认值，因此该字段是必需的，并且必须在对象创建期间指定。如果可能，字符串、字节或浮点数将被强制转换为整数；否则，将引发异常。
*   `username` —字符串变量代表用户名，是必需的。
*   `password` —字符串变量代表密码，是必需的。
*   `confirm_password` —字符串变量代表确认密码，是必需的。稍后它将用于数据验证。
*   `alias` —字符串变量代表别名。它不是必需的，如果在对象创建期间没有提供，它将被设置为匿名。
*   `timestamp` —日期/时间字段，不是必需的。默认为无。`pydantic` 将处理 unix 时间戳 int 或表示日期/时间的字符串。
*   `friends` —整数输入列表。

## 对象实例化

下一步是从`User`类实例化一个新对象。

```
data = {'id': '1234', 'username': 'wai foong', 'password': 'Password123', 'confirm_password': 'Password123', 'timestamp': '2020-08-03 10:30', 'friends': [1, '2', b'3']}user = User(**data)
```

当您打印出`user`变量时，您应该得到以下输出。您可以注意到`id`已经被自动转换为整数，即使输入是一个字符串。同样，字节被自动转换成整数，如`friends`字段所示。

```
id=1234 username='wai foong' password='Password123' confirm_password='Password123' timestamp=datetime.datetime(2020, 8, 3, 10, 30) friends=[1, 2, 3] alias='anonymous'
```

## BaseModel 下的方法和属性

继承`BaseModel`的类将有以下方法和属性:

*   `dict()` —返回模型字段和值的字典
*   `json()` —返回一个 JSON 字符串表示字典
*   `copy()`-返回模型的深层副本
*   `parse_obj()` —如果对象不是字典，用于将任何对象加载到带有错误处理的模型中的实用程序
*   `parse_raw()` —加载多种格式字符串的实用程序
*   `parse_field()` —类似于`parse_raw()`，但用于文件
*   `from_orm()` —将数据从任意类加载到模型中
*   `schema()` —返回将模型表示为 JSON 模式的字典
*   `schema_json()` —返回`schema()`的 JSON 字符串表示
*   `construct()` —创建模型而不运行验证的类方法
*   `__fields_set__` —初始化模型实例时设置的字段名称集
*   `__fields__` —模型领域词典
*   `__config__` —模型的配置类

让我们将`id`的输入改为一个字符串，如下所示:

```
data = {'id': 'a random string', 'username': 'wai foong', 'password': 'Password123', 'confirm_password': 'Password123', 'timestamp': '2020-08-03 10:30', 'friends': [1, '2', b'3']}user = User(**data)
```

当您运行代码时，应该会得到以下错误。

```
value is not a valid integer (type=type_error.integer)
```

## 验证错误

为了更好地了解错误的详细信息，强烈建议将其包装在 try-catch 块中，如下所示:

```
from pydantic import BaseModel, ValidationError# ... codes for User classdata = {'id': 'a random string', 'username': 'wai foong', 'password': 'Password123', 'confirm_password': 'Password123', 'timestamp': '2020-08-03 10:30', 'friends': [1, '2', b'3']}try:
    user = User(**data)
except ValidationError as e:
    print(e.json())
```

它将打印出以下 JSON，这表明`id`的输入不是有效的整数。

```
[
  {
    "loc": [
      "id"
    ],
    "msg": "value is not a valid integer",
    "type": "type_error.integer"
  }
]
```

## 字段类型

`pydantic`为 Python 标准库中的大多数常见类型提供支持。完整列表如下:

*   弯曲件
*   （同 Internationalorganizations）国际组织
*   漂浮物
*   潜艇用热中子反应堆（submarine thermal reactor 的缩写）
*   字节
*   目录
*   元组
*   词典
*   设置
*   frozenset
*   日期时间
*   日期时间
*   日期时间.日期时间
*   日期时间.时间增量
*   打字。任何的
*   打字。类型变量
*   打字。联盟
*   打字。可选择的
*   打字。目录
*   打字。元组
*   打字。词典
*   打字。一组
*   打字。FrozenSet
*   打字。顺序
*   打字。可迭代的
*   打字。类型
*   打字。请求即付的
*   打字。模式
*   IP 地址。IPv6 地址
*   IP 地址。IP v4 接口
*   IP 地址。IP v4 网络
*   IP 地址。IPv6 地址
*   IP 地址。IPv6 接口
*   IP 地址。IPv6 网络
*   枚举。列举型别
*   枚举。内部
*   十进制。小数
*   pathlib。小路
*   uuid。UUID
*   字节大小

## 约束类型

您可以通过`Constrained Types`执行自己的限制。让我们看看下面的例子:

```
from pydantic import (
    BaseModel,
    NegativeInt,
    PositiveInt,
    conint,
    conlist,
    constr
)class Model(BaseModel):
    # minimum length of 2 and maximum length of 10
    short_str: constr(min_length=2, max_length=10) # regex
    regex_str: constr(regex=r'^apple (pie|tart|sandwich)$') # remove whitespace from string
    strip_str: constr(strip_whitespace=True)

    # value must be greater than 1000 and less than 1024
    big_int: conint(gt=1000, lt=1024)

    # value is multiple of 5
    mod_int: conint(multiple_of=5)

    # must be a positive integer
    pos_int: PositiveInt

    # must be a negative integer
    neg_int: NegativeInt

    # list of integers that contains 1 to 4 items
    short_list: conlist(int, min_items=1, max_items=4)
```

## 严格类型

如果您正在寻找通过验证的严格限制，当且仅当验证的值属于相应的类型或者是该类型的子类型时，您可以使用以下严格类型:

*   StrictStr
*   严格限制
*   严格浮动
*   斯特里布尔

下面的例子说明了在你继承的类中执行`StrictBool`的正确方法。

```
from pydantic import BaseModel, StrictBool,class StrictBoolModel(BaseModel):
    strict_bool: StrictBool
```

字符串`‘False’`将引发 ValidationError，因为它只接受`True`或`False`作为输入。

## 验证器

此外，您可以在继承的类中使用`validator` decorator 创建自己的定制验证器。让我们看看下面的例子，它确定`id`是否是四位数，以及`confirm_password`是否与`password`字段匹配。

```
from datetime import datetime
from typing import List, Optional
from pydantic import BaseModel, ValidationError, validatorclass User(BaseModel):
    id: int
    username : str
    password : str
    confirm_password : str
    alias = 'anonymous'
    timestamp: Optional[datetime] = None
    friends: List[int] = [] @validator('id')
    def id_must_be_4_digits(cls, v):
        if len(str(v)) != 4:
            raise ValueError('must be 4 digits')
        return v @validator('confirm_password')
    def passwords_match(cls, v, values, **kwargs):
        if 'password' in values and v != values['password']:
            raise ValueError('passwords do not match')
        return v
```

# 3.结论

让我们回顾一下今天所学的内容。

我们首先详细解释了 Pydantic，它有助于解析和验证数据。

接下来，我们创建了一个虚拟环境，并通过 pip 或 conda 安装了 Pydantic。它还包括对基于我们的用例的三个附加依赖项的支持。

安装完成后，我们深入研究了软件包提供的基本功能。基本的构建块是创建一个继承自`BaseModel`的新类。

我们了解到 Pydantic 提供了对 Python 标准库下大多数常见数据类型的支持。我们测试了`Constrained Types`和`Strict Types`，这有助于执行我们自己的自定义限制。

最后，您摆弄了一下`validator`装饰器，只允许为`id`输入四位数，并且`confirm_password`必须与`password`字段匹配。

感谢你阅读这篇文章。希望在下一篇文章中再见到你！

# 参考

1.  [Pydantic Github 页面](https://github.com/samuelcolvin/pydantic/)
2.  [Pydantic 文档](https://pydantic-docs.helpmanual.io/)