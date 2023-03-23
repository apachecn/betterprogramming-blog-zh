# 如何在 React Native 中创建一个漂亮的动画加载器

> 原文：<https://betterprogramming.pub/how-to-create-a-beautifully-animated-loader-in-react-native-21da37a8f6b0>

## 使用 Airbnb 的 [Lottie](https://airbnb.design/lottie/) 库来激活你的装载机

![](img/4de20528ab0ff29ee19ec91d9e1e6e9f.png)

装载机的洛蒂动画

Web 或 Mobile 中的加载器是一个基本的设计元素，通常在我们需要执行一些异步任务(如数据处理或获取)时使用。这些任务可能需要一些时间，在此期间，用户必须娱乐，所以这就是加载器派上用场的地方。

加载器有助于开发人员在用户等待时吸引他们，并避免应用程序缺乏响应。

> 不想等了？检查 npm 包[React-Native-Animated-Loader](https://github.com/vikrantnegi/react-native-animated-loader)。

# 入门指南

React Native 内置了一个`[ActivityIndicator](https://facebook.github.io/react-native/docs/activityindicator)`，可以用作加载指示器。

但是对于`Loaders`,我们不能只使用`ActivityIndicator`,因为我们想防止用户在任务完成之前执行任何动作。为此，我们将使用`[Modals](https://facebook.github.io/react-native/docs/modal#docsNav)`。

如果你只是想要一个简单的装载机，那么看看这个教程，但是如果你想在你的装载机中加入一些神奇的东西，继续学习这个教程。

# Airbnb 的洛蒂

[Lottie](https://airbnb.design/lottie/) 是一个 iOS、Android 和 React 原生库，可以实时渲染 After Effects 动画，允许应用程序像使用静态图像一样轻松地使用动画。

我们将使用它的 React 本机包装库 [lottie-react-native](https://github.com/react-native-community/lottie-react-native) 来制作我们的自定义加载器动画。

# 创建应用程序

我们将使用`react-native-cli`来创建一个 React 本地项目，但是你也可以使用 Expo。

使用以下命令创建一个示例项目:

```
~ react-native init LoaderExample
```

# 安装依赖项

现在让我们添加必要的包。先安装`react-native-animated-loader`和`lottie-react-native`。

```
~ npm install react-native-animated-loader --save~ npm i --save lottie-react-native
```

> 如果你使用 Expo，你不需要安装 Lottie。

因为`lottie-react-native`需要本地链接，所以运行以下命令:

```
~ react-native link lottie-ios~ react-native link lottie-react-native
```

之后，打开 Xcode 项目配置，将`Lottie.framework`添加为`Embedded Binaries`。

> 如果您在链接 Lottie 后遇到任何错误，请遵循此处的详细安装说明。

# 让我们加入魔法

现在用下面的代码更新您的`App.js`:

单击时，您应该会在几秒钟内看到以下动画。

![](img/52d0d97a12eeb4a5b24658d5d42e321e.png)

# 自定义动画

您看到的动画是默认的，但您可以添加自己的洛蒂动画。如果你想看一些很酷的装载机动画，去 [lottiefiles](https://lottiefiles.com/) ，在那里你会找到预建的装载机动画。选择你喜欢的，下载它的 JSON 文件就可以了。

现在将下载的 JSON 文件添加到`LoaderExample`项目中，并将源道具添加到`AnimatedLoader`中。添加源后，它应该看起来像这样:

```
<AnimatedLoader
  *visible*={visible}
  *overlayColor*="rgba(255,255,255,0.75)"
  *animationStyle*={styles.lottie}
  *speed*={1}
  source={require("./path-of-your-json-file.json")} // Add here
/>
```

您还可以通过添加`animationStyle`道具自定义加载器样式。

# 使用

在我们的例子中，我使用了`setTimeout`来模拟一个异步任务。在现实世界中，您可能会将它用于各种异步任务，比如从 API 获取数据。

# 结论

现在你知道如何制作一个很酷的动画加载器，我希望你不要再为你的加载器使用旧的，无聊的活动指示器。

> 在此找到库回购[。](https://github.com/vikrantnegi/react-native-animated-loader)

如果你喜欢这篇文章，请用你的掌声表达你的爱。