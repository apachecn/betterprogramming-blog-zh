# 地形、AWS 和幂等性

> 原文：<https://betterprogramming.pub/terraform-aws-and-idempotency-78220aaa5372>

## Terraform 里的 bug？或者是对某一节的工作原理的误解？或者甚至是我们自己围绕 Terraform 的自动化？

![](img/48bd5a269961565439d5562cff6bd2ff.png)

詹姆斯·哈里森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

希望这只是这个系列的第一部分，因为到目前为止还没有一个令人满意的结局，但仍然是一个值得分享的故事。我们在 Terraform 中遇到了一个短暂的错误，但似乎会自行消失，很可能是一些比赛条件。这篇文章将会讲述这个失败。这比我的一些其他帖子更具技术性，所以那些寻求领导思想的人可能会看呆了。您的里程可能会有所不同。

首先简单介绍一下我们的部署流程。我们在我们的环境中使用[蓝/绿部署策略](https://martinfowler.com/bliki/BlueGreenDeployment.html)(不包括数据库)。围绕 Terraform 的自动化负责启动第二个应用程序堆栈并向其部署代码。在创建第二个堆栈的过程中，我们收到了一条从未遇到过的错误消息。

```
Error: error creating EC2 Launch Template: IdempotentParameterMismatch: Client token already used before. 
  status code: 400, request id: 4c6edce7-7497-4884-ab63-f215f9b82f6e
  on ../terraform-asg/main.tf line 33, in resource "aws_launch_template" "launch_template":
  33: resource "aws_launch_template" "launch_template" {
```

这里有一些事情需要研究。

# 幂等参数不匹配错误

当一个动作是幂等的，这意味着它可以被多次执行，而不会改变最初应用之外的结果。我

实际上，这通常意味着，如果我运行命令 X，该命令知道它以前是否在这个上下文中运行过，并且可能跳过该命令的通常应用，而是返回一个状态或结果。一个简单的例子是使用-p 标志时的`mkdir`命令(如果完整路径不存在，它告诉`mkdir`创建完整路径)。

如果我在本地工作站上运行，会发生以下情况。

```
mkdir -p /tmp/test
ls -l /tmp/|grep test
drwxr-xr-x  2 jeff.smith  wheel    64 Jun  9 06:31 test
mkdir -p /tmp/test
```

`mkdir`命令创建了路径`/tmp/test`，我们可以看到它被成功创建。当我再次运行该命令时，它成功完成，没有错误消息。

这使得这个命令是幂等的。不管我运行这个命令多少次，我知道最终我会得到一个成功的结果，并且路径`/tmp/test`会存在。现在对比一下`mkdir`。

```
mkdir /tmp/test2
ls -l /tmp/|grep test2
drwxr-xr-x  2 jeff.smith  wheel    64 Jun  9 06:35 test2
mkdir /tmp/test2
mkdir: /tmp/test2: File exists
```

现在使用 mkdir，在第二次执行时，我得到一个错误消息，这是一个不同于第一次执行的最终结果。目录存在，但是我得到的错误代码是不同的。这使得这成为一个非幂等运算。但是我们为什么要在乎呢？在第二个例子中，首先我需要做更多的错误处理。这是一个基本的例子。

当做类似创建基础设施的事情时，可能会导致启动比预期更多的实例，这就是这个`IdempotentParameterMismatch`错误旨在防止的。

当您调用 AWS API 来创建基础设施时，几乎每个端点(据我所知)都是以异步的方式进行的。这意味着您的 API 调用立即返回，但是实际创建您所请求的基础设施的工作仍在进行中。因此，您通常需要某种轮询机制来确定操作何时完成。

几个 AWS API 端点[支持等幂](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/Run_Instance_Idempotency.html)，这允许您指定一个客户机令牌来惟一地标识这个请求。如果您进行相同的 API 基础结构创建调用并使用相同的客户端令牌，而不是创建新的实例，它将返回先前请求的实例的状态。当以编程方式创建基础设施时，这可以是一个大的安全网，以避免创建相同基础设施的许多副本。这就是我们的错误所在。

该错误表明我们已经使用了客户端令牌。深入研究一下文档，更具体的意思是我们更改了请求中的参数，但是重用了客户端令牌，这意味着 API 不知道我们的实际意图是什么。我们需要具备这些参数的新基础设施吗？或者，我们是否期望现有的基础设施符合这些参数？为了安全起见，它抛出这个错误。

用户始终负责生成客户端令牌。但在这种情况下，用户实际上是 Terraform。这让我们有些惊讶和宽慰，因为这意味着它很可能不是我们的任何包装自动化。但是我们仍然需要确定。我们做的第一件事是试图找出使用了什么客户端令牌，以及它是否实际上使用了两次。

幸运的是，这个错误给了我们一个`RequestId`,我们使用 CloudTrail 来查找它。在该请求的`requestParameters`字段中，我们能够找到所使用的 ClientToken。

```
"requestParameters": {
      "CreateLaunchTemplateRequest": {
        "LaunchTemplateName": "sidekiq-worker-green_staging02-launch_template20220510183630312700000005",
        "LaunchTemplateData": {
          "UserData": "<sensitiveDataRemoved>",
          "SecurityGroupId": [
            {
              "tag": 1,
              "content": "sg-0743bcbd08cadba1d"
            },
            {
              "tag": 2,
              "content": "sg-6daf421e"
            },
            {
              "tag": 3,
              "content": "sg-0818108e24803a418"
            }
          ],
          "ImageId": "<removed>",
          "BlockDeviceMapping": {
            "Ebs": {
              "VolumeSize": 100
            },
            "tag": 1,
            "DeviceName": "/dev/sda1"
          },
          "IamInstanceProfile": {
            "Name": "asg-staging02-20220510183628901400000003"
          },
          "InstanceType": "m4.2xlarge"
        },
        "ClientToken": "terraform-20220510183630312700000006"
      }
```

它看起来足够随机，与 Terraform 经常用来生成随机值的格式相同。

它显然不是我们制造的。然后，我们决定搜索这段时间内的所有请求，看看是否有任何请求具有相同的客户机令牌。

果然，第二个请求重用了相同的 Terraform 模块(我们编写的)来生成第二个 ASG 和启动模板。

```
"requestParameters": {
      "CreateLaunchTemplateRequest": {
        "LaunchTemplateName": "biexport-worker-green_staging02-launch_template20220510183630312700000005",
        "LaunchTemplateData": {
          "UserData": "<sensitiveDataRemoved>",
          "SecurityGroupId": [
            {
              "tag": 1,
              "content": "sg-0743bcbd08cadba1d"
            },
            {
              "tag": 2,
              "content": "sg-6daf421e"
            },
            {
              "tag": 3,
              "content": "sg-0818108e24803a418"
            }
          ],
          "ImageId": "<removed>",
          "BlockDeviceMapping": {
            "Ebs": {
              "VolumeSize": 200
            },
            "tag": 1,
            "DeviceName": "/dev/sda1"
          },
          "IamInstanceProfile": {
            "Name": "asg-staging02-20220510183629183700000003"
          },
          "InstanceType": "m5.xlarge"
        },
        "ClientToken": "terraform-20220510183630312700000006"
      }
```

如您所见，请求中存在差异，但是客户端令牌保持不变。现在我们开始抓狂，认为这可能是我们的代码，但我们仍然看不出是怎么回事。

# 生成客户端令牌

正如我前面提到的，从 AWS 的角度来看，生成客户机令牌是用户的工作。从我们的角度来看，那个用户是 Terraform。我们的团队无论如何都不是围棋专家(尽管我们正在寻找一些好的项目来尝试一下)。我们有很多兴趣)。

但是为了理解客户机令牌是如何生成的，我们必须看一下 Terraform 源代码。经过一点挖掘，我们发现代码作为一个助手函数存在于 [terraform-plugin-sdk](https://github.com/hashicorp/terraform-plugin-sdk/blob/main/helper/resource/id.go) 中。

```
func PrefixedUniqueId(prefix string) string {
    // Be precise to 4 digits of fractional seconds, but remove the dot before the
    // fractional seconds.
    timestamp := strings.Replace(
        time.Now().UTC().Format("20060102150405.0000"), ".", "", 1) idMutex.Lock()
    defer idMutex.Unlock()
    idCounter++
    return fmt.Sprintf("%s%s%08x", prefix, timestamp, idCounter)
}
```

在这个函数中，作者生成精确到秒的时间戳。多个执行可能会在同一个第二时间跨度内命中，但是该值还会附加一个计数器。

计数器在一个[互斥体](https://gobyexample.com/mutexes)中，所以`idCounter`的值在执行中共享，互斥体防止并发执行。这个函数不可能两次生成同一个客户端令牌。但这并不意味着调用客户机令牌的函数没有存储它，也没有可能重用它。

# 包裹

我们的故事暂时到此结束。我们开始调查客户端令牌是如何以及在哪里被使用的，但是由于我们强烈地感觉到这将与某种类型的地形问题有关，我们必须改变解决方案。我们不打算运行定制补丁版本的 Terraform。我们不会当场升级。我们不会等到公关获得批准、合并和发布，这样我们就走上了一条不同的补救之路。

我们当前的解决方案是为冲突的两个资源指定一个`[depends_on](https://www.terraform.io/language/meta-arguments/depends_on)`参数。其他时候，当错误发生时，我们注意到总是这两个资源发生冲突，所以希望`depends_on`标志可以防止它们被并行创建。

到目前为止，这种希望得到了回报，我们没有在任何环境中再次看到错误。但是出于病态的好奇，我们计划继续研究这个问题。它可能会让我们发现 Terraform 中的一个 bug，对某个特定节的工作方式产生误解，或者甚至是我们自己围绕 Terraform 的自动化。

谁知道呢？如果我们找到了它，您一定会找到本文的第 2 部分！