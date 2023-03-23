# 为 AWS Lambda 创建 Python OpenCV 层

> 原文：<https://betterprogramming.pub/creating-a-python-opencv-layer-for-aws-lambda-f2d5266d3e5d>

## 为 Python 的 OpenCV 包装器创建 AWS Lambda 层的简单方法

![](img/776dfeace346e0f1544388f2377a2a80.png)

法尔扎德在 [Unsplash](https://unsplash.com/photos/p-xSl33Wxyc) 上的照片。

[Lambda](https://aws.amazon.com/lambda/) 是 AWS 提供的一款无服务器产品，无需配置服务器即可运行可伸缩代码。每个 Lambda 环境(对应于不同版本的兼容语言)都预装了一些软件包。

然而，当我们需要一个没有提供的包时会发生什么呢？有几种方法可以解决这个缺点，其中之一是使用 [Lambda 层](https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html)。

在下一节中，我们将讨论一个编程解决方案，为需要 [OpenCV](https://pypi.org/project/opencv-python/) 的 Python 3.7 应用程序创建一个 Lambda 层。

# 1.在兼容的操作系统中安装库

Lambda 函数运行在亚马逊 Linux (AMI)上。因此，为了确保我们的 OpenCV 版本能够在 Lambda 上运行，我们需要为 AMI 编译它。一种方法是在 EC2 实例上安装这个库。

然而，为了方便起见，我们将使用 Docker。下面的 Docker 文件创建了一个安装了 OpenCV 的 Docker 映像:

# 2.将相关的安装文件从 Docker 复制到本地

一个已安装的 Python 库创建了它需要运行的工件。在 OpenCV 的情况下，这些工件(文件)可以在`/packages/opencv-python-3.7/python/lib/python3.7/site-packages`中找到。为了让我们的 Lambda 能够使用这个库，它需要能够访问那些文件所在的位置。

因此，我们将这些文件从 Docker 映像复制到我们自己的计算机上，使用以下命令创建 Lambda 层:

```
docker build --tag=lambda-layer-factory:latest .docker run --rm -it -v $(pwd):/data lambda-layer-factory cp -r /packages/opencv-python-3.7/ /data
```

这将在运行该命令的机器(我们的本地计算机)上创建一个名为`opencv-python-3.7`的文件夹。

# 3.Zip 文件夹

创建 Lambda 层的界面需要一个包含所有相关工件的 zip 文件。为了压缩文件，我们在`opencv-python-3.7`目录中执行下面的命令:

```
zip -r opencv-python-37.zip python/
```

注意:为了让层工作，zip 文件(未压缩时)需要创建一个名为`python`的根文件夹。不同 Lambda 运行时环境所需的路径在文档中进行了概述[。](https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html)

# 4.上传压缩文件到 S3

我们将在下一步中用于创建图层的命令要求 Zip 文件位于 S3。为了将文件上传到 S3，我们使用下面的命令创建一个存储桶:

```
aws s3 mb s3://my-unique-bucket-name
```

然后，我们使用以下命令上传文件:

```
aws s3 cp opencv-python-37.zip s3://my-unique-bucket-name
```

# 5.创建层

最后，我们使用以下命令创建层:

```
aws lambda publish-layer-version --layer-name python-opencv-37 --description "Open CV for Python 3.7" --content S3Bucket=my-unique-bucket-name,S3Key=opencv-python-37.zip --compatible-runtimes python3.7
```

# 使用我们的层

可以通过几种不同的方式将层添加到 Lambda 函数中，包括:

1.  使用带有`--layers`标志的`aws lambda update-function-configuration`命令添加到现有函数，如文档中的[所述。](https://docs.aws.amazon.com/cli/latest/reference/lambda/update-function-configuration.html)
2.  使用 AWS GUI 编辑附加到功能的层。
3.  我最喜欢的方法是使用 [SAM](https://aws.amazon.com/serverless/sam/) 在创建时定义 Lambda 的属性。下面的脚本显示了如何在 Lambda 函数中包含我们刚刚创建的层:

```
YourLambdaName:
  Type: AWS::Serverless::Function
  Properties:
  CodeUri: src/
  Handler: app.lambda_handler
  Runtime: python3.7
  MemorySize: 2048
  Timeout: 30
  Environment:
    Variables:
      MY_FAVORITE_NUMBER: 42
  Policies:
    - AWSLambdaExecute # Managed Policy
    - Version: '2012-10-17' # Policy Document
      Statement:
        - Effect: Allow
          Action:
            - logs:CreateLogGroup
            - logs:CreateLogStream
            - logs:PutLogEvents
            - lambda:Getlayerversion
         Resource: '*'
  Layers:
    - arn:aws:lambda:MY-REGION:MY-ACCOUNT:layer:python-opencv-37:1
```

我将在下面的教程中详细介绍如何使用 SAM 创建 Lambda:

[](https://medium.com/better-programming/private-serverless-rest-api-with-lambda-using-sam-2eb31864b243) [## 使用 SAM 的 AWS Lambda 私有无服务器 REST APIs

### 使用 Lambdas 快速创建无服务器微服务

medium.com](https://medium.com/better-programming/private-serverless-rest-api-with-lambda-using-sam-2eb31864b243) 

# 结论

我们已经讨论了一种以编程方式创建 Lambda 层的方法。这是在安装 Python-OpenCV 的上下文中展示的，但是可以扩展到其他编程语言和库。

# 参考

1.  Dockerfile 基于这个[项目](https://github.com/iandow/opencv_aws_lambda)中的一个。
2.  [这个](https://stackoverflow.com/questions/64016819/cant-use-opencv-python-in-aws-lambda) StackOverflow 答案给出了创建图层的一些见解。