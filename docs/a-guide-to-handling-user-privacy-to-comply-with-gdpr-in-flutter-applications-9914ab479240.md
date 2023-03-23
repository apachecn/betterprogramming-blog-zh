# 颤振应用中符合 GDPR 的用户隐私处理指南

> 原文：<https://betterprogramming.pub/a-guide-to-handling-user-privacy-to-comply-with-gdpr-in-flutter-applications-9914ab479240>

## 通过在收集数据前询问来保持合规性

![](img/8fe98f22d4265884a20855954ea35f10.png)

[杰森·登特](https://unsplash.com/@jdent?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

自 2018 年 5 月 18 日起，在欧盟地区发布应用程序的公共和私人组织必须征得用户同意才能收集和处理个人数据，这已经不是什么秘密。

处理隐私设置似乎很有挑战性，但并不复杂。我将描述一种通用而快速的方法来满足您的需求，使用或者不使用专用的隐私框架。

> 像往常一样，我将使用[阻塞](https://pub.dev/packages/flutter_bloc)和[依赖注入](https://pub.dev/packages/injectable)，这只是一个偏好。您可以手动实例化您的类。那没什么大不了的。

# 让我们做一些编码

为了这篇文章，让我们假设我们想要`allow`或`deny`所有的跟踪工具。我们可以很容易地识别三种状态:

*   `authorized`:用户授权启用我们需要的每一个追踪工具。
*   `denied`:没有。我们必须取消一切可能违反 GDPR 法规的东西。
*   `requested`:用户对我们来说是陌生的，我们必须询问他们的隐私管理。

姑且称之为`privacy status`。最简单的方法似乎是创建一个`enum`来描述它们。

然后我们需要一个实体来管理这些，某种类型的`privacy manager`。这是该功能最重要的部分。

我们可以发挥想象力，创造一个`preferences manager`，但这不会有很大的不同。尽管如果你想把所有与[相关的东西都集中到里面，这可能会很好。](https://pub.dev/packages/shared_preferences)

只有两种方法，`savePermission`保存用户的选择，`checkStatus`检索用户的选择。`checkStatus`根据本地保存的值返回前面`PrivacyStatus`中的一个。

> 如果你正在使用一个隐私管理 SDK，你只需要用它替换`*SharedPreferences*`,并且可能将隐私状态包装到另一个对象中。特别是如果你想更具体地了解允许或不允许的权限。例如，作为一个用户，我可能同意分享一些 crashlytics，但不分享我的设备。

为什么是`PrivacyManager`？有时，您可能想要创建自定义跟踪事件，比如当前访问的屏幕。你也不想在每个电话前都加上`if (sharedPreferences.getBool(authorized)…`。相反，你可以创建一个`analytics manager`，注入/实例化你的`privacy manager`，然后嘣！现在可以直接叫`analyticsManager.track(…)`了。

快好了！我们需要在业务层和表示层之间架起一座桥梁。毕竟，关注点分离是一个非常好的架构实践。让我们创建一个简单的[腕尺](https://pub.dev/packages/flutter_bloc#usage)叫做… `privacy cubit`。

这件事没什么好说的。它只是处理我们的`PrivacyManager`和`emits`状态。当然，您需要创建一个`PrivacyConfiguration`状态来将适当的`PrivacyStatus`传递给当前屏幕。

最后，实现表示层来与我们的用户进行交互。那很简单。

用一个`BlocListener`包好你的应用，把`_privacyCubit`传给它，立刻`check`当前的隐私状态。

在`listener`回调中，处理发出的状态。

*   `authorized`:初始化您的监控工具(碰撞分析、性能、分析…)
*   `requested`:显示`AlertDialog`说明并征求用户同意。
*   `denied`:伤心，禁用你的工具，尊重用户的决定。

另外，记得提供一种方法来更新用户的选择。这就是为什么，对于这个例子，我只是在调用`request`的`AppBar`中增加了一个按钮来再次提示`AlertDialog`。

# 最后的话

就是这样！你会一切就绪的。这可能看起来有点让人不知所措，但是这并不复杂，而且这比处理 EDPB 的事情要好。

如果你已经学习并喜欢这篇文章，请留下你的评论。

# 了解更多信息

[](/detect-user-inactivity-in-flutter-applications-af42b08101c) [## 检测颤振应用中的用户不活动

### 防止数据泄露

better 编程. pub](/detect-user-inactivity-in-flutter-applications-af42b08101c) [](https://medium.com/@julien.duribreux_43255/flutter-dependency-injection-flavors-and-secrets-management-75181705045d) [## 依赖注入和风味的颤振秘密管理

### 软件开发中的一个常见用例是需要处理多种环境和相关的…

medium.com](https://medium.com/@julien.duribreux_43255/flutter-dependency-injection-flavors-and-secrets-management-75181705045d)