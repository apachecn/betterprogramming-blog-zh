# 让你的文本在 HTML 中可编辑的简单技巧

> 原文：<https://betterprogramming.pub/a-simple-trick-to-make-your-text-editable-in-html-95d36820bb41>

## 了解内容可编辑

![](img/3b0eeb75f63e20d0d68df261060be08e.png)

Christopher Gower 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

如果你一直在关注[我最近的推文](https://twitter.com/annieliao)，你可能已经注意到我目前正在和韦斯·博斯的 [JavaScript30 挑战](https://javascript30.com/)一起编码。

虽然许多挑战都是为了复习我的 JavaScript 知识，但有时我不得不暂停教程，在心里大喊:“哇，我为什么不早点了解这些呢？”

当我面临创建可移动文本阴影的挑战时，我又有了那个时刻。

事实证明，有一个名为`contenteditable`的 HTML 全局属性允许用户在浏览器上编辑目标文本。

根据 [MDN 的文档](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/contenteditable) , `contenteditable`是一个枚举属性(意味着它有一个可能值的列表，与布尔值相反)，其中`true`或空字符串表示文本是可编辑的，否则为`false`。

如果该属性未被赋予任何值，则该值将被视为空字符串，从而使文本可编辑:

```
<div>
  <h1 contenteditable>🔥WOAH!</h1>
</div>
```

就这样，你可以在你的浏览器上输入任何你想要的文本。

没有 CSS，没有 JavaScript，也不需要输入字段。只是一个普通的 HTML 属性。有关安全相关的信息，请随意查看[这个堆栈溢出讨论](https://stackoverflow.com/questions/41623980/is-the-html5-property-contenteditable-secure)。

![](img/697b841ad0a0b01a7cba79d3154a8266.png)

你在工作或项目中使用过`contenteditable`吗？有没有其他的前端魔术，你希望早点发现？请在评论中分享你的经历。我很想看到一些实际的使用案例！