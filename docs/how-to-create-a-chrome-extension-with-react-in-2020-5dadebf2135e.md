# 如何用 React 创建一个 Chrome 扩展

> 原文：<https://betterprogramming.pub/how-to-create-a-chrome-extension-with-react-in-2020-5dadebf2135e>

## 使用 Create React App 快速启动 Chrome 扩展

![](img/f1970e9d4050269b1302379ac4b4e205.png)

图片由 [russellstreet](https://www.flickr.com/photos/russellstreet/) 在 [Flickr](https://www.flickr.com/photos/russellstreet/4668188279) 上提供

你可以对几乎所有的事情使用 React，对吗？嗯，对于 Chrome 扩展来说，可能比你想象的要复杂一些，因此有了这个指南。

Create React App 不能直接与 Chrome 扩展一起使用。原因是 React 使用了内嵌脚本，这违反了 Chrome 的安全准则。但不用担心——我们；我将实现一个变通办法。

# **1。创建 React 应用程序**

首先，我们需要创建我们的 React 应用程序。创建一个新的文件夹——你可以把它叫做类似于`cool-extension`的东西——并在 VS 代码中打开它(或者任何你喜欢使用的 IDE)。打开一个终端(确保您在您的文件夹中)，并输入以下命令:

`npx create-react-app`

这将在您的文件夹中设置一个新的 React 应用程序。

# 2.创建 manifest.json 文件

通常，当你创建一个 Chrome 扩展时，你需要创建一个`manifest.json`文件，但是我们很幸运！Create React App 默认创建一个`manifest.json`文件——你可以在你的公共文件夹里找到它。

删除该文件中的所有内容，并粘贴以下代码:

```
{ "short_name": "Your Extension Name", "name": "Your Extension Name", "icons": { "16": "favicon.ico", "48": "logo192.png", "128": "logo512.png" }, "permissions": [], "manifest_version": 2, "version": "0.0.1", "browser_action": { "default_popup": "index.html", "default_title": "Your Extension Name" }}
```

那么这个文件是做什么的呢？这个文件被 Chrome 用来获取你的扩展的主要信息:叫什么名字？我应该显示什么图标？我们需要用户的什么权限来运行这个扩展？诸如此类。

我会很快浏览一下`manifest.json`，但是如果你想深入了解，请看看[文档](https://developer.chrome.com/extensions/manifest)。

*   `**short_name**` **和** `**name**`应该是不言自明的——它们采用您的扩展的名称
*   这些图标会出现在你的 Chrome 标题栏上吗
*   `**permissions**`**——**这里变得更有趣了。你不能在另一个用户的机器上为所欲为——他们需要首先允许。因此，如果你想改变你的用户的 Chrome 存储，你需要把它放在权限数组中。它看起来像这样:`“permissions”: [“storage”]`。但是对于本教程，我们不需要它。
*   仅仅是你的发布版本——当你在 Chrome 商店更新你的应用时，你需要改变它
*   需要`**browser_action**` **、** `**default_popup**` **和** `**default_title**` 来指定您的扩展将做什么。`default_popup`指定您希望您的扩展呈现的页面(在我们的例子中，就是`index.html`)。`default_title`是将鼠标悬停在扩展图标上时显示的标题。

还有很多明显的属性，但是你必须自己去探索。目前，它们并不相关。

正常情况下，我们现在已经完事了。您的 React 扩展功能齐全，但是正如我前面提到的，我们需要解决内联脚本的使用问题。

# 3.创建一个。环境文件

在主文件夹(`cool-extension`)中，您现在需要创建一个名为`.env`的文件来保存您的环境变量。添加这行代码:

`INLINE_RUNTIME_CHUNK=false`

这将确保 Chrome 没有任何问题。

这是最后一步。我们现在可以启动我们的分机了。

# 4.构建您的扩展

在命令行中，您现在必须运行:

`npm run build`

这将在您的`cool-extension`文件夹中创建一个构建文件夹。

# 5.在 Chrome 中打开你的扩展

在你的 Chrome 搜索栏中输入:

`chrome://extensions/`

在右上角，打开开发人员模式。这将在左上角呈现两个按钮。加载未打包的扩展和打包的扩展。

单击“Load unpacked extension”，并选择您的构建文件夹。

您的扩展现在会显示在您的浏览器中，并且会在您每次使用`npm run build`时自动更新。

# 6.(可选):添加一些 CSS

我们的扩展成功了——恭喜！但是看起来有点小。为了改变这一点，我们需要添加一些 CSS。

转到您的`App.css`文件，并添加以下内容:

```
.App {   text-align: center;
  min-width: 300px;
  max-width: 300px;
  min-height: 300px;
  max-height: 300px;}
```

再次运行`npm run build`，等待它完成，然后打开你的扩展。

就是这样！感谢您阅读本指南。