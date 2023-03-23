# 你知道 HTML 中的键盘标签吗？

> 原文：<https://betterprogramming.pub/do-you-know-about-the-keyboard-tag-in-html-55bb3986f186>

## 更好的键盘命令文本格式

![](img/be8d0eec11b3f47c5310177d72cccccc.png)

照片由[弗洛里安·克拉姆](https://unsplash.com/@floriankrumm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

HTML5 中的`<kbd>`标签用于提供键盘输入。用这个标签包装键盘命令文本将提供更多语义正确的结果，并且还允许您有针对性地应用一些很酷的样式。使用`<kbd>`标签特别适合文档。

让我们看看它的实际效果。

# 超文本标记语言

## 带`标签`

```
<kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Del</kbd>
```

![](img/d50a49a637343cea6bccfc2818f157ba.png)

带`标签`

## 不带`标签`

只是为了比较，这是没有`<kbd>`标签时的样子:

```
<p>Ctrl+Alt+Del</p>
```

![](img/9086715ddd310080b9aa4dbc876006bd.png)

不带`标签`

# 半铸钢ˌ钢性铸铁(Cast Semi-Steel)

单有`标签，看起来也不怎么样。但是通过添加一些样式，它可以类似于实际的键盘按钮，以提供更真实的感觉:`

```
kbd {
border-radius: 5px;
padding: 5px;
border: 1px solid teal;
}
```

![](img/c98fc9bc0e7f817566d83d37ace52c67.png)

增加了造型

如果您查看控制台中的元素，您会发现它没有什么特别之处，除了一个等宽字体的变化。

![](img/2ff9e69eb6d64f44eede5773e6494ce7.png)

# 结论

使用`<code>`标签也可以产生同样的效果。那为什么创造了`<kbd>`？

答案在于语义。`<code>`用于显示简短的代码片段,`<kbd>`用于显示键盘输入。

感谢您的宝贵时间！