# 如何将 API 与 React 功能组件一起使用

> 原文：<https://betterprogramming.pub/how-to-use-apis-with-react-functional-components-f4d36bd0be1>

## 让我们看看在 React 功能组件中使用 API 的两个用例

![](img/c3d4314ceaca64e75095bcbf8f92cc40.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [La-Rel Easter](https://unsplash.com/@lastnameeaster?utm_source=medium&utm_medium=referral) 拍摄的照片

在 React 项目中使用 API 是一个常见的用例。在本教程中，我们将看两个用例:

*   在后台加载 API 数据
*   点击按钮时加载 API 数据

我们将使用功能组件和 useEffect 挂钩。有些熟悉是意料之中的。

# 在后台加载 API 数据

![](img/5332a2a5b33e6982b0f990089677ddbd.png)

作者在后台显示 API 加载数据的 GIF。显示姓名列表。

在这个用例中，数据只加载一次——每当用户查看应用程序或刷新页面时。最初，会显示“正在加载…”文本。这个文本稍后会用实际的 API 数据进行更新。下面是导致上述行为的组件的代码片段:

让我们分三部分来讨论代码:状态、useEffect 挂钩和呈现逻辑。

# 各州

```
const [isLoading, setIsLoading] = 
React.useState(true);
```

我们有两种状态。 **isLoading** 状态是一个初始化为真的布尔变量。此状态用于跟踪数据是仍在加载还是已经加载。 **setIsLoading** 函数用于切换该状态变量。在 API 返回数据之后，我们将使用这个函数来切换 **isLoading** 的值:

```
const [data, setData] = React.useState([]);
```

接下来，我们有了**数据**状态。这个状态被初始化为一个空数组。它将用于存储 API 返回的数据。您也可以将状态初始化为一个空对象。然而，我在示例中使用的 API 返回一个列表，因此，空列表似乎是正确的选择。 **setData** 函数用于在 API 返回数据后更新状态变量 **data** 。

# 使用效果挂钩

```
React.useEffect(() => {
    const url = "https://randomuser.me/api/?results=15";
    fetch(url)
      .then((response) => response.json())
      .then((json) => setData(json['results']))
      .catch((error) => console.log(error));
  }, []);
```

上面的 useEffect 挂钩用于向 API 发出请求。'[]'参数告诉 React 只运行这个钩子一次。钩子在页面加载后运行。发出一个简单的获取请求，在承诺被解析后，我们使用 **setData** 函数来更新状态变量 **data** 。

```
React.useEffect(() => {
    if (data.length !== 0) {
      setIsLoading(false);
    }
    console.log(data);
  }, [data]);
```

每当状态变量**数据**更新时，下一个 useEffect 钩子运行。它做一个简单的检查:如果状态变量**数据**不为空，即 API 已经返回数据，它将状态变量 **isLoading** 设置为 False。

**注意:**上面的案例假设了一个皆大欢喜的案例，即 API 总是会返回数据。然而，事实并非如此。因此，还应该添加错误处理。

# 渲染逻辑

```
return (
    <div>
      {isLoading ? (
        <h1>Loading...</h1>
      ) : (
        data.map((user) => (
          <h1>
            {user.name.first} {user.name.last}
          </h1>
        ))
      )}
    </div>
  );
}
```

渲染逻辑非常简单。如果状态变量“isLoading”为真，我们将显示“Loading…”指示。如果为 false，我们只需映射状态变量“data”并显示数组中的所有项目。

# 点击按钮时加载 API 数据

![](img/5004367f46f3fe4d5a0cba6f7070b004.png)

按作者显示点击按钮时 API 加载数据的 GIF

以下是完整的代码片段:

我们将分三部分讨论代码。

# 州

```
const [showData, setShowData] = 
React.useState(false);
```

前两个状态变量与上一节中的相同。我们将讨论第三个状态变量 **showData** 。

当用户第一次查看页面时，我们不希望他们看到 API 数据或“正在加载……”文本。因此，我们添加了一个简单的检查来查看用户是否点击了按钮。用户单击按钮一次后，只有两个视图:

*   “装载…”文本
*   API 数据

每当用户再次点击按钮时，我们就在上面提到的两个视图之间切换。

# 手柄点击功能

这类似于第一个用例中的第一个 useEffect 钩子。唯一的区别是我们将状态变量 **showData** 设置为 True。

# 渲染逻辑

首先，我们对 showData 进行检查，这是为了确保最初用户不会看到“正在加载…”文本或 API 数据。用户单击按钮后，showData 被设置为 True。在这之后，渲染逻辑类似于第一个用例。

# 结论

我希望这篇文章对你有所帮助。在 [LinkedIn](https://www.linkedin.com/in/rahulbanerjee2699/) 和 [Twitter](https://twitter.com/rahulbanerjee99) 上加我。

# 资源

[Bikash Paneru](/how-to-write-better-functional-components-in-react-bc974f777145)撰写的这篇文章很好地介绍了 React 中的功能组件。

[Hossein Ahmadi](https://javascript.plainenglish.io/react-hooks-how-to-use-useeffect-ecea3e90d84f)[的这篇文章](https://medium.com/u/ee208eb13e4d?source=post_page-----f4d36bd0be1--------------------------------)是 React 中关于使用效果挂钩的一篇好文章。

> 最初发表于[realpythonproject.com](https://www.realpythonproject.com/how-to-use-apis-with-react-functional-components/)