# 使用钩子在 React 中搜索和过滤数据

> 原文：<https://betterprogramming.pub/search-and-filter-data-in-react-using-hooks-de9238e54423>

## 了解如何使用 React 钩子来搜索和过滤数据

![](img/e0f7f7cf38ad81231101ca01952af324.png)

图德·巴休在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

搜索和过滤数据是所有应用程序中的一个常见功能。用户应该能够在使用应用程序时搜索特定的东西。

就我而言，最近我不得不在一个简单的 React 应用程序中实现这个特性，我想与您分享我的解决方案。

# 语境

在继续之前，我想提供一些背景。让我们假设您有一个简单的电话簿，其中存储了人们及其电话号码。

下面的对象数组代表电话簿:

```
const people = [
  { name: 'Alan Turing', number: '040-123456', id: 1 },
  { name: 'Ada Lovelace', number: '39-44-5323523', id: 2 },
  { name: 'Barbara Liskov', number: '12-43-234345', id: 3 },
  { name: 'Mary Poppendieck', number: '39-23-6423122', id: 4 }
]
```

因此，用户应该能够通过他们的名字来搜索和筛选人。弄清楚这一点后，让我们开始构建解决方案吧！

# 搜索和过滤数据

对于本文，您可以使用以下命令创建一个标准的 React 应用程序:

```
npx create-react-app phonebook
```

你只需要`App.js`和`index.js`，那么你就可以移除 CSS 和测试文件等其他文件。

# 应用程序框架

让我们从创建应用程序的框架开始。打开`App.js`文件，编写以下代码:

此时，应用程序将在页面上显示三件事情:

*   标题“电话簿”
*   输入字段
*   标题“数字”

尽管输入字段已经存在，但它还没有做任何事情。

# 添加状态

下一步是向应用程序添加状态。我们通过使用状态挂钩— `useState`向 React 应用程序添加状态。

在`App.js`文件中编写以下代码:

增加了两项内容:

1.  您在组件顶部导入了人员数组。对于这个应用程序，您将数据作为数组存储在组件本身中，因为它是一个简单的应用程序。
2.  `search`变量被赋予一个空字符串作为初始状态。您将使用函数`setNewSearch`来修改变量`search`的状态。

下一步是处理搜索功能。

# 处理搜索

当用户在输入字段中输入内容时，`search`常量的状态也应该被更新。

例如，如果用户在输入字段中键入“Alan”，那么`search`状态也应该是“Alan”。

因此，在声明状态变量后，编写以下函数:

```
const handleSearchChange = (e) => {
    setNewSearch(e.target.value);
};
```

这个`handleSearchChange`函数在每次输入字段改变时被调用，它将来自输入字段的数据设置为`search`状态。

此外，更新 input 元素，如下所示:

```
<input type="text" value={search} onChange={handleSearchChange} />
```

现在，您向输入的`onchange`属性注册了一个事件处理程序。一旦输入值改变，函数`handleSearchChange`就会触发。代码如下:

下一步是使用`search`来过滤人员数组，只显示与搜索数据匹配的人员。

# 过滤数据

过滤的逻辑如下:

*   如果用户没有搜索任何内容，则显示所有人
*   如果用户输入了数据，则只返回那些姓名包含输入数据的人

在`handleSearchChange`函数后编写以下代码:

```
const filtered = !search
    ? people
    : people.filter((person) =>
        person.name.toLowerCase().includes(search.toLowerCase())
      );
```

在上面的代码中，您使用条件三元运算符来决定返回什么。`!search`如果输入字段为空，返回“真”，否则返回“假”。

如果用户没有在搜索框中键入任何内容，该函数将返回所有人。另一方面，如果用户搜索一个人，该函数只返回其姓名包含搜索词的人。

例如，如果您在搜索框中键入“lac”，函数`filtered`只返回“阿达·洛芙莱斯”，因为这是唯一一个姓名包含搜索词“lac”的人

`filter`方法创建一个新的数组，其中包含所有通过测试的元素。在这种情况下，测试是`person.name.toLowerCase().includes(search.toLowerCase())`。

到目前为止的代码如下:

下一步也是最后一步是在网页上显示数据。

# 显示数据

为了在网页上显示数据，您将映射到`filtered`数组并显示适当的人员列表。

在“数字”标题后编写以下代码:

```
{filtered.map((person) => {
    return (
        <p key={person.id}>
            {person.name} - {person.number}
        </p>
    );
})}
```

`App.js`文件的完整代码如下:

你完了！如果您运行该应用程序，您应该能够在电话簿中搜索特定的人。

该应用程序位于以下 URL : [电话簿搜索和过滤](https://omq93.csb.app/)。

*本文原载* [*此处*](https://catalins.tech/search-and-filter-data-in-react-using-hooks) *。*