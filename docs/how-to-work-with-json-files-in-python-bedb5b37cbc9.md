# 如何在 Python 中使用 JSON 文件

> 原文：<https://betterprogramming.pub/how-to-work-with-json-files-in-python-bedb5b37cbc9>

## 使用 Python 读写 JSON 文件

![](img/d7e69034a37929c658f310f29bf5d867.png)

克里斯·里德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

大多数时候，我们在一个文件中找到 JSON 对象，这就是为什么今天，我将告诉你如何只使用 Python 来读写 JSON 文件。

# 文章图

为了让你对这篇文章有一个大概的了解，我写了这个小地图，它将向你展示我们将在本教程中做什么。

1.  JSON 模块概述。
2.  JSON 文件的示例。
3.  JSON 文件结构。
4.  使用`json`模块读取 JSON。
5.  在 JSON 文件中编写 JSON 对象。
6.  JSON 文件和对象的主要用法。
7.  JSON 文件的另一种用法。

现在，您已经更具体地了解了我们将在教程中做什么，您已经准备好开始使用 Python 处理 JSON 了。

# Python 的 JSON 模块

要使用 JSON(包含 JSON 对象的字符串或文件)，可以使用 Python 的`json`模块。您需要先导入该模块，然后才能使用它。

这里有[官方 JSON 文档](https://docs.python.org/3/library/json.html)，但是在进入下一步之前，我想向您展示一下`json`模块的两个我们最常用的功能:

*   `json.load()`
*   `json.dump()`

您可能已经猜到了，我们将使用`load()` 函数读取 JSON 文件，使用`dump()`函数写入 JSON 文件。

# JSON 文件的示例

为了让您理解 JSON 是如何工作的，这里有一个简单的 JSON 文件示例:

# JSON 的结构

JSON 语法源自 JavaScript 的对象符号语法:

*   数据是名称/值对。
*   数据用逗号分隔。
*   花括号表示对象。
*   方括号包含数组。

在 JSON 中，值必须是以下数据类型之一:

*   一根绳子
*   一个数字
*   一个对象(JSON 对象)
*   阵列
*   布尔型
*   空

在 JavaScript **，**值可以是上述所有值，加上任何其他有效的 JavaScript 表达式，包括:

*   一项功能
*   约会
*   不明确的

JSON 的语法如下。

*   对象用大括号(`{}`)括起来，它们的名称-值对用逗号(`,`)分隔，一对中的名称和值用冒号(`:`)分隔。对象中的名称是字符串，而值可以是七种值类型中的任何一种，包括另一个对象或数组。
*   数组用括号(`[]`)括起来，它们的值用逗号(`,`)隔开。数组中的每个值可以是不同的类型，包括另一个数组或对象。
*   当对象和数组包含其他对象或数组时，数据具有树状结构

# 读取 JSON 文件

![](img/16e72b800a2e5aea98dbcd299b6a4bb5.png)

照片由[法比奥拉·佩尼亚巴](https://unsplash.com/@fabspotato?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

正如我们已经看到的，为了读取 JSON 文件，我们将使用`json.load()`函数。但是在我们从一个文件加载一些 JSON 对象之前，我们必须使用 Python 的`open()`内置函数读取它。

假设我们想使用`read_json.py`脚本读取`test.json`文件，我们:

1.  导入`json`模块。
2.  使用`open()`内置功能打开`test.json`。
3.  使用`json.load()`函数加载`test.json`文件中的 JSON 对象。
4.  打印出`test.json`文件中 JSON 对象的值。

在编写脚本之前，我们需要创建`test.json`文件，并将以下代码粘贴到其中:

该文件包含一个 JSON 对象，该对象有三个值的名称和三个值，每个值名称对应一个值:

*   `"Macbook Air"`值对应于`"product"`值的名称。
*   `"5.0"` 值对应于`"overall"`值的名称。
*   `"I really love this computer"`值对应于“文本”值的名称。

也就是说，我们可以开始构建我们的`read_json.py`脚本了:

*   第 1 行:导入的`json`模块。
*   第 4 行:使用`open()`功能打开`test.json`文件。
*   第 5 行:使用`json.load()`函数加载 JSON 对象。
*   第 6 行:关闭`test.json`文件。
*   第 8-10 行:通过使用 value-name 获取 JSON 对象的值。
*   第 12-14 行:打印 JSON 对象的值。

# 编写 JSON 文件

![](img/b0f138ab27dcee6b0d23afb6baf5200d.png)

帕特里克·福尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在学会读取 JSON 文件之后，我们学习如何编写 JSON 文件。

如前所述，要编写一个内部包含 JSON 对象的 JSON 文件，我们将使用`json.dump()`函数。

让我们开始构建我们的`write_json.py` 文件:

*   第一行:进口`json`模块。
*   第 4-8 行:定义我们的 JSON 数据。
*   第 10 行:以写模式(`"writed_json.json", “w”`)打开`writed_json.py`文件，这意味着如果文件已经存在，脚本将只把 JSON 数据写入文件，否则，如果文件不存在，脚本将创建它，然后把 JSON 数据写入其中。
*   第 11 行:使用`json.dump()`函数在`"writed_json.json"`文件中写入我们之前定义的`json_data`。
*   第 12 行:关闭文件。

之后，打开您的`writed_json.json`文件，您应该看到以下内容:

这正是我们在`write_json.py`脚本中定义的 JSON 对象。

# JSON 的主要用途

JSON 格式通常用于通过网络连接序列化和传输结构化数据。它主要用于在服务器和 web 应用程序之间传输数据，作为 XML 的替代。

# JSON 的替代用法

虽然这不是它的主要用途，但我发现 JSON 文件对于以更有序的方式管理项目的设置非常有用。

事实上，由于它的结构，JSON 很容易阅读和编写(正如我们在本教程中看到的)，所以我发现它们是管理设置的好资源。

显然，JSONs 还有很多其他的用法，你可以找到自己喜欢的用法！

# 结论

学习如何管理 JSON 是 web 开发人员的基本资源。但是它甚至对软件工程师或数据科学家也很有用，正如我们在第*段的*JSON 的替代用法中看到的。**

无论如何，学习新事物总是好的，尤其是如果它只需要几分钟。