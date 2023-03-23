# 将数据传递给 props.children in React

> 原文：<https://betterprogramming.pub/passing-data-to-props-children-in-react-5399baea0356>

## 本周，我在 React 中遇到了一个障碍

# 问题是

本周，我在 React 中遇到了一个障碍，我需要将数据从父组件传递到子组件。执行此操作的传统方式如下:

上面假设了一些明确定义的`ChildComponent,`，其中数据、状态和功能作为属性传递给组件。

然而，在现实世界中，将数据传递给孩子有时并不简单。考虑这个例子，其中使用`this.props.children`定义和呈现子组件:

如何将数据和状态传递给每个子组件？

# 对救援做出反应

为了解决上述问题， [React.cloneElement 顶层 API](https://reactjs.org/docs/react-api.html#cloneelement) 很方便。根据 React 文档，该函数被定义为:

```
React.cloneElement(
  element,
  [props],
  [...children]
)
```

该函数将一个 react 组件作为第一个参数，在我们的例子中，它将是`child`组件。第二个参数是一个对象，它包含我们想要绑定到孩子的任何附加道具，例如`someData`、`someState`和`someFunction`，最后返回一个新元素。这几乎相当于:

```
<element.type {...element.props} {...props}>
    {children}
</element.type>
```

因此，在我们的场景中，`cloneElement`函数的实现变成了:

我们正在创建一个数组，使用`React.Children.map`和`this.props.children`作为第一个参数。第二个参数是一个函数，提供对每个孩子的访问，这个函数是使用`React.cloneElement`克隆的。在克隆过程中，包含`someData`、`someState`和`someFunction`的附加道具作为对象传递给每个孩子。

# 用例及示例代码

您可能想知道为什么会出现这种情况。考虑尝试构建一个可重用的“手风琴”,如下所示:

“accordion”中的每个孩子都需要访问`isActive`父状态，以了解哪个孩子当前是活动的。此外，单击子组件中的元素应该会触发父组件中的`isActive` 状态的变化。进行这种状态转换的函数也需要传递给孩子，即`someFunction`。

# 编辑:2019 年 11 月 30 日

正如评论中提到的，React 自撰写这篇博客以来已经有所改进，因此可能有更好的方法将数据传递到组件层次结构中。有人在评论中建议 [renderProps](https://reactjs.org/docs/render-props.html) 和使用 [React context](https://reactjs.org/docs/context.html) 是更好的选择。

我仍然认为这个解决方案足够好，如果你正在寻找快速的东西，但它可能不是最好的方法。

# 结论

React 是一个强大的库，使开发变得有趣和快速。它附带了许多 API，这些 API 可能很难理解，但是希望您现在知道了一种改进开发的方法。

如果你喜欢这个帖子，别忘了点赞推荐。

# 信用

丹尼尔·尤斯奇克— [反应—把状态传给 JSX 的{道具.儿童}](https://codepen.io/Yuschick/post/react-passing-state-to-props-children-in-jsx)