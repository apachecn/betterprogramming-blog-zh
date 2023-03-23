# 看看 React Pokedom——一个在 React 中捕捉神奇宝贝的库

> 原文：<https://betterprogramming.pub/check-out-react-pokedom-a-library-to-catch-pok%C3%A9mon-alongside-dom-events-eb1c4f391e3c>

## 你的 DOM 里藏着 800 多只神奇宝贝。必须抓住他们！

![](img/9c7ce1394d7a1546bbf76368c8bc5032.png)

[巴林吉特酒保](https://unsplash.com/@bahnijitb?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

几天前，我在 LinkedIn 上看到一篇宣布发布 [React Pokedom](https://github.com/aurasphere/react-pokedom) 的帖子。这个项目背后的想法似乎很吸引我，我忍不住想深入研究一下。我必须学习更多。

React Pokedom 由 [GomorraSQL](/check-out-gomorrasql-a-library-to-write-queries-in-neapolitan-3e85568dddb4) 的作者[多纳托·里门蒂](https://github.com/aurasphere)创建，是一个用于捕捉神奇宝贝的 React 库。这些神奇宝贝不是藏在草丛里，而是藏在一个网页的 DOM 里。800 多只神奇宝贝藏在大教堂里，挑战是要抓住它们。

[](/check-out-gomorrasql-a-library-to-write-queries-in-neapolitan-3e85568dddb4) [## 查看 GomorraSQL——一个用那不勒斯语编写查询的库

### 像那不勒斯人一样用 GomorraSQL 查询数据库

better 编程. pub](/check-out-gomorrasql-a-library-to-write-queries-in-neapolitan-3e85568dddb4) 

让我们来看看 React Pokedom 库是如何工作的，以及如何在 React 中捕捉神奇宝贝！

# 什么是 React Pokedom？

![](img/689825285df579b74594cae7f4e0b180.png)

对来自 [GitHub](https://github.com/aurasphere/gomorra-sql) 的 Pokedom 的 logo 作出反应 [Federica Lisci](https://www.linkedin.com/in/federica-lisci-377220162/)

首先，我们试着了解一下 React Pokedom 是什么:

> “React Pokedom 是一个 React 库，用于在 dom 事件的同时捕捉神奇宝贝。你的 DOM 里藏着 800 多只神奇宝贝。你能把他们都抓到吗？”— [React Pokedom GitHub 页面](https://github.com/aurasphere/react-pokedom#react-pokedom)

具体来说，React Pokedom 是 React 的 [TypeScript npm 库。该库基于两个定制的 React 挂钩:](https://www.npmjs.com/package/@aurasphere/react-pokedom)

*   `usePokeball`
*   `usePokedex`

现在让我们学习如何使用这两个钩子来玩 React Pokedom，并开始捕捉神奇宝贝。但是首先，让我们看看如何安装 React Pokedom。

# React Pokedom 入门

您可以使用以下 npm 命令将 React Pokedom 添加到项目的依赖项中:

```
npm install @aurasphere/react-pokedom
```

或者，如果你是纱线用户:

```
yarn add @aurasphere/react-pokedom
```

然后，你可以在这里查看 React Pokedom 使用的所有 TypeScript 类型:[https://github . com/aura sphere/React-Pokedom/blob/main/src/types . ts](https://github.com/aurasphere/react-pokedom/blob/main/src/types.ts)。

恭喜你。你现在有一切可以开始捕捉躲在你的 DOM 的神奇宝贝！

# 如何使用 React Pokedom

React Pokedom 是关于在 dom 事件旁边捕捉神奇宝贝，并查看在 Pokédex 中捕捉到的神奇宝贝。让我们来学习如何执行这两种操作！

## 抓住你的第一个神奇宝贝

在 React Pokedom 中捕捉一只神奇宝贝是围绕着`usePokeball`钩子进行的。具体来说，这个钩子返回一个函数，您可以将它用作 [DOM 事件处理函数](https://reactjs.org/docs/handling-events.html)，如下所示:

```
// importing the usePokeball hook from React Pokedom
import { usePokeball } from '@aurasphere/react-pokedom';

// ...

// initializing the DOM event callback to catch Pokémon
const pokeball = usePokeball();

//...

// by clicking this button, the pokeball() callback
// gets called
<button onClick={pokeball}>
  Click here try to catch a Pokemon!
</button>
```

每次调用`pokeball()` DOM 事件回调时，您可能会捕捉到一个神奇宝贝。这取决于神奇宝贝在 DOM 中的隐藏程度以及神奇宝贝球是否成功。就像神奇宝贝游戏一样，这是一个随机的过程。

如果您想知道您是否在不查看 Pokédex 的情况下捕获了一个神奇宝贝，您可以将一个回调函数作为参数传递给挂钩。这个回调参数函数接受一个类型为`PokedomEvent`的参数，它只不过是一个带有可选的`pokemon`字段的`[Event](https://developer.mozilla.org/en-US/docs/Web/Events)`类型。如果`pokemon`字段存在，则一个神奇宝贝被捕获。

您可以向`usePokeball`传递一个回调函数，如下所示:

```
const pokemonCaptureCallback = (event) => {
    if (event.pokemon) {
        console.log("Pokémon caught!");
    } else {
        console.log("You weren't lucky! Try again...");
    }
}

const pokeball = usePokeball(pokemonCaptureCallback);
```

`pokemonCaptureCallback()`将在`pokeball()`事件回调函数中被调用，并将告诉您是否有神奇宝贝被捕获。

## 探索扑克指数

没有神奇宝贝相关的游戏没有神奇宝贝索引。感谢`usePokedex`挂钩，你可以查看到目前为止你抓到的神奇宝贝。具体来说，`usePokedex`返回你抓到的神奇宝贝的数组。

但是，请记住，挂钩只在神奇宝贝世界中有效！

因此，如果您想让`usePokedex`工作，您首先必须用`<KantoRegion>`包装您调用`usePokedex`的组件，如下所示:

```
import { KantoRegion } from '@aurasphere/react-pokedom';

// ...

<KantoRegion>
    <MyComponent />
</KantoRegion>
```

现在，在`<MyComponent>`中，您可以如下使用`usePokedex`:

```
// src/components/MyComponent.js

import { usePokedex } from '@aurasphere/react-pokedom';

// ...

export default function MyComponent() {
    // getting the list of Pokémon caught up to this moment
    const pokedex = usePokedex()

    // ...

    return (
        // ...
    )
}
```

瞧啊！你刚刚学会了如何玩 React Pokedom！现在，剩下的就是抓住藏在你的 DOM 里的所有神奇宝贝。必须抓住他们！

# 结论

在本文中，我们研究了 React Pokedom，这是一个 TypeScript npm 库，允许您通过 dom 事件捕捉神奇宝贝。像这样的项目在帮助好奇的人和孩子接近并进入编程世界方面发挥了重要作用。将编码变成一种有趣的体验并不容易，React Pokedom 成功了！

感谢阅读！我希望这篇文章对你有所帮助。如果有任何问题、意见或建议，请随时联系我。