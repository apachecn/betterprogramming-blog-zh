# 加速页面加载的 7 个 Web 开发指南

> 原文：<https://betterprogramming.pub/7-web-development-guidelines-to-speed-up-page-loading-e8f0e13a53b>

## 为了你的访问者的爱，改善用户体验

![](img/7e01d65063943308ce33668383b2bbf4.png)

照片由 [Guillaume Jaillet](https://unsplash.com/@i_am_g?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Web 开发在过去的几年中不断发展。后端系统中的更多逻辑已经转移到了前端。我们在浏览器中加载了更多更重的文件，但是这些东西会影响用户体验。

在这篇文章中，我想分享一些帮助我保持高质量性能和用户体验的指导原则。

# 1.尽量减少请求的数量

我知道这听起来很明显，但是最小化请求的数量需要更多的思考。如果你的页面不需要图片、视频、图标、pdf，即使用户看不到也不要加载。

因此，如果你在 iframe 中有一个包含 YouTube 的弹出窗口，当用户打开弹出窗口时加载它。不是更早。

当用户不滚动页面时，不要在你的页脚加载图片！用户看不到，所以不要在上面浪费任何资源。

仅在需要时加载外部源！就这么简单。

# 2.首选网页安全字体

我知道我们喜欢 Google Fonts、TypeKit 或其他服务提供的漂亮字体。但是，我们需要它们吗？你需要所有的字体，还是我们只使用一种字体？

也许我们需要研究找到一个伟大的[安全网页字体](https://www.w3schools.com/cssref/css_websafe_fonts.asp)，看起来类似于我们的自定义字体。如果它接近，我会建议选择那一个！

加载自定义字体需要用户花费一些时间来下载，尤其是当他们的连接速度很慢的时候。它会导致糟糕的用户体验，因为它会阻止整个页面的响应。

网页安全字体总是更安全、更快速，并在各方面改善用户体验。对于设计师来说，即使是网页安全字体也能提高可读性，并有助于网站的品牌推广。

你还不相信吗？

我强烈推荐阅读 Matt Claffey 的文章“[以 FOUT 方式加载字体](https://mattclaffey.medium.com/loading-fonts-the-fout-way-92beed75dc38)”。里面有一些很棒的提示，可以让你以最佳方式加载字体。

除此之外，Joshua Stopper 的《[自定义 web 字体的性能成本，以及如何解决这个问题](https://www.wholegraindigital.com/blog/performant-web-fonts/)》是另一个关于如何保持 web 字体性能的深入指导。

# 3.使用 SVG 图标代替图标字体

图标，在网站和网络应用中，我们离不开它。

像 FontAwesome，Google Material Icons，Iconmoon 这样的服务已经让图标字体流行起来了！图标字体非常适合为你的品牌定制颜色。但是像标准的网络字体一样，它们也是有成本的。

更好的替代方法是使用内联 SVG。我的意思是，只是 SVG 文件中的代码，而不是将它链接到图像标签中。通过使用内联 SVG，您可以随心所欲地用 CSS 对其进行样式化。检查下面的例子。

酷的是，你还可以用它来制作一些 CSS 动画。你不能用图标字体做到这一点。😊

# 4.对图像使用延迟加载

之前我说过，你不应该加载不使用或隐藏的东西。这对于图像来说是至关重要的。现在比以前更容易修理。

只需将`loading="lazy"`添加到您的图像标签中，如下所示。

```
<img src="image.jpg" loading="lazy" alt="Landscape The Netherlands" width="480" height="640px"  />
```

大多数现代浏览器都支持它。检查[can use](https://caniuse.com/loading-lazy-attr)是否符合你的浏览器要求。如果你想了解更多，你可以在你的浏览器中阅读[天生懒惰加载图像](/native-lazy-loading-in-the-browser-85dabe6653ed)。

如果你需要支持一些还不支持这个特性的浏览器，并且你对用一点 JavaScript 实现相同的功能感兴趣，那么你可以用[交叉点观察器](/lazy-loading-images-with-the-intersection-observer-e8ad57a1682c)来实现。它将帮助你用一点 JavaScript 实现同样的功能。

# 5.最小化 CSS 和 JavaScript 文件

由于我们在浏览器中加载了如此多的外部文件，我们必须尽可能地保持它们的小。

大多数 JavaScript 框架已经内置了优化特性。缩小、树抖动等等都是他们用来尽可能优化 CSS 和 JavaScript 文件的方法。

但是，如果您不使用任何特定的 JavaScript 框架，您可以自己很快完成。我强烈推荐两个工具让你的生活变得更简单。

[ViteJS](https://vitejs.dev/) (由 VueJS [的创造者尤雨溪](https://medium.com/u/4f198f5f1f12?source=post_page-----e8f0e13a53b--------------------------------)构建)和 [ParcelJS](https://parceljs.org/) 是超级有用的工具，可以帮助你使你的 CSS 和 JavaScript 变得更小以用于生产。它们都支持一些 CSS 预处理程序，所以不用担心。如果你用打字稿，他们会帮你搞定的。

查看这两个工具的入门页面，看看有多简单。

*   [vite js 入门](https://vitejs.dev/guide/)
*   【ParcelJS 入门

我是 ViteJS 的忠实粉丝，因为这是一种现代的方法，而且速度超级快。

# 6.加载您需要的内容

这更多的是一种心态，而不是指导方针。

仅在需要时加载外部资源。并且只在使用它的页面、视图或对话框中加载它。

不要浪费宝贵的互联网捆绑和用户设备的处理器能力。

# 7.格式化图像

有时你领导一个网站，他们在顶部有横幅。但是他们忘了预先格式化图像。几 MB 的图像会导致加载时间延长，这不是你想让你的用户看到的；-).

格式化你的图片是非常重要的！

使用正确的高度、宽度和文件扩展名。小心 png 文件。它们可以是高质量的，但是占用大量的 MB。

*   **JPG** :用这个拍照
*   **PNG** :用于下载高质量照片和透明图像。小心点。这伴随着昂贵的数据。
*   GIF :用它来显示颜色不丰富的图片或图标
*   **WebP** :这是一种更新的图像类型。它可以保存非常高质量的照片，而没有 png 文件的缺点。

但要做到这一点，最简单的方法是在 [Cloudinary](https://cloudinary.com/) 创建一个免费账户，你就可以动态地格式化你所有的图片。

例如，我博客中的图片是用 Cloudinary 格式化的，链接如下:

```
[https://res.cloudinary.com/{accountName}/image/upload/c_scale,g_center,w_300,f_auto/{imageUrl}](https://res.cloudinary.com/{accountName}/image/upload/c_scale,g_center,w_300,f_auto/{imageUrl})
```

Cloudinary 提供了[很棒的文档](https://cloudinary.com/documentation/image_transformations)，告诉你如何用他们的服务格式化你的图像和视频。

# 结论

正如你所看到的，创建一个快速加载的网站需要遵循一些原则。这些是我个人的最爱。我很清楚，除了我的七条指导原则之外，还有很多东西，所以如果你有一些你自己使用的工具，请在评论中留下来，与世界分享。

***快乐编码！🚀***

# 从我这里读更多

[](/how-promises-actually-work-in-javascript-1c80b1af7193) [## JavaScript 中承诺的实际工作方式

### 了解何时以及如何使用它们

better 编程. pub](/how-promises-actually-work-in-javascript-1c80b1af7193) [](https://devbyrayray.medium.com/css-variable-with-styled-components-7e91d89f13f3) [## 带有样式化组件的 CSS 变量

### 在 Next.js/React.js 轻松使用它们

devbyrayray.medium.com](https://devbyrayray.medium.com/css-variable-with-styled-components-7e91d89f13f3) [](/how-promises-actually-work-in-javascript-1c80b1af7193) [## JavaScript 中承诺的实际工作方式

### 了解何时以及如何使用它们

better 编程. pub](/how-promises-actually-work-in-javascript-1c80b1af7193) [](https://javascript.plainenglish.io/cant-access-camera-or-microphone-in-webrtc-apps-solve-using-navigator-mediadevices-web-api-c0ffe5cbd32c) [## 无法在 WebRTC 应用程序中访问摄像头或麦克风？使用“navigator . media devices”Web API 求解

### 如何用 navigator.mediaDevices Web API 解决这个问题

javascript.plainenglish.io](https://javascript.plainenglish.io/cant-access-camera-or-microphone-in-webrtc-apps-solve-using-navigator-mediadevices-web-api-c0ffe5cbd32c)