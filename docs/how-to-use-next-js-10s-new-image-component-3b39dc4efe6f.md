# 如何使用 Next.js 10 的新图像组件

> 原文：<https://betterprogramming.pub/how-to-use-next-js-10s-new-image-component-3b39dc4efe6f>

## Next.js 10 内置的图像组件和自动图像优化是盖茨比图像的替代方案吗？

![](img/b442e62fc57f97aee8539b241ab65d1d.png)

由 [Markus Spiske](https://unsplash.com/photos/4W5WWKaxsKs) 在 [Unsplash](https://unsplash.com/) 上拍摄的照片。

从版本 [10.0.0](https://nextjs.org/blog/next-10) 开始，Next.js 内置了图像组件，并自动进行图像优化。

图像组件`[next/image](https://nextjs.org/docs/api-reference/next/image)`，是 HTML `<img>`元素的扩展，已经为现代 web 发展。

# next/image 在引擎盖下做什么？

1.  自动提供像 WebP 这样的现代图像格式的图像，如果浏览器支持的话，它比 JPEG 小 30%。
2.  按需优化图像。构建时间不会改变。
3.  仅当滚动视区时达到某个阈值时，才延迟加载页面内的图像。
4.  你可以决定将它与 Vercel 的内部 CDN 或像 Cloudinary 或 Akamai 这样的其他提供商一起使用。
5.  您可以指定[不同的图像尺寸](https://nextjs.org/docs/basic-features/image-optimization#device-sizes)以动态使用不同的自定义分辨率。
6.  自动将照片质量更改为设置为 75%的较低阈值。这可以在每次调用时进行更改。

# 怎么用呢？

首先，您需要运行下一版本 10+:

```
npm install next@latest react@latest react-dom@latest
```

或者如果你使用纱线:

```
yarn add next@latest react@latest react-dom@latest
```

# 我如何在实践中使用它？

这个示例页面将从通常的`img`标签转换而来:

转换成新的`'next/image'`格式:

## 将类名移到标签之外！

由于 Next 的新图像组件将在运行时对生成的 HTML 代码进行修改，您需要将`className`移到旧的`img`标签之外。通过将您的类移动到外部元素，每种样式都将应用到您的图像中。

## 必备道具！

你需要一直通过`width`和`height`。这些道具是必须的，过不了就需要过`layout="fill"`。

## 布局类型

您可以使用四种不同类型的布局值(默认为`intrinsic`):

*   `fixed` —图像尺寸不会随着视窗的改变而改变(无响应)。
*   `intrinsic` —图像将缩小较小视窗的尺寸，但保持较大视窗的原始尺寸。
*   `responsive` —图像将缩小较小视窗的尺寸，放大较大视窗的尺寸。
*   `fill` —图像将宽度和高度拉伸到父元素的尺寸。

# 重要警告

## **本地和远程图像**

截至 2020 年 11 月，只有 Vercel 为您的下一个项目自动处理[图像优化](https://vercel.com/docs/next.js/image-optimization)。默认情况下，只有图像被本地上传到 Git 存储库。

虽然这对于一个基本的应用程序可能是有用的，但 Vercel 的团队通常建议从像 [Sanity](https://www.sanity.io/) 这样的无头 CMS 加载内容(也包括图像)。

为此，您需要在您的`next.config.js`文件中传递一个额外的`images`属性，如[所示:](https://nextjs.org/docs/basic-features/image-optimization#domains)

```
module.exports = {
  images: {
    domains: ['example.com'],
  },
}
```

## **CDN**

另一个警告是，如果您不使用 Cloudinary 或当前支持的加载程序，您将需要等待或添加对其他图像 CDN 服务的支持，如:

*   [ImageKit.io](https://github.com/vercel/next.js/issues/18408)
*   [快速图像](https://github.com/vercel/next.js/pull/18391)
*   [图像引擎](https://github.com/vercel/next.js/pull/18495)

# 如果你想了解更多关于钩子的知识

我曾亲自阅读**[**学习 React Hooks**](https://amzn.to/2UFiZ5F)**当我开始使用 Hooks 和 NextJs 时，它帮助我了解 React 及其语法和生态系统的插件和库每天都在增加:[https://amzn.to/2UFiZ5F](https://amzn.to/2UFiZ5F)****

# ****资源****

*   ****[https://nextjs.org/docs/basic-features/image-optimization](https://nextjs.org/docs/basic-features/image-optimization)****
*   ****[https://nextjs.org/docs/api-reference/next/image](https://nextjs.org/docs/api-reference/next/image)****
*   ****[https://vercel.com/docs/next.js/image-optimization](https://vercel.com/docs/next.js/image-optimization)****
*   ****https://nextjs.org/blog/next-10****
*   ****【https://amzn.to/2UFiZ5F ****