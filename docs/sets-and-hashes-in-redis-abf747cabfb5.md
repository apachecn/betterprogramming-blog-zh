# Redis 中的集合和散列

> 原文：<https://betterprogramming.pub/sets-and-hashes-in-redis-abf747cabfb5>

## 为您的应用选择正确的数据类型

![](img/cc1d58069fc7f53497e1ed6d6e9cc497.png)

作者图片

今天的主题是 Redis 中的集合和散列。这是 Redis 中最有用的两种数据类型，它们经常被开发人员忽略。理解它们背后的基本概念很重要，因为选择正确的数据类型在尝试扩展应用程序时会有很大帮助。

在本教程中，我将使用 [Redis-Py](https://github.com/andymccurdy/redis-py) 来展示这些功能。无论您使用哪种编程语言，其背后的概念都是一样的。Redis-Py 使用与原始命令相同的函数调用名称，除了一些小的例外。你可以在下面的链接找到[支持的编程语言的完整列表。](https://redis.io/clients)

[](https://redis.io/clients) [## 雷迪斯

### StackExchange 的 COM 包装。Redis 允许在 COM 环境中使用 Redis，如经典 ASP (ASP 3.0)使用…

redis.io](https://redis.io/clients) 

# 设置

确保您的本地计算机上安装了 Redis。本地机器必须包含具有最低配置设置的 Linux 发行版。

## 自己编译

您可以使用以下命令在您的机器上下载并编译它(编写本文时版本为 6.0.8):

```
wget http://download.redis.io/releases/redis-6.0.8.tar.gz
tar xzf redis-6.0.8.tar.gz
cd redis-6.0.8
make
```

完成后，您应该会看到一个`src`文件夹。将工作目录更改为`src`文件夹。

## 人的本质

对于 Ubuntu 用户，安装如下:

```
sudo apt-get update 
sudo apt-get install redis-server
```

## 正在启动 Redis 服务器

完成后，用下面的命令启动 Redis 服务器:

```
redis-server
```

为了检查您的 Redis 服务器是否在工作，请使用以下命令启动命令行界面:

```
redis-cli
```

在 Redis 提示符下向它发送一个`ping`命令:

```
redis> ping
```

如果您的 Redis 服务器返回`PONG`，则它工作正常。

## redispy

你可以通过`pip install`轻松安装 Redis-Py。强烈建议您在继续之前创建一个虚拟环境。

```
pip install redis
```

为了使用它，您需要导入它并传递适当的配置。

```
import redisr = redis.Redis(host="localhost", password='password', port=6379, db=0)
```

# 设置

在 Redis 中，集合只是字符串的集合，分为有序和无序两类。在本教程中，我们只是探索无序集。集合中的每个元素称为`member`。集合的一个主要特征是它不允许重复成员。向集合中添加相同的元素没有任何效果。因此，在将特定成员添加到集合之前，不需要检查它是否存在于集合中。

此外，您可以执行大多数针对集合的操作，如交集和并集。如果您需要计算或合并成千上万的数据，这是非常有用的。例如，假设您已经收集了平台 A 和平台 b 中用户使用的所有标签，您可以在查询数据时调用 intersect 函数。这比在应用程序中查询所有内容并执行交集要干净和优化得多。

当处理大量唯一标识的数据点时，集合是理想的选择，例如用于身份验证的 ID 号和电子商务产品的标签。您可以将所有 ID 号建模为单个键中的元素，而不是为每个 ID 声明多个键。

## 初始化

让我们通过初始化以下变量来测试一下。第一个变量表示用户的 id 列表，而第二个变量保存管理员的 id。用户可以是管理员，也可以通过 ID `1001`显示。根据您的偏好，您可以使用 Python 集合而不是列表。

```
user_id = ["1001", "1002", "1003", "1004", "1005"]
admin_id = ["1001", "1006", "1007"]
```

## 添加新的键或元素

向现有的添加一个新的键或元素非常简单明了。只需调用`sadd`函数:

```
for key in user_id:
    r.sadd("user", key)for key in admin_id:
    r.sadd("admin", key)
```

## 检查键/元素是否存在

通常，您应该使用`exists()`来检查 Redis 数据库中是否存在一个键。对于 Redis 集合，它带有`sismember()`,可以用来确定一个元素是否存在于集合中。如果存在则返回`1`，否则返回`0`。此外，如果键不存在，它也将返回 0。为了方便起见，Redis-Py 自动将返回的结果映射为布尔值:

```
r.sismember("user", "1004")
# Truer.sismember("admin", "1004") 
# False
```

## 获取所有元素

如果您正在寻找一种快速获取集合中所有元素的方法，`smembers()`是您的正确选择:

```
r.smembers("user")
# {b'1001', b'1004', b'1002', b'1005', b'1003'}
```

## 获取元素的数量

从逻辑上讲，您可以使用`smembers()`提取所有元素，并通过 Python 中的`len()`计算元素的数量。但是，如果您只想知道元素的总数，这种方法是无效的。幸运的是，Redis 为这样的用例提供了一个名为`scard()`的特殊函数。

```
r.scard("user")
# 5
```

## 差异

正如我前面提到的，当查询数据时，可以在 Redis 内部直接执行一些 set 操作。例如，您可以通过下面的代码片段找到`user`集合和`admin`集合之间的差异:

```
r.sdiff("user", "admin")
# {b'1003', b'1004', b'1002', b'1005'}
```

## 交集

同样，您可以使用`sinter()`来识别两个集合之间的公共元素。以下示例说明了如何获取同时也是管理员的用户:

```
r.sinter("user", "admin")
# {b'1001'}
```

## 联盟

Redis 集合中的另一个常见操作是`sunion()`，它将两个集合中的所有元素组合在一起。返回的结果将不包含任何重复的成员:

```
r.sunion("user", "admin")
# {b'1001', b'1004', b'1002', b'1005', b'1007', b'1003', b'1006'}
```

# 混杂

与集合不同，Redis 中的散列是用来存储复杂数据的。哈希表示为字符串字段和字符串值之间的映射。因此，它们是存储 Python 中的字典等对象的完美数据类型。

例如，带有姓名和年龄字段的`User`对象。与其对姓名和年龄使用不同的键，不如使用包含所有必需字段的单个散列更有效。不用初始化不同的`<key>:<id>:<field>`字符串键，比如`userhash:1001:name`，您可以简单地使用`{“userhash:1001”: {“name”: “Alice”}}`创建它。

事实上，官方文档建议尽可能使用散列，因为[小散列编码在一个非常小的空间里](https://redis.io/topics/memory-optimization)。哈希能够存储许多元素，也可以用来存储其他实例，而不是对象。

## 初始化

让我们创建一个新的 Python 字典作为测试数据的一部分。为了简单起见，我将用一个名称字段来初始化它:

```
hash_data = {"userhash:1001": {"name": "Alice"}, "userhash:1002": {"name": "Bonnie"}, "userhash:1003": {"name": "Cassie"}, "userhash:1004": {"name": "Dolores"}, "userhash:1005": {"name": "Erika"}}
```

## 添加新哈希

Redis 提供了两个不同的内置函数来设置 hash 中的键值对:

*   `hset` —用值设置散列中的字段(键)。如果散列不存在，将创建一个新的散列。如果字段(键)已经存在于现有散列中，它将被新值覆盖。
*   `hmset` —类似于`hset`，但它允许在一个命令中有多个输入参数。该函数已被弃用，从现在开始，您应该使用`hset`作为单个或多个输入参数。

在撰写本文时，Redis-py 提供了更新的`hset`,其结构如下:

```
def hset(self, name, key=None, value=None, mapping=None)
```

*   `name` —哈希的标识符。
*   `key` —散列中字段的名称。
*   `value` —相应字段的值。
*   `mapping` —接受字典作为输入。用作`hmget`的替代，用于将字典解析为相应的键值对。

因此，您可以使用:

```
r.hset("userhash:1001", None, None, {"name": "Alice"})
```

或者:

```
r.hset("userhash:1001", mapping={"name": "Alice"})
```

向 Redis 服务器添加新散列。

运行以下命令循环遍历我们的数据以添加新的散列:

```
for key in hash_data:
    r.hset(key, mapping=hash_data[key])
```

## 检查哈希/字段是否存在

要检查现有散列或散列中字段的存在，您应该使用`hexists()`:

```
r.hexists("userhash:1001", "name")
# True
```

## 获取特定字段的值

`hget()`是 hash 中最重要的函数之一，用于获取特定字段的值。

```
r.hget("userhash:1001", "name")
# b'Alice'
```

## 获取所有字段

如果您正在寻找一种快速简单的方法来获取 hash 中所有字段的名称，那么对于这种用例来说，`hkeys()`是最好的选择。在 Redis-Py 中，它将返回包含所有字段的列表:

```
r.hkeys("userhash:1001")
# [b'name']
```

## 获取所有字段和值

您可以通过`hgetall()`函数轻松获得单个散列的所有字段及其各自的值。

```
r.hgetall("userhash:1001")
# {b'name': b'Alice'}
```

## 处理嵌套数据结构

在撰写本文时，Redis 本身不支持嵌套数据结构。如果您的数据包含嵌套字典，最好的方法是用`json.dumps`进行序列化，并将其存储为一个字符串。然后，从 Redis 中检索后，您可以简单地用`json.loads`将其反序列化。

您也可以使用`pickle`来实现这一点，但是在存储用户输入时必须小心，因为它容易出现远程代码执行漏洞。

# 临时演员

这里有一些可以在 Redis 中使用的有用命令。

## 从 Redis 服务器获取所有密钥

最新版本的 Redis 自带`scan_iter()`功能，比`keys()`更值得推荐的选择。`keys()`应仅在开发中用于调试目的。在 Redis-Py 中，您可以使用以下代码片段获取并打印数据库中的所有键:

```
for key in r.scan_iter():
    print(key)
```

## 清除数据库

有两个函数可以用来清除 Redis 数据库。

*   `flushdb` —清除并移除当前数据库中的所有密钥。
*   `flushall` —清除并移除服务器中所有数据库的所有密钥。

# 结论

让我们回顾一下今天所学的内容。

我们首先简单解释了 Redis 中的集合和散列数据类型。然后，我们执行了设置和安装 Redis 服务器的必要步骤。

然后我们继续深入探讨 Redis 集合背后的基本概念。我们测试了一些有用的功能，比如设置和从集合中检索数据。

接下来，我们研究了 Redis 散列，这是在 Python 中存储字典等对象的理想选择。为了提高效率，强烈建议使用哈希而不是字符串日期类型。

感谢阅读这篇文章。希望下次能再见到你！

# 参考

*   [Redis 数据类型和抽象的介绍](https://redis.io/topics/data-types-intro)
*   [Redis —数据类型](https://redis.io/topics/data-types)
*   [stack overflow—Redis set vs hash](https://stackoverflow.com/questions/13557075/redis-set-vs-hash)