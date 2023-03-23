# 通过使用 GPT-3 生成查询来自动化您的数据库作业

> 原文：<https://betterprogramming.pub/automate-your-database-job-by-using-gpt-3-to-generate-queries-9e0ad2004f01>

## 人工智能现在可以根据你的命令编写复杂的数据库查询

![](img/a6b47352e0975bfbff7cbe257fb2b091.png)

照片由[亚历山大·帕萨里克](https://www.pexels.com/@apasaric?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)从[佩克斯](https://www.pexels.com/photo/lighted-vending-machines-on-street-2338113/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄。

*   我:“嗨，Excel！告诉我美国人和喜欢玩电子游戏的人的性别分布。”
*   Excel:“给你，先生！您可以在几秒钟内调出图形报告。”
*   我:“嗯。现在删除国家的过滤器，并显示修改后的全球图表。”
*   Excel:“给你！”

你能想象吗？编写复杂的数据库查询、用过滤后的数据制作图形图表、进一步修改查询，以及最终显示新修改的图表，这些完整的任务在几秒钟内就可以通过语音命令完成！

听起来很有趣，对吧？这不是梦。许多公司利用这种方法来节省时间和成本。

这是深度学习方法的一个例子，称为生成性预训练变压器-3 (GPT-3)。GPT 3 是最强大的语言模型，有 1750 亿个参数。

GPT-3 模型来自 OpenAI 社区。如果你想在工作中使用这个模型，你可以[请求他们的 API 访问](https://openai.com/blog/openai-api/)。

这是一个预训练的生成模型，这意味着您只需将输入传递给他们的模型即可获得结果。这个模型已经在数百万次的观察中训练过了。但是，如果您想根据您的数据定制他们的模型，那么您可以传递您的定制数据来重新训练模型。我们将在本文的下一部分看到这一点。

让我们使用几行 Python 代码来看看完整的模型训练过程是如何工作的。

# GPT-3 如何生成数据库查询

为了从文本数据生成数据库查询，我们需要首先根据我们的需要训练我们的 GPT-3 模型。为了在我们的数据库查询上训练 GPT-3 模型，我们将需要一组问题和答案。这里，问题代表文本查询，答案代表数据库查询。

首先，你需要安装`openai`库。运行以下命令:

```
!pip install openai
```

您现在可以像这样导入包:

```
import json
import openai
from gpt import GPT
from gpt import Example
```

申请 API 访问后，您将获得一个带有 API 键的 JSON 文件，您可以在下面的 Python 代码行中导入该文件:

```
with open('GPT_SECRET_KEY.json') as f:
    data = json.load(f)openai.api_key = data["API_KEY"]
```

现在，我们可以通过运行以下命令来创建 GPT-3 的实例:

```
gpt = GPT(engine="davinci",
          temperature=0.5,
          max_tokens=100)
```

这就对了。我们现在可以添加一些数据库查询来学习:

```
gpt.add_example(Example('Fetch unique values of SALARY from payment table.','Select distinct SALARY from payment;'))
```

通过这种方式，我们可以手动添加所有的查询来创建一个查询列表。您也可以使用循环来添加它们。

最后，在添加完所有数据后，我们可以调用`gpt.submit_request()`，它将文本作为输入，并提供一个数据库查询作为输出。

# 添加语音命令

现在我们有了一个模型，它可以根据我们作为输入传递的文本为我们提供数据库命令。此外，我们还可以使用 [Python 语音到文本模块](https://pypi.org/project/SpeechRecognition/)将人类的声音转换成文本。

运行此代码以获得相同的结果:

最后，将转换后的文本传递给模型，以获得数据库查询。一旦我们有了数据库查询，我们就可以运行 Python 命令从特定的数据库中获取数据。

# 获得正确的可视化策略

我们根据语音命令获取具体数据。最后，我们需要训练一个多标签分类模型来对不同可视化标签中的文本进行分类。

如果我的文本包含单词“分布”，那么我的分类标签应该有一组显示数据分布的图。我会把它留给你作为练习。

# 将这些点连接起来

我们现在有了一个系统，我可以说话，Python 库会把我的声音转换成文本。那条短信会传到 GPT 3 号，给我提供一个数据库查询。与此同时，我们的文本将被归类为多标签来绘制我的数据。

然后嘣！您不需要编写任何代码或考虑任何绘图策略来获得最终的数据报告。

许多大公司，如微软和谷歌，在他们不同的应用程序中使用这种方法，如邮件和聊天机器人。根据 [TechRepublic](https://www.techrepublic.com/article/from-low-code-to-no-code-azure-gpt-3-and-microsofts-power-platform/) 的说法，微软正在通过描述你想做的事情并为你编写代码片段，将低级代码变成“不要写代码”。

主要目标是在行业中创建一个无代码环境，以便专注于业务部分——而不是编码。

# 结论

本文到此为止。我们用几行 Python 代码讲述了如何使用 GPT-3 自动生成数据库查询。此外，我们讨论了如何使用 Python 将语音命令转换成文本输入。

最后，我们看到了如何对我们的文本数据进行多标签分类，以获得合适的可视化机制。我希望你喜欢这篇文章。敬请关注更多内容。感谢阅读！

*更多数据科技文章，* [*查看我的简讯*](https://mailchi.mp/4d33914bb328/pranjals-newsletter) *。*