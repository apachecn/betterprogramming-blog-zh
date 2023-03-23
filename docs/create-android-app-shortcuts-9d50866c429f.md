# 创建 Android 应用快捷方式

> 原文：<https://betterprogramming.pub/create-android-app-shortcuts-9d50866c429f>

## 了解静态、动态和锁定快捷方式

![](img/07687f106708717affc1d2652a2dfe76.png)

普通技术人员在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由[拍摄的照片。](https://unsplash.com/@the_average_tech_guy?utm_source=medium&utm_medium=referral)

在 Android 生态系统中，快捷方式是一种快速向用户提供特定功能或特性的方式。每个快捷方式可以引用多个目的。应用程序的快捷方式类型通常取决于应用程序的核心用例。

例如，如果您使用 Gmail 应用程序，“撰写电子邮件”作为静态快捷方式之一是有意义的。如果你使用 Chrome 应用程序，“导航到最常访问的网站”可以被认为是一个理想的动态快捷方式。

# 快捷方式类型

Android 中的快捷方式主要分为三种:静态、动态和钉住快捷方式。让我们了解一下每一种类型。

## 静态快捷方式

这些类型的快捷方式在应用程序的整个生命周期中为用户提供一致的行为，并且它们不是上下文相关的。正如上一节所讨论的，“撰写电子邮件”是静态快捷方式的最好例子。

## 动态快捷方式

动态快捷方式用于执行上下文相关的操作。这些类型的快捷方式可以在运行时基于用户行为动态创建。动态快捷方式确保用户可以快速访问应用程序中他们喜欢的功能。

## 锁定快捷方式

锁定快捷方式用于执行特定的用户驱动的操作。这些快捷方式将确保用户只需在主屏幕上点击一下，就可以访问特定的功能或特性。

例如，用户可以通过 Chrome 等浏览器应用程序将特定网站作为固定快捷方式添加到他们的主屏幕上，只需点击一下即可访问该网站。我个人使用固定快捷方式在手机上启动 Roam Research 网站。

# 限制

*   开发者在结合静态和动态快捷方式时，最多只能发布五个快捷方式。
*   虽然开发人员可以创建五个快捷方式，但大多数启动器只显示四个快捷方式。
*   与静态和动态快捷方式不同，固定快捷方式没有限制。但是，我们不能删除锁定的快捷方式。
*   只能在 Android 8 及以上版本的设备上创建固定快捷方式。

# 创建静态快捷方式

如前所述，静态快捷方式用于在应用程序中执行特定的操作，这些操作在应用程序的整个生命周期中都是一致的。要创建静态快捷方式，请按照下列步骤操作。

## 第一步

首先，我们需要通过引用 XML 资源文件将元数据添加到活动中(我们将在下面的步骤中讨论这个文件):

## 第二步

在`res/xml`目录下创建一个名为`shortcuts.xml`的资源文件。

## 第三步

在这个资源文件中，我们可以定义一个或多个快捷方式。文件应该以一个`shortcuts`标签开始，在里面，我们可以添加多个快捷方式:

大多数属性，像`shortcutId`、`icon`、`shortLabel`、`LongLabel`和`DisableMessage`，不需要任何解释就有意义。最棒的是我们可以使用资源文件中的字符串。同时，`targetClass`是一个重要的属性，我们可以用它来定义当用户点击这个快捷方式时要启动的活动。

仅此而已。我们创建了静态快捷方式。

# 创建动态快捷方式

动态快捷方式提供了一种执行上下文相关操作的方式，这意味着操作会因用户而异，甚至会因同一用户而异。

我们可以使用`ShortcutManager` API 创建发布、更新和删除动态快捷方式。如[所述，文件](https://developer.android.com/guide/topics/ui/shortcuts/creating-shortcuts):

> 发布:使用`setDynamicShortcuts()`重新定义动态快捷方式的完整列表，或者使用`addDynamicShortcuts()`扩充现有的动态快捷方式列表。
> 
> 更新:使用`updateShortcuts()`功能。
> 
> 移除:使用`removeDynamicShortcuts()`移除一组动态快捷方式或使用`removeAllDynamicShortcuts()`移除所有动态快捷方式。"

# 创建固定快捷方式

锁定快捷方式用于执行特定的用户驱动的操作。它们可以方便地提供对这些特性的快速访问。与静态和动态快捷方式不同，锁定的快捷方式在启动器中显示为不同的图标。

> "注意:当您尝试将快捷方式固定到支持的启动器上时，用户会收到一个确认对话框，询问他们是否允许固定快捷方式。如果用户不允许锁定快捷方式，启动器将取消请求。— [安卓开发者](https://developer.android.com/guide/topics/ui/shortcuts/creating-shortcuts)

类似于创建动态快捷方式，我们也必须为固定快捷方式使用`ShortcutManager` API。首先，为了检查启动器是否支持固定快捷方式，我们需要使用`isRequestPinShortcutSupported`。

如果启动器支持固定快捷方式，那么我们需要创建一个`ShortcutInfo`对象，我们必须在其中提供快捷方式 ID 和意图。之后，我们必须创建一个挂起的意图，并将`ShortcutInfo`和挂起的意图传递给`ShortcutManager`实例上的`requestPinShortcut`函数。看一看:

> “注意:另请参见支持库 API， `isRequestPinShortcutSupported()`和 `requestPinShortcut()`，它们在 Android 7.1 (API 级别 25)和更低版本上工作。支持库回退到已弃用的 `EXTRA_SHORTCUT_INTENT`extra 来尝试锁定过程。— [苹果开发者](https://developer.android.com/guide/topics/ui/shortcuts/creating-shortcuts)

# 禁用快捷方式

有时快捷方式会将用户引导到你的应用程序中已经过时或不再存在的操作。为了处理这样的快捷方式，我们可以在`ShortcutManager`实例上调用`disableShortcuts`函数。我们必须传递一个字符串列表，其中包含您想要禁用的快捷方式的 id，以删除快捷方式。

当我们调用`disableShortcuts`时，静态和动态快捷键列表中的快捷键被移除，而固定快捷键只能被禁用。如果您在更新应用程序时移除了一些应用程序的静态快捷方式，系统会自动禁用这些快捷方式。

# 更新快捷方式

如果在清单文件中添加了`android:allowBackup=” true”`属性，当用户在设备之间切换时，它将有助于恢复某些快捷方式。

*   当用户在新设备上重新安装应用程序时，静态快捷方式将被重新发布。
*   动态快捷方式不会恢复。我们需要编写最适合重新发布它们的逻辑。
*   锁定的快捷方式会自动恢复到设备的启动器中，但系统不会备份与锁定的快捷方式相关联的图标。

感谢阅读！