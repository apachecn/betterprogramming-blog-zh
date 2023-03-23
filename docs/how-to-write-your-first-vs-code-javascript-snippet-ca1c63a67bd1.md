# 如何编写你的第一个 VS 代码 JavaScript 片段

> 原文：<https://betterprogramming.pub/how-to-write-your-first-vs-code-javascript-snippet-ca1c63a67bd1>

## 节省编写重复代码的时间

![](img/d5f1a464e496c8cd03205b4a1cf5d6aa.png)

Emile Perron 在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

您是否厌倦了在整个 Javascript 项目中为几乎每个组件编写相同的代码模板？您的 VS 代码扩展还没有这些代码片段吗？是时候编写自己的可重用自定义代码片段了！

# 为什么是代码片段？

代码片段是任何一小段代码。在我们的例子中，我们将代码片段作为模板来开始编码，通过使用预先编写的代码来代替从头开始编写代码行的麻烦，从而节省时间。

# 如何编写您的代码片段

例如，我们将为带有样式表的 React 功能组件编写一个简单的代码片段。

## 1.用普通的 JavaScript 语法编写模板代码

```
import React from ‘react’;
import { View, StyleSheet } from ‘react-native’;function newFunction(props) {
   return <Views style={{styles.container}} ></View>;
}export default newFunction;const styles = StyleSheet.create({
    container: {},
});
```

这是一个简单的模板，当我们使用 React native 上的样式表为任何功能组件编写代码时，都会用到它。

请注意，在这种情况下，我们必须使用单引号，而不是双引号。

## 2.选择代码段语言

你可以通过点击你的 VS 代码左下方的“设置齿轮”并点击*用户片段来实现。*然后，从下拉框中选择 *javascript* 。您将到达一个名为 *javascript.json.* 的文件

## 3.编写代码片段

```
{
    "React Native Function Component": {"prefix": "rnfc","body": ["import React from 'react';",
            "import { View, StyleSheet } from 'react-native';",        
            "",
            "function ${TM_FILENAME_BASE}(props) {",
                "return <View style={styles.container}>$0</View>;",
            "}",
            "",
            "export default  ${TM_FILENAME_BASE};",
            "",
            "const styles = StyleSheet.create({",
            "container: {},",
            "});",
            "",
     ]
   }
}
```

正如你在上面看到的，我们首先写一个键值对。

这个键可以是任何你想称之为代码片段的常量名。我把它命名为 React 本地函数组件。

这些值有两个属性:前缀和正文。

前缀**是在文本编辑器中激活代码片段的简写代码。确保它有意义。在本例中，我们将使用“RNFC”，它代表 React 本机功能组件。**

**主体**是一个数组，它将包含您之前用普通 javascript 语法编写的定制模板代码的所有行。将我们编写的自定义模板代码的所有行粘贴到 body 数组中。然后，突出显示每一行，并用双引号将它们引出来，后跟一个逗号。

请注意，对于空行，请使用中间没有空格的空双引号，并在其后跟一个逗号。

为了将组件名的名称作为文件名，我们使用***$ { TM _ FILENAME _ BASE }***。为了提高可访问性，将在视图组件之间包含一个 ***$0*** ，以便光标从该点开始。

保存更改！

## 4.测试您的代码片段

创建一个空的 JavaScript 文件，并以任何首选名称保存。现在输入前缀， *rnfc* ，然后按回车键！您应该最终得到一个完美的工作代码片段。

请注意，组件的名称与文件的名称相同，光标位于功能组件中的视图组件之间。

# 结论

这是一个简单的例子，展示了如何使用 Javascript 为 Visual Studio 代码编写一个简单的代码片段。您可以将这些代码片段替换为您喜欢的任何工作重复代码行。

关于如何创建 Visual Studio 代码片段的更多信息，请点击[这里](https://code.visualstudio.com/docs/editor/userdefinedsnippets)。

希望这篇文章对你有所教育，为你节省了一些时间。享受编码的乐趣吧！