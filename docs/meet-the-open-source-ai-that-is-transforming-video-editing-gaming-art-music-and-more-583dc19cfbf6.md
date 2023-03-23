# 如何在亚马逊 SageMaker 上部署稳定的扩散模型

> 原文：<https://betterprogramming.pub/meet-the-open-source-ai-that-is-transforming-video-editing-gaming-art-music-and-more-583dc19cfbf6>

## 认识正在改变视频编辑、游戏、艺术、音乐等世界的开源人工智能！

![](img/d50fcdaccd1af032e7158e30508b77d6.png)

用 AWS 上的稳定扩散 1.4 在 14 秒内从文字“独角兽在外太空飞”生成。

# 为什么将文本转化为图像的能力如此重要？

想象一下，如果你可以无限制地免费接触图形艺术家，你的作品会发生怎样的变化。一个极富创造力，甚至能提出新想法的人？可以将文本转化为艺术的人工智能模型在过去几年里有了长足的进步，变得非常容易获得。

因此，许多在过去成本高昂的艺术相关任务现在变得简单、快捷，而且几乎免费。这开启了创造力和创新的各种选择，我们刚刚开始看到这种转变在几个不同行业的影响。视频编辑公司正在提供基于人工智能的服务，以自动添加图形或编辑视频的方式，这在以前需要制作人员。

游戏开发者可以很容易地生成新的图形，有些会带来新的想法，这大大提高了开发速度。新公司可以让人工智能免费为一个标志产生数百个想法，而不是付钱给昂贵的设计公司。作者可以很容易地为小说的每一章制作封面，甚至是图片，而在以前，这样的图片成本很高。

虚拟现实公司承诺，在不久的将来，只需几个字，你就可以在 3D 空间中想象出任何物体。这样的例子不胜枚举，而且每天都在以新的方式增长。

![](img/e2b6a9de330d70b0d093a9a20d9fd410.png)

文字提示:“一杯俯瞰星空的茶”

# 什么是生成式 AI？

说几句话(“伯爵茶，热”)并让计算机将你的话转化为真实图像的能力就像是对未来的一瞥。这正是你现在可能做到的，这要归功于新一波的生成式人工智能模型，如稳定扩散。这些模型诞生于最初用于文本翻译的变形金刚，正在以极快的速度发展和改进。

在过去的几个月里，开源社区已经创建了许多不同的工具，导致了质量和性能的巨大飞跃。我们在瞬间创造壮观的艺术或专门构建的图像的能力从未如此之强。稳定扩散是引领这场革命的最佳模式之一。

# 为什么稳定扩散不一样？

稳定扩散是完全开源的。许多这些生成式人工智能模型都在付费墙后面，部分原因是它们需要大量的时间和金钱来创建。稳定扩散在亚马逊网络服务上使用 256 个 Nvidia A100 GPUs 进行了 150，000 GPU 小时的训练，费用为 600，000 美元。

然而，与之前的趋势不同，稳定扩散在 2022 年由稳定人工智能免费开源发布，包括模型本身和用于创建它的代码。与此同时，Stability AI 在 2022 年末筹集了 1.01 亿美元的资金。

从那以后，使用稳定扩散生成的人工智能艺术一直在网上和社交媒体上掀起波澜，因为这种模型相对容易使用，可以在大多数消费者可用的商品硬件上运行。生殖人工智能的力量掌握在你手中。

![](img/099482437039d6900328b0237200c452.png)

文字提示:“权力在人工智能手中”

# 我们开始吧！如何部署稳定扩散

现在我们有了稳定扩散的背景，是时候动手了。只需几个步骤，我们就可以在亚马逊 SageMaker 上部署[稳定扩散模型](https://huggingface.co/CompVis/stable-diffusion-v1-4)。这个 [GitHub](https://github.com/aws-samples/amazon-sagemaker-image-based-transformers-examples) repo 包含其他任务和模型的代码，但是对于本文，我们将重点关注[文本到图像](https://github.com/aws-samples/amazon-sagemaker-image-based-transformers-examples/tree/main/text-to-image-custom-container)任务。我们的游戏计划如下:

*   从拥抱脸下载模型
*   创建自定义推理脚本
*   创建一个 Docker 容器，打包模型和推理脚本
*   将容器部署到 SageMaker 端点
*   测试模型

注意:[笔记本](https://github.com/aws-samples/amazon-sagemaker-image-based-transformers-examples/blob/main/text-to-image-custom-container/Text%20to%20Image%20Container%20Build.ipynb)将详细说明将稳定扩散模型部署到 AWS SageMaker 端点的步骤，包括本地模型测试。这篇文章仅仅强调和解释了笔记本中最相关的部分。部署模型时，请参考笔记本。

## 下载模型

我们从下载模型并将其保存在本地开始。这允许我们将权重打包到 docker 容器中，并且消除了每当端点启动时再次下载模型的需要。这将删除端点的所有外部依赖项，同时加快端点的启动时间。

在下载模型之前，您需要创建一个 HuggingFace 帐户，并接受稳定扩散回购的条款和条件。在下载模型时，当出现提示时，您将使用相同的用户名。

这个模型很大，所以你需要 git 的大文件存储(LFS)扩展。我们可以使用以下命令安装并下载该模型:

```
git lfs install
git clone https://huggingface.co/CompVis/stable-diffusion-v1-4
git lfs pull
```

注意，这下载了我们需要的整个 git 回购历史和模型权重。有关在端点上打包之前删除除模型本身之外的所有内容的说明，请参阅笔记本。

## 创建自定义推理脚本 predictor.py

现在我们有了模型，我们可以编写推理脚本，使用模型来响应请求。关于定制 SageMaker 端点如何工作的更多信息，你可以参考这个[研讨会](https://sagemaker-workshop.com/custom/containers.html)。我们从那个研讨会中获得了基本的 web 服务器脚本，因为它们为处理 web 请求提供了一个良好的 SageMaker 兼容的基础。这篇文章重点介绍了我们为实现稳定扩散而定制的部件。

首先，我们导入 PyTorch 和 HuggingFace 库:

```
import torch
from diffusers import StableDiffusionPipeline
```

在[笔记本](https://github.com/aws-samples/amazon-sagemaker-image-based-transformers-examples/blob/main/text-to-image-custom-container/Text%20to%20Image%20Container%20Build.ipynb)中，我们还提供了本地测试说明，用于在将模型部署到端点之前对其进行测试。当进行本地测试时，我们在 GPU 实例上运行我们的笔记本。请注意，该模型可以在基于 CPU 的实例上运行，但速度会慢得多，可能会慢 60 倍。让我们的脚本从我们刚刚用 git lfs 创建的本地目录中加载模型。

```
local_model_dir = "./stable-diffusion-v1-4"
pipe = StableDiffusionPipeline.from_pretrained(local_model_dir)
```

SageMaker 需要定制代码来响应`/ping`和`/invocations`。我们把我们的`/invocation`逻辑，实际的推理逻辑，放在转换函数中。这是由 Python web 框架 flask 提供的。这里，我们将提示从请求中提取出来，并将提示传递给算法。请注意，在完整的笔记本中，我们包括对几个附加超级参数的支持。

现在我们有了一个图像，我们将它保存到 S3，并将图像位置发送回客户端。我们可以直接发送回图像，但是对于常见的用例，我们发现最好将它放在 S3，以便可以在未来的任务中轻松引用。

既然我们已经定义了推理脚本，并且下载了模型，我们就可以打包我们的容器了。还需要一些其他文件，这些文件来自标准定制容器[车间](https://sagemaker-workshop.com/custom/containers.html)。这些文件是 [serve](https://github.com/aws/amazon-sagemaker-examples/blob/main/advanced_functionality/scikit_bring_your_own/container/decision_trees/serve) 的，其中有一些关于运行多少线程的逻辑。在我们的例子中，我们更改 model_server_workers = 1，以限制端点仅将单个模型加载到 GPU 中。我们还有一个 [nginx](https://github.com/aws/amazon-sagemaker-examples/blob/main/advanced_functionality/scikit_bring_your_own/container/decision_trees/nginx.conf) 配置文件和一个用于 [wsgi](https://github.com/aws/amazon-sagemaker-examples/blob/main/advanced_functionality/scikit_bring_your_own/container/decision_trees/wsgi.py) 的小包装器，用来启动 web 服务器和加载我们的推理脚本。

## 创建自定义 Docker 容器

现在我们已经有了容器所需的所有文件，我们创建 Dockerfile。我们将使用标准的 [Dockerfile](https://github.com/aws/amazon-sagemaker-examples/blob/main/advanced_functionality/scikit_bring_your_own/container/Dockerfile) 并稍作修改，它为我们的模型安装了特定的 PyTorch 和 HuggingFace 库，并为 s3 安装了用于图像生成的 boto3。

```
RUN pip install **--**upgrade torch diffusers transformers scipy boto3
```

接下来，我们运行 Docker build 来[构建容器](https://docs.aws.amazon.com/sagemaker/latest/dg/adapt-inference-container.html#byoc-inference-build-register)。一旦构建完成，我们将它推送到 AWS 弹性容器注册中心(ECR)，它就可以部署了！

```
docker build  -t ${algorithm_name} .
docker push ${fullname}
```

## 定义和部署模型

现在我们已经准备好了容器，我们需要为 SageMaker 设置一些配置。这包括模型对象和端点配置。

我们将从 [SageMaker 模型对象](https://docs.aws.amazon.com/sagemaker/latest/dg/realtime-endpoints-deployment.html#realtime-endpoints-deployment-create-model)开始，它包括一个惟一的模型名称、上一步中容器映像构建的位置以及端点角色。

接下来，我们定义[端点配置](https://docs.aws.amazon.com/sagemaker/latest/dg/realtime-endpoints-deployment.html#realtime-endpoints-deployment-create-endpoint-config)，并将实例类型设置为基于 GPU 的 g4dn.2xlarge。我们还将自动缩放组设置为 1，这意味着将只添加一个最大实例。这有助于降低测试成本。

最后，我们[使用模型对象和端点配置来部署模型](https://docs.aws.amazon.com/sagemaker/latest/dg/realtime-endpoints-deployment.html#w1127aac27c17b9b9b7c29)。请注意，当端点启动时，此步骤可能需要 10 分钟。

```
%%time
import time
endpoint_name = 'tti-endpoint' + strftime("%Y-%m-%d-%H-%M-%S", gmtime())
print('Endpoint name: ' + endpoint_name)
create_endpoint_response = sm_client.create_endpoint(
EndpointName=endpoint_name,
EndpointConfigName=endpoint_config_name)
print('Endpoint Arn: ' + create_endpoint_response['EndpointArn'])
resp = sm_client.describe_endpoint(EndpointName=endpoint_name)
status = resp['EndpointStatus']
print("Endpoint Status: " + status)
print('Waiting for {} endpoint to be in service...'.format(endpoint_name))
waiter = sm_client.get_waiter('endpoint_in_service')
waiter.wait(EndpointName=endpoint_name)
```

## 测试端点

一旦端点上线，我们就可以使用 SageMaker 中的[调用端点](https://boto3.amazonaws.com/v1/documentation/api/1.9.42/reference/services/sagemaker-runtime.html#SageMakerRuntime.Client.invoke_endpoint)功能对其进行测试。端点生成的图像将保存到前面步骤中定义的 S3。对于我们的测试，我们添加了几行代码，将图像从 S3 拉下来并显示在笔记本上，这样我们就可以看到我们的结果。

![](img/521fffd5de66f5cb77bd9a1da2333caf.png)

文字提示:“*一只狗在花丛中奔跑的照片*”

14 秒还不错！

![](img/76d19f5c360acfeb277e11bd5a8786fc.png)

文字提示:“*一只太空虎*”

# 结论

几乎每天都在宣布生殖人工智能的新进展，企业正处于这项技术的早期采用阶段。即使模型现在有多么强大，我们还没有看到任何接近其全部潜力的东西，这似乎是合理的。或许有一天，一本书分分钟被拍成电影。或许《星际迷航》全息甲板风格的生成式虚拟现实即将出现。想象一下会有什么样的可能性是令人兴奋的！

在本教程中，我们在 SageMaker 端点上部署了稳定扩散模型，展示了如何为自己部署和使用该模型。此外，同样的方法可以用于尝试其他稳定的扩散功能，如图像到图像(编辑现有图像)、图像修复(编辑现有图像的一部分)，甚至其他模型，如 OFA，用于基于文本的图片查询(可在 [GitHub](https://github.com/aws-samples/amazon-sagemaker-image-based-transformers-examples) 上获得)。

现在，用您部署的模型玩一玩，在视觉上扩展您的想象力，创造一些令人惊叹的艺术！

请留下任何问题或反馈的评论，并让我们知道你认为如何使用生成式人工智能。

这篇文章是由 AWS 的解决方案架构师贾斯汀·穆勒和 T2·阿米特·乔德里共同撰写的。