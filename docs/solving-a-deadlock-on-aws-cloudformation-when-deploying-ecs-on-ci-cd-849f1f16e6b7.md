# 在 CI/CD 上部署 ECS 时，解决 AWS CloudFormation 上的死锁

> 原文：<https://betterprogramming.pub/solving-a-deadlock-on-aws-cloudformation-when-deploying-ecs-on-ci-cd-849f1f16e6b7>

## 创建一个验证步骤来决定 CodeDeploy 是否触发任务修订更新

![](img/add94a107781aeab3633acc24c391f60.png)

尼古拉斯·斯旺森在 [Unsplash](https://unsplash.com/search/photos/clouds?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

这篇文章假设你对 [AWS 服务](https://aws.amazon.com/)有很好的了解，比如 [CloudFormation](https://aws.amazon.com/cloudformation/) 、 [ECS](https://aws.amazon.com/ecs/) 和代码(构建/部署/管道)，所以我不会深究任何资源，因为这不是这篇文章的目的。也就是说，你知道该怎么做。

# 让我们想象一下

您已经创建了一个 CloudFormation 模板，它将启动 EC2 集群来运行一些微服务和[代码管道](https://aws.amazon.com/codepipeline/)来将映像推送到 ECR 并将更新部署到 ECS。

到目前为止，一切看起来都很顺利。

这是您第一次部署模板，一切都开始得很好，创建了 EC2 实例，创建了 ECS 集群，创建了 ECR repo，创建了 ECS 服务和任务定义，创建了代码管道的所有阶段…

但是，过了一会儿，您注意到您的 CloudFormation 模板仍在运行，并且永远不会完成。几个小时后，一切都被回滚，你不知道为什么。

# 问题是

部署 ECS 微服务时，您需要一个指向 ECR 报告中 Docker 映像的任务定义和一个带有任务定义配置的服务。

这个工作流程看起来不错，但是我们缺少了一个步骤——如果我们有一个完全自动化的模板，我们将如何推送 Docker 映像？

我们的线人会处理的。我们的 CloudFormation 模板已经为我们创建了一切，与 git 的集成以拉代码，CodeBuild 以构建 Docker 映像并将其推送到 ECR，CodeDeploy 以触发任务定义修订，这一切都很完美！

*没那么快……*

使用 CloudFormation 将你的基础设施写成代码有时会很棘手，你不能在资源创建期间执行验证，这可能会导致竞争条件和死锁。

# 真正的问题是

云形成提供资源是基于条件和依赖关系的(你已经知道了)*。*

基于您的模板，将并行创建微服务和 CI/CD 资源，在上述问题之后，我们有两种情况:

## 场景 1

任务定义将在 CI/CD 构建阶段完成之前*创建，这将导致任务定义无法稳定，因为 ECR 中缺少映像。*

幸运的是，任务定义将继续尝试，直到它找到一个映像或者云结构请求回滚。CI/CD 构建阶段完成，任务定义找到要运行的映像，然后 CI/CDs 部署阶段触发任务定义修订更新。

也许你现在看到问题了。还没有？别担心，继续走。

## 场景 2

CI/CD 构建阶段首先完成，在任务定义创建之前，这真的很好。

任务定义将有一个 Docker 映像可用于稳定服务，但我们仍有 CI/CD 部署阶段要运行，它将触发任务定义修订更新。

如果部署阶段在任务定义创建之前运行，它将使代码管道执行失败，但不会使 CloudFormation 堆栈失败。

这就是竞争条件/死锁所依赖的地方。

当 CloudFormation 创建一个任务定义时，它将从一个代码修订开始，并等待它的稳定信号来完成堆栈创建。

但是，如果您并行运行 CI/CD，并且您正在触发任务定义修订更新，它会将代码修订提升到两个，导致 CloudFormation 永远等待代码修订一稳定下来。

# 解决办法

最后是解决方案，也是最激动人心的部分。

我尝试了资源顺序的每一种组合来解决这个竞争条件问题，直到我意识到主要问题出在部署阶段，所以只要我修复了部署阶段，这应该就可以正常工作了。

如果您遵循了官方的 [ECS 持续部署教程](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-cd-pipeline.html)，那么在您的`buildspec.yml`文件中应该有类似这样的内容

```
version: 0.2phases:   
  pre_build:     
    commands:       
      - echo Logging in to Amazon ECR...        
      - $(aws ecr get-login --region $AWS_DEFAULT_REGION --no-include-email)       
      - REPOSITORY_URI=*${ACCOUNT_ID}.dkr.ecr.us-west-2.amazonaws.com/${ECR_REPO_NAME}*
      - COMMIT_HASH=$(echo $CODEBUILD_RESOLVED_SOURCE_VERSION | cut -c 1-7)       
      - IMAGE_TAG=${COMMIT_HASH:=latest}   
  build:     
    commands:       
      - echo Building the Docker image...                 
      - docker build -t $REPOSITORY_URI:latest .       
      - docker tag $REPOSITORY_URI:latest $REPOSITORY_URI:$IMAGE_TAG
  post_build:     
    commands:
      - echo Pushing the Docker images...       
      - docker push $REPOSITORY_URI:latest       
      - docker push $REPOSITORY_URI:$IMAGE_TAG       
      - echo Writing image definitions file...       
      - printf '[{"name":"*${ECR_REPO_NAME}*","imageUri":"%s"}]' $REPOSITORY_URI:$IMAGE_TAG > imagedefinitions.json 
artifacts:     
  files: imagedefinitions.json
```

我的解决方案是创建一个验证步骤来决定 CodeDeploy 何时触发任务版本更新，何时不触发。

这是通过在执行结束时不提供`imagedefinitions.json`来实现的。

好的，但是……这里决定的标准是什么？

我想到了在执行期间检查存储库是否已经有了 Docker 映像的想法。如果存储库至少有一个映像，则这不是第一次运行。AWS CLI 将在这里为我们完成这项工作。

# 仔细讨论

使用以下命令查询 ECR 存储库映像:

```
aws ecr list-images --repository-name ${ECR_REPO_NAME} --max-items 1
```

我们只需要一个项目来决定存储库是否有图像，选项`--max-items 1`将加快响应速度。

这个命令的结果将是一个 JSON，带有一个名为`imageIds`的顶层对象，它将是一个对象数组或一个空数组。

```
{     
  "imageIds": [         
    {             
      "imageDigest": "sha256:236ce1ed44...",             
      "imageTag": "latest"         
    }
  ]
}or{
  "imageIds": []
}
```

有了这个结果，我们现在有了验证第一次运行所需的所有数据。

我选择了值`imageDigest`作为决定存储库是否为空的键。如果您对更多的项目运行该命令，您将会看到一些图像没有`imageTag` 属性，使得`imageDigest`值在这里更有保证。

现在，我们需要做的，就是创造一个条件来提供，或者不提供，`imagedefinitions.json` 文件。

```
version: 0.2phases:   
  pre_build:     
    commands:       
      - echo Logging in to Amazon ECR...        
      - $(aws ecr get-login --region $AWS_DEFAULT_REGION --no-include-email)       
      - REPOSITORY_URI=*${ACCOUNT_ID}.dkr.ecr.us-west-2.amazonaws.com/${ECR_REPO_NAME}*
      - COMMIT_HASH=$(echo $CODEBUILD_RESOLVED_SOURCE_VERSION | cut -c 1-7)       
      - IMAGE_TAG=${COMMIT_HASH:=latest}   
  build:     
    commands:
      - |
        echo Checking Repository Images
        **REPOSITORY_IMAGES=$(aws ecr list-images --repository-name ${ECR_REPO_NAME} --max-items 1)**

        echo Building the Docker image...
        docker build -t $REPOSITORY_URI:latest .
        docker tag $REPOSITORY_URI:latest $REPOSITORY_URI:$IMAGE_TAG echo Pushing the Docker images...
        docker push $REPOSITORY_URI:latest
        docker push $REPOSITORY_URI:$IMAGE_TAG **# Validate if JSON response has imageDigest value
        if echo "$REPOSITORY_IMAGES" | grep -q "imageDigest"; then
          echo Repository contain images and Deploy should run** echo Writing image definitions file...printf '[{"name":"*${ECR_REPO_NAME}*","imageUri":"%s"}]' $REPOSITORY_URI:$IMAGE_TAG > imagedefinitions.json **else
          echo Repository DOES NOT contain images, DO NOT run deploy
        fi**
artifacts:     
  files: imagedefinitions.json
```

# 变更细分

```
REPOSITORY_IMAGES=$(aws ecr list-images --repository-name ${ECR_REPO_NAME} --max-items 1)
```

`$(command)`语法将结果存储在变量中。即使结果是 JSON 格式的，它仍然是纯文本，允许我们执行字符串函数。

```
if echo "$REPOSITORY_IMAGES" | grep -q "imageDigest";
```

`[grep](https://ss64.com/bash/grep.html)` [命令](https://ss64.com/bash/grep.html)允许我们在文件中搜索文本，而`echo "$VALUE" |` 将为`grep`模拟一个文件，如果找到则返回`0`，如果没有找到则返回`1`。

在条件中使用这种方法，我们可以处理何时创建或不创建一个`imagedefinitions.json`文件。该文件的存在将决定 CodeDeploy 是否运行。

# 结论

没什么要说的了，我希望这个场景已经解释清楚了，并且这个解决方案对你有帮助。

你可以在我的 [GitHub 账号](https://github.com/cferrera/medium-stories/tree/master/aws-deadlock-ecs)中找到构建规范文件。