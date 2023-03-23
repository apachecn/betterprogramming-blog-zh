# 理解键、虚拟 DOM、协调和差异

> 原文：<https://betterprogramming.pub/understand-keys-virtual-dom-reconciliation-and-diffing-in-react-e65a9bee316f>

## React 开发人员的基本概念

![](img/b4f87bf99dfb89307a4e140137e7c1c5.png)

图片来源:[壁纸访问](https://wallpaperaccess.com/react-js)

许多开发人员知道如何用 React 构建复杂的 ui，但很少有人了解 React 的内部。为了充分发挥 React 的潜力，学习这些概念非常重要。

让我们从虚拟 DOM 开始。

# 虚拟 DOM

它是 UI(DOM 的副本)的虚拟表示，保存在内存中，并通过像 [ReactDOM](https://reactjs.org/docs/react-dom.html) 这样的库与真实的 DOM 同步。

## 既然有实际的 DOM，为什么我们还需要一个虚拟的 DOM 呢？

嗯，DOM 操作是昂贵的，并且在每次属性/状态改变时更新整个 DOM 是非常低效的。下面是虚拟 DOM 如何处理这种低效率:

*   组件道具/状态改变
*   React 触发重新渲染
*   React 将虚拟 DOM(更新前的虚拟 DOM)与更新后的虚拟 DOM(更新后的虚拟 DOM)进行比较
*   React 通过对真实 DOM 进行最少的操作来确定反映 UI 变化的最佳方式。

所以它的虚拟 DOM 帮助 React 更新 UI 以匹配最新的树。

# 和解

保持虚拟 DOM 与真实 DOM 同步的过程称为协调。

所以我们上面讨论的整个过程被称为和解。

# 差异

虚拟 DOM 之间的比较(找出 UI 中需要更新的内容)被称为 diffing，进行这种比较的算法被称为 diffing 算法。

## 差分算法是如何工作的？

diffing 算法通过首先比较它们的根节点来比较这两棵树。

## 不同的根节点

考虑下面的片段:

```
<div>
  <p>Hello</p>
</div><section>
  <p>Hello</p>
</section>
```

比较上面的两棵树将导致完全的重建，因为根元素`<div>`和`<section>`是不同的。根目录下的任何组件也将被重建。

## 相同的根节点

当比较相同类型的 DOM 元素时，React 会比较两者的属性，保持相同的底层 DOM 节点，并更新更改后的属性。

```
<div className="foo" /><div className="foo bar" />
```

React 修改底层 DOM 节点上的类名。

## 不同列表

当区分 DOM 节点的子节点时，React 会比较两个列表的子节点，并在有差异时生成一个变异。

考虑下面的待办事项列表:

```
<ul>
  <li>Breakfast</li>
  <li>Lunch</li>
</ul>
```

现在，让我们在列表的开头添加一个新项目:

```
<ul>
  <li>Exercise</li>
  <li>Breakfast</li>
  <li>Lunch</li>
</ul>
```

当新项目添加到列表中时，会发生以下情况:

*   React 将虚拟 DOM 中的`<li>Breakfast</li>`与更新后的虚拟 DOM 中的`<li>Exercise</li>`进行比较
*   这个比较告诉 React 列表项被改变了
*   React 将重新绘制 DOM 中的所有列表项

这是非常低效的，因为 React 必须重新绘制所有的列表项。为了解决这个问题，我们可以给一个`key`属性一个值，在它的兄弟列表中唯一地标识一个列表项。

# 键

属性让 React 识别列表中的每个元素。它用于跟踪被更改、添加或删除的项目。键在其同级中应该是唯一的，而不是全局唯一的。

现在让我们在上面的例子中添加一个关键道具:

```
<ul>
  <li key="br">Breakfast</li>
  <li key="lu">Lunch</li>
</ul><ul>
  <li key="ex">Exercise</li>
  <li key="br">Breakfast</li>
  <li key="lu">Lunch</li>
</ul>
```

现在 React 知道带有关键字`'ex'`的元素是新的元素，React 将只是把新元素添加到 DOM 中，而不是再次操作 DOM 中的所有项目。

# 选择什么作为键？

通常，你正在迭代的数据集已经有一个 ID 或者可以用作 ID 的东西。所以你可以用这个。

```
<li key={item.id}>{item.name}</li>
```

当您的数据集中没有稳定的 id 时，您可以使用像 [nanoid](https://www.npmjs.com/package/nanoid) 这样的库，它可以为您生成唯一的 ID。

```
import { nanoid } from 'nanoid';{items.map(item => (
  <div key={nanoid()}>{item.name}</div>
))}
```

但是，索引并不总是最佳选择，可能会导致性能下降和意外的呈现问题。[这里有一篇文章解释了使用索引作为关键字的负面影响](https://robinpokorny.medium.com/index-as-a-key-is-an-anti-pattern-e0349aece318)。

感谢阅读。如果你喜欢你读到的东西，也可以看看相关的故事。

# 有关系的

[](https://haseeb-anwar.medium.com/react-router-6-features-upgradation-guide-886b9bc2fb71) [## React 路由器 6 功能和升级指南

### 迄今为止最强大的 React 客户端路由器

haseeb-anwar.medium.com](https://haseeb-anwar.medium.com/react-router-6-features-upgradation-guide-886b9bc2fb71) [](/8-best-practices-every-react-developer-should-follow-44177670c646) [## 每个 React 开发人员应该遵循的 8 个最佳实践

### 编写更好的 React 代码的技巧

better 编程. pub](/8-best-practices-every-react-developer-should-follow-44177670c646)