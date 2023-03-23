# 使用 Google 云平台安排简单的 Go 应用程序工作负载

> 原文：<https://betterprogramming.pub/simple-scheduled-workloads-using-google-cloud-b23448a027ff>

## 关于使用 GCP、Docker 和 Terraform 的基础设施即代码来安排工作负载的简短教程

![](img/517e5005814b19934d73624b6fd608a6.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由[Towfiqu barb huya](https://unsplash.com/@towfiqu999999?utm_source=medium&utm_medium=referral)拍摄的照片

假设您需要定期运行一个作业。你会怎么做？

传统的方法是提供一个 VM(在某个地方)，运行 crontab，然后调用任何您想要的脚本/二进制文件。

这很简单，也很有效，但是有一些挑战…

*   如果你的机器坏了怎么办？
*   重新启动却停不下来怎么办？
*   如果有人拿到了怎么办？

我大概能想出十多个这样的问题。关键是这种方法有风险。

你只是不知道它什么时候会对你不利。迟早，它最终会发生，通常是在最不合适的时候！

因此，您现在面临另一个问题:监控和警报。那通常是两个问题。复杂性不断扩大。

当然之前解决过很多次了，但是何必呢？正如他们所说，时间就是金钱！在当今世界，那句话更准确地表述为“时间有错失机会的成本！”

进入谷歌云和手头的任务…

对于我当前最喜欢的项目，我需要定期处理新文档。任务是什么无关紧要。重点在于调度和运行这些工作负载。

经过一番挖掘，我得出了以下结论:

*   [谷歌云运行乔布斯](https://cloud.google.com/run/docs/create-jobs)
*   [谷歌云调度器](https://cloud.google.com/scheduler/docs/schedule-run-cron-job)
*   [谷歌神器注册表](https://cloud.google.com/artifact-registry/docs)

分为以下几个步骤:

*   将代码推送到 GitHubCI/CD(这部分我就不细说了，我在之前的[文章](https://MihaiBojin.com/projects/golang/best-practices-vs-time?utm_source=Medium&utm_medium=organic&utm_campaign=rss)里讲过一点)
*   构建代码
*   将二进制文件打包成 Docker 容器
*   将容器推送到 Google 工件注册表
*   将容器作为 Google Cloud 运行作业运行
*   定期运行作业以处理新工作

这里没有什么疯狂的，但是我想把这些都写下来也许有一天能帮助到某个人；从工作实例开始总是更容易！

> 开始之前，安装并配置 [GCloud CLI](https://cloud.google.com/sdk/docs/install-sdk) ！

## 构建 Golang 应用程序

Golang 是当今大多数工作负载的绝佳选择。它的生态系统欣欣向荣；你会发现一个几乎什么都有的图书馆；学习和编码都很简单；它非常适合生成静态链接库，从而产生小容器(例如，大约 13MB，而不是 Java 应用程序的 120MB 以上)

## 把它归档

让你的容器尽可能小的最好方法是使用 Docker 的[多阶段构建](https://docs.docker.com/develop/develop-images/multistage-build/#use-multi-stage-builds)。

粗略地说，这意味着在标准映像上构建应用程序，然后只将生成的工件复制到“scratch”(Docker 对基本上“只是主机操作系统的内核”的名称)。

docker 文件如下所示:

```
# syntax = docker/dockerfile:1.3
FROM golang:1.18-buster as builder

# Install and update root SSL certificates
RUN apt-get update \
     && export DEBIAN_FRONTEND=noninteractive \
     && apt-get -y install --no-install-recommends \
        ca-certificates \
     && apt-get clean -y \
     && update-ca-certificates

# Cache go dependencies to avoid downloading them on every rebuild
WORKDIR /app
ENV CGO_ENABLED=0
COPY go.* ./
RUN go mod download

# Copy local code to the container image.
COPY . .

# Build the binary using buildkit's cache to speed up rebuilds
# https://github.com/moby/buildkit/blob/master/frontend/dockerfile/docs/syntax.md#example-cache-go-packages
ARG TARGETOS
ARG TARGETARCH
RUN --mount=type=cache,target=/root/.cache/go-build GOOS=${TARGETOS} GOARCH=${TARGETARCH} go build -ldflags "-s -w" -v -o app .

# Make the smallest possible container
# https://docs.docker.com/develop/develop-images/multistage-build/#use-multi-stage-builds
FROM scratch
COPY --from=builder /app/app /app/app
COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
CMD ["/app/app"]
```

关于此步骤的几点注意事项:

*   您可以定义一个更简单的 Dockerfile 文件；这实际上取决于您希望重新构建它的次数——因为这意味着在 Google Cloud 中运行，您可以假设远程构建过程将在每次提交时运行和重新部署，并且很可能没有预先存在的缓存，因此所有这些都是不必要的；在本地，这有很大的不同，这就是为什么我这样实现！
*   我的代码需要通过 TLS 连接到其他服务；“scratch”缺少[根证书](https://en.wikipedia.org/wiki/Root_certificate)；上面的定义通过安装`ca-certificates`包并确保包含最新的证书来添加它们(`update-ca-certificates`；最后，它通过`COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/`将它们包含在最终结果中
*   如果你在 M1 Mac 上构建，记住`arm != x64`；因此，您的 Docker 构建命令应该针对您的预期运行时平台(在 GCP，即`x86_64`)的正确架构，即:`docker build --platform linux/x86_64 ...`

## 将容器推送到工件注册表

第一，创建回购；我用 Terraform 来做这个。 **IaaC FTW！**

```
variable "gcloud_region" {
  default = "europe-west3" # your region of choice
}
variable "gcloud_project" {
  default = "..." # your GCP project ID
}

# GCloud Run Jobs are not fully GA yet, and you need to use the beta provider for now
provider "google-beta" {
  project     = var.gcloud_project
  region      = var.gcloud_region
  zone        = format("%s-c", var.gcloud_region) # desired AZ, usually something like: 'europe-west3-c'
}

resource "google_artifact_registry_repository" "containers" {
  provider = google-beta
  location = var.gcloud_region
  repository_id = "containers" # the repo's name
  format = "DOCKER"
  lifecycle {
    prevent_destroy = true # avoids accidentally deleting all published images
  }
}
```

给定一个现有的存储库(在我的例子中是`REPO=containers`)，推送您的映像非常简单:

*   标记图像:`docker tag "${IMAGE}" "${REGION}-docker.pkg.dev/${GCP_PROJECT_ID}/${REPO}/${IMAGE}"`(显然，正确设置了上述变量)
*   配置 Docker 推送到 GCP
    `gcloud auth configure-docker --project ${GCP_PROJECT_ID} --quiet ${REGION}-docker.pkg.dev`
*   推送图像
    `docker push "${REGION}-docker.pkg.dev/${GCP_PROJECT_ID}/${REPO}/${IMAGE}"`
*   并且可选地，检查它是否被正确上传
    `gcloud artifacts docker images list "${REGION}-Docker.pkg.dev/${GCP_PROJECT_ID}/${REPO}/${IMAGE}"`

## 将容器作为 Google Cloud 运行作业运行

我不会在这里涉及很多细节；这应该是不言自明的；运行一个`gcloud`命令来定义作业。

不幸的是， [GCP 平台提供商](https://registry.terraform.io/providers/hashicorp/google/latest/docs)还不支持创造就业机会。

```
gcloud beta run jobs create ${JOB_NAME} \
  --region "${REGION}" \
  --image "${REGION}-docker.pkg.dev/${GCP_PROJECT_ID}/containers/roles.tech/${APP_NAME}:latest" \
  --tasks 1 \ # Only run one task
  --max-retries 0 \ # Do not retry
  --set-env-vars "KEY1=VALUE1,KEY2=VALUE2,..." # set any env vars required by the running code
```

> 这里有一个小提示:如果您通过 UI 定义作业，它将使用当前可用的任何图像；使用`...:latest`总是运行最新的 Docker 镜像，避免每次代码重建都必须更新作业的定义！

## 使用 CronJob 触发定期执行

再一次，地球形态来拯救我们了:

```
# retrieves the default service account
# this is an anti-pattern, and you're probably better off creating a dedicated service account
data "google_compute_default_service_account" "default" {
}
variable "job_name" {
  default = "..." # the name of your job ($JOB_NAME above)
}

resource "google_cloud_scheduler_job" "cronjob-name" {
  name             = "cronjob-name"
  schedule         = "0 * * * *" # run every hour on the dot
  time_zone        = "Etc/UTC" # UTC timezone
  attempt_deadline = "15s"

  retry_config {
    retry_count = 0 # do not retry, if the job fails
  }

  # trigger the Cloud Run Job by calling its handler via HTTPS, with an empty body
  http_target {
    http_method = "POST"
    uri         = format("https://%s-run.googleapis.com/apis/run.googleapis.com/v1/namespaces/%s/jobs/%s:run", var.gcloud_region, var.job_name, var.gcloud_project)
    body        = ""

    # authenticate via OAuth using the specified service account
    oauth_token {
      service_account_email = data.google_compute_default_service_account.default.email
    }
  }
}
```

就是这样！如果你需要一个简单可靠的机制来周期性地执行代码，只需要看看 [Google Cloud Run](https://cloud.google.com/run/docs/create-jobs) 和 [Terraform](https://www.terraform.io/) 就可以了。

谢谢大家！

```
**Want to Connect?**Opinions and suggestions are always welcome (find me on [Twitter](https://twitter.com/mihaibojin)).
```