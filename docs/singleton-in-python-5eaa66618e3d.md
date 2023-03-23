# Python 中的单例

> 原文：<https://betterprogramming.pub/singleton-in-python-5eaa66618e3d>

## 编写一个单例装饰器用于任何类

![](img/3a212cd205418ac0c142fd4abc33c554.png)

帕特里克·施耐德在 [Unsplash](https://unsplash.com/s/photos/decorate?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

通常，我们根据自己的需要创建许多类的对象。有时，创建多个对象会占用大量资源。

数据库连接类就是一个真实的例子。您不希望有多个数据库对象，因为其中一个对象的更改可能会导致其他对象不一致。

在这种情况下，我们可以使用单例模式。使用单例模式，我们确保一个类只创建一个实例。

[Python](https://www.python.org/) 中有几个你经常使用的单例，包括`None`、`True`、`False`。事实上，`None`是一个单例，允许你使用`is`关键字来比较`None`。

在 Python 中，创建单例模式的典型方式是使用装饰器。可以阅读 Python 中的文章 [*Decorators，了解更多关于 Decorators 的知识。*](https://medium.com/@goutomroy/decorators-in-python-72a1d578eac4)

假设我们希望在应用程序的整个生命周期中只有一个数据库连接。让我们创建一个单例类来满足我们的目的。

现在，创建数据库连接类:

```
@Singleton
class DBConnection(object):

    def __init__(self):
        *"""Initialize your database connection here."""* pass

    def __str__(self):
        return 'Database connection object'
```

让我们访问这个连接类:

```
c1 = DBConnection.Instance()
c2 = DBConnection.Instance()

print("Id of c1 : {}".format(str(id(c1))))
print("Id of c2 : {}".format(str(id(c1))))

print("c1 is c2 ? " + str(c1 is c2))
```

输出:

```
Id of c1 : 139699882512960
Id of c2 : 139699882512960
c1 is c2 ? True
```

如果我们做如下，我们将得到`TypeError`:

```
try:
    p = DBConnection()
except TypeError as ex:
    print("ERROR: {}".format(ex.message))
```

我希望这篇文章能帮助你在任何类中实现单例模式。