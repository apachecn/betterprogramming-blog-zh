# 没有 Thunks 和动作创建器的面向钩子的 Redux 编码模式

> 原文：<https://betterprogramming.pub/react-hooks-oriented-redux-coding-pattern-without-thunks-and-action-creators-b338b42c5327>

## 小型应用程序和初学者指南

![](img/bb579d7bd22be2a621a67acd3ae7769e.png)

照片由 [Berkay Gumustekin](https://unsplash.com/@berkaygumustekin) 在 [Upsplash](https://unsplash.com/photos/fogjAvfUERE) 上拍摄

# 动机

我爱 Redux。但这并不意味着我喜欢 Redux 生态系统的所有部分。有些人不喜欢 Redux，因为它的样板代码。真可悲。样板代码不是来自 Redux 核心，而是来自生态系统。不要误解我。最佳实践很好，我认为 Redux Starter Kit 最近的工作很棒。(向马克鼓掌。)

对于 Redux 怎么和 React 一起用，我想我有自己的理解。可能不常见，大概也永远不会是主流。我知道 Redux 是有用的，并针对较大的应用程序进行了调整，但我想到的是较小的应用程序和初学者的用法。

对于较小的应用程序和初学者来说，似乎有几个障碍。我的第一个是`mapStateToProps`。我开发了 [reactive-react-redux](https://github.com/dai-shi/reactive-react-redux) 来解决。它提供了一个超级简单的`useTrackedState`。它是在 Redux hooks API 可用之前开发的。现在，`useSelector`从新的钩子 API 是如此之好。比`mapStateToProps`丑多了。注意，`useTrackedState`仍然更容易，因为它不需要内存化来优化。

我面临的另一个障碍是异步操作。我一般喜欢 Redux 的中间件系统和 Redux Thunk 实现的优雅。但是我发现它有些困难。基本上就是太灵活了。这在某种程度上就像将中间件系统暴露给用户。就像人们通过繁重的计算误用选择器一样，人们误用 thunks——或者过度使用它们。Redux-Observable 和 Redux-Saga 似乎提供了更好的抽象，但它们是复杂的系统。它们适合更大的应用程序。

所以在这篇文章中，我想展示一个替代模式的示例代码。它不使用中间件，而是反应自定义挂钩。以下是这种模式中的一些要点。

*   没有异步库(在 Redux 之外运行异步任务)
*   无操作创建者(在 TypeScript 中定义操作类型)

不多说了，让我们深入代码。

(顺便说一下，对我来说还有一个障碍是`combineReducers`，但这超出了本文的范围。)

# 例子

要用的例子是官方 Redux 高级教程中的[异步动作](https://redux.js.org/advanced/async-actions)。

# 密码

## **文件夹结构**

```
- src/
  - index.tsx
  - store/
    - actions.ts
    - reducers.ts
  - hooks/
    - useSelectSubreddit.ts
    - useInvalidateSubreddit.ts
    - useFetchPostsIfNeeded.ts
  - components/
    - App.tsx
    - Picker.tsx
    - Posts.tsx
```

## **src/index.tsx**

```
import * as React from 'react';
import { render } from 'react-dom';
import { Provider } from 'react-redux';import rootReducer from './store/reducers';
import App from './components/App';const store = createStore(rootReducer);render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('app'),
);
```

这是切入点。你在这个文件里找不到什么特别的东西。

## **src/store/actions.ts**

```
export type Post = {
  id: string;
  title: string;
};export type SubredditPosts = {
  isFetching: boolean;
  didInvalidate: boolean;
  items: Post[];
  lastUpdated?: number;
};export type PostsBySubreddit = {
  [subreddit: string]: SubredditPosts;
};export type SelectedSubreddit = string;export type State = {
  selectedSubreddit: SelectedSubreddit;
  postsBySubreddit: PostsBySubreddit;
};type SelectSubredditAction = {
  type: 'SELECT_SUBREDDIT';
  subreddit: string;
};type InvalidateSubredditAction = {
  type: 'INVALIDATE_SUBREDDIT';
  subreddit: string;
};type RequestPostsAction = {
  type: 'REQUEST_POSTS';
  subreddit: string;
};type ReceivePostsAction = {
  type: 'RECEIVE_POSTS';
  subreddit: string;
  posts: Post[];
  receivedAt: number;
};export type Action =
  | SelectSubredditAction
  | InvalidateSubredditAction
  | RequestPostsAction
  | ReceivePostsAction;
```

这定义了`State`和`Action`类型。没有定义动作常数和动作创建者。

## **src/store/reducers.ts**

```
import { combineReducers } from 'redux';
import {
  SubredditPosts,
  SelectedSubreddit,
  PostsBySubreddit,
  State,
  Action,
} from './actions';const selectedSubreddit = (
  state: SelectedSubreddit = 'reactjs',
  action: Action,
): SelectedSubreddit => {
  switch (action.type) {
    case 'SELECT_SUBREDDIT':
      return action.subreddit;
    default:
      return state;
  }
};const posts = (state: SubredditPosts = {
  isFetching: false,
  didInvalidate: false,
  items: [],
}, action: Action): SubredditPosts => {
  switch (action.type) {
    case 'INVALIDATE_SUBREDDIT':
      return {
        ...state,
        didInvalidate: true,
      };
    case 'REQUEST_POSTS':
      return {
        ...state,
        isFetching: true,
        didInvalidate: false,
      };
    case 'RECEIVE_POSTS':
      return {
        ...state,
        isFetching: false,
        didInvalidate: false,
        items: action.posts,
        lastUpdated: action.receivedAt,
      };
    default:
      return state;
  }
};const postsBySubreddit = (
  state: PostsBySubreddit = {},
  action: Action,
): PostsBySubreddit => {
  switch (action.type) {
    case 'INVALIDATE_SUBREDDIT':
    case 'RECEIVE_POSTS':
    case 'REQUEST_POSTS':
      return {
        ...state,
        [action.subreddit]: posts(state[action.subreddit], action),
      };
    default:
      return state;
  }
};const rootReducer = combineReducers<State>({
  postsBySubreddit,
  selectedSubreddit,
});export default rootReducer;
```

这是一个带有类型注释的普通 reducer 文件。注意，我们没有使用显式或隐式的`any`。

## **src/hooks/useselectsubreddit . ts**

```
import { useCallback } from 'react';
import { useDispatch } from 'react-redux';import { Action } from '../store/actions';const useSelectSubreddit = () => {
  const dispatch = useDispatch<Action>();
  const selectSubreddit = useCallback((subreddit: string) => {
    dispatch({
      type: 'SELECT_SUBREDDIT',
      subreddit,
    });
  }, [dispatch]);
  return selectSubreddit;
};export default useSelectSubreddit;
```

这是某种东西而不是一个动作创造者。它是一个返回回调函数的钩子，该函数创建并调度一个动作。姑且称之为*动作钩。*这是一个同步动作挂钩:

## src/hooks/use invalidatesubreddit . ts

```
import { useCallback } from 'react';
import { useDispatch } from 'react-redux';import { Action } from '../store/actions';const useInvalidateSubreddit = () => {
  const dispatch = useDispatch<Action>();
  const invalidateSubreddit = useCallback((subreddit: string) => {
    dispatch({
      type: 'INVALIDATE_SUBREDDIT',
      subreddit,
    });
  }, [dispatch]);
  return invalidateSubreddit;
};export default useInvalidateSubreddit;
```

这是另一个同步动作挂钩:

## **src/hooks/usefetchpostsifneed . ts**

```
import { useCallback } from 'react';
import { useDispatch, useStore } from 'react-redux';import { Action, State, Post } from '../store/actions';const shouldFetchPosts = (state: State, subreddit: string) => {
  const posts = state.postsBySubreddit[subreddit];
  if (!posts) {
    return true;
  }
  if (posts.isFetching) {
    return false;
  }
  return posts.didInvalidate;
};const extractPosts = (json: unknown): Post[] | null => {
  try {
    const posts: Post[] = (json as {
      data: {
        children: {
          data: {
            id: string;
            title: string;
          };
        }[];
      };
    }).data.children.map(child => child.data);
    // type check
    if (posts.every(post => (
      typeof post.id === 'string' && typeof post.title === 'string'
    ))) {
      return posts;
    }
    return null;
  } catch (e) {
    return null;
  }
};const useFetchPostsIfNeeded = () => {
  const dispatch = useDispatch<Action>();
  const store = useStore<State>();
  const fetchPostsIfNeeded = useCallback(async (subreddit: string) => {
    if (!shouldFetchPosts(store.getState(), subreddit)) {
      return;
    }
    dispatch({
      type: 'REQUEST_POSTS',
      subreddit,
    });
    const response = await fetch(`[https://www.reddit.com/r/](https://www.reddit.com/r/)${subreddit}.json`);
    const json = await response.json();
    const posts = extractPosts(json);
    if (!posts) throw new Error('unexpected json format');
    dispatch({
      type: 'RECEIVE_POSTS',
      subreddit,
      posts,
      receivedAt: Date.now(),
    });
  }, [dispatch, store]);
  return fetchPostsIfNeeded;
};export default useFetchPostsIfNeeded;
```

这是一个异步操作钩子。这是用 Thunk 编写的逻辑。这个文件中有几个要点。

*   因为这不是中间件，所以我们不能直接访问状态。它用的是`useStore`，这是我们不应该误用的。这是整个模式中最大的警告。
*   `extractPosts`是一种从网络上测试 JSON 的类型卫士。
*   我们没有像原来教程中的[那样实现错误处理。](https://redux.js.org/advanced/async-actions#note-on-error-handling)

## **src/components/App.tsx**

```
import * as React from 'react';
import { useCallback, useEffect } from 'react';
import { useSelector } from 'react-redux';import { State, SelectedSubreddit } from '../store/actions';
import useSelectSubreddit from '../hooks/useSelectSubreddit';
import useFetchPostsIfNeeded from '../hooks/useFetchPostsIfNeeded';
import useInvalidateSubreddit from '../hooks/useInvalidateSubreddit';import Picker from './Picker';
import Posts from './Posts';const App: React.FC = () => {
  const selectedSubreddit = useSelector((state: State) => state.selectedSubreddit);
  const postsBySubreddit = useSelector((state: State) => state.postsBySubreddit);
  const {
    isFetching,
    items: posts,
    lastUpdated,
  } = postsBySubreddit[selectedSubreddit] || {
    isFetching: true,
    items: [],
    lastUpdated: undefined,
  }; const fetchPostsIfNeeded = useFetchPostsIfNeeded();
  useEffect(() => {
    fetchPostsIfNeeded(selectedSubreddit);
  }, [fetchPostsIfNeeded, selectedSubreddit]); const selectSubreddit = useSelectSubreddit();
  const handleChange = useCallback((nextSubreddit: SelectedSubreddit) => {
    selectSubreddit(nextSubreddit);
  }, [selectSubreddit]); const invalidateSubreddit = useInvalidateSubreddit();
  const handleRefreshClick = (e: React.MouseEvent) => {
    e.preventDefault();
    invalidateSubreddit(selectedSubreddit);
    fetchPostsIfNeeded(selectedSubreddit);
  }; const isEmpty = posts.length === 0;
  return (
    <div>
      <Picker
        value={selectedSubreddit}
        onChange={handleChange}
        options={['reactjs', 'frontend']}
      />
      <p>
        {lastUpdated && (
          <span>
            Last updated at {new Date(lastUpdated).toLocaleTimeString()}.
            {' '}
          </span>
        )}
        {!isFetching && (
          <button type="button" onClick={handleRefreshClick}>
            Refresh
          </button>
        )}
      </p>
      {isEmpty && isFetching && <h2>Loading...</h2>}
      {isEmpty && !isFetching && <h2>Empty.</h2>}
      {!isEmpty && (
        <div style={{ opacity: isFetching ? 0.5 : 1 }}>
          <Posts posts={posts} />
        </div>
      )}
    </div>
  );
};export default App;
```

这是一个根组件或容器组件。不幸的是，代码看起来像样板文件。但它应该与普通的 React 应用程序基本相同。我认为这种模式的第二个警告是需要`useCallback`钩子。

## **src/components/picker . tsx**

```
import * as React from 'react';const Picker: React.FC<{
  value: string;
  onChange: (value: string) => void;
  options: string[];
}> = ({ value, onChange, options }) => (
  <span>
    <h1>{value}</h1>
    <select
      onChange={e => onChange(e.target.value)}
      value={value}
    >
      {options.map(option => (
        <option value={option} key={option}>
          {option}
        </option>
      ))}
    </select>
  </span>
);export default Picker;
```

这是一个无状态组件。除了类型注释之外，什么都没有改变。

## **src/components/posts . tsx**

```
import * as React from 'react';const Posts: React.FC<{
  posts: {
    id: string;
    title: string;
  }[];
}> = ({ posts }) => (
  <ul>
    {posts.map(post => (
      <li key={post.id}>{post.title}</li>
    ))}
  </ul>
);export default Posts;
```

这是另一个无状态组件。我们可以从`actions.ts`导入`Post`。

就这些了。我们都准备好了。

# 演示

[回购中的源代码](https://github.com/dai-shi/reactive-react-redux/tree/master/examples/12_async)

注意这段代码是基于 [reactive-react-redux](https://github.com/dai-shi/reactive-react-redux) 而不是 react-redux。reactive-react-redux 有一个与 react-redux 兼容的 hooks API，除了`useStore`。在这个演示中，`useStore`是用另一个上下文实现的。

# 结束语

这种编码模式可能并不新鲜，我相信已经有人尝试过了。然而，使用 React 钩子和 TypeScript 更有意义。它可以消除一些样板代码。这个例子使用了一个`isFetching`标志来显示加载状态，但是它会随着 React 悬念而改变。这种模式应该可以缓解悬念的过渡。