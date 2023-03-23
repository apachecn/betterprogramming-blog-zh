# 如何存储你的 AWS Lambda 秘密

> 原文：<https://betterprogramming.pub/how-to-store-your-aws-lambda-secrets-cheaply-without-compromising-scalability-or-security-d3e8a250f12c>

## 正确地完成它，这样当应用程序负载增加时，您就不必重做它

![](img/30bd18cc4eaf0e3252ac78e7d2ac71f3.png)

不要告诉任何人你的秘密。一定要告诉人们如何储存秘密。(照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Kristina Flour](https://unsplash.com/@tinaflour?utm_source=medium&utm_medium=referral) 拍摄)

大多数 Lambda 部署使用环境变量将配置传递给部署的函数。这是 AWS 在文档中提出的[建议，这是一个很有吸引力的选择，因为它很简单，静态加密，并且允许在一开始就灵活地获取值。](https://docs.aws.amazon.com/lambda/latest/dg/lambda-configuration.html)

关于[lambda](https://medium.com/@furqanshaikh/securing-secrets-in-aws-lambda-93c12861371f)配置 s [已经](https://dev.to/dvddpl/where-do-you-keep-credentials-for-your-lambda-functions-5dno)有很多[文章](https://epsagon.com/blog/aws-lambda-and-secret-management/)跟[好的推荐](https://theburningmonk.com/2017/09/you-should-use-ssm-parameter-store-over-lambda-env-variables/)。你为什么要读这本书？

这不是比较和对比各种方法，而是为那些主要价值是在不牺牲可伸缩性或安全性的情况下最小化成本的人提供的操作指南。如果你有额外的或不同的需求，我建议你也阅读上面的链接。

本指南面向中小型团队，他们的工作环境中安全性很重要，但是细粒度的权限管理可能不重要。

## 告诉我答案

如果你是从谷歌来的，只是想要一份推荐，可以直接跳到最后看总结。如果你想了解该建议背后的详细理由，请继续阅读。

# 环境变量中的秘密

将配置存储在环境变量中对于非秘密的细节(如 API 端点位置、主机名、公钥等)来说是很好的。然而，对于像密码或 API 密匙这样潜在敏感的配置来说，它并不那么有效。

尽管 Lambda 环境变量在静态时被[加密，但是它们对于任何有权限在控制台中查看 Lambda 的人来说都是](https://docs.aws.amazon.com/lambda/latest/dg/env_variables.html#env_encrypt)[可见的。](https://theburningmonk.com/2017/09/you-should-use-ssm-parameter-store-over-lambda-env-variables/)这并不好，因为它违反了最小特权的[原则——不需要的人或服务不需要轻易访问敏感数据。](https://en.wikipedia.org/wiki/Principle_of_least_privilege)

还有一个实际问题——让环境变量将敏感数据存储在非敏感数据旁边，很有可能会使值被来自云形成或一般执行的日志意外公开。如果人们习惯于在日常工作中显示敏感数据，特别是当它显示在非敏感数据旁边时，他们就很难记住保护敏感数据。

值得注意的是，即使无服务器 [Secrets 插件](https://github.com/serverless/serverless-secrets-plugin)使用了这种方法，文档还是以下面的警告开始:

> 重要提示:正如在 [AWS 文档](http://docs.aws.amazon.com/lambda/latest/dg/env_variables.html)中指出的，为了存储敏感信息，Amazon 建议使用 AWS KMS，而不是像这个插件一样的环境变量。

也就是说，AWS 建议不要把明文秘密放在你的环境变量里，我同意。

![](img/683fde1b5196a7e4c1fb94e7cc6d5bad.png)

哪些是应该保密的？(照片由 [Jason D](https://unsplash.com/@cdfman?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄)

# 用 KMS 加密的环境变量

正如引用所暗示的，AWS [建议](https://docs.aws.amazon.com/lambda/latest/dg/env_variables.html)预先用 KMS 加密环境变量。这意味着加密版本是将在控制台中公开的全部内容。方法很简单——在将值放入环境变量之前，使用 KMS 加密该值，然后在 Lambda 代码中使用 KMS 解密它。这里有一个[不错的演练](https://www.humankode.com/security/how-to-encrypt-secrets-with-the-aws-key-management-service-kms)。

如果你只关心价格和安全性，这种方法是完美的。然而，由于我们正在寻找一些扩展性很好的东西，如果我们有一些集中的配置会更好。

# 集中配置

AWS 提供了两种集中配置的主要方法— [秘密管理器](https://aws.amazon.com/secrets-manager/)和系统管理器的[参数存储库](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html)(容易混淆地缩写为 SSM 参数存储库)。

## 秘密经理

如果您需要详细控制何时何地可以使用每个秘密，Secrets Manager 是一个很好的选择。然而，考虑到存储和访问成本，它[比参数存储的免费基本存储](https://aws.amazon.com/secrets-manager/pricing/)要贵得多。

由于成本增加，我不考虑这里的秘密经理。然而，如果它适合你的需要，这个教程[是一个很好的起点。](https://medium.com/@eoins/securing-github-tokens-in-a-serverless-codepipeline-dc3a24ddc356)

![](img/251b828d01d5e42e3b9a4228cb9f31cf.png)

不是那种集中的(图片由 [K8](https://unsplash.com/@k8_iv?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄)

# SSM 参数存储中的秘密

参数存储有几个不错的特性，包括[分层参数](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-paramstore-su-organize.html)。这意味着您可以用路径来命名参数，以便于检索。例如，如果用斜杠分隔的命名空间来命名参数:

```
aws ssm put-parameter              \
    --name "/your/app/some_value"  \
    --type "SecureString"          \
    --value "foo is a value" aws ssm put-parameter              \
    --name "/your/app/other_data"  \
    --type "SecureString"          \
    --value "another value"
```

然后，您可以授予函数权限来检索该路径下的所有参数:

```
- Effect: Allow
  Action:
   - ssm:GetParameters
   - ssm:GetParameter
  Resource: 'arn:aws:ssm:<REGION>:<ACCOUNT>:parameter/your/app/*'
```

## 运行时获取参数

另一个简洁的特性是能够在运行时检索参数。这很好——它将部署和配置解耦。如果你想这样做，这里有一个我写的[帮助要点](https://gist.github.com/TimothyJones/8d71a52665bca3196a6c19488efbc2c4)，它平滑了节点 Lambdas 的过程。

但是，参数存储有一个缺点:吞吐量非常低。用`get-parameters`检索的每个参数都算作一个请求，每秒只允许 100 个请求。

您可以[增加这个限制](https://docs.aws.amazon.com/systems-manager/latest/userguide/parameter-store-throughput.html)，但是这样做会提高价格，并且仍然只允许每秒 1000 个请求。

*此外:如果你走这条路，我强烈建议在你的处理程序之外加载参数存储参数，这样它们将在每个 Lambda 实例中加载一次，而不是在每个处理程序执行中加载一次。*

# 在部署时从参数存储中读取加密值

因此，为了更好的缩放，我们需要一个集中的参数存储。Parameter Store 的免费成本很有吸引力，但由于每秒请求数的限制，它不会扩展。

在部署时读取加密数据(不解密)怎么样？由于参数存储由 KMS 支持，我们可以在部署时读取参数存储的安全参数而无需解密，并在运行时使用 KMS API 解密。

如果你使用的是[无服务器框架](https://serverless.com/)，这实际上[非常简单](https://serverless.com/framework/docs/providers/aws/guide/variables/):

```
${ssm:/path/to/secureparam~false} 
```

`false`说“请不要解密这个”。

然而，我不喜欢使用无服务器框架——而且 CloudFormation 不支持像这样使用 SecureString 参数。

另外:如果你正在使用无服务器框架，并且想走这条路，注意你需要记住参数的 ARN，以便在解密过程中用作加密上下文。更多详情 [*此处*](https://docs.aws.amazon.com/kms/latest/developerguide/services-parameter-store.html#parameter-store-encryption-context) *。*

![](img/c5437888c4c1d8f9556b52206ed9cbf1.png)

有时你看不到其他云的云(照片由[ukasz ada](https://unsplash.com/@lukaszlada?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄)

# 安全参数和云形成

如果你直接使用 CloudFormation，那么 SecureString 参数[不被支持](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html#aws-ssm-parameter-types-unsupported)。您不能将它们用作参数:

```
# Broken CloudFormation:Parameters:
  SomeParam:
    Type: 'AWS::SSM::Parameter::Value<String>'
    Default: '/path/to/secureparam/'## ERROR ##An error occurred (ValidationError) when calling the CreateChangeSet operation: Parameters [/path/to/secureparam] referenced by template have types not supported by CloudFormation.
```

也不能在[批准的地点](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/dynamic-references.html#template-parameters-dynamic-patterns-resources)之外使用:

```
# Broken CloudFormation: Resources:
  ReceiveLambda:  
    Type: AWS::Serverless::Function
    Properties: 
      Environment:
        Variables:
          VAR_NAME: '{{resolve:ssm-secure:/path/to/secureparam:1}}'## ERROR ###Failed to create the changeset: Waiter ChangeSetCreateComplete failed: Waiter encountered a terminal failure state Status: FAILED. Reason: SSM Secure reference is not supported in: [AWS::Lambda::Function/Properties/Environment/Variables/VAR_NAME]
```

但是，您*可以*使用字符串参数，这些参数没有被参数存储加密。所以，我建议先用 KMS 加密参数。

*先不说:优秀的*[*stack master*](https://github.com/envato/stack_master)*工具为 SSM 的参数存储(以及许多其他有用的特性)增加了更好的支持。然而，它仍然解密 SecureString 参数，这意味着它们不会在 Lambda 控制台中被加密。*

![](img/4a3cabfc4b7df4528efe2b33983184e6.png)

我们需要下两级台阶(照片由[布雷特·乔丹](https://unsplash.com/@brett_jordan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄)

# 推荐:KMS +参数存储字符串

与其使用参数存储的 SecureStrings 来抽象掉 KMS 步骤，我建议使用 KMS 来加密作为普通字符串进入参数存储的字符串。这提供了:

*   低成本(KMS 客户密钥每月 1 美元)
*   可扩展性(没有速率限制)
*   安全性(不暴露密钥)
*   使用的灵活性(可由 CloudFormation 使用)

这有一些缺点:

*   不完全自由
*   配置仅在部署时更新(但是为了额外加分，您可以使用[参数存储的触发器](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-paramstore-cwe.html)在参数改变时启动部署)

# 使用 KMS 手动加密参数

首先，您需要[在 KMS 创建一个客户管理密钥。目前，这每月花费 1 美元，但是只要你有至少三个秘密，它仍然比秘密管理器便宜。](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html)

此命令会将加密的字符串参数插入到参数存储中:

```
aws ssm put-parameter                       \
   --type String                            \
   --name '/YOUR/PARAM/NAME'                \
   --value $(aws kms encrypt                \
              --output text                 \
              --query CiphertextBlob        \
              --key-id <YOUR_KEY_ID>       \
              --plaintext "PLAIN TEXT HERE")
```

您的用户必须对您在上面创建的密钥拥有`kms:Encrypt`权限。注意，KMS IAM 权限需要密钥 ARN，而不能使用别名 ARN 的密钥。

# 使用存储在云信息中参数

有几种方法可以将你的 SSM 参数放入你的云系堆栈。这里有一个我特别喜欢的模式:

```
Parameters:
  SomeParameter:
    Type: AWS::SSM::Parameter::Value<String>
    Default: '/your/param/name'        # This is your parameter nameResources:
  ReceiveLambda:  
    Type: AWS::Serverless::Function
    Properties: 
      Environment:
        Variables:
          SOME_PARAMETER: !Ref SomeParameter
```

# 使用 Lambda 中的 KMS 在运行时解密

首先，您需要 Lambda 执行角色的以下权限:

```
- Effect: Allow
  Action:
    - kms:Decrypt
  Resource: 
    - <KEY_ARN>           # Note, the key ARN, not the key alias ARN
```

然后，您可以解密参数。下面是将在 node 中完成的代码片段:

就是这样！

旁白:现在你所要做的就是小心不要记录或者暴露解密的秘密。

# 摘要

要获得便宜、可扩展的配置，同时又不会不必要地暴露您的秘密:

*   在将参数以纯字符串的形式保存到参数存储之前，使用 KMS 手动加密参数(这里有一个 [bash 脚本](https://gist.github.com/TimothyJones/633c63b0a332692d85c518928ca20e5c)来使这变得非常简单)
*   在部署生命周期中使用参数存储，以 Lambda 上的环境变量结束
*   使用 KMS 在运行时解密环境变量(这里的[是一个可以借鉴的 node.js 模块的例子)。](https://gist.github.com/TimothyJones/8165694951385246c665dc94ef18702e)
*   在函数加载时进行解密，而不是在处理程序中，以尽量减少 KMS 调用。

我编写并分享了一个 [bash 脚本来帮助创建 KMS 加密的参数](https://gist.github.com/TimothyJones/633c63b0a332692d85c518928ca20e5c)。如果你使用 node 作为你的 Lambdas，我已经分享了这个模块作为你解密的起点。

最后，这里有一段关于 AWS Lambda 配置的引用，来自 AWS 的主题演讲人:MiddleEarth(大概):

![](img/fc5781c3b9238700a931ad52d6ba9bdc.png)

甘道夫谈如何处理你的 Lambda 中的秘密(图片:[胡庆炉·坦恩](https://www.slideshare.net/helgetenno/the-outcome-economy/2-KEEP_ITSECRETKEEP_ITSAFE)和新线电影公司)