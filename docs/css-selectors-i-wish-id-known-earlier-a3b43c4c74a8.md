# 我希望我能早点知道 CSS 选择器

> 原文：<https://betterprogramming.pub/css-selectors-i-wish-id-known-earlier-a3b43c4c74a8>

## 节省时间和代码的提示和技巧

![](img/1a88dd71b791c3047e69bd8ab4b3f1e0.png)

米卡·鲍梅斯特在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 选择多个元素以应用相同的 CSS

```
.random-class__content1, .random-class__content2, .random-class__content3
```

我曾经写过 *n* 个单独的 CSS 类，但是你猜怎么着？这个选择器可以应用于多个类，只要你声明这些 CSS 类并用逗号连接它们。

# 选择元素的直接后代

```
.random-class__content > a
```

有些情况下，您只想将 CSS 应用于特定类中的所有锚标记，而不是针对所有锚标记。

这经常与以下内容相混淆:

```
.random-class__content .a
```

但它们是不同的。

选择器`#random-container > ul`将只针对`ul` s，它们是带有`random-container`的`id`的`div`的直接后代。它不会将第一个`li`、的子节点`ul`作为目标，而`#random-container ul` 会。

# 选择具有 N 个类的元素

```
.random-class1.random-class2
```

这(没有空格，是)将使您能够只选择应用了两个类的元素。请考虑以下情况:

```
<div class="random-class1 random-class2"></div>
<div class="random-class1"></div>
<div class="random-class2"></div>
```

您只能选择应用了两个指定类的元素。

# 选择紧接在 X 元素之后的 Y 元素

```
.random-class__content +.random-class__content2
```

这意味着 CSS 将仅应用于`.random-class_content`之后的所有`.random-class_content2`。例如，如果有一个独立的`.random-class_content2` 在`.random-class_content`之后，则相同的 CSS 将不适用。

这经常与下面的混淆。

```
.random-class__content ~ .random-class__content2
```

但它们是不同的。

请考虑以下情况:

```
<div class="random-class_content"/>
<div class="random-class_content2"/>
<div class="random-class_content3"/>
<div class="random-class_content2"/>
```

选择器`#random-class_content+.random-class_content2` 将只瞄准`div`之后带有`random-class_content`的`class`的元素。它不会瞄准紧接在`random-class_content3`之后的`random-class_content2`，而`#random-class_content~.random-class_content2`会。

# 选择不是第一个元素的元素

```
.random-class__content:not(:first-child)
```

这意味着给定以下内容，上面的 CSS 将只应用于第二、第三和第四个 div，并将方便地跳过列表中的第一个。

```
<div class="random-class_content"/>
<div class="random-class_content"/>
<div class="random-class_content"/>
<div class="random-class_content"/>
```

我希望你已经学到了一些东西！快乐学习！