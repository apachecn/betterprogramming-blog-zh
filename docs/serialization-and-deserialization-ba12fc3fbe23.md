# 序列化和反序列化

> 原文：<https://betterprogramming.pub/serialization-and-deserialization-ba12fc3fbe23>

## 它们是什么——什么时候使用？

![](img/b29b1ddc6772c236403f97cfdc389f79.png)

照片由[米卡·鲍梅斯特](https://unsplash.com/@mbaumi?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 序列化

序列化将内存中的数据结构转换为可以存储或传输的值。

假设我们在一个 JavaScript 脚本中有一个`users`对象，它包含用户总数和用户列表，如下所示:

```
const usersObject = {
  "total": 2,
  "users": [
    {
      "name": "Patrick"
    },
    {
      "name": "Michael"
    }
  ]
}
```

当 JavaScript 运行时执行上面的代码时，它会在内存中创建一个类似下面的数据结构。引擎会将每个对象和数组存储在内存中单独的位置，并提供对它们使用位置的引用:

```
***object_in_memory_a***
{
  name: "Patrick"
}***object_in_memory_b***
{
  name: "Michael"
}***array_in_memory_c***
[
  ***object_in_memory_a***, ***object_in_memory_b*** ]***object_in_memory_d***
{
  total: 2,
  users: ***array_in_memory_c***
}
```

在程序的进程结束后，上述内容不容易传输到另一台机器或被同一台机器使用。为此，我们必须序列化数据。也就是说，我们必须将内存中的数据结构转换成一系列字节(通常是 ASCII 字符)，这些字节以可恢复的格式记录数据结构。

在我们的 JavaScript 示例中，我们可以通过将`usersObject`传递给`JSON.stringify()`函数来序列化它，如下所示。

命令:

```
JSON.stringify(usersObject);
```

结果:

```
**"{"total":2,"users":[{"name":"Patrick"},{"name":"Michael"}]}"**
```

正如我们所看到的，内存中的对象已经被转换为一个字符串。这个字符串现在可以转移到其他机器上，或者存储到磁盘上以备后用。

# 反序列化

在我们的例子中，我们知道我们可以将序列化的结果传递到另一台机器，或者如果我们将它保存到磁盘上，我们可以在以后自己检索它。

假设这种序列化发生在服务器上，当对系统用户发出请求时，我们希望将它传递给浏览器客户端。客户端将收到我们的序列化结果，如下所示:

```
**"{"total":2,"users":[{"name":"Patrick"},{"name":"Michael"}]}"**
```

数据很棒，但是是个`string`。

此时，浏览器客户端不能使用这些数据来知道有多少用户或他们的名字。

要具备这种能力，我们必须将这个序列化的字符串转换成内存中的数据结构。从序列化字符串到内存中数据结构的转换是反序列化。我们可以用 JavaScript `JSON.parse()`函数来完成这个任务。

```
const usersObject = JSON.parse(responseFromServer);
```

在反序列化之后，`usersObject`将变成与我们在服务器端看到的相同的内存数据结构:

```
***object_in_memory_a***
{
  name: "Patrick"
}***object_in_memory_b***
{
  name: "Michael"
}***array_in_memory_c***
[
  ***object_in_memory_a***, ***object_in_memory_b*** ]***object_in_memory_d***
{
  total: 2,
  users: ***array_in_memory_c***
}
```

既然浏览器客户端有了内存中的数据结构，我们就有能力导航它。

我们现在可以知道用户的数量以及他们的名字。

```
console.log(usersObject.total); // 2
console.log(usersObject.users[0].name); // Patrick
console.log(usersObject.users[1].name); // Michael
```

# 摘要

序列化采用内存中的数据结构，并将其转换为一系列可以存储和传输的字节。

反序列化接受一系列字节，并将其转换为可以以编程方式使用的内存中数据结构。

# 资源

*   [https://en.wikipedia.org/wiki/Serialization](https://en.wikipedia.org/wiki/Serialization)