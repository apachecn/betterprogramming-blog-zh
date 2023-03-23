# 在 Android 中将数据导出为 PDF

> 原文：<https://betterprogramming.pub/exporting-data-as-pdf-in-android-ca6522e77b9b>

## 如何使用 iText Android 库

![](img/cfb0020b6d3366ad3157b790c00a9572.png)

照片由 [CURVD](https://unsplash.com/@curvd?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

“我需要一份 PDF 格式的列表”——这是许多 Android 用户的普遍需求。

在特殊情况下，用户可能希望将他们的数据打印成 PDF 文件。付款凭证、交易日志、历史报告等数据。这是一个简单的需求。不幸的是，许多 android 应用程序让这一功能远离了他们的用户。

应用程序中没有 PDF 导出功能有一些原因。其中之一是其实现的复杂性——这是一个假设。拥有一个让用户将数据导出到 PDF 文件的功能并不像解决算术问题那么复杂。让我们看看如何在一个 Android 应用程序中实现它。

# **要求**

开发人员易于实现的 PDF 库之一是`iText`库。要开始使用这个库，我们需要在我们的应用程序 Gradle 中实现 iText 依赖项。

```
implementation ("com.itextpdf:itextpdf:5.5.13.1")
```

一旦它被同步到项目中，我们就可以进入下一步，设置我们的清单。这是为了让应用程序访问手机存储，因为我们要创建一个 PDF 文件，当然，保存在手机存储。

将此代码添加到清单中。

```
<manifest
...
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
```

别忘了支持 Android 11 及更高版本的遗留请求。

```
android:requestLegacyExternalStorage="true"
```

我们现在已经完成了项目中的`iText`库。容易吗？等等，这甚至不是我们 PDF 导出方法的开始。

# 导出 PDF 服务配置

首先，让我们创建一个 PDF 服务类，这样我们的 UI 类就不会受到 PDF 配置的影响。在这个类中，我们可以直接定义我们的字体样式。

PDF 服务类别介绍

我们只需要通过调用`Font`来设置字体样式。这就是我们如何设置下一个配置中要使用的字体样式。

我们还准备了我们的 pdf writer，即`pdf`，稍后我们将初始化它。现在让我们为我们的 PDF 文档准备文件。

PDF 文件和文档初始化

`path`是将 PDF 文件导出到的手机存储目录。现在我们来看看`file`的配置。`if (!file.exists()) file.createNewFile()`行的功能是创建我们的 PDF 文件。

现在我们已经准备好了存储目录和文件。但是，我们创建的文件仍然是一个空白文件。这一步跟它没关系。

现在让我们更深入地研究文档初始化。要制作一个 PDF 文件，我们需要初始化`iText`文档。这个初始化的变量将作为我们写数据的纸。`document = Document()`是文档初始化。

`document.setMargins`和`document.setPageSize`是我们设置边距和页面布局的方式。我们现在有一份空白的文件。

文档创建并没有就此结束。由于我们要制作一个 PDF 文件，我们必须通过`pdf = PdfWriter.getInstance(document,FileOutputStream(file))`将文件设置为 PDF 文件，为了减少文件大小的内存消耗，我们也通过调用`pdf.setFullCompression()`来压缩文件。

简单。让我们来看看将数据写入文档的基本概念。

当然，要开始处理文档，我们需要打开文档。上面的代码通过调用`document.open()`直接打开了我们的文档。

# 基本的文档写作技巧

编写文档实际上类似于在 Microsoft Office 中直接键入 Doc 文件。它的不同之处在于我们没有光标，我们需要通过编程来控制它。

换句话说，我们需要确保添加到文档中的元素位于正确的位置。以下是关于如何以编程方式将数据写入文档的基本提示。

*   要添加空行，我们可以添加一个带有空格字符串的段落。

```
document.add(Paragraph(" "))
```

*   要写一段话，我们只需要在`Paragraph("YOUR_PARAGRAPH")`上方的段落括号里面写一段话的内容就可以了。然而，为了避免重复创建相同样式的段落，我们可以在一个函数中创建一个段落，以便在每次需要时调用。

```
private fun createParagraph(content: String): Paragraph{
        val paragraph = Paragraph(content, BODY_FONT)
        paragraph.firstLineIndent = 25f
        paragraph.alignment = Element.ALIGN_JUSTIFIED
        return paragraph
    }
```

*   然后，我们可以通过调用要添加到文档中的`createParagraph`函数来轻松创建段落。下面是使用方法。

```
document.add(createParagraph(“PARAGRAPH_CONTENT”))
```

*   要添加新页面:

```
document.newPage()
```

*   要制作表格，我们需要定义一个 PDF 表格。以下代码显示了如何制作表格:

```
private fun createTable(column: Int, columnWidth: FloatArray): PdfPTable {
        val table = PdfPTable(column)
        table.widthPercentage = 100f
        table.setWidths(columnWidth)
        table.headerRows = 1
        table.defaultCell.verticalAlignment = Element.ALIGN_CENTER
        table.defaultCell.horizontalAlignment = Element.ALIGN_CENTER
        return table
    }
```

在上面的代码中，我们有一个名为`column:Int`的参数，它是列数。此外，我们还有一个`columnWidth`是一个 float 数组。这个浮点数组是创建的表中每一列的列表。容易吗？是的，一切都是可以理解的。

在一个表中，必须有行和列。其中的任何特定区域都称为一个单元。但是，这里没有定义行。它将由我们填充的单元格自动生成。现在让我们看看下一步的更多内容。

*   以下代码显示了如何在单元格内书写:

```
private fun createCell(content: String): PdfPCell {
        val cell = PdfPCell(Phrase(content))
        cell.horizontalAlignment = Element.ALIGN_CENTER
        cell.verticalAlignment = Element.ALIGN_MIDDLE
        //setup padding
        cell.setPadding(8f)
        cell.isUseAscender = true
        cell.paddingLeft = 4f
        cell.paddingRight = 4f
        cell.paddingTop = 8f
        cell.paddingBottom = 8f
        return cell
    }
```

上面代码的要点是，对于我们想要在任何特定单元格中写入的任何内容，我们需要定义一个 PDF 单元格。PDF 单元格作为我们的光标位置播放。为了避免重复定义单元格，上面的函数要求单元格内容直接将内容放入单元格中。

> 注意:如果一行中的所有列都已被单元格填充，当我们添加另一个单元格时，表格将自动生成新的一行。

# 样本案例

我们将有一篇由两段组成的论文，我们的用户数据有四个变量(id、名字、中间名、姓氏)。

用户数据类及其列表如下:

```
data class User(
   val id: Int,
   val firstName: String,
   val middleName: String,
   val lastName: String
)val userData = listOf(
        User(1, "Amir", "Tan", "Khan"),
        User(2, "Michael", "Calvin", "Gonzalez"),
        User(3, "Alim", "Bin", "Hasan"),
        User(4, "John", "Carl", "Tean"),
        User(5, "James", "Brown", "Cold"),
        User(6, "Virats", "Kader", "Can"),
        User(7, "Lim", "Lui", "Pao"),
        User(8, "Endro", "Tava", "Pero"),
        User(9, "Dani", "Pedro", "Leo"),
        User(10, "Leonardo", "Chris", "Luiza")
    )
```

现在让我们创建一个函数来完成这些任务。

创建 PDF 函数示例

完成后，我们已经配置了将用户数据导出到 PDF 所需的所有功能。现在让我们从活动、片段或可组合上下文中调用这个函数。

下面的示例显示了如何从 activity 调用函数:

在活动中调用创建 PDF 函数

一切就绪。我们已经在 UI 类中创建了 PDF 导出函数及其结果响应。然而，在不同的情况下，我们的反应会根据我们的需要而有所不同。

在这个示例中，我们让应用程序在 PDF 创建后直接打开文件。下面是结果预览。

![](img/b55ec29c9871b9d86d70772929092fae.png)

结果预览

> 注意:当打开我们创建的文件时，我们需要访问文件的真实路径。上面代码中使用的`FileHandler`不是来自`java.util.logging`类。参考[此处](https://github.com/annasta13/Pdf-Export/blob/master/app/src/main/java/com/habileducation/pdfexport/pdfService/FileHandler.kt)获取源代码。另一个重要注意事项是向用户请求读取文件权限。点击查看更多[活动的完整源代码。](https://github.com/annasta13/Pdf-Export/blob/3e29181c157d300a8be42c744c3872df0d177cb8/app/src/main/java/com/habileducation/pdfexport/MainActivity.kt)

下面是项目示例代码:

[](https://github.com/annasta13/Pdf-Export) [## GitHub-annasta 13/Pdf-Export:Pdf 导出的示例项目

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/annasta13/Pdf-Export) 

# 结论

将数据导出到 PDF 是一个很酷的特性，只要我们知道 iText 文档如何添加元素的基本概念，这个特性实际上很容易实现。

总之，我们可以简单地利用这个库在我们的应用程序中提供一个 PDF 导出特性，而不必请求我们的后端服务器这样做。

希望这是值得学习的东西。编码快乐，干杯。