# 直接从 AWS CDK 管理第三方提供商

> 原文：<https://betterprogramming.pub/manage-third-party-providers-directly-from-aws-cdk-6afef21c9586>

## 通过 CDK 使用 CloudFormation 自定义资源

![](img/7430015609cb9537bee9b92ac62e2b98.png)

普里西拉·杜·普里兹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

自从我开始使用 [AWS CDK](https://aws.amazon.com/cdk/) 以来，我一直试图用它来做一切与基础设施相关的事情。但我想更进一步，用它来管理我的监控。

为了实现这一点，我在定制资源的帮助下扩展了 CloudFormation。

> “自定义资源使您能够在模板中编写自定义供应逻辑，AWS CloudFormation 会在您创建、更新(如果您更改了自定义资源)或删除堆栈时运行这些模板。例如，您可能希望包含不作为 AWS CloudFormation [资源类型](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html)提供的资源。您可以通过使用自定义资源来包含这些资源。这样，您仍然可以在一个堆栈中管理所有相关资源。”— [AWS 云形成文档](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-custom-resources.html)

正如您在上面的定义中所看到的，定制资源允许您使用 CloudFormation 向*非 AWS* 资源运行代码。这意味着您可以编写一些代码(这是一个 lambda 函数)并在配置阶段运行它。CloudFormation 定制资源通过在处理 CloudFormation 模板时触发 webhook 来工作。您的处理程序将接收这个 webhook 并运行您想要的任何逻辑。

CDK 有[定制资源模块](https://docs.aws.amazon.com/cdk/api/latest/docs/custom-resources-readme.html) 帮助你部署定制 lambda 并运行你的定制代码，但是大多数大的第三方供应商( [auth0](https://github.com/binxio/cfn-auth0-provider) 、 [datadog](https://github.com/DataDog/datadog-cloudformation-resources) 等)。)直接给你提供这个代码，它确实不适合这个模块。

# 如何使用 CDK 创建第三方自定义资源

最近，CloudFormation 团队发布了一项新功能，允许直接从位于 S3 存储桶中的归档文件创建资源(以前只能在 CLI 上实现):

 [## AWS::cloud formation::resource version

### 向 CloudFormation 服务注册资源版本。注册一个资源版本使它可供使用…

docs.aws.amazon.com](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-cloudformation-resourceversion.html) 

由于这是一个新特性，CDK 没有提供创建资源的高级构造，所以解决方法是使用一个`CfnResource` 到在你的 CDK 堆栈中执行一个 CloudFormation 模板。

下面的代码解释了如何添加一个新的 CloudFormation 类型，`Datadog::Monitors::Monitor`，它允许你用 CloudFormation 创建`Datadog`监视器。由于 CDK 正在部署 CFN，我们可以直接使用所有的新功能。

如您所见，我们正在创建一个类型为`AWS::CloudFormation::ResourceVersion`的新`CfnResource`，这是 CFN 类型，允许您创建一个新的定制资源。现在我们将为这个新类型设置一些属性:

*   `TypeName`是我们每次想要创建新显示器时使用的名称。
*   `SchemaHandlerPackage` 是使用该类型时我们应该执行的代码的源代码的位置。

## 验证您的新资源是否已创建

由于您在控制台中看不到您的定制资源，如果您想检查您的资源是否创建良好，您可以使用以下命令:

```
$ **aws cloudformation list-types***# output*
{
  "TypeSummaries": [
    {
      "Type": "RESOURCE",
      "TypeName": "Datadog::Monitors::Monitor",
      "DefaultVersionId": "00000010",
      "TypeArn": "arn:aws:cloudformation:eu-west-2:XXXX:type/resource/Datadog-Monitors-Monitor",
      "LastUpdated": "2021-02-17T10:37:44.800000+00:00",
      "Description": "Datadog Monitor"
    }
  ]
}
```

## 如何使用这个新的自定义资源

现在您有了自己的自定义资源类型，您可以在堆栈中使用它，但是当您尝试在创建它的同一堆栈中使用新类型时，可能会出现这种类型的错误:

```
**Error [ValidationError]: Template format error: Unrecognized resource types: [Datadog::Monitors::Monitor]**
```

为了避免这个问题，您应该在一个`NestedStack`中使用这个类型，并为这个`NestedStack`的类型创建添加一个`dependency`:

# 结论

既然您已经引入了这个新资源，那么您可以轻松地创建您的构造，使用您的新 CloudFormation 定制资源来创建实例。然后，您可以管理 CDK 堆栈中的非 AWS 资源。