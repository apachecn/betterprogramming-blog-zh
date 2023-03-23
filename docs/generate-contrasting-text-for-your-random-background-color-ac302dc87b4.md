# 为您的随机背景颜色生成对比文本

> 原文：<https://betterprogramming.pub/generate-contrasting-text-for-your-random-background-color-ac302dc87b4>

## 如何在 JavaScript 中为你的随机背景颜色自动生成对比文本

![](img/2a9494a0bac93d305357d941097fdacb.png)

照片由[戴维斯科](https://unsplash.com/@codytdavis?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

我目前正在开发一个新的应用程序，以显著提高我的 [React Redux](https://react-redux.js.org) 技能。在这个新项目中，用户将能够将他们选择的颜色分配给一些实体的背景。这就是为什么我必须找到一个显示对比文本的解决方案。

# 信用

这个周末我正在寻找解决这个问题的方法，突然想起[离子颜色发生器](https://ionicframework.com/docs/theming/colors)已经解决了这个问题。我看了他们的[代码](https://github.com/ionic-team/ionic-docs/blob/master/src/components/color-gen/color.ts)(你一定会喜欢开源❤️)并基于他们的实现提出了解决方案。

我还必须实现一个将十六进制转换成 RGB 颜色的函数。为此，我在由 [xameeramir](https://stackoverflow.com/users/2404470/xameeramir) 提供的[堆栈溢出](https://stackoverflow.com/a/5624139/5404186)上找到了一个干净的正则表达式。

# 生成对比文本

为了生成对比文本，我们使用了由[万维网联盟(W3C)](https://www.w3.org/WAI/ER/WD-AERT/#color-contrast) 定义的以下公式，以确保当有色彩缺陷的人或在黑白屏幕上观看时，前景和背景颜色组合能够提供足够的对比度:

```
((Red value X 299) + (Green value X 587) + (Blue value X 114)) / 1000
```

上述算法采用从 RGB 公式转换的 YIQ 颜色方案作为输入，并输出颜色的感知亮度。

因为我使用的是十六进制颜色，所以在能够计算亮度之前，实现需要两次转换。它首先需要将输入转换为 RGB，然后转换为 YIQ 颜色。

最后，在阈值的帮助下，它可以确定对比度应该是暗还是亮，并作为结果提供对比文本颜色，黑色或白色。

```
interface RGB {
    b: number;
    g: number;
    r: number;
}function rgbToYIQ({ r, g, b }: RGB): number {
    return ((r * 299) + (g * 587) + (b * 114)) / 1000;
}function hexToRgb(hex: string): RGB | undefined {
    if (!hex || hex === undefined || hex === '') {
        return undefined;
    }

    const result: RegExpExecArray | null =
          /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);

    return result ? {
        r: parseInt(result[1], 16),
        g: parseInt(result[2], 16),
        b: parseInt(result[3], 16)
    } : undefined;
}export function contrast(colorHex: string | undefined,
                         threshold: number = 128): string {
    if (colorHex === undefined) {
        return '#000';
    }

    const rgb: RGB | undefined = hexToRgb(colorHex);

    if (rgb === undefined) {
        return '#000';
    }

    return rgbToYIQ(rgb) >= threshold ? '#000' : '#fff';
}
```

# 用普通 Javascript 演示

让我们用普通的 Javascript 试试上面的解决方案。

![](img/00c6f3c23d6e9f11eab8b2fb50ea6428.png)

为所选颜色自动生成对比文本

在 HTML 页面中，我们添加一个颜色选择器来选择一个动态值。为此，我们使用为 [DeckDeckGo](https://deckdeckgo.com) 开发的组件，这是我们用于演示的 web 开源编辑器。我们从 [unpkg](https://unpkg.com) 加载组件；这就是为什么不需要在本地安装依赖项的原因。

```
<!DOCTYPE html>
<html dir="ltr" lang="en">
<head>
  <meta charset="utf-8">
  <title>Contrast color</title>

  <script type="module" src="https://unpkg.com/@deckdeckgo/color@latest/dist/deckdeckgo-color/deckdeckgo-color.esm.js"></script>
  <script nomodule=""
src="https://unpkg.com/@deckdeckgo/color@latest/dist/deckdeckgo-color/deckdeckgo-color.js"></script>
</head><body style="background: #F78DA7;">
  <h1 style="font-size: 40px;">Hello World</h1> <deckgo-color></deckgo-color> <!-- Here we will add the contrast function --> <!-- Here we will bind the event and modify the colors --></body>
```

然后我们添加上面的函数来生成对比文本。注意，我们只是删除了 Typescript 部分，只解析 JavaScript 代码。

```
<script>
  function rgbToYIQ({r, g, b}) {
    return ((r * 299) + (g * 587) + (b * 114)) / 1000;
  }

  function hexToRgb(hex) {
    if (!hex || hex === undefined || hex === '') {
      return undefined;
    }

    const result =
          /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);

    return result ? {
      r: parseInt(result[1], 16),
      g: parseInt(result[2], 16),
      b: parseInt(result[3], 16)
    } : undefined;
  }

  function contrast(colorHex, threshold = 128) {
    if (colorHex === undefined) {
      return '#000';
    }

    const rgb = hexToRgb(colorHex);

    if (rgb === undefined) {
      return '#000';
    }

    return rgbToYIQ(rgb) >= threshold ? '#000' : '#fff';
  }
</script>
```

最后，我们将一个事件绑定到颜色选择器，以侦听所选的颜色，我们将该颜色应用于页面背景，并使用它来生成一个对比度值，最终将该值设置为页面文本的颜色。

```
<script>
  document.querySelector('deckgo-color')
          .addEventListener('colorChange', updateColor, true);

  function updateColor($event) {
    document.querySelector('body').style.background = 
                                   $event.detail.hex;
    document.querySelector('body').style.color = 
                                   contrast($event.detail.hex);
  }
</script>
```

# 蛋糕上的樱桃🍒🎂

我们的 web 组件是开源的。它是一个 [StencilJS](https://stenciljs.com) web 组件，因此可以用在普通的 Javascript 或任何现代框架中。也比较轻便；根据 [Bundlephobia](https://bundlephobia.com/result?p=@deckdeckgo/color@1.0.0-rc.3-2) 的说法，它只给任何包增加 418 字节(一旦缩小和压缩)。如果您想在您的应用程序中尝试一下，请查看它的[文档](https://docs.deckdeckgo.com/components/color)并开始尝试吧！

到无限和更远的地方！🚀