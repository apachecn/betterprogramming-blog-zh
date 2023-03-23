# 使用 AWS SDK 启动和删除 EC2 实例

> 原文：<https://betterprogramming.pub/launch-delete-your-ec2-instances-using-aws-sdk-dca4f3310301>

## 使用 Amazon 的软件开发工具包启动和删除实例的教程

![](img/78b113a44be971e6fcaced3776fe8f09.png)

照片由 [Christian Wiediger](https://unsplash.com/@christianw?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

[弹性计算云](https://aws.amazon.com/ec2/)或 EC2，是 [AWS 最受欢迎的服务之一。](https://aws.amazon.com/)

对于 AWS 用户来说，有许多方法可以启动实例。

一种方法是网站——一旦你清楚地理解了这个过程的步骤，设置就非常简单了。启动实例的其他选项是通过命令行或使用 Amazon 的软件开发工具包。

有足够的文档可供您决定哪一个选项最适合您的项目。

最近，我们的团队参与了一个项目，选择了 SDK 方法，通过点击一个按钮来启动和删除 EC2 实例。要使用这种方法，需要一些设置。

让我们过一遍。

# 入门指南

首先，SDK 必须可以在项目中使用。

因此，将 IAM 访问密钥保存在根目录下名为`*.aws*`的文件夹中的一个名为`credentials`的文件中非常重要。

之后，你可以从经典的`[npm](https://www.npmjs.com/) install`开始:

```
$ npm install aws-sdk
```

这样，您就可以通过在想要使用它的文件中要求它来使用所有的 SDK 方法。

```
const AWS = require("aws-sdk");
```

虽然它让您可以访问您感兴趣的 EC2 方法，但是创建或删除并不像执行 run 或 terminate 方法那么简单。我们仍然需要几个步骤，类似于你将在 AWS 网站上看到的。

初始设置后的第一步是配置要启动实例的区域:

```
AWS.config.update({region: [exampleRegion]});
```

然后，您需要从 AWS 实例化 EC2 方法:

```
const ec2 = new AWS.EC2();
```

# 启动 EC2 实例

现在，与所有 AWS SDK 方法一样，您需要准备启动实例所需的参数。

大多数方法通常包含一个庞大的参数列表，但其中只有少数是必需的。这完全取决于你想要完成的具体任务。

启动实例的基本参数有:`ImageId`、`InstanceId`、`Maxcount`、`MinCount`和一个`Security Group`。

您也可以将`KeyPair`作为一个参数，类似于您在网站上遵循常规步骤时发生的情况。

如果您选择将它包含在您的实例中，您需要在本地机器上创建并保存一个带有`KeyPair`凭证的`.pem`文件，这允许您安全地 SSH 到您的实例中。

以下逻辑步骤用于准备要启动的参数:

## **创建新的安全组**

所有实例必须链接到一个安全组(记住，有必要给它一个唯一的名称)。

在我们的例子中，我们选择不使用 AWS 的命名约定来了解通过 web 界面创建了什么，以及通过项目的算法启动了什么。

```
**let** params = {
 Description: “My security group”,
 GroupName: “my-security-group-unique-name-XXXX”
};
```

这是参数对象创建安全组时仅有的两个必需字段。

您可以向它添加更多的字段，但是在这种情况下，不需要更多的字段，因为您正在将它链接到指定区域中的一个新 EC2 实例。

```
ec2.createSecurityGroup(params, **function**(err, data) {
  **if** (err) console.log(err, err.stack); *// an error occurred* **else**     console.log(data);           *// successful response* });
```

## **创建新的密钥对**

键名也必须是唯一的。

但是，与 AWS 网站上发生的情况类似，它不是自动生成的。在我们的项目中，我们允许用户输入这个步骤。

上面的方法返回 SSH 密钥，供您访问它所链接到的实例。这就是你必须保存在一个`.pem`文件中的东西，在你的文件系统中。

因为我们的团队正在使用一个[电子](https://electronjs.org/)应用程序，所以我们通过向主进程发送`KeyPair`数据来利用它的 IPC 通信，在主进程中我们使用了 [Node.js](https://nodejs.org/) 文件系统方法。

## 创建实例

最后，您可以创建您的实例，将安全组和`KeyPair`链接到它。

# 删除 EC2 实例

要删除 EC2 实例，可以遵循类似的步骤。

我们认为删除实例的功能也会删除其安全组。为了实现这一点，安全组不能附加到任何东西，并且除了默认规则之外，它们不能有任何入站/出站规则。

因此，有必要实现一个算法，在检查安全组是否有任何已配置的规则时，找到与感兴趣的实例相关联的安全组。

让我们经历所有的步骤。

## 获取 EC2 的详细信息

获取您要删除的 EC2 的详细信息。

在这种情况下，您需要的唯一参数是实例的 ID:

此方法返回的对象包含嵌套信息，因此您应该解构该对象以筛选所需的数据。

## 获取安全组详细信息

使用上一步返回的对象中的`groupId`作为参数，获取安全组的详细信息，**T5。**

返回的对象应该包含一个嵌套在其中的`IPpermissions`键。

这个键的给定对是安全组规则的数组，包括启动时创建的默认规则。

如果数组包含的规则不止默认规则，那么在删除整个 EC2 实例之前，您需要终止剩余的入站/出站规则。

知道您必须终止规则是很重要的——在特定的实例和它所连接的其他服务中。

## 撤销出口/入口规则

对于两个实例的 id，撤销出口/入口规则:

并非所有参数都是必需的，但重要的是要使其更加具体，以保留您可能需要的不删除实例的某些规则。

```
ec2.revokeSecurityGroupEgress(params, **function**(err, data) {
  **if** (err) console.log(err, err.stack); *// an error occurred* **else**     console.log(data);           *// successful response* });
```

撤销入口规则的参数是相同的。您只需要构建特定于每个实例的两个逻辑。

## 删除实例

最后，您可以运行方法来删除实例。

唯一需要的参数是实例的 ID。

在我们的项目中，这些方法都是通过点击一个按钮来执行的。

出于这个原因，我们决定使用承诺，这使我们能够将所有这些事件联系起来。

## 删除安全组

我们的主要挑战是知道何时运行最终的 SDK 方法来删除安全组。

我们发现的最佳解决方案是通过再次运行`describeInstances`方法来定期检查实例的状态。

最近终止的实例仍然会在某个时间段内从该调用返回，该时间段通常持续不到一个小时。

一旦实例终止，触发`deleteSecurityGroup`。对此可能有更好的解决方案，所以这肯定是我们项目的一个改进点。

# 结论

如果您有兴趣了解更多信息，或者您在设置 AWS 服务时遇到了一些挑战，我们为 EC2、RDS 和 S3 实例，甚至是 [Lambda](https://aws.amazon.com/lambda/) 函数开发了一个可视化和配置工具。

合作者:[吉尔](https://medium.com/u/25856a3eb6c3?source=post_page-----dca4f3310301--------------------------------)、[瑞秋·金](https://medium.com/u/baed7becc729?source=post_page-----dca4f3310301--------------------------------)、[塔兰·加尔达索夫](https://medium.com/u/e057d50fd7f?source=post_page-----dca4f3310301--------------------------------)。

# 参考

*   我们在 GitHub 上的[开源项目](https://github.com/oslabs-beta/MiraCloud)
*   [我们应用的测试版](http://www.themiracloud.com)

 [## 用于 JavaScript 的 AWS SDK

### 编辑描述

docs.aws.amazon.com](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/index.html) [](https://coderade.github.io/post/aws/launching-ec2-instances-nodejs-aws-sdk/) [## 使用 Node.js AWS SDK 启动 EC2 实例

### “EC2 服务上的 Back to home Posted 是 AWS 提供的最基本的服务之一。它支撑着许多…

coderade.github.io](https://coderade.github.io/post/aws/launching-ec2-instances-nodejs-aws-sdk/)