# 用 Sass Mixins 清理样式表

> 原文：<https://betterprogramming.pub/how-to-clean-up-your-stylesheets-with-sass-mixins-22fe4ac87f48>

## 更好的样式表

![](img/21b49d32eb23655f6e3d4fe577409cfd.png)

照片由 [Eric Prouzet](https://unsplash.com/@eprouzet?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/broom?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 什么是 Sass Mixins？

[Sass mixins](https://sass-lang.com/documentation/at-rules/mixin) 是我们可以在整个 CSS 中重用的代码块，而无需重写代码块。

# 写你的第一个混音

写一个 mixin 毫不费力。你所需要做的就是使用`@mixin`关键字，后跟我们想要的 mixin 名称，一组花括号，然后是我们想要的任何声明。它本质上类似于编写 CSS 声明块，只是在声明前使用了`@mixin`命令。

```
@mixin blue-list {
    li {
       color:blue;
    }
}
```

**注意**:在命名混合字符时，连字符和下划线被认为是相同的。所以`blue-list`和`blue_list`将指定同一个 mixin。

要使用我们的 mixin，我们需要使用`@include`关键字。

```
ul {
 @include blue-list;
}
```

当 SCSS 被编译成 CSS 时，它变成这样:

```
ul li {
 color: blue;
}
```

# 争论

到目前为止，我们所学的非常有用，但是 mixins 可能会变得更加有趣。我们可以将参数传递到 mixin 中。

例如，如果我们想让我们以前的 mixin 接受一个参数，而不是硬编码颜色，我们可以这样做:

```
@mixin blue_list($color) {
li {
  color:$color;
 }
}
```

注意`$`。这指定后面的单词是一个变量名。

此外，变量有范围。例如，考虑以下代码块:

`$local-color`是未定义的，因为它不是全局变量，而`$color`是定义的，因为它是全局变量。另一个漂亮的特性是`!default` 关键字。`!default`的意思是“嘿编译器，如果没有定义`$color`，使用我的回退值黑色。”

有时，您会想要为 mixin 的参数定义后备值。这些回落被称为默认值。它们可以这样定义:

```
@mixin blue_list($color: blue) {
 li {
  color:$color;
 }
}
```

这意味着如果调用了`@include blue-list`，即使没有传递颜色值，Sass 也会返回蓝色，因为我们定义了一个默认值。

需要注意的是，默认参数必须在参数列表的最后定义。例如，类似这样的内容是不正确的语法，会导致抛出错误:

```
@mixin blue_list($color: blue $width) {
///Code
 }
```

在 CSS 中，有些属性可以接受多个参数。例如，页边空白可以用以下任何一种形式书写:

```
a {
 margin: 10px;
 margin: 10px 20px;
 margin: 10px 20px 50px;
 margin: 10px 20px 50px 20px;
}
```

如果我们的 margin 属性有一个 mixin，就会出现这样的问题:

```
@mixin margin($values) {
 @each $let in $values {
 margin: #{$let};
 }
}
```

这很好，但是如果我们不是传递一个 margin 值，而是像在 CSS 中那样传递几个 margin 值呢？调用类似于`@include margin(2px 4px 6px);` 的代码会编译成这样:

```
ul {
 margin: 2px;
 margin: 4px;
 margin: 6px;
}
```

如果我们想要传递多个值，我们必须指定 Sass 这不是一个列表，而是一个变量。我们通过使用三个点`…`来实现这一点，就像这样:

```
@mixin margin($values…) {
 //Code in here
}
```

通过在我们的论点中使用三点，调用`@include margin(2px 4px 6px)`编译成这样:

```
ul {
 margin: 2px 4px 6px;
}
```

而关于三点的话题，SCSS 也支持传播运营商。这意味着我们可以只传递一个叫做 *map* 的东西，而不是痛苦地逐个传递每个属性。例如，我们可以这样做:

# 流控制

其他优秀的 SCSS 功能是流量控制功能。没错:SCSS 有 *if* 、 *else* 、 *else if* 、*各*运算符。

`@if`、`@else if`和`@else`运算符的定义如下:

```
@if $let == true{
// Conditional code
@else if $let == true {
// Conditional code
} 
@else {
// Neither were true, so fallback to this
 }
}
```

`@each`这样叫:

```
@each <variables> in <list or map> {
//code
}
```

实际上，一个`@each`是这样的:

```
@each $name,$size in (“normal”:5px,”big”:10px,”huge”: 20px) {
.#{$name} { text-size: $size;}
}
```

会编译成这样:

```
.normal {
text-size: 5px;
}.big {
text-size: 10px;
}.huge {
text-size: 20px;
}
```

# 结论

Mixins 只是 Sass 特性的开始。从[文档](https://sass-lang.com/)中可以学到更多关于 Sass 的东西。

如果您有任何问题、建议或其他任何事情，请随时发表评论。