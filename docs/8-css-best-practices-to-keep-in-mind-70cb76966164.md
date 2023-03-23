# 8 SCSS 最佳实践

> 原文：<https://betterprogramming.pub/8-css-best-practices-to-keep-in-mind-70cb76966164>

## 为什么你的 CSS 应该首先是移动的和其他有用的提示

![](img/21b07fa749f04e47d271ce1b4f89b6e8.png)

约翰·施诺布里奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在过去的一周里，我有机会浏览了一家公司的编码指南，我发现其中一些不仅在协作环境中非常有用，而且在开发个人项目中也非常有用。

以下是来自这些指导方针的八个 SCSS 最佳实践，它们让我重新思考我构建 CSS 代码的方式。

# 1.移动优先

当谈到响应式设计时，通常会优先考虑桌面版本，这可能会使移动定制成为一个痛苦的过程。相反，我们应该设计成可扩展的——而不是把东西塞进手机里。

不要:

```
.bad {
  // Desktop code @media (max-width: 768px) {
    // Mobile code
  }
}
```

做:

```
.good {
  // Mobile code @media (min-width: 768px) {
    // Desktop code
  }
}
```

# 2.设置变量

定义 CSS 变量和 mixins 应该是初始设置的一部分，这可以使项目更易于维护。

根据指南，以下是一些将受益于变量的常见属性:

*   `border-radius`
*   `color`
*   `font-family`
*   `font-weight`
*   `margin`(天沟、网格天沟)
*   `transition`(持续时间、缓动)—考虑混音

# 3.避免#id 和！重要的

`!important`和`#id`都被认为过于具体，会扰乱 CSS 渲染的顺序——尤其是在协作开发时。

不要:

```
#bad {
  #worse {
     background-color: #000;
  }
}
```

做:

```
.good {
  .better {
     background-color: rgb(0, 0, 0);
  }
}
```

# 4.避免神奇的数字

尽量不要设置任意的数字，因为它们“正好可以工作”其他开发人员可能不理解为什么属性必须设置为如此特殊的数字。相反，尽可能地创建相对值。

如果你感兴趣，CSS-Tricks 有一个清晰的解释为什么幻数不好。

不要:

```
.bad {
  left: 20px;
}
```

做:

```
.good {
  // 20px because of font height
  left: ($GUTTER - 20px - ($NAV_HEIGHT / 2));
}
```

# 5.描述性命名

根据外观定义 CSS 选择器很容易。最好描述一下等级制度。

不要:

做:

# 6.零值和单位

这可能取决于个人选择或特定项目的风格指南，但一致性是关键。下面的规则要求您在零持续时间而不是零长度值上指定单位。还有，小数位加个前导零，但是小数位不要狂(超过三位)。

不要:

```
.not-so-good {
  animation-delay: 0;
  margin: 0px;
  opacity: .4567;
}
```

做:

```
.better {
  animation-delay: 0s;
  margin: 0;
  opacity: 0.4;
}
```

# 7.内嵌注释

这里的最佳实践是对你所描述的房产进行评论。此外，使用行内注释(`//`)而不是块级注释(`/* */`)，后者更难取消注释。

不要:

```
.bad {
  background-color: red; // Not commenting on top of property
  /* padding-top: 30px;
  width: 100% */
}
```

做:

```
.good {
  // Commenting on top of property
  background-color: red;
  // padding-top: 30px;
  // width: 100%;
}
```

# 8.嵌套媒体查询

为了方便定位媒体查询，建议您将媒体查询放在声明的根位置，而不是将它们嵌套在每个选择器中。

不要:

做:

# 结论

这绝不是最佳编码实践的详尽列表，但它们在设计可读、可伸缩的 web 应用程序中无疑扮演着重要角色。作为你的北极星，有没有你遵循的 CSS 准则？请在评论中告诉我！

感谢阅读！