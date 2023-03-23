# AWS 安全专业—密钥管理服务

> 原文：<https://betterprogramming.pub/aws-security-specialty-kms-notes-5045c505a33b>

## 学习某事的最好方法是写下来

![](img/ede26f95271797c57212aed527ede9ff.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [olieman.eth](https://unsplash.com/@moneyphotos?utm_source=medium&utm_medium=referral) 拍摄的照片

我目前正在为 [AWS 认证安全-专业](https://aws.amazon.com/certification/certified-security-specialty/)考试学习，围绕加密和密钥管理服务(KMS)的材料数量有点令人生畏。在通过了几次模拟考试，并把考试改了两次后，我决定写几篇关于我正在为自己和其他人努力的话题的文章，这可能会有所帮助。通过把这些都写下来，并且一丝不苟地核对每一个细节，我希望这些细节能够坚持下去。虽然我还在学习，所以如果有错误，给我留言。

AWS 在其[最佳实践白皮书](https://d0.awsstatic.com/whitepapers/aws-kms-best-practices.pdf)中，将 KMS 定义如下:

> AWS 密钥管理服务(AWS KMS)是一项托管服务，使您可以轻松创建和控制用于加密数据的加密密钥。AWS KMS 使用硬件安全模块(HSM)来保护您的密钥的安全。您可以使用 AWS KMS 来保护 AWS 服务和应用程序中的数据。

如果您有在 AWS 中加密或解密数据的业务需求，KMS 是您的最佳选择。它与包括 EC2、S3、参数存储和 Lambda 在内的许多服务相集成，使得向 AWS 工作负载添加额外的安全层变得非常简单。值得注意的是，KMS 是地区性的，而最近他们增加了使用多地区键的能力。

它更多地来自传统的数据中心环境，帮助我在考试中了解 AWS 服务如何映射到内部通常使用的解决方案。凭借其对对称和非对称密钥的管理，KMS 类似于 CyberArk PAM 或 HashiCorp Vault 产品。我肯定有很多不同之处，但如果我迷失在一个问题中，而我根据 CyberArk 会如何做有一个很好的猜测，它很有可能是正确的。

# **CloudHSM**

正如描述中提到的，KMS 使用了一个 [HSM](https://en.wikipedia.org/wiki/Hardware_security_module) 来保护存储在里面的密钥。然而，如果你想从上到下管理你的密钥 [CloudHSM](https://aws.amazon.com/cloudhsm/) 就是你要去的地方。在这种情况下，HSM 完全专用于您的帐户，密钥在 CloudHSM 中生成并存在，供 KMS 在 KMS 支持的所有托管服务中使用。

如果为了全面管理密钥管理生命周期，您的组织有特定的需求，那么可能有必要使用 CloudHSM。重要的一点是，AWS 对它没有管理(尽管它是通过 CloudTrail 登录的[，它需要一个单独的 SDK/CLI 来与之交互，并且它是经过](https://docs.aws.amazon.com/cloudhsm/latest/userguide/get-api-logs-using-cloudtrail.html)[FIPS 3 级](https://en.wikipedia.org/wiki/FIPS_140-2)验证的。

# **密钥很重要，让我们从限制访问开始**

授予和限制对 KMS 键的访问权限有三种主要方法:键策略、IAM 策略和授权。

[键策略](https://docs.aws.amazon.com/whitepapers/latest/kms-best-practices/key-policies.html)被附加到每个键上，每个键必须有且只有一个键策略。该策略概述了谁可以管理和使用密钥。关键政策是区域性的。

[IAM 策略](https://docs.aws.amazon.com/kms/latest/developerguide/iam-policies.html)是提供使用 KMS 密钥的可选方法。为了允许 IAM 策略授予对密钥的访问权限，密钥策略需要允许帐户使用 IAM 策略。

```
“Principal”: {“AWS”: “arn:aws:iam::*111122223333*:root”},
“Action”: “kms:*”,
```

[密钥授权](https://docs.aws.amazon.com/kms/latest/developerguide/grants.html)是一种向 AWS 主体提供对临时的、粒度的和编程的密钥的访问的方式。与在键或 IAM 策略中定义访问不同，为键创建授权将允许访问，主要由 AWS 资源使用。授权只能允许访问，不能拒绝。

# **政策行动**

我遇到过几个处理 KMS 服务可用的特定权限和操作的场景。虽然这不是一个详尽的列表，也没有详细的解释，但它帮助我在回答政策问题时更加自信。

*   `kms:Decrypt` —允许对 KMS 加密的密文进行解密。
*   `kms:DescribeKey` —允许存储在 KMS 的客户管理或 AWS 管理的密钥的详细信息。
*   `kms:Encrypt` —允许加密小于 4KB 的数据。如果您只生成一个数据密钥，则没有必要。
*   `kms:GenerateDataKey` —生成唯一的对称数据密钥，该密钥提供明文版本的密文和加密版本。
*   `kms:GenerateDataKeywithoutPlaintext` —类似于 GenerateDataKey，但是它不提供密文的明文版本。

需要注意的是**加密和解密功能只与 KMS 密钥**相关。例如，因为`GenerateDataKeywithoutPlaintext`提供了一个加密的数据密钥，EBS 只需要那个许可和`kms:Decrypt`就可以正确地加密存储卷。EBS 卷中的数据加密独立于 KMS，因此不需要特定的加密权限。

# **政策条件**

基于我所看到的一切，`kms:ViaService`是唯一值得了解的政策条件。ViaService 条件允许密钥管理员将密钥访问权限限制为仅需要它的 AWS 服务。例如，只有当请求来自特定地区(us-west-1)的特定服务(EC2)时，才允许访问密钥。

## **按键、按键和更多按键**

通常，KMS 管理对称和非对称密钥。创建密钥的方法有几种类型，如何管理它们决定了密钥的许多属性。这似乎是模拟考试中的一个热门话题，所以了解不同类型的键的细微差别以及它们是如何被管理的对很多问题都有好处。

[AWS 自有密钥](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#kms_keys) —这些密钥归 AWS 服务所有，跨多个帐户使用，不能在 KMS 查看或编辑，并且具有客户无法设置的可变轮换计划。他们也是免费的。

[AWS 托管密钥](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#key-mgmt) —定制密钥的最简单选项，这些密钥由 AWS 生成，对您的帐户是唯一的，存储在您的 KMS 实例中，由 AWS 每 3 年自动轮换一次。这些映射到资源，如 S3 或 EBS，并链接到资源的生命周期。

[客户管理的密钥](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#key-mgmt) —一个简单的选项，允许对 KMS 密钥进行更多控制，客户可以管理许多方面，包括禁用、删除、轮换和密钥策略。轮换可以设置为每年轮换，也可以按需轮换。

[客户管理的密钥(导入)](https://docs.aws.amazon.com/kms/latest/developerguide/importing-keys.html) —这允许更好地遵守密钥生成，并允许在 AWS (Azure、on-prem 等)之外使用密钥。).密钥必须手动旋转，并且可以删除，没有 7 到 30 天的时间限制。

[客户管理密钥(CloudHSM)](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#keystore-concept) —位于 KMS 的自定义密钥库可以直接映射到 CloudHSM 实例。连接后，KMS 可以生成对称密钥，并将它们存储在 CloudHSM 中的自定义密钥库中。

需要注意的是，KMS 的一些租户适用于所有密钥:

*   KMS 主密钥不会让 KMS 处于未加密状态。
*   您不能导出非对称密钥的私钥。

# **关于对称和非对称密钥的注释**

我不打算讨论这两者之间的区别，因为这并不是真正的 AWS 重点，而且您可能从一般的安全知识中了解到这一点。大多数 KMS 课程都集中在对称密钥上。但是，有一些关于非对称密钥的[注意事项需要理解:](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html)

*   你可以导出公钥，但是你必须调用 KMS 来使用私钥。
*   与设置 EC2 实例时不同，您不能将非对称私钥导出到 KMS 之外。您需要 CloudHSM 来创建和导出一个非对称密钥对。
*   对于非对称密钥，您不能将自己的密钥材料导入 KMS。
*   您不能将加密上下文(下面讨论)与非对称密钥一起使用。

# **按键别名**

为键使用别名允许更用户友好的命名方案，并且还为应用程序提供了一个参考，其中被参考的键可以在不改变应用程序的情况下被改变。当引用带有导入密钥材料的客户管理密钥时，这很方便。

如上所述，具有导入的密钥材料的客户管理的密钥不能自动旋转，并且后备密钥材料不能简单地改变。它需要一把全新的钥匙。旋转密钥时，您可以选择通过更改密钥的源代码引用来更新应用程序，或者只需更改密钥别名以指向新密钥。

# **KMS 键与数据键**

到目前为止，这篇文章中的所有内容都集中在 KMS 群岛 T2。如前所述，这些密钥只能用于加密或解密少量信息(4KB ),不能在 KMS 之外使用。对于大量需要加密数据的用例来说，这并没有什么帮助。

为了加密和解密 KMS 以外的数据，包括大量数据，KMS 可以生成使用 KMS 密钥保护的[数据密钥](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#data-keys)。由于数据密钥由 KMS 密钥支持，它实质上允许通过数据密钥保护的数据由 KMS 密钥保护。这被称为 E [nvelope 加密](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#enveloping)，也允许通过 Cloudtrail 进行审计。

# **加密上下文和附加认证数据**

对于可能需要更精细地访问受 KMS 保护的数据的应用程序，管理员可以使用[加密上下文](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#encrypt_context)。每个加密和解密调用都会传递一个非秘密的密钥对，在操作返回明文数据之前，需要验证所有的密钥对。这些密钥对被视为“附加认证数据”

例如，假设您有一个管理客户 SSN 数据库的应用程序，您希望用 KMS 加密该数据库。应用程序可以访问加密和解密所有数据的 KMS 密钥。但是，如果您想确保系统进行的查询是有限的，该怎么办呢？您可以确保当数据被加密时，`userEmail: john@gmail.com`加密上下文也随之传递。如果系统想要查找客户的 SSN，它们需要传递客户的电子邮件地址，作为额外的验证器，表明它正在请求正确的信息。如果能够获得密钥的人试图在没有电子邮件的情况下解密数据，将会失败。

需要注意的是，传递的数据是非秘密的，并且记录在 Cloudtrail 中。对于上面的例子，不要使用 SSN 作为你的键对！

# **服务器端与客户端加密**

在 S3 有很多关于如何保护数据的资料，理解服务器端加密和客户端加密之间的区别很重要。

服务器端加密是 AWS 管理加密和解密的地方，利用 AWS 拥有的密钥( [SSE-S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingServerSideEncryption.html) )、KMS 的 AWS/客户管理的密钥( [SSE-KMS](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingKMSEncryption.html) )，或者利用亚马逊加密 SDK ( [SSE-C](https://docs.aws.amazon.com/AmazonS3/latest/userguide/specifying-s3-c-encryption.html) )保存在 AWS 外部的客户管理的密钥，当数据被写入桶时，它被自动加密。但是，任何能够访问 bucket 使用的键(以及 bucket 本身)的人都可以访问整个 bucket 中的数据。

[客户端加密](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingClientSideEncryption.html)，使用 AWS Encryption SDK 或任何其他加密工具，可以更好地控制谁可以访问存储桶中的数据。客户端加密的密钥可以存储在 KMS，或者应用程序所有者可以以另一种方式存储它们，完全不用 AWS。S3 看到的唯一数据是加密的有效载荷，AWS 没有参与加密。

最后一点很重要，需要理解:在 AWS 中加密数据的唯一方法是使用 SDK 并单独管理密钥，要么通过 SSE-C，要么通过客户端加密。当数据需要满足最高级别的安全性或法规遵从性时，这可能是最佳答案，例如医疗保健数据。

# 把所有的放在一起

在浏览了大量关于 KMS 的资料后，我列出了下面这些我还在继续研究的东西，这些东西激发了我写这篇文章的灵感。

*   了解 KMS 键可用的不同选项:
    —命名
    —元数据
    —管理(启用/禁用、删除、手动轮换等)
    —轮换计划
*   为什么你会在 KMS 使用进口钥匙？
    —在 AWS 之外的应用中使用
    —可用性/耐用性要求
    —合规性
*   KMS 不直接加密/解密数据，除非数据小于 4KB。
*   必须通过创建新的备用密钥并更改正在使用的密钥或更新别名来手动旋转导入的密钥材料。
    —您不能简单地将新的密钥材料导入同一个 KMS 密钥。
*   KMS 将保留旧密钥，并且将总是解密数据，即使该密钥不是主要加密密钥(例如，它已经被旋转)。不要删除旧的密钥，除非你知道不再需要它。
*   如果您被要求在 S3 存储数据，而合规性要求规定 AWS 根本不能访问这些数据，那么您可能会考虑 SSE-C 或客户端加密。
*   S3 中用 KMS 加密的对象提供了另一层防止公共共享的保护，因为请求用户除了访问桶之外还需要访问密钥，而匿名(公共)用户将不能访问。
*   如果你需要提供**临时**、**粒度**或**程序化**访问密钥，你需要使用密钥授权。
*   `kms:ViaService`限制从特定的 AWS 服务访问 KMS 密钥。
*   直到最近，键都是特定于地区的。如果您处理的是特定于地区的密钥，则在地区之间移动的任何数据都需要在目标地区用 KMS 密钥重新加密。
*   客户管理的 KMS 密钥的密钥删除期为 7 至 30 天。导入的关键材料可以随时删除。
*   如果您需要 FIPS 3 级，您可以选择 CloudHSM。KMS 只有 FIPS 二级。
*   密钥授权只能允许访问，不能用于拒绝访问。
*   加密 EBS 卷(以及其他服务的基线)的最小值是`kms:Decrypt`和`kms:GenerateDataKeyWithoutPlaintext`。数据密钥的生成处理数据密钥的原始加密，因此`kms:Encrypt`是不必要的。

# 结论

写完这篇文章后，阅读 KMS 章节变得简单多了。这也让我对我的 KMS 知识感到足够舒服，我实际上可以应用我所学到的东西，我不仅限于试图通过考试。希望它也能帮助其他人。