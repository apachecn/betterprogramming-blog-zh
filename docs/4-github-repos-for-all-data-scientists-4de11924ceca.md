# 面向所有数据科学家的 4 个 GitHub Repos

> 原文：<https://betterprogramming.pub/4-github-repos-for-all-data-scientists-4de11924ceca>

## 免费的数据科学项目、自定义图像标签、谷歌图像下载器等等

![](img/3aaefcdf44e5976367a0be3143f5add2.png)

照片由来自[佩克斯](https://www.pexels.com/photo/eyeglasses-in-front-of-laptop-computer-1181253/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)的[克里斯蒂娜·莫里洛](https://www.pexels.com/@divinetechygirl?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄

GitHub 是科技爱好者真正的谷歌。我们可以在这里找到各种有见地的开源项目。在过去的五年里，我遇到了一些很棒的 GitHub 项目。这些项目对所有开发人员和数据科学家来说都非常方便。当你探索它们时，你会爱上它们。

本文将涵盖我在过去五年中遇到的四大 GitHub 管理库。

# 1.免费深度学习项目

GitHub stars:3500
分叉数量:1900

如果你是一个数据科学爱好者，并且正在寻找一些项目来练习你的深度学习技能，这个存储库可以成为你的一个神奇的盒子。

深度神经网络报告的[应用来自](https://github.com/jeffheaton/t81_558_deep_learning)[杰夫·希顿](https://github.com/jeffheaton)，他是一位专注于数据科学和人工智能的计算机科学家。他的 GitHub repo 有超过 50 个真实世界的项目。我通常参考他的 GitHub repo 来练习我的数据科学技能，它也可以帮助你从事数据科学相关的工作。

存储库 URL:[https://github.com/jeffheaton/t81_558_deep_learning](https://github.com/jeffheaton/t81_558_deep_learning)

# 2.使用 GPT-3 进行英语到 LaTeX 的翻译

GitHub stars:2000
分叉数量:477

你有没有考虑过，只要让你的计算机用英语编写数据库查询就可以了？GPT 3 号来了。GPT-3 的完整形式是*创成式预训练变压器 3* 。它使用生成器模型来使用任何语言生成 LaTeX。

[这个回购](https://github.com/shreyashankar/gpt3-sandbox)来自 [Shreya Shankar](https://github.com/shreyashankar) ，他专注于在现实世界中实现机器学习工作。你可以使用回购协议，也可以自己玩 GPT-3 模型。

```
>>> from api import GPT, Example, set_openai_key
>>> gpt = GPT()
>>> set_openai_key(key)
>>> prompt = "integral from a to b of f of x"
>>> print(gpt.get_top_reply(prompt))
output: integral from a to be of f of x
```

存储库 URL:[https://github.com/shreyashankar/gpt3-sandbox](https://github.com/shreyashankar/gpt3-sandbox)

# 3.自定义图像标签

GitHub stars:13000
分叉数量:4500

如今计算机视觉正在蓬勃发展。现在每个领域都在使用计算机视觉来追踪不同种类的活动。计算机视觉的一些例子是自动驾驶汽车和人类活动跟踪。

当你进行计算机视觉任务时，第一步是给你的图像加标签。这些被标记的图像然后进入你的计算机视觉模型，并相应地学习。标记图像是一项非常耗时的任务；这就是数据科学家寻找便捷工具的原因。

这个 repo 是一个 Python 工具，可以帮助标记图像。如果你想知道这个工具是如何工作的，你可以访问这篇文章。

[这份回购](https://github.com/tzutalin/labelImg)来自[卓塔林](https://github.com/tzutalin)。您可以使用 repo 并开始标记图像以执行对象检测任务。

存储库 URL:[https://github.com/tzutalin/labelImg](https://github.com/tzutalin/labelImg)

# 4.谷歌图片下载

GitHub stars: 164
分叉数量:1800

计算机视觉任务需要大量的图像来训练你的模型。我们经常寻找可以为我们的模型提供相关图像的来源。

谷歌是最好的资源。但是我们不能通过搜索来逐个下载每张图片。另一个奇妙的方法是使用 Python 脚本的强大功能。

[Ultralytics](https://github.com/ultralytics) 提供了一个 [GitHub](https://github.com/ultralytics/google-images-download) 库，它是开源的，可以通过一个命令下载高质量的图像。

```
$ git clone https://github.com/ultralytics/google-images-download
$ cd google-images-download
$ pip install -r requirements.txt
```

存储库 URL:[https://github.com/ultralytics/google-images-download](https://github.com/ultralytics/google-images-download)

# 结论

这就是本文的全部内容。我们已经讨论了对数据科学家和开发人员来说非常方便的四大 GitHub 储存库。

保持联系，获取更多有见地的文章。感谢阅读！