# 在 Swift 中使用 PDFKit 动态生成 pdf

> 原文：<https://betterprogramming.pub/swift-generating-pdfs-dynamically-using-pdfkit-12c37168e106>

## 使用公共 API 构建鸡尾酒菜单 PDF

![](img/3e5add4bdc498108008cadac89b26f97.png)

照片由[伊戈尔·斯捷潘诺夫](https://unsplash.com/@istepanoff?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

在开发 Swift 应用程序时，动态生成 PDF 报告非常有用。您的用户将能够在旅途中创建他们自己的个性化报告，并且能够非常轻松地导出这些报告。

# 目标

*   生成 PDF 文档
*   以有组织的方式展示您的数据
*   设置文本格式(字体、大小、下划线……)
*   需要时，转到新的一页
*   分享您的 PDF

![](img/a2259e9edaca4ab1eca10c23f5bbbf96.png)

# 概观

在本教程中，我们将使用公共的`[thecocktaildb](https://www.thecocktaildb.com/api.php)` API 生成一份鸡尾酒菜单。

在我们的浏览器上使用空搜索字符串“[https://www.thecocktaildb.com/api/json/v1/1/search.php?s=](https://www.thecocktaildb.com/api/json/v1/1/search.php?s=)”测试了他们的搜索 API 后，我们可以看到鸡尾酒及其成分的列表，以及一些说明和附加数据。

这个 API 还允许我们根据饮料进行过滤。

例如，使用网址"[https://www.thecocktaildb.com/api/json/v1/1/search.php?s=margarita](https://www.thecocktaildb.com/api/json/v1/1/search.php?s=margarita) “会给我们看所有的玛格丽塔酒。

我们将开发一个应用程序，生成一个 PDF 文件，向我们显示由用户输入的字符串过滤的所有鸡尾酒列表。

# 创建应用程序

首先，让我们通过点击 XCode 创建一个新的单视图应用程序，并选择 create a new XCode project。

![](img/f8ea47c695b3ac9b68b7b3813e3347a1.png)

之后，我们来选择 App。

![](img/89ffbc2086082c282b4781b3e50cc904.png)

我把这个应用程序叫做玛格丽塔 PDFKit。选择 Storyboard 和 Swift，点击 Next，然后点击 Finish。

# 获取数据

获取和解析数据超出了本教程的范围，所以让我们快速浏览一下。

创建一个新的 Swift 文件，将其命名为 NetworkManager.swift

![](img/174fd2690bd2e46fd0ada6c6c75c9ed7.png)![](img/0c19e4416471993cf03117883bcf326a.png)

…并键入以下内容:

**“错误:在范围内找不到菜单”**，我们马上添加！

通过右键单击“Margaritas pdf kit”**组**创建一个名为`Cocktail.swift`的新 Swift 文件，并单击**新文件:**

![](img/d32842ffe76c9d5193ff281ad482ea68.png)![](img/4e99cd30d3c58903b2705222d7b3172a.png)

调用文件`Cocktail.swift`并点击创建。

现在将以下内容粘贴到文件中:

这个类将把鸡尾酒 API 的响应解析成这些类。

通过使用 Codable，JSON 响应将被自动映射到这里的对象中。

# 用户界面

首先，让我们跳到`main.storyboard`并从库中添加几个对象，约束如下:

**一个 UITextField**

![](img/3307d9d72c1d4ed15a43183630b1c9d7.png)![](img/07257a93f9e27d0659b91f4ca3465490.png)

**一个 UILabel**

![](img/92d17f6af5d67b30cdbf56dda89c57fb.png)![](img/8e460316b761bf3b19685e25f18b661f.png)

**和一个 UIButton**

![](img/2d517992fd57e6a0ebe8a31af9dc89da.png)

现在通过: **ctrl + click** 从按钮到 ViewController.swift 文件创建一个动作，并调用 generatePDF 方法。

![](img/846bde12f3680be3f80f3ea16118c396.png)

同样，做同样的事情，创建一个从 TextField 到 Siwft 文件的出口，并调用出口`queryField`。

# 表示文本

让我们从定义能够为文本分配属性并在我们的上下文中呈现该文本的函数开始

首先，确保在 ViewController.swift 文件的顶部添加:

```
**import** PDFKit
```

然后在文件底部添加:

这些方法被添加为对`UIGraphicsPDFRendererContext`的扩展，这是将要呈现我们的 PDF 的上下文。

我们所要做的就是在上下文中指定我们需要的所有东西(PDF 元数据、PDF 尺寸、分页符，当然还有我们要在文档中呈现的文本)。

在函数 1、2 和 3 的实现中，属性被分配给属性化字符串变量中的文本。然后，我们使用 indent 和 Page Size 参数将我们的字符串放在由函数定义描述的`CGRect`中(居中、单行左对齐和多行左对齐)。这样做之后，我们使用函数 4 ( `checkContext()`)来检查我们的文本是否超出了页面的边界，如果是，就动态地开始一个新的页面并重置光标。

最后，函数 5 计算多行句子的高度，这样我们就知道文本在哪里结束，下一段从哪里开始。

# 配置上下文

下一步，我们将实现一个函数，该函数提取所有相关信息，并使用前面的扩展将其添加到上下文中。

执行功能`generateDrinkText()`和`addDrinksIngredients()`:

在函数`generateDrinkText()`中，传递了上下文，以及饮料。我们还传递光标来知道在 y 轴上从哪里开始写。这个游标值也在函数结束时返回，用于下一杯饮料。

另一个功能`addDrinkIngredients()`有助于检查配料并展示它们，因为并非所有鸡尾酒都有所有配料。

# 生成 PDF

现在，我们可以通过创建一个函数来完成生成过程，该函数接受一个鸡尾酒数组，并返回稍后将被转换为 PDF 文档的数据。

转到`ViewController.swift`，在名为`generatePDFData()`的新函数中添加以下内容

现在我们有了一个函数，它通过配置上下文来创建我们需要的 PDF，我们剩下要做的就是从我们的 API 获取鸡尾酒，并将它们传递给这个函数。

我们的 IBAction 现在应该如下所示:

# 从 PDF 数据显示 PDF 文档

转到`main.storyboard`，添加一个新的 ViewController 文件。

![](img/db70719811f6b7fcf1cad05f6a48f897.png)![](img/a7aca22e6e6553da54edb19b1dd748db.png)![](img/2099f84f033bf947774d7067903f0e72.png)

将新的视图控制器文件分配给新的视图控制器场景，如下所示。

![](img/1f82e4078e5966c1b3228f8d6fbfb7da.png)

现在使用 **ctrl + click 从初始视图控制器添加一个片段到新场景。**

![](img/c8a5a2a4b9f738a4e8f679c0fc333bae.png)

选择 Present Modally，然后单击 segue 并设置标识符，如下所示:

![](img/d19e4d4ac9c41dfc0813cf949d230ec9.png)

使用以下坐标添加工具栏和 UIVIew:

![](img/fb8d3e9a179772c22061562ad7a0c109.png)![](img/636949fa048ac112231c2a52adcf0a4c.png)![](img/cb599009b7404338a60c591f66cd1038.png)![](img/233a95b87532208e686d4976eb13b68d.png)![](img/032ce67e3156692c3d14a819aa2b26a3.png)

**别忘了把 UIView 类设置成“PDFView”！**

![](img/52b898b3de1cf96d8b2ed7014d7d96f8.png)

完成所有这些后，将以下内容粘贴到您的新`PdfPreviewVC`中:

将`shareButton`链接到工具栏按钮，将`pdfView`链接到我们添加的主界面视图，当然还有工具栏按钮的分享功能。

注意:如果 UIView 的链接不起作用，确保从故事板助手编辑器侧边栏将该类设置为 PDFView。

最后，返回到`ViewController.swift`并更新文件，使其看起来像这样:

# 测试一切

运行应用程序，并填写“玛格丽塔”或任何想到的东西，PDF 应该会动态生成。

尝试避免空格或非法 URL 字符。

![](img/90053804194c1a255cc08003bc183cba.png)![](img/0116ab2fe162312aa6adbe2d6c1be022.png)

我们现在有一个可以从动态数据中生成的饮料菜单，页面也可以动态缩放，多行文本的说明正在快速工作(双关语)。

干杯。

# 源代码:

你可以在这里找到最终代码:[https://github.com/Marcdaou/PDFKit](https://github.com/Marcdaou/PDFKit)