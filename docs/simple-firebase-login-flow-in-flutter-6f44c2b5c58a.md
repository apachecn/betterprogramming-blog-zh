# 在 Flutter 中的简单登录流程，然后是 Firebase

> 原文：<https://betterprogramming.pub/simple-firebase-login-flow-in-flutter-6f44c2b5c58a>

## 第一部分:实现不带 Firebase 的登录流，为不使用 Firebase 的人构建一个基线应用程序

![](img/3324d2fc3e74900f3a5b71ee791fb3c5.png)

在本文中，我们将使用以下组件创建一个简单的应用程序:

*   默认主应用入口点
*   使用 [FutureBuilder 小部件](https://api.flutter.dev/flutter/widgets/FutureBuilder-class.html)在呈现 UI 之前等待数据，这个概念在整个应用程序中使用
*   登录页面
*   主页
*   认证服务
*   演示在 Flutter 文档[简单应用状态管理](https://flutter.dev/docs/development/data-and-backend/state-mgmt/simple#accessing-the-state)中讨论的提供者的使用

网上有很多关于为 Flutter 设置 Firebase 的例子，所以我将直接跳到代码中，而不是从基础开始。

> 参见[Google CodeLabs Flutter for Firebase](https://codelabs.developers.google.com/codelabs/flutter-firebase/index.html?index=..%2F..index#5)获取在 iOS 或 Android 上设置项目的分步说明

# 在 Firebase 中创建一个测试用户

因为我们只是构建应用程序，现在还没有创建用户的功能，所以登录到 Firebase 控制台，向您的项目添加一个用户。在 Firebase 控制台中更新项目时，一定要启用电子邮件身份验证。

# 清理默认的颤振项目

首先，让我们创建项目:

```
flutter create simple_firebase_auth
```

现在让我们做一些项目清理，打开项目并从文件`main.dart`中删除现有的`HomePage`和`HomePageState`小部件。

更改`MaterialApp`小部件的`home`属性，指向我们将在下一节创建的`LoginPage`小部件。

完成后，该文件应类似于以下内容:

# 创建登录页面小部件

让我们浏览一下应用程序的`LoginPage`的创建过程。我们需要捕获一个`email`和一个`password`来传递给`AuthService`以调用登录函数。

我们将创建一个简单的页面，其中包含所需的`TextFormField`小部件和一个`RaisedButton`小部件，单击后用户可以登录。

1.  打开编辑器，在`lib`目录下创建一个名为`login_page.dart`的新文件
2.  将以下内容粘贴到文件`login_page.dart`

您应该能够运行代码来查看屏幕现在的样子。当我们在 UI 中工作时，请确保将`main.dart`小部件中的默认路线或`home`属性更改为`LoginPage`，这样您就可以看到实时更新的更改。

# 样式和添加文本字段

让我们将页面主体设为居中的`Column`，列的子列主要是`TextFormField`和`RaisedButton`。

放置表单域和按钮的居中容器:

接下来，添加实际的表单域小部件和按钮作为`Column`小部件的子部件。我们将对表单字段进行一些基本的样式设计，使其看起来更像样。有关[文本表单域](https://api.flutter.dev/flutter/material/TextFormField-class.html)的更多信息，请参见 Flutter 文档。

让我们在列中的字段之间增加一些间距，这样更好看。我们将使用`SizedBox`小部件并设置`height`属性以在应用程序中获得一些间距。替换`Column`小部件的`children`属性以获得所需的间距。

# 从表单域获取文本值

我们将使用一个`Form`小部件和一个`GlobalKey`，关于这些概念的更多信息可以在 flutter cookbook 章节[构建一个带验证的表单](https://flutter.dev/docs/cookbook/forms/validation)中找到。

在`LoginPage`小部件中添加 formKey:

然后添加两个新字段来保存我们需要发送到 Firebase 进行身份验证的电子邮件地址和密码值:

接下来，将属性`onSaved`添加到我们拥有的用于电子邮件和密码的`TextFormFields`中。当在表单上调用`save`方法时，保存方法上的所有小部件都将被调用来更新局部变量。

用一个新的`Form`小部件包装`Column`小部件，代码应该如下所示:

既然已经设置了字段并且更新了`TextFormField`，我们不仅可以使用`_formKey`来验证提供的字段，还可以通过调用`save`方法在本地检索值。

将`RaisedButton` `onPressed`方法中的代码替换为以下代码，您将看到我们正在获取在 out 小部件中设置的电子邮件和密码的值。我们现在可以将这些值传递给包装了 Firebase 登录功能的`AuthService`。

# 创建主页小部件

现在，我们将保持主页简单，因为我们的目标是演示流程是如何工作的。忽略注释掉的`LogoutButton`小部件，我们将在本教程的后面部分讨论。

1.  打开编辑器，在`lib`目录下创建一个名为`home_page.dart`的新文件
2.  将以下内容粘贴到文件`home_page.dart`

3.打开`main.dart`并添加以下导入语句

4.要验证页面是否正常工作，请更改下面的`home`属性:

对此:

# 为认证服务创建模板

在这里，我们将构建独立于 Firebase 的身份验证服务，验证一切正常，然后集成 Firebase。

在这个服务中，我们使用了一个名为`ChangeNotifier`的 mixin 和一个方法`notifyListeners`，它允许使用这个服务的小部件在方法被调用时被更新。当我们更新`currentUser`属性时，我们调用`notifyListeners`,因为这意味着用户已经登录或注销，我们希望应用程序根据用户的状态进行更新。

> 关于`Provider`和状态管理的更多信息可以在[颤振文档](%5Bhttps://flutter.dev/docs/development/data-and-backend/state-mgmt/simple%5D(https://flutter.dev/docs/development/data-and-backend/state-mgmt/simple))中找到

我们需要的基线如下:

我们将在服务调用`currentUser`中保留一个本地属性，它是当用户调用`login`方法时存储用户的对象，如果密码匹配，我们将设置`currentUser`，用户将登录。这将在调用`getUser`方法时向用户提供。为了注销用户，我们将把`currentUser` 属性设置为 null，表示我们不再登录系统。

# 应用程序启动时确定用户状态

使用应用程序的第一个挑战是确定应用程序启动时打开哪个页面。这里我们想做的是确定我们是否有一个用户。我们将使用上面创建的`AuthService`并结合 Flutter 的`FutureBuilder`小部件来呈现`HomePage`或`LoginPage`的正确首页。

# 使用提供程序

在`main.dart`中，我们需要更新默认的`main`方法，如下所示；我们用`ChangeNotifierProvider`包装整个应用程序，以获得扫描小部件树并找到类型为`AuthService`的对象的能力。

# 修改 MyApp 小部件

进入`main.dart`并进行以下更改，这将允许`MyApp`小部件设置路线。这个小部件将决定应用程序启动时是应该导航到`HomePage`小部件还是`LoginPage`小部件。

# 修改登录页面小部件

现在可以使用 Provider 访问`AuthService`,我们可以在 used 单击按钮时调用 login 函数。打开文件`login_page.dart`，找到登录按钮的`onPressed`方法，进行如下修改

我们使用`Provider.of`方法来查找小部件树并得到我们的`AuthService`，然后我们可以访问所有的方法，特别是`loginUser`方法。

# 结论

此时，您应该有一个具有基本登录流的正常工作的应用程序，如果您提供 password123 作为密码，用户将成功登录。

我添加了一点额外的功能，如果出现某种错误，就在对话框中显示错误消息。

> [**见本故事第二部分**](https://medium.com/better-programming/simple-firebase-login-flow-in-flutter-now-firebase-79ecfe283dcf)

# 资源

> 这里有源代码:[确保你在第一部分的分支上][https://github . com/aaronksaunders/simple _ firebase _ auth/tree/completed-part-one](https://github.com/aaronksaunders/simple_firebase_auth/tree/completed-part-one)