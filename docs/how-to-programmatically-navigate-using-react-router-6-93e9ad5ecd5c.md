# 如何使用 React Router 6 以编程方式导航

> 原文：<https://betterprogramming.pub/how-to-programmatically-navigate-using-react-router-6-93e9ad5ecd5c>

## 这个紧迫问题的简要指南

![](img/fbce6ca89b161febc03ff349d839e7ab.png)

照片由[劳塔罗·安德烈亚尼](https://unsplash.com/@lautaroandreani?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

通常，当您在 React 中构建应用程序时，您需要在单击事件或操作后将用户路由到新页面。React Router 6 提供了`useNavigate`钩子来处理这种需要编程导航的情况。

我们先从一个叫做`Home.js`的基本功能组件开始，它有一个按钮。代码如下:

```
import React from 'react';

function Home() {

    return (
        <div>
            <button>NAVIGATE TO DESTINATION</button>
        </div>
    );
}

export default Home;
```

接下来，我们将把`useNavigate`钩子添加到组件的顶部。(注意:钩子的[规则](https://reactjs.org/docs/hooks-rules.html)声明钩子需要在组件的顶层调用。所以，如果你试图在一个函数内部声明`useNavigate`，你会得到一个错误)。

```
function Home() {

    let navigate = useNavigate();

    return (
        <div>
            <button>NAVIGATE TO DESTINATION</button>
        </div>
    );
} 
```

然后在一个函数中(或者如果你愿意的话，甚至是内联的)，我们可以添加导航函数调用，并将用户指引到'/destination '路径，React Router 已经将该路径定义为一个路径(在一个`App.js`文件中，未显示)。

```
 let navigate = useNavigate();

    const navigateToDestination = () => {
        navigate("/destination")
    }

    return (
        <div>
            <button onClick={() => navigateToDestination()}>
                NAVIGATE TO DESTINATION
            </button>
        </div>
    );
}
```

现在，按下按钮将导航到目标组件。

# 如果我需要向导航目的地传递道具怎么办？

将道具传递到目的地很容易实现，只需在 navigate 函数中用 state 键添加一个对象作为参数。

```
const navigateToDestination = () => {
    navigate("/destination", {state: "Destination Reached"})
}
```

然后在目的地组件中，可以使用`useLocation`钩子访问这个状态对象:

```
function Destination() {

    const location = useLocation()

    return (
        <div>
            {location.state}
        </div>
    )
}
```

# 使用历史呢？

`useHistory`，其功能与`useNavigate`非常相似，在 React 路由器 6 中被弃用，现在仅与 React 路由器 5 一起使用。这是一篇`useHistory` 迁移到`useNavigate`的[大帖](https://stackoverflow.com/questions/72474436/how-to-migrate-usehistory-replacex-y-with-usenavigate-in-react-router-v6)。

感谢您的阅读。敬请关注更多内容。