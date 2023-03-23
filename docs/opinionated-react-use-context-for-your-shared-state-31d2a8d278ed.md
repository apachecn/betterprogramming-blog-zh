# 固执己见的反应:为你的共享状态使用上下文

> 原文：<https://betterprogramming.pub/opinionated-react-use-context-for-your-shared-state-31d2a8d278ed>

## 在应用中使用 React 的上下文 API 的有效模式

![](img/d5f54ab3b7a7ed734afda5f456850c1f.png)

照片由[普里西拉·杜·普里兹](https://unsplash.com/@priscilladupreez?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](/collections/10646975/wise-humanity?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

# 介绍

我已经和 React 合作了四年多。在这段时间里，我形成了一些我认为应用程序应该是什么样子的观点。这是这类固执己见的文章系列的第 5 部分。

# 我对上下文的反应模式

我的好友 Nader [问](https://twitter.com/dabit3/status/1233180102747246593)我如何在我的应用程序中使用 React 上下文。我答应过要写下来，所以我们来了。

# 为什么

在某些情况下，多个组件需要您的应用程序状态。如果这个共享状态需要大量的训练，我将使用上下文。在过去，Redux 是避免支柱钻孔的流行解决方案。但是，我相信 Redux 已经不需要了。React 的上下文 API 非常适合这一点。

# 用例—重要！

*   您应该对全局状态使用 React 上下文。也就是说，一个全球国家没有那么多的组成部分。全局状态的一些很好的例子是当前用户、当前语言设置或功能标志的映射。
*   您不需要只对全局状态使用上下文。上下文可以应用于应用程序的特定子树。
*   拥有多个特定于子树的上下文是很常见的

# 灵感

我最初是从 Kent C. Dodd 的精彩文章“如何有效地使用 React 上下文”中学到这个模式的推荐看这个。Tanner Linsley 也在他的演讲“[React 中的定制钩子:终极 UI 抽象层](https://www.youtube.com/watch?v=J-g9ZJha8FE&feature=emb_title)”中涉及了类似的概念

# 例子

最终目标是有一个类似这样的 API。

```
export const App = ({ userId }) => {
  return (
    <UserProvider id={userId}>
      <Dashboard />
    </UserProvider>
  );
};

const Dashboard = () => {
  const { isLoading, user } = useUserState();
  if (isLoading) {
    return <div>Loading...</div>;
  }
  return (
    <div>
      <h1>Dashboard</h1>
      <div>Hello {user.displayName}!</div>
    </div>
  );
};
```

让我们倒推得出这个解。

首先，让我们从定义上下文的状态开始，以及我们将要创建的两个上下文。

```
interface UserState {
  user?: User;
  isLoading: boolean;
}

const UserStateContext = React.createContext<UserState | undefined>(undefined);
const UserDispatchContext = React.createContext<UserDispatch | undefined>(
  undefined
);
```

我们正在创建两个独立的上下文，因为不是所有的组件都需要访问`State`和`Dispatch`。这样，组件可以只使用它需要的上下文。额外的好处是，如果一个组件只使用`Dispatch`，它不会在`State`变更时重新呈现，因为它没有使用那个上下文。

对于上下文中的状态管理，我们将使用`useReducer`。

```
// omitted rest of the file

enum UserActionTypes {
  LOADING = "loading",
  SUCCESS = "success"
}
type UserAction =
  | { type: UserActionTypes.LOADING }
  | { type: UserActionTypes.SUCCESS; payload: User };
type UserDispatch = (action: UserAction) => void;

function userReducer(state: UserState, action: UserAction): UserState {
  switch (action.type) {
    case UserActionTypes.LOADING: {
      return { isLoading: true };
    }
    case UserActionTypes.SUCCESS: {
      return { isLoading: false, user: action.payload };
    }
    default: {
      throw new Error("Invalid action type");
    }
  }
}
```

我经常这样写上下文。在应用程序启动时，我们希望获取当前登录用户的信息，并在全球范围内提供这些数据。

您想要获取的用户可能由一个 ID 决定，由于`Provider`组件可以接受 props，我们可以简单地传入一个`id`，这样当我们的上下文装载时，我们就可以获取用户。

下面是`Provider`组件的样子。

```
export const UserProvider: React.FC<{ id: string }> = ({ id, children }) => {
  const [state, dispatch] = React.useReducer(userReducer, { isLoading: true });

  React.useEffect(() => {
    const handleGetUser = async id => {
      dispatch({ type: UserActionTypes.LOADING });
      const user = await getUserById(id);
      dispatch({ type: UserActionTypes.SUCCESS, payload: user });
      return;
    };
    handleGetUser(id);
    return;
  }, [id]);

  return (
    <UserStateContext.Provider value={state}>
      <UserDispatchContext.Provider value={dispatch}>
        {children}
      </UserDispatchContext.Provider>
    </UserStateContext.Provider>
  );
};
```

在我的大多数应用程序中，我使用了钩子，所以我们将在这里定义钩子。

```
export const useUserState = () => {
  const userStateContext = React.useContext(UserStateContext);
  if (userStateContext === undefined) {
    throw new Error("useUserState must be used within a UserProvider");
  }
  return userStateContext;
};

export const useUserDispatch = () => {
  const userDispatchContext = React.useContext(UserDispatchContext);
  if (userDispatchContext === undefined) {
    throw new Error("useUserDispatch must be used within a UserProvider");
  }
  return userDispatchContext;
}
```

# 包扎

这是所有的东西:

```
import * as React from "react";
import { getUserById } from "../services/user-service";
import { User } from "../types/user";

interface UserState {
  user?: User;
  isLoading: boolean;
}
enum UserActionTypes {
  LOADING = "loading",
  SUCCESS = "success"
}
type UserAction =
  | { type: UserActionTypes.LOADING }
  | { type: UserActionTypes.SUCCESS; payload: User };
type UserDispatch = (action: UserAction) => void;

const UserStateContext = React.createContext<UserState | undefined>(undefined);
const UserDispatchContext = React.createContext<UserDispatch | undefined>(
  undefined
);

function userReducer(state: UserState, action: UserAction): UserState {
  switch (action.type) {
    case UserActionTypes.LOADING: {
      return { isLoading: true };
    }
    case UserActionTypes.SUCCESS: {
      return { isLoading: false, user: action.payload };
    }
    default: {
      throw new Error("Invalid action type");
    }
  }
}

export const UserProvider: React.FC<{ id: string }> = ({ id, children }) => {
  const [state, dispatch] = React.useReducer(userReducer, { isLoading: true });

  React.useEffect(() => {
    const handleGetUser = async id => {
      dispatch({ type: UserActionTypes.LOADING });
      const user = await getUserById(id);
      dispatch({ type: UserActionTypes.SUCCESS, payload: user });
      return;
    };
    handleGetUser(id);
    return;
  }, [id]);

  return (
    <UserStateContext.Provider value={state}>
      <UserDispatchContext.Provider value={dispatch}>
        {children}
      </UserDispatchContext.Provider>
    </UserStateContext.Provider>
  );
};

export const useUserState = () => {
  const userStateContext = React.useContext(UserStateContext);
  if (userStateContext === undefined) {
    throw new Error("useUserState must be used within a UserProvider");
  }
  return userStateContext;
};

export const useUserDispatch = () => {
  const userDispatchContext = React.useContext(UserDispatchContext);
  if (userDispatchContext === undefined) {
    throw new Error("useUserDispatch must be used within a UserProvider");
  }
  return userDispatchContext;
};
```

# 结论

这是我将要做的系列文章中的第五篇。下面是另外四篇文章。

[](https://medium.com/better-programming/an-opinionated-guide-to-react-folder-structure-file-naming-8b723d39a0d6) [## 一个固执己见的指南反应文件夹结构和文件命名

### 如何以简洁的方式组织你的项目

medium.com](https://medium.com/better-programming/an-opinionated-guide-to-react-folder-structure-file-naming-8b723d39a0d6) [](https://medium.com/better-programming/an-opinionated-guide-to-react-component-file-structure-5eb9f4a8d763) [## 固执己见的反应指南

### 组件文件结构

medium.com](https://medium.com/better-programming/an-opinionated-guide-to-react-component-file-structure-5eb9f4a8d763) [](https://medium.com/better-programming/opinionated-react-state-management-200d577ff122) [## 固执己见的反应:状态管理

### 您不需要使用第三方库来管理 React 应用程序中的状态

medium.com](https://medium.com/better-programming/opinionated-react-state-management-200d577ff122) [](https://medium.com/better-programming/opinionated-react-use-state-co-location-8123b904a971) [## 固执己见的反应:使用状态协同定位

### 为什么你应该把你的状态放在尽可能靠近它被使用的地方

medium.com](https://medium.com/better-programming/opinionated-react-use-state-co-location-8123b904a971) 

感谢阅读。