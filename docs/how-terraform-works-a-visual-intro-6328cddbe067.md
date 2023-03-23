# 地形如何工作:视觉介绍

> 原文：<https://betterprogramming.pub/how-terraform-works-a-visual-intro-6328cddbe067>

## 通过插图了解 Terraform

为了更新我的地形知识，我保存了一些图表。

在任何题目中，我总是发现创作插图的练习对学习很有帮助。当我必须向我的同事解释一些地形概念时，他们也很有帮助。

# Terraform 的主要特征

![](img/ddb0014b6615e00cbc9bf53ef2578616.png)

# 它是如何工作的

![](img/d78c3f74920b9c4cad36afd61db322f0.png)

# 核心工作流程

![](img/05e14dd1909c65ef26281f0eb440393f.png)

# terraform 基本 CLI |运行 terraform

您通常在包含**的目录中使用 CLI 运行 terrafom。** **tf 文件。**

```
terraform init
```

![](img/53db463c13eb7d4afb0b5310ca3f8b66.png)

```
terraform plan
```

![](img/a62108597d8e486efdd9eb09d319bfba.png)

```
terraform apply
```

![](img/52181ccd8987878b7dc8ebd2ff316167.png)

# HCL 语法

## 一点语言等级

![](img/42a75d84a1804d2a623fe8d86e5cb088.png)

## 块类型

![](img/9510300bb593873af79c4fc00602b5e8.png)

## 专用块

地形块

![](img/752eec59c249163613a70b21e0230597.png)

提供程序块

![](img/81e27cf3acf016e50fbc69fc1d4c1a65.png)

变量块

![](img/260782a85837f9a075f2bc4b8d9e91d8.png)

变量类型约束和验证嵌套块

![](img/c3ca8ccb1afa539bdbb433e2daa40c2f.png)

变量定义

![](img/e403054960e2d2676f58e6feb0782130.png)

查看后续文章:[图解的 Terraform 模块](https://medium.com/@mfundo/terraform-modules-illustrate-26cbc48be83a)

*如果你认为这是有帮助的，并想表示你的支持，这里是我的:*

```
 [**PayPal page**](http://paypal.me/mfndou)[**Buy me a coffee page**](https://www.buymeacoffee.com/mfundo)[**Ko-fi page**](https://ko-fi.com/mfundo)
```

*或者在推特上关注我:*[*https://twitter.com/mfund0_*](https://twitter.com/mfund0_)