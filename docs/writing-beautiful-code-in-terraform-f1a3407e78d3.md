# 如何在 Terraform 中写出漂亮的代码

> 原文：<https://betterprogramming.pub/writing-beautiful-code-in-terraform-f1a3407e78d3>

## 当你不能用你喜欢的语言编码时

![](img/bd04437e58e292dd1add6fe700b2f771.png)

图片作者。徽标的版权属于 Terraform 和 Azure

这篇文章完全不是关于写漂亮的代码，而是一个简单的需求如何变得漂亮(双关语！)Terraform 恐怖。

我们都写过不值得骄傲的代码。有时候，这段代码是我们拥有的最好的“瞬间”解决方案——并且在 git 中永远存在——作为我们必须弯曲多少规则和逻辑来使事情工作的标志。我可以自豪地说，我也贡献了迫使我走上耻辱之路的代码！

在我看来，很难找到更好的替代方案来完成同样的任务。

我们担心网络安全，有一个默认的白名单政策，包括著名的容器注册表。这导致了在我们的注册中心中需要一些基本容器，然后使用 terragrunt 配置将它们传递到我们不同的环境中。

[](https://www.techtarget.com/searchsecurity/definition/application-whitelisting) [## 什么是应用程序白名单？

### 应用程序白名单是指定批准的软件应用程序或可执行文件的索引的做法…

www.techtarget.com](https://www.techtarget.com/searchsecurity/definition/application-whitelisting) 

我们必须——手动——首次将它们添加到 Azure 注册表中，这样我们就可以使用`terra{form, grunt}`将它们包含在不同的环境中。

位于项目根目录中的 nginx 最小 alpine dockerfile 文件

是的，羞耻分请继续向右滚动！

可爱的地形地貌

我们使用 Azure CLI 命令将映像导入到环境中，使其出现在 ACR 注册表中。接下来，我们在 Dockerfile 参数中定义一个 Dockerfile(包含图像及其版本),并在我们的注册表中标记它。将来，如果其他应用程序想要访问 Nginx 容器，他们可以简单地从我们的 Azure 注册表中取出它。

order 文件按以下顺序组织:

```
docker-images
- nginx
- - Dockerfile
```

我可以试着解释一下`add-to-docker-registry-via-terraform.tf`文件中漂亮的代码。我们按照 terragrunt 的配置，迭代出现在`docker-images`文件夹中的 docker 图像。接下来，我们选取名称为`Dockerfile`的文件，在名称上加上 ACR 地址，然后寻找图像文件夹的名称(这是图像本身的名称！).

最后，我们使用`[trimsuffix](https://www.terraform.io/language/functions/trimsuffix)`、`[trimspace](https://www.terraform.io/language/functions/trimspace)`和`[split](https://www.terraform.io/language/functions/split)`来结束。该过程可分为以下几个部分:

*   `${trimsuffix(each.value, "/Dockerfile")}`:我们将`/Dockerfile`从文件路径中移除。
*   `file("$var.images_root_directory/${each.value}")`:我们通过完整路径读取 dockerfile 文件。
*   `split("\n", file("$var.images_root_directory/${each.value}"))[0]`:我们通过换行符分割文件，并挑选出与版本匹配的第一部分。
*   `trimspace(split("\n", file("$var.images_root_directory/${each.value}"))[0])`:删除任何前导或结尾空格。
*   `split(" ", trimspace(split("\n", file("$var.images_root_directory/${each.value}"))[0]))[1]`:我们用空白分割 docker 文件的第一行，并从中选取`VERSION=alpine`部分。
*   `trimspace(split(" ", trimspace(split("\n", file("$var.images_root_directory/${each.value}"))[0]))[1])`:我们删除任何开头或结尾的空白。
*   `${split("=", trimspace(split(" ", trimspace(split("\n", file("$var.images_root_directory/${each.value}"))[0]))[1]))[1]}`:我们通过`=`操作符拆分`VERSION=alpine`，从版本中提取标签并附加到图像地址的末尾。

唷！没那么难。

这确实让人觉得这段代码本可以写得更好，或者这种情况本可以有更好的解决方案。

关于第一部分，Terraform 的配置语言的语法限制使得很难想到更好的方法来做这件事(就此而言，有更好的方法吗？).

专注于第二部分，我想从专家那里了解我是否可以做得更好，或者在这种情况下你会如何解决这个问题。

我发现用 Terraform 写好代码很难。命令式语言(如 Python)的语法优势在 HCL 中消失了。它确实解决了它的设计目的，但是现在有了更好的替代产品，比如 pulumi 或 terraform cdk。他们让你用自己选择的语言编码，这样你就能写出漂亮、易读的代码。

[](https://www.pulumi.com/) [## Pulumi -通用基础设施代码

### 欢迎从 60 多家云提供商中选择所有体系结构，包括公共、私有和混合体系结构…

www.pulumi.com](https://www.pulumi.com/) [](https://www.terraform.io/cdktf) [## 哈希公司的 CDK

### 搜索 Terraform 文档面向 Terraform 的云开发工具包(CDKTF)允许您使用熟悉的编程…

www.terraform.io](https://www.terraform.io/cdktf)